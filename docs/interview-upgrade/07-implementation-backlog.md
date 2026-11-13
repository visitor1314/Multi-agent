# 07 文件级实施 Backlog

> 本文件描述未来改造位置；本次没有创建或修改这些业务文件。

## Epic 0：恢复可复现基线（P0，0.5–1 天）

| 任务 | 位置 | 验收 |
| --- | --- | --- |
| 新建本机 venv 并锁定依赖 | `requirements*.txt`，未来可增加 lock | 后端完整 pytest 通过，保存日志 |
| 升级 Node 环境 | 本机/CI Node 22 | `npm test && npm run build` 通过 |
| Python-only 全栈 profile | 拟新增 `docker-compose.interview.yml` 或 profiles | 不依赖 Java 后端即可启动 Vue + Python + Redis + Chroma |
| 冻结 baseline | `reports/` 或 `docs/.../evidence/` | 环境、测试、评测、性能原始输出齐全 |

## Epic 1：流式 Run 与实时轨迹（P0，2–3 天）

### 后端

| 拟新增/修改 | 职责 |
| --- | --- |
| `api/v1/chat_runs.py` | 创建、查询、取消 Run；SSE 输出 |
| `api/schemas/chat_runs.py` | Run、Event、Error schema 与版本 |
| `core/run_manager.py` | Run 状态机、任务生命周期、取消语义 |
| `core/event_store.py` | Redis Stream/内存测试实现、TTL、Last-Event-ID |
| `core/telemetry.py` | Trace/span 与脱敏属性 |
| `agents/agent_orchestrator.py` | 增加可选 event sink，不改变核心返回模型 |
| `tooling/tool_manager.py` | 工具 started/finished 事件，缓存/熔断字段 |
| `api/main.py` | 注册 v1 router；旧 `/chat` 保持兼容 |

实现策略：先让 Run 内部仍调用现有编排器并发送阶段事件，再接模型 token streaming。不要一次性重写 `/chat`。

### 前端

| 拟新增/修改 | 职责 |
| --- | --- |
| `src/lib/chatRuns.js` | 创建 Run、解析 SSE、重连与取消 |
| `src/lib/runEvents.js` | schema 校验、sequence 去重、状态归约 |
| `src/composables/useChatRun.js` | 页面生命周期、AbortController、后端切换隔离 |
| `src/components/ExecutionTimeline.vue` | Agent/Tool/阶段实时轨迹 |
| `src/components/StreamingAnswer.vue` | 增量文本、终态与重试 |
| `src/App.vue` | 抽离聊天/评测/知识库逻辑，降低单文件复杂度 |

### 测试

- 后端：幂等创建、终态唯一、Last-Event-ID、取消三阶段、事件脱敏、Redis 断开。
- 前端：乱序/重复事件、断线恢复、切后端、旧事件隔离、终态后不再追加。
- E2E：复合问题实时展示 + 主动取消 + 重连。

## Epic 2：版本化评测与质量门禁（P0，2–3 天）

| 拟新增/修改 | 职责 |
| --- | --- |
| `evaluation/manifest.py` | dataset/system/Judge 版本与哈希 |
| `evaluation/datasets/*.jsonl` | 分层 Golden Set 与风险标签 |
| `evaluation/comparison.py` | paired diff、置信区间、门禁结论 |
| `evaluation/store.py` | 原始 case 与 summary 持久化 |
| `api/v1/evaluations.py` | run/list/detail/compare API |
| `src/components/EvaluationCompare.vue` | 指标差异、回归案例和筛选 |
| `src/lib/evaluations.js` | 新评测 API 适配 |
| `.github/workflows/ci.yml` | mock/deterministic 小套件 |
| `.github/workflows/ai-eval.yml` | 手动/夜间真实模型评测 |

验收：任何报告都能回答“哪份数据、哪个模型、哪个 Prompt/Skills、哪次提交、花了多少 Token、哪些 case 回归”。

## Epic 3：OpenTelemetry 与 SLO（P1，1–2 天）

| 拟新增/修改 | 职责 |
| --- | --- |
| `core/telemetry.py` | SDK 初始化、采样、属性规范 |
| `config/otel-collector.yml` | OTLP 接收与导出 |
| `config/grafana/dashboards/echomind.json` | 请求/Agent/Tool/LLM/Eval 面板 |
| `config/alerts/*.yml` | 持续窗口和最低流量告警 |
| Compose interview 配置 | collector/trace backend/Grafana |

验收：一次聊天可在 Trace 中看到阶段瀑布，Dashboard 能定位最慢 stage；日志与 Trace 不含明文 Key/支付信息。

## Epic 4：受控语义缓存（P1，2–3 天）

| 拟新增/修改 | 职责 |
| --- | --- |
| `core/cache_policy.py` | 可缓存判定、风险绕过、版本 key |
| `tooling/semantic_cache.py` | 向量查找、阈值、TTL、失效 |
| `tooling/tool_manager.py` | exact/semantic/miss 三态与指标 |
| `evaluation/datasets/cache_adversarial.jsonl` | 否定、金额、时间、身份对抗集 |
| `tests/test_semantic_cache.py` | 隔离、失效、错误命中和并发 |

验收：高风险对抗集 0 错误命中；所有效果数字由 A/B/C replay 报告生成。若达不到，降级为只缓存检索结果。

## Epic 5：CI/CD 与安全（P1，1 天）

| Workflow/任务 | 内容 |
| --- | --- |
| backend | Ruff、mypy、pytest、coverage |
| frontend | Node 22、test、component、build |
| contract | OpenAPI/SSE JSON Schema |
| e2e | fake provider + Playwright |
| container | build、health smoke、Trivy、SBOM |
| secret | Gitleaks、禁止跟踪 `.env` |

## 工期组合

| 可用时间 | 推荐范围 | 可交付故事 |
| --- | --- | --- |
| 2 天 | M0 + 流式 happy path | “全栈实时 Agent 可解释链路” |
| 5 天 | M0 + M1 + 评测版本化 | “可展示、可回归的 AI 应用” |
| 7–10 天 | 再加 OTel/SLO 与语义缓存实验 | “质量、性能、成本闭环” |

## 风险与回退

- 模型不支持 token streaming：仍发送阶段事件，最终回答一次性 `answer.delta`，不阻塞主 Demo。
- Redis Stream 不稳定：开发测试用内存 EventStore，生产/演示切 Redis。
- 在线评测昂贵：PR 跑 mock，完整评测仅手动；设置 token budget。
- 语义缓存误命中：收紧到“只缓存检索候选”，最终生成仍调用模型。
- OTel 组件过多：先使用 Console/OTLP + 单一 Trace backend，Dashboard 后补。

## 不纳入首轮

- 微服务拆分、Kafka、Kubernetes、服务网格、多模型自动路由、GPU 微调。
- 这些可以写在后续演进，但没有真实规模和压测数据时不作为已完成亮点。

