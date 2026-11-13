# EchoMind-Frontend 项目摸底报告

## 基本信息

| 字段 | 值 |
| --- | --- |
| repo_path | E:\project\agent\EchoMind所有代码+详细文档+简历\EchoMindFrontend |
| generated_at | 2026-11-13T00:55:13.343904+00:00 |
| file_count_scanned | 23 |
| approx_total_bytes | 145048 |

## 语言和文件类型

| 语言 | 文件数 |
| --- | --- |
| Other | 13 |
| JavaScript | 8 |
| YAML | 1 |
| Shell | 1 |

## 依赖和环境线索

- `Dockerfile`
- `docker-compose.yml`
- `package.json`

## README

- `README.md`

## 核心链路线索

| 类别 | 命中文件数 | 代表路径 |
| --- | --- | --- |
| api_backend | 1 | src/main.js |
| async_jobs | 2 | src/lib/evaluationJobs.js<br>tests/evaluationJobs.test.js |
| config | 2 | public/runtime-config.js<br>vite.config.js |
| devops_deploy | 6 | .dockerignore<br>Dockerfile<br>docker-compose.yml<br>docker/entrypoint.sh<br>docker/nginx-gateway.conf<br>docker/nginx.conf |
| evaluation | 3 | src/lib/evaluationJobs.js<br>tests/evaluationJobs.test.js<br>tests/requestState.test.js |
| frontend_mobile | 2 | src/components/AppIcon.vue<br>src/components/RequestInspector.vue |
| inference_demo | 2 | src/App.vue<br>src/components/AppIcon.vue |
| testing_quality | 3 | src/components/RequestInspector.vue<br>tests/evaluationJobs.test.js<br>tests/requestState.test.js |

## Notebook / Docker / Test 线索

### Notebooks
- 无

### Docker
- `.dockerignore`
- `Dockerfile`
- `docker-compose.yml`
- `docker/entrypoint.sh`
- `docker/nginx-gateway.conf`
- `docker/nginx.conf`

### Tests
- `tests/evaluationJobs.test.js`
- `tests/requestState.test.js`

## 潜在数据/状态/模型/资源路径

- `public/runtime-config.js`

## 目录树摘要

```text
EchoMindFrontend/
  docker/
  public/
  src/
  tests/
  .dockerignore
  .gitignore
  Dockerfile
  README.md
  docker-compose.yml
  index.html
  package-lock.json
  package.json
  vite.config.js
    entrypoint.sh
    nginx-gateway.conf
    nginx.conf
    runtime-config.js
    assets/
    components/
    lib/
    App.vue
    main.js
    styles.css
      AppIcon.vue
      RequestInspector.vue
      backends.js
      evaluationJobs.js
      requestState.js
    evaluationJobs.test.js
    requestState.test.js
```

## 下一步人工确认

- 找到最小可运行命令：API、页面、CLI、worker、测试、训练或 demo 至少一个。
- 确认依赖、环境变量、数据库/数据文件、端口和外部服务。
- 确认 baseline/demo 是否能在本地、Docker、云服务器或 GPU 环境上跑通。
- 确认自己要做的面试亮点：改造点、demo、测试、报告或实验计划。
