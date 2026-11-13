# 03 API 与事件协议设计

> 以下均为拟改造契约，当前代码中尚未实现。保留现有 `/chat`，新增 `/v1` 路径以兼容旧前端。

## 设计原则

- 业务幂等和流连接分离：`client_request_id` 防重复执行，`run_id` 管事件流，`request_id` 管 Trace。
- 先持久化状态，再对外发事件；SSE 断开不等于任务失败。
- 事件可重放但副作用不可重放；工具输入输出默认脱敏。
- 前端根据显式状态机渲染，不从自然语言日志猜状态。

## 创建对话 Run

`POST /v1/chat/runs`

```json
{
  "message": "应用登录报 401，而且本月多扣了 50 元",
  "user_id": "u1001",
  "conv_id": "optional-conversation-id",
  "client_request_id": "87dc37b5-23fb-47a8-87fd-9c81de500120",
  "stream": true
}
```

响应 `202 Accepted`：

```json
{
  "run_id": "run_01J...",
  "request_id": "req_01J...",
  "conv_id": "conv_01J...",
  "status": "queued",
  "events_url": "/v1/chat/runs/run_01J.../events",
  "status_url": "/v1/chat/runs/run_01J..."
}
```

规则：

- 相同用户/会话下，相同 `client_request_id + message` 返回同一结果或同一活动 Run。
- 相同 `client_request_id` 携带不同 message 返回 `409 idempotency_conflict`。
- 达到用户/全局并发上限返回 `429 concurrency_limit` 和 `Retry-After`。

## 订阅事件

`GET /v1/chat/runs/{run_id}/events`

请求头：

```http
Accept: text/event-stream
Last-Event-ID: 17
```

响应头建议：

```http
Content-Type: text/event-stream
Cache-Control: no-cache, no-transform
X-Accel-Buffering: no
```

事件信封：

```json
{
  "schema_version": "1.0",
  "event_id": 18,
  "type": "tool.finished",
  "run_id": "run_01J...",
  "request_id": "req_01J...",
  "occurred_at": "2026-11-13T09:12:31.221+08:00",
  "sequence": 18,
  "data": {}
}
```

### 事件类型

| 事件 | 关键字段 | 前端用途 |
| --- | --- | --- |
| `run.accepted` | `conv_id` | 建立时间线 |
| `memory.loaded` | `recent_count`, `episodic_count`, `profile_used` | 展示上下文来源，不展示正文 |
| `intent.resolved` | `intent`, `group`, `confidence`, `sources` | 意图卡片 |
| `route.selected` | `primary`, `supporting`, `reason`, `confidence` | 路由图 |
| `agent.started` | `agent`, `role` | Agent 状态 |
| `tool.started` | `tool`, `call_id`, `input_summary` | 工具时间线 |
| `tool.finished` | `call_id`, `status`, `latency_ms`, `cached`, `result_summary` | 工具结果 |
| `answer.delta` | `text` | 增量渲染 |
| `usage.updated` | `stage`, `input_tokens`, `output_tokens` | 成本估算 |
| `run.completed` | 完整 `ChatResponse` 摘要 | 终态与落盘结果 |
| `run.failed` | `error_code`, `retryable`, `stage` | 错误态 |
| `run.cancelled` | `safe_to_retry`, `side_effect_state` | 取消态 |
| `heartbeat` | `server_time` | 保活，不进入业务时间线 |

示例：

```text
id: 12
event: route.selected
data: {"schema_version":"1.0","event_id":12,"type":"route.selected","data":{"primary":"technical","supporting":["billing"],"confidence":0.86}}

id: 13
event: answer.delta
data: {"schema_version":"1.0","event_id":13,"type":"answer.delta","data":{"text":"先排查 401 的认证链路，"}}
```

## 查询与取消

`GET /v1/chat/runs/{run_id}` 返回 `queued/running/completed/failed/cancelling/cancelled`、开始/结束时间和最终摘要。

`DELETE /v1/chat/runs/{run_id}`：

- 未开始：直接取消。
- 生成中：取消模型流和未开始的只读工具。
- 副作用工具已开始：状态变为 `cancelling`，最终事件必须说明 `completed/failed/outcome_unknown`，不能假装回滚成功。

## 评测 API

### 创建版本化评测

`POST /v1/evaluations/runs`

```json
{
  "dataset_id": "customer-service-golden",
  "dataset_version": "2026-11-v1",
  "candidate": {
    "git_sha": "abc1234",
    "model": "provider/model-id",
    "prompt_hash": "sha256:...",
    "skills_hash": "sha256:...",
    "retrieval_config_hash": "sha256:..."
  },
  "baseline_run_id": "eval_01J...",
  "repetitions": 3,
  "budget": {"max_cases": 100, "max_estimated_tokens": 300000}
}
```

### 对比报告

`GET /v1/evaluations/runs/{run_id}/comparison`

返回：样本数、确定性断言、质量均值/区间、路由混淆矩阵、P50/P95、Token、回归案例以及门禁结论。任何缺失或 Judge 错误单独计入 `evaluation_errors`，不得补默认分。

## 语义缓存可观测字段

只对管理员/调试接口暴露：

```json
{
  "cache": {
    "mode": "semantic",
    "hit": true,
    "similarity": 0.934,
    "entry_age_s": 81,
    "kb_revision": "kb_42",
    "policy": "faq-readonly-v1",
    "bypass_reason": null
  }
}
```

普通用户响应不暴露原查询、缓存向量或其他用户信息。

## 错误格式

所有 `/v1` API 统一：

```json
{
  "error": {
    "code": "run_not_found",
    "message": "任务不存在或已过期",
    "retryable": false,
    "request_id": "req_01J...",
    "details": {}
  }
}
```

推荐错误码：`validation_error`、`unauthorized`、`forbidden`、`idempotency_conflict`、`concurrency_limit`、`run_not_found`、`provider_timeout`、`tool_outcome_unknown`、`evaluation_budget_exceeded`。

## 数据保留与安全

- SSE 事件存 Redis Stream，建议 TTL 30 分钟；最终回执沿用现有会话存储策略。
- `input_summary/result_summary` 经过 `core/redaction.py`；默认不记录完整 Prompt 和回答正文。
- `user_id` 进入日志/Trace 前做不可逆 HMAC；Token、Authorization、API Key 永不进入事件。
- 多租户校验以认证身份为准，不能信任请求体 `user_id`。

