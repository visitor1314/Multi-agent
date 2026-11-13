# 04 local-full-run 运行手册

## 当前判定

本次只完成了预审，没有完成 `local-full-run`。阻塞项是：当前终端无 Docker、后端 POSIX `.venv` 不能在 Windows 使用且系统 Python 缺依赖、Node 20.17.0 低于当前 Vite 要求。

## 目标环境

| 组件 | 建议版本 |
| --- | --- |
| Windows | Windows 11 + WSL2 或原生 PowerShell |
| Docker | Docker Desktop，Compose v2 |
| Python | 3.12.x，新建 Windows/WSL 专属 venv，不复用跨平台 `.venv` |
| Node | 22.12+ LTS |
| 内存 | 至少 8 GB，建议 16 GB |
| 模型 | 一个支持 Anthropic Messages + tool use 的模型服务 |

## Phase 0：环境与敏感信息检查

```powershell
docker version
docker compose version
python --version
node --version
npm --version
git status --short
git ls-files .env
```

验收：`.env` 不被 Git 跟踪；Node/Python/Docker 满足版本；先记录工作树已有改动，避免误覆盖。

## Phase 1：后端离线测试

在 `EchoMind` 根目录新建本机专用环境：

```powershell
python -m venv .venv-win
.\.venv-win\Scripts\python.exe -m pip install --upgrade pip
.\.venv-win\Scripts\python.exe -m pip install -r requirements-dev.txt
$env:INTENT_EMBEDDING_PROVIDER='local'
.\.venv-win\Scripts\python.exe -m pytest -q tests
```

建议补跑：

```powershell
.\.venv-win\Scripts\python.exe -m pytest -q tests\test_chat_reliability.py tests\test_performance.py tests\test_standard_mcp.py
```

记录 Python、依赖锁定结果、用例数、通过/失败数、耗时。不要只截“进程启动成功”。

## Phase 2：前端测试与构建

在 `EchoMindFrontend`：

```powershell
npm ci
npm test
npm run build
```

验收：14 个现有 Node 测试至少全部通过；Vite 构建成功；记录 bundle 大小。实施改造后还应运行 Playwright E2E。

## Phase 3：Python 后端完整依赖

在 `EchoMind`：

```powershell
docker compose up -d --build
docker compose ps
docker compose logs --tail 200 echomind
```

检查：

```powershell
Invoke-RestMethod http://localhost:8000/health
Invoke-WebRequest http://localhost:8000/metrics
Invoke-RestMethod http://localhost:8000/knowledge/stats
Invoke-RestMethod http://localhost:8000/integrations/embedding
Invoke-RestMethod http://localhost:8000/integrations/mcp
```

注意：`/health` 成功不代表模型 Key/额度/模型名可用。

## Phase 4：Vue + Python 最小全栈

当前前端 Compose 会同时拉起 Java 后端，不适合作为 Python-only 的最短路径。未实施新 Compose profile 前，推荐后端 Docker + 前端开发服务器：

```powershell
$env:VITE_PYTHON_API_URL='http://localhost:8000'
npm run dev
```

访问 `http://localhost:5173`，选择 Python 后端。后续实施时增加 `docker-compose.interview.yml` 或 Compose profile，明确只启动 `redis/chromadb/echomind-python/frontend/gateway/prometheus/otel`。

## Phase 5：五条核心场景

| 场景 | 输入 | 预期证据 |
| --- | --- | --- |
| 技术支持 | “登录一直报 401” | technical 路由、排查步骤、trace |
| 账单 | “为什么多扣 50 元” | billing 路由、字段核验、不伪造退款 |
| 多 Agent | “登录失败且重复扣款” | 主辅 Agent 与合成回复 |
| RAG | 导入退款政策后提问 | `knowledge_used=true`、Tool Trace 有检索 |
| 幂等 | 同一 `client_request_id` 重试 | 不重复写消息/执行工具，返回相同回执 |

实施流式改造后再增加：中途取消、SSE 断线重连、`Last-Event-ID` 恢复和旧请求不覆盖新页面。

## Phase 6：评测与性能基线

```powershell
$body = @{ } | ConvertTo-Json
$job = Invoke-RestMethod -Method Post -Uri http://localhost:8000/eval/run -ContentType 'application/json' -Body $body
Invoke-RestMethod "http://localhost:8000/eval/jobs/$($job.job_id)"
```

轮询至终态，保存原始 JSON。至少记录：suite/system 版本、样本量、pass rate、Macro-F1、完整性、有用性、P50/P95、Token、模型调用数和失败案例。

## Phase 7：故障演练

- 暂停 ChromaDB：验证 development 降级和 production fail-closed 边界。
- 让一个 MCP 工具超时：验证熔断、trace 和 fallback。
- 模型超时：验证 `retryable/error_code` 与前端错误态。
- 两个相同会话并发：验证 FIFO/lease；不同会话保持并行。
- 重启应用后查询评测 Job：记录当前内存 Job 丢失的限制。

## 证据目录建议（实施时）

```text
docs/interview-upgrade/evidence/
  environment.md
  pytest.txt
  frontend-test.txt
  frontend-build.txt
  compose-ps.txt
  smoke-http.json
  eval-baseline.json
  eval-candidate.json
  performance-comparison.json
  screenshots/
  demo-script.md
```

## 完整通过条件

- 后端测试、前端测试和构建均通过。
- Compose 中核心服务健康，前端能完成 5 条场景。
- 至少一份版本化评测报告和一份性能报告可重跑。
- 所有指标可追溯到原始输出；失败场景和环境版本被记录。
- 停止命令和数据保留策略明确，演示不会误删用户数据。

