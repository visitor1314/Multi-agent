# 面试拷问 Q&A

## 可讲线索

- `backend/audit.json`: "docker-deploy.sh"
- `backend/audit.json`: "api/main.py",
- `backend/audit.json`: "docker-deploy.sh",
- `backend/audit.json`: "data/eval/baseline.json",
- `backend/audit.json`: "wiki/assets/architecture/05-monitor-eval.svg",
- `backend/audit.json`: "wiki/教程/examples/compose.metrics-auth.yml"
- `backend/audit.json`: "api/main.py",
- `backend/audit.json`: "wiki/assets/architecture/05-monitor-eval.svg",
- `backend/audit.json`: "wiki/教程/examples/compose.metrics-auth.yml",
- `backend/audit.json`: "wiki/教程/examples/prometheus.auth.yml"

## 高频拷问

### 1. 这个项目原本解决什么问题？你为什么选它？

- 回答要点：JD 匹配点、项目来源、问题定义、输入输出。
- 准备材料：TODO: README/audit report path。

### 2. 你选择的运行深度是什么？为什么？

- 回答要点：解释 interview-only / smoke-test / local-full-run / remote-full-run 的选择，以及时间、资源、投递节奏的取舍。
- 准备材料：TODO: profile header / run plan。

### 3. 这个项目的核心链路是什么？

- 回答要点：请求入口、页面事件、数据读写、状态变化、任务调度、模型/算法步骤和输出。
- 准备材料：TODO: core file path and line notes。

### 4. 你做了什么调整？为什么面试官应该关心？

- 回答要点：API、页面、数据库、缓存、测试、部署、数据处理、模型/算法、demo 或工程整理里最能贴 JD 的增量。
- 准备材料：TODO: changed files / design notes / demo path。

### 5. baseline 是怎么跑的？复现失败时你如何定位？

- 回答要点：环境、依赖、命令、数据、随机种子、日志、错误定位过程。
- 准备材料：TODO: command / error / fix notes。

### 6. 如果没有完整指标，你怎么讲这个项目？

- 回答要点：讲清已跑通部分、卡点、资源估算、完整实验设计和预期对比口径。
- 准备材料：TODO: smoke test result / experiment plan / cost estimate。

### 7. 如果继续做，你会怎么改？

- 回答要点：优先说低风险、一天内能推进的改动，再说中高风险探索。
- 准备材料：TODO: modification design doc path。
