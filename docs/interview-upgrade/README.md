# EchoMind 面试展示改造材料

> 目标岗位：AI 应用开发｜规划深度：`local-full-run`｜交付性质：只设计与记录，不修改业务代码

## 推荐结论

EchoMind 已经具备多 Agent、RAG、记忆、MCP、评测、缓存、监控和容器化，不适合再用“增加 Redis / 增加测试 / 增加 Prometheus”作为主要亮点。最有面试区分度的路线是把现有能力连成可验证的 AI 工程闭环：

1. **流式对话与实时执行轨迹**：把意图、路由、工具、模型输出从最终结果升级为可取消、可重连、可解释的事件流。
2. **AI 评测实验台与发布门禁**：把已有评测升级为带数据集、Prompt、模型、Skills 版本的可比较实验，并接入 CI。
3. **安全语义缓存与成本/SLO 闭环**：在已有精确缓存之上设计受约束的语义缓存，用命中率、错误命中率、P95 延迟和 Token 成本证明效果。

横向工程能力由 OpenTelemetry、结构化日志、契约/E2E 测试和 CI/CD 贯穿。建议先完成前两项，再根据时间实现语义缓存实验。

## 材料索引

| 文件 | 用途 |
| --- | --- |
| [project-profile.md](project-profile.md) | 已确认偏好、边界和事实口径 |
| [01-current-state-audit.md](01-current-state-audit.md) | 前后端现状、代码证据、基线验证与缺口 |
| [02-upgrade-blueprint.md](02-upgrade-blueprint.md) | 三项主改造、架构和分阶段路线 |
| [03-api-and-event-design.md](03-api-and-event-design.md) | 新 API、SSE 事件、错误与幂等契约 |
| [04-local-full-run-runbook.md](04-local-full-run-runbook.md) | 本地完整运行步骤、场景和证据清单 |
| [05-quality-observability-cicd.md](05-quality-observability-cicd.md) | 测试金字塔、监控、SLO 与流水线门禁 |
| [06-ai-experiment-plan.md](06-ai-experiment-plan.md) | 评测与语义缓存实验设计、指标与防泄漏 |
| [07-implementation-backlog.md](07-implementation-backlog.md) | 文件级任务、工期、验收和依赖 |
| [08-interview-pack.md](08-interview-pack.md) | 简历口径、演示脚本、代码讲解与面试问答 |
| `audit/backend/` | shushu-internship-tool 生成的后端审计原件 |
| `audit/frontend/` | shushu-internship-tool 生成的前端审计原件 |
| `generated-interview-pack/` | 工具生成的原始面试包骨架，保留作溯源；正式版本以 `08-interview-pack.md` 为准 |

## 当前状态

- 已完成：前后端只读审计、工具标准审计产物、现有测试/构建探测、改造设计和面试材料。
- 未完成：Docker baseline、真实模型请求、真实 Redis/ChromaDB 联调以及任何拟改造代码。
- 当前不能声称：已实现流式接口、语义缓存、OpenTelemetry 或 CI/CD；也不能把历史评测数字表述为本次改造结果。

## 建议实施顺序

```text
先恢复可复现基线
  -> 流式事件链路 + 前端时间线
  -> 评测版本化 + PR 门禁
  -> OTel/SLO + Dashboard
  -> 安全语义缓存实验
  -> 7 分钟演示录屏与简历定稿
```

