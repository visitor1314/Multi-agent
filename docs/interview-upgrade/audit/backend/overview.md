# EchoMind-Python 项目摸底报告

## 基本信息

| 字段 | 值 |
| --- | --- |
| repo_path | E:\project\agent\EchoMind所有代码+详细文档+简历\EchoMind |
| generated_at | 2026-11-13T00:55:12.644650+00:00 |
| file_count_scanned | 143 |
| approx_total_bytes | 6425990 |

## 语言和文件类型

| 语言 | 文件数 |
| --- | --- |
| Other | 90 |
| Python | 44 |
| YAML | 6 |
| Shell | 3 |

## 依赖和环境线索

- `Dockerfile`
- `docker-compose.yml`
- `requirements-dev.txt`
- `requirements.txt`

## README

- `README.md`
- `skills/README.md`
- `wiki/教程/README.md`

## 核心链路线索

| 类别 | 命中文件数 | 代表路径 |
| --- | --- | --- |
| api_backend | 7 | api/main.py<br>config/mcp_servers.json<br>skills/general_customer_service/SKILL.md<br>tests/mcp_fixture_server.py<br>tooling/mcp_server.py<br>wiki/assets/flows/memory-maintenance-current.mmd<br>wiki/assets/flows/memory-maintenance-current.png |
| async_jobs | 2 | evaluation/jobs.py<br>tests/test_eval_jobs.py |
| config | 5 | config/mcp_servers.json<br>config/nginx/frontend.conf<br>config/nginx/nginx.conf<br>config/nginx/web-gateway.conf<br>config/prometheus.yml |
| data_pipeline | 1 | core/skill_loader.py |
| database_state | 1 | data/chroma/chroma.sqlite3 |
| devops_deploy | 9 | .dockerignore<br>Dockerfile<br>config/nginx/frontend.conf<br>config/nginx/nginx.conf<br>config/nginx/web-gateway.conf<br>docker-compose.web.yml<br>docker-compose.yml<br>docker-deploy.sh |
| evaluation | 38 | data/eval/availability-2026-10-24.json<br>data/eval/availability-2026-10-24.junit.xml<br>data/eval/baseline.json<br>data/eval/deepseek-intent-diagnostic-2026-10-25.json<br>data/eval/embedding-2026-10-24.junit.xml<br>data/eval/embedding-chain-fixed-2026-10-25.json<br>data/eval/embedding-chain-live-2026-10-25.json<br>data/eval/embedding-live-2026-10-24.json |
| inference_demo | 6 | api/main.py<br>config/mcp_servers.json<br>data/demo_docs/sample_knowledge.json<br>data/demo_docs/troubleshooting.md<br>tests/mcp_fixture_server.py<br>tooling/mcp_server.py |
| model | 6 | wiki/assets/architecture/01-overall-architecture.svg<br>wiki/assets/architecture/02-chat-flow.svg<br>wiki/assets/architecture/03-agent-skills.svg<br>wiki/assets/architecture/04-data-storage.svg<br>wiki/assets/architecture/05-monitor-eval.svg<br>wiki/assets/architecture/06-deployment.svg |
| security_auth | 3 | tests/test_audit_regressions.py<br>wiki/教程/examples/compose.metrics-auth.yml<br>wiki/教程/examples/prometheus.auth.yml |
| testing_quality | 19 | tests/mcp_fixture_server.py<br>tests/test_agent_orchestrator.py<br>tests/test_audit_regressions.py<br>tests/test_chat_reliability.py<br>tests/test_concurrency.py<br>tests/test_eval_jobs.py<br>tests/test_evaluator.py<br>tests/test_intent_embedding.py |

## Notebook / Docker / Test 线索

### Notebooks
- 无

### Docker
- `.dockerignore`
- `Dockerfile`
- `docker-compose.web.yml`
- `docker-compose.yml`
- `docker-deploy.sh`

### Tests
- `tests/mcp_fixture_server.py`
- `tests/test_agent_orchestrator.py`
- `tests/test_audit_regressions.py`
- `tests/test_chat_reliability.py`
- `tests/test_concurrency.py`
- `tests/test_eval_jobs.py`
- `tests/test_evaluator.py`
- `tests/test_intent_embedding.py`
- `tests/test_intent_recognizer.py`
- `tests/test_knowledge_base.py`
- `tests/test_llm_utils.py`
- `tests/test_optimization_regressions.py`
- `tests/test_performance.py`
- `tests/test_standard_mcp.py`
- `tests/test_tool_manager.py`
- `tests/verify_chat_atomic.py`
- `tests/verify_memory_concurrency.py`
- `tests/verify_memory_keys.py`
- `tests/verify_request_concurrency.py`

## 潜在数据/状态/模型/资源路径

- `data/chroma/1f7ec483-dfba-4ddc-8919-72c86652ac76/data_level0.bin`
- `data/chroma/1f7ec483-dfba-4ddc-8919-72c86652ac76/header.bin`
- `data/chroma/1f7ec483-dfba-4ddc-8919-72c86652ac76/length.bin`
- `data/chroma/1f7ec483-dfba-4ddc-8919-72c86652ac76/link_lists.bin`
- `data/chroma/563fc5ab-16c6-43e6-ae77-08bf025fb028/data_level0.bin`
- `data/chroma/563fc5ab-16c6-43e6-ae77-08bf025fb028/header.bin`
- `data/chroma/563fc5ab-16c6-43e6-ae77-08bf025fb028/length.bin`
- `data/chroma/563fc5ab-16c6-43e6-ae77-08bf025fb028/link_lists.bin`
- `data/chroma/chroma.sqlite3`
- `data/demo_docs/sample_knowledge.json`
- `data/demo_docs/troubleshooting.md`
- `data/eval/availability-2026-10-24.json`
- `data/eval/availability-2026-10-24.junit.xml`
- `data/eval/baseline.json`
- `data/eval/deepseek-intent-diagnostic-2026-10-25.json`
- `data/eval/embedding-2026-10-24.junit.xml`
- `data/eval/embedding-chain-fixed-2026-10-25.json`
- `data/eval/embedding-chain-live-2026-10-25.json`
- `data/eval/embedding-live-2026-10-24.json`
- `data/eval/history/036d5ce02abd460fa38fb638e0073085.json`
- `data/eval/speed-cost-after-2026-10-25.json`
- `data/eval/speed-cost-before-2026-10-25.json`
- `data/eval/speed-cost-chat-2026-10-25.json`
- `data/eval/speed-cost-chat-initial-2026-10-25.json`
- `data/eval/speed-cost-summary-2026-10-25.json`
- `wiki/assets/architecture/01-overall-architecture.svg`
- `wiki/assets/architecture/02-chat-flow.svg`
- `wiki/assets/architecture/03-agent-skills.svg`
- `wiki/assets/architecture/04-data-storage.svg`
- `wiki/assets/architecture/05-monitor-eval.svg`

## 目录树摘要

```text
EchoMind/
  agents/
  api/
  config/
  core/
  data/
  evaluation/
  logs/
  memory/
  monitor/
  scripts/
  skills/
  tests/
  tooling/
  wiki/
  .dockerignore
  .env
  .env.example
  .gitignore
  2026.08.29改动说明.md
  2026.09.28更新说明.md
  2026.10.02更新说明.md
  2026.10.13更新说明.md
  2026.10.14更新说明.md
  Dockerfile
  README.md
  build-image.sh
  docker-compose.web.yml
  docker-compose.yml
  docker-deploy.sh
  requirements-dev.txt
  requirements.txt
  run-image.sh
    agent_orchestrator.py
    tools.py
    main.py
    alerts/
    grafana/
    nginx/
    mcp_servers.json
    prometheus.yml
      dashboards/
      provisioning/
      ssl/
      frontend.conf
      nginx.conf
      web-gateway.conf
    access.py
    concurrency.py
    conversation_state.py
    intent_embedding.py
    intent_recognizer.py
    llm_utils.py
    performance.py
    redaction.py
    skill_loader.py
    chroma/
    demo_docs/
    eval/
      1f7ec483-dfba-4ddc-8919-72c86652ac76/
      563fc5ab-16c6-43e6-ae77-08bf025fb028/
      chroma.sqlite3
      sample_knowledge.json
      troubleshooting.md
      history/
      availability-2026-10-24.json
      availability-2026-10-24.junit.xml
      baseline.json
      deepseek-intent-diagnostic-2026-10-25.json
      embedding-2026-10-24.junit.xml
      embedding-chain-fixed-2026-10-25.json
      embedding-chain-live-2026-10-25.json
      embedding-live-2026-10-24.json
      speed-cost-after-2026-10-25.json
      speed-cost-before-2026-10-25.json
      speed-cost-chat-2026-10-25.json
      speed-cost-chat-initial-2026-10-25.json
      speed-cost-summary-2026-10-25.json
    evaluator.py
    few_shot.py
    jobs.py
    conversation_memory.py
    performance_monitor.py
    check_embedding.py
    billing_support/
    general_customer_service/
    technical_support/
    README.md
      SKILL.md
      SKILL.md
      SKILL.md
    mcp_fixture_server.py
    test_agent_orchestrator.py
    test_audit_regressions.py
    test_chat_reliability.py
    test_concurrency.py
    test_eval_jobs.py
    test_evaluator.py
    test_intent_embedding.py
    test_intent_recognizer.py
    test_knowledge_base.py
    test_llm_utils.py
    test_optimization_regressions.py
    test_performance.py
    test_standard_mcp.py
    test_tool_manager.py
    verify_chat_atomic.py
    verify_memory_concurrency.py
    verify_memory_keys.py
    verify_request_concurrency.py
    __init__.py
    knowledge_base.py
    mcp_client.py
    mcp_server.py
    tool_manager.py
    assets/
    教程/
    Echo Agent一周如何速成.md
    EchoMind 场景扩展指南.md
    EchoMind学习文档.md
    EchoMind定位与技术亮点.md
    NexusOps企业智能运营协同中枢.md
    业务流程说明.md
    完整使用指南.md
    带数据指标的加强版简历模板.md
    技术亮点.md
    文档中心.md
    架构图.md
    简历包装.md
    重点代码.md
      architecture/
      flows/
      posters/
      examples/
      scripts/
      01-项目全貌与基础概念.md
      02-环境搭建与页面体验.md
      03-架构代码与数据存储.md
      04-请求入口鉴权与重试回执.md
      05-上下文读取与多轮状态.md
      06-意图识别与三路融合.md
      07-Agent路由Skills与工具执行.md
      08-知识库RAG与MCP扩展.md
      09-记忆提交压缩与画像更新.md
      10-监控降权与恢复.md
      11-评测Few-shot与回归基线.md
      12-测试与故障排查.md
      README.md
      完善说明.md
      附录-接口配置与数据字典.md
```

## 下一步人工确认

- 找到最小可运行命令：API、页面、CLI、worker、测试、训练或 demo 至少一个。
- 确认依赖、环境变量、数据库/数据文件、端口和外部服务。
- 确认 baseline/demo 是否能在本地、Docker、云服务器或 GPU 环境上跑通。
- 确认自己要做的面试亮点：改造点、demo、测试、报告或实验计划。
