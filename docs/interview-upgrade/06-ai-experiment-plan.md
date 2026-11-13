# 06 AI 实验计划

## 实验问题

主问题：在不降低客服回答正确性与安全性的前提下，流式链路和受控语义缓存能否降低体感延迟、模型调用和 Token 成本，并保持路由/工具行为稳定？

## 数据集设计

建议首版 80–120 条，拆成固定测试集和开发集，避免用测试结果反复调 Prompt。

| 子集 | 数量建议 | 关注点 |
| --- | ---: | --- |
| 单意图 | 20 | general/technical/billing/escalation 覆盖 |
| 多意图 | 15 | 主辅 Agent、合成回复、部分失败 |
| 多轮状态 | 15 | 订单/金额/错误码继承与领域切换 |
| RAG grounded | 15 | 必须引用知识，不能无依据承诺 |
| 工具与失败 | 10 | 超时、熔断、重试、副作用未知 |
| 安全/隐私 | 10 | Key、银行卡、越权、Prompt injection |
| 缓存对抗对 | 20 对 | 否定词、金额、时间、地区、身份差异 |

每条用例至少包含：`case_id`、输入/turns、预期 Agent/意图、必须/禁止工具、关键实体、参考证据、禁止声明、风险级别和标签。

## 系统版本 Manifest

```json
{
  "git_sha": "...",
  "dataset": {"id": "customer-service-golden", "version": "2026-11-v1", "sha256": "..."},
  "model": {"provider": "...", "id": "...", "temperature": 0},
  "prompts": {"orchestrator": "sha256:...", "agents": "sha256:..."},
  "skills": {"bundle_hash": "sha256:..."},
  "retrieval": {"top_k": 5, "rewrite": true, "rerank": true, "config_hash": "..."},
  "cache": {"mode": "exact|semantic", "policy": "faq-readonly-v1", "threshold": 0.93},
  "environment": {"python": "3.12.x", "node": "22.x", "platform": "windows|wsl2"}
}
```

## 实验 1：流式体验

对照同一组 30 条请求：

- A：现有同步 `/chat`。
- B：新 Run + SSE，但业务链路不变。

指标：TTFT、总耗时、事件完整率、断线恢复率、取消生效耗时、最终文本一致性、重复工具执行数。流式只应改善体感与可观测性，不应声称降低模型计算总耗时，除非数据确实如此。

## 实验 2：缓存 A/B/C

- A：关闭缓存。
- B：现有精确缓存。
- C：精确缓存 + 语义缓存。

请求分布包含：重复、同义改写、关键实体变化、否定变化、个性化问题和写操作。先离线 replay，再在隔离环境执行。

### 语义缓存阈值校准

1. 在开发集上计算正/负对相似度。
2. 选择满足高风险负对 0 误命中的最高召回阈值；初始候选可试 0.90/0.93/0.95，不预设最优值。
3. 在冻结测试集上报告 precision、recall、false-hit rate 和 95% 置信区间。
4. 若无法把高风险 false-hit 降为 0，语义缓存只用于知识检索结果，不缓存最终回答。

## 质量评分

### 确定性评分优先

- Agent/意图是否正确。
- required/forbidden tool 是否满足。
- 必需实体是否保留。
- 禁止承诺、敏感字段、越权行为是否出现。
- RAG 证据 ID 是否存在且来自当前 KB revision。

### Judge 评分补充

相关性、正确性、完整性、有用性、groundedness 各 0–1。Judge prompt/few-shot/model 也必须版本化；Judge 失败记 error，不记 0.5 或默认通过。

## 统计与报告

- 至少报告样本量、均值、中位数、P95 和 bootstrap 95% CI。
- 模型输出有随机性时，同一配置重复 3 次；确定性断言按 case 取最差结果。
- 对配对样本报告差值，不只比较两组总体均值。
- 把 provider 限流、缓存预热、网络波动和失败重试单独标注。

## 成本口径

使用 provider 返回的 input/output/cache token；价格通过独立版本化 price table 计算。未上报的内部重试和费用不得估算成精确值，报告中保留 `usage_unreported_calls`。

## 防止数据泄漏

- 调 Prompt/阈值只看开发集，最终测试集一次性评估。
- 语义缓存不预热测试集的最终回答；仅可预热公开知识库内容。
- Judge 不接收候选配置名称，避免“candidate”暗示。
- 所有用户/订单/支付数据使用合成值并脱敏。

## 预期输出

```text
reports/experiments/<run_id>/
  manifest.json
  summary.json
  cases.jsonl
  comparison.json
  confusion-matrix.csv
  latency.csv
  cache-errors.jsonl
  report.md
```

简历只使用 `comparison.json` 中能复跑的数字；如果尚未实现，就写“设计评测方案/质量门禁”，不写提升百分比。

