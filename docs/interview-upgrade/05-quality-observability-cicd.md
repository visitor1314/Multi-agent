# 05 测试、可观测性与 CI/CD

## 测试体系

| 层级 | 范围 | 工具/策略 | PR 是否必跑 |
| --- | --- | --- | --- |
| 单元 | 意图融合、路由、缓存、状态机、脱敏 | pytest、Node test | 是 |
| 组件 | Vue 时间线、错误态、取消按钮、评测 diff | Vue Test Utils/Vitest（拟引入） | 是 |
| 契约 | OpenAPI 与 `backends.js` 字段、SSE schema | Schemathesis/JSON Schema | 是 |
| 集成 | FastAPI + fakeredis + fake LLM/MCP | pytest-asyncio | 是 |
| E2E | 聊天、RAG、评测、断线重连 | Playwright + mock provider | 是 |
| 性能 | 1/10/30 并发，流式连接和缓存对照 | k6 或 Locust | 主分支/手动 |
| 在线 AI 评测 | 真实模型、完整数据集、成本预算 | 版本化 evaluator | 定时/手动 |

### 必补用例

- SSE 事件 sequence 单调且终态唯一。
- `Last-Event-ID` 恢复不丢事件、不重复业务执行。
- 取消发生在模型生成、只读工具和副作用工具三个阶段时语义正确。
- 相同 `client_request_id` 的并发创建只产生一个 Run。
- 缓存命中不跨用户/租户/知识库 revision/Prompt 版本。
- “可以退款”与“不可以退款”、“50 元”与“500 元”等对抗对不能语义误命中。
- 前端切换后端或连续提交后，旧 SSE 事件不能覆盖当前页面。
- Judge 输出非法、截断、认证失败时不生成虚假分数。

## OpenTelemetry Span 设计

```text
http.server POST /v1/chat/runs
  chat.run
    memory.load
      redis.get
      chroma.query
    intent.resolve
      llm.call
      embedding.query
      pattern.match
    agent.route
    agent.execute technical
      tool.call search_knowledge_base
        cache.lookup
        rag.rewrite
        chroma.query
        rag.rerank
      llm.call
    memory.commit
```

低基数字段：`service.name`、`stage`、`status`、`model`、`agent`、`tool`、`cache.mode`、`error.type`。禁止把原始用户消息、Prompt、Token 或完整订单号作为 label/attribute。

## 指标与 SLO

| 类别 | 指标 | 初始目标 |
| --- | --- | --- |
| 可用性 | 成功/部分成功请求占比 | 本地评测关键用例 100%；演示运行 > 99% 仅作后续生产目标 |
| 体验 | TTFT P95 | < 2.5 s（需 baseline 后冻结） |
| 总延迟 | `/chat` P95 | 相对 baseline 不恶化 > 15% |
| 工具 | Tool 成功率 | > 95%，按工具拆分 |
| 质量 | 关键安全断言 | 100% |
| 评测 | Intent Macro-F1 | 不低于冻结基线 - 0.02 |
| 缓存 | semantic false-hit rate | 安全/金额/否定对抗集为 0 |
| 成本 | 平均 Token/请求 | 相对 baseline 不恶化 > 10% |

## Dashboard 建议

1. 请求总量、成功/部分/失败、P50/P95/TTFT。
2. 各 Agent 调用量、成功率、延迟、路由降权状态。
3. 各 Tool 调用量、缓存命中、熔断状态、错误类型。
4. LLM 按 stage/model 的调用量、Token、估算成本、超时。
5. RAG 召回/重排耗时、命中来源、知识库 revision。
6. 评测 pass rate、Macro-F1、Judge error、最近回归。

告警应采用“持续窗口 + 最低流量”避免冷启动噪音。例如 10 分钟内至少 20 次调用且 Tool 成功率低于 90% 才告警。当前进程内告警列表还需补 resolved 生命周期和持久化/外部 Alertmanager。

## CI Workflow 设计

### `ci.yml`（每个 PR）

1. `secret-scan`：Gitleaks；确认 `.env` 未跟踪。
2. `backend-static`：Ruff + mypy（先以新增/关键模块为门禁，逐步覆盖旧代码）。
3. `backend-test`：Python 3.12，安装 `requirements-dev.txt`，本地 Embedding，pytest + coverage。
4. `frontend-test-build`：Node 22，`npm ci`、`npm test`、组件测试、`npm run build`。
5. `contract`：导出 OpenAPI，校验前端 API/SSE schema。
6. `e2e-mock`：启动 fake LLM/MCP/Redis，跑 Playwright。
7. `docker-build`：构建后端和前端镜像，运行 health smoke。
8. `security`：pip-audit/npm audit、Trivy 镜像扫描、生成 SBOM。

### `ai-eval.yml`

- PR：10–20 条不调用真实模型或使用固定响应的确定性套件。
- 手动/夜间：真实模型完整评测；设 Token/金额预算、并发和超时。
- 将 comparison JSON、失败案例和 HTML 报告上传为 artifact。
- 真实模型波动造成的非确定性指标先 advisory，关键安全断言 hard fail。

### `release.yml`

- 仅在 CI 与质量门禁通过后构建带 Git SHA 的镜像。
- 生成 SBOM 与 provenance；镜像签名。
- 本项目仅规划本地/演示交付时，可停在镜像 artifact，不必为了简历强行部署云环境。

## 分支保护建议

- 要求后端测试、前端测试/构建、契约和安全扫描通过。
- 禁止直接把真实 API Key 放入 CI；在线评测只读取受保护 secret。
- 覆盖率不以一次性高数字为目标：先冻结现状，再要求新增代码覆盖率 >= 85%、总覆盖率不下降。

