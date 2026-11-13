# 项目偏好与执行边界

## 已确认信息

| 项目 | 选择 |
| --- | --- |
| 目标方向 | AI 应用开发 |
| 项目范围 | EchoMind Python 后端 + `EchoMindFrontend` Vue 前端 |
| 运行深度 | 预按 `local-full-run` 设计，当前任务不要求完成真实运行 |
| 输出目录 | `EchoMind/docs/interview-upgrade/` |
| 修改授权 | 只新增方案与材料，不修改前后端业务代码 |

## 采用的策略

采用“**面试亮点优先、生产级演进兜底**”：每个主改造必须能在本地演示、能测量、能解释取舍；生产级内容只保留与演示主线直接相关的部分，不做微服务、Kubernetes、多机多卡等无必要扩张。

## 结构化偏好

```json
{
  "taste_text": "基于现有 EchoMind 前后端，面向 AI 应用开发岗位，优先本地可运行、可量化、可面试展示的工程化改造",
  "prefer_tags": [
    "ai-app",
    "python-backend",
    "vue-frontend",
    "local-full-run",
    "evaluation-driven",
    "observable",
    "interview-friendly"
  ],
  "avoid_tags": [
    "pure-frontend",
    "multi-gpu",
    "cloud-only",
    "rewrite-from-scratch",
    "unverified-metrics"
  ]
}
```

## 保守假设

用户未指定时间、GPU 和预算，因此方案按以下默认条件编排：

- 实施预算：约 7–10 个有效开发日，可按阶段停在任意可演示节点。
- 资源：单机 CPU、本地 Docker、可选一个兼容 Anthropic Messages 的在线模型 Key；不依赖 GPU。
- 当前水平：已有完整项目，重点从“代码存在”提升到“证据可复现、设计可抗追问”。
- 固定项目：不再执行外部候选仓库筛选。原工具的 Repo Discovery/Candidate Ranking 在本任务中替换为“内部改造候选排序”，以服从用户指定 EchoMind 的要求。

## 事实口径

- “现有”：能在当前代码、配置或历史报告中找到证据。
- “本次验证”：2026-11-13 在当前环境实际执行并记录的结果。
- “拟改造”：设计稿，未实现，不得写成简历完成项。
- “目标值”：验收阈值，不是已经取得的效果。

