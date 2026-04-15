---
name: claude-ai-task
description: 调用 DHF Agent 任务进行 Claude AI 搜索。输入问题，自动打开 Claude 进行 AI 问答
version: 1.0.0
metadata:
  tags: [dhf, automation, ai, claude, search, chat]
  categories: [automation, ai]
  author: "DHF Community"
  license: MIT
  homepage: https://dhf.pub
  repository: https://dhf.pub
---

# claude-ai-task

自动调用 DHF Agent **任务**，在 Claude AI 上进行搜索/问答。

## ⚠️ 重要：这是任务，不是工作流

**这是任务（Task），不是工作流（Workflow）！**

| 对比项 | 任务（Task） | 工作流（Workflow） |
|--------|-------------|-------------------|
| **ID 类型** | task_id | workflow_id |
| **MCP 服务** | `dhf_rpa_task` | `dhf_rpa_workflow` |
| **调用方法** | `task_market_run` | `workflow_market_run` |
| **结构** | 单个自动化流程 | 多个任务节点组成的工作流 |
| **当前使用** | ✅ **使用此任务** | ❌ 不使用 |

**当前任务 ID：** `待确认`

## 功能特性

- ✅ 验证输入参数
- ✅ 检查 DHF Agent 服务
- ✅ 调用 Claude AI 任务
- ✅ 浏览器自动打开执行 AI 问答

## 使用方式

```bash
# 基本调用
/claude-ai-task -k "你好"

# 带角色设定
/claude-ai-task -k "帮我写代码" -r "你是一个资深程序员"

# 完整参数
/claude-ai-task -k "分析这段代码" -r "代码专家" -o "markdown"
```

## 参数说明

| 参数 | 简写 | 必填 | 说明 |
|------|------|------|------|
| `--input` / `--keyword` | `-i` / `-k` | ✅ | 输入内容/问题 |
| `--role` | `-r` | ❌ | AI 角色设定 |
| `--output` | `-o` | ❌ | 输出格式要求 |
| `--check` | `-c` | ❌ | 检查 DHF Agent 连接 |
| `--help` | `-h` | ❌ | 显示帮助 |

## 前置条件

1. ✅ **DHF Agent 已安装并运行**
   - 启动：`/dhf-install-agent --open`

2. ✅ **浏览器可用**
   - DHF Agent 会自动调用浏览器

3. ✅ **Claude 可访问**
   - 确保 Claude 网站可以正常访问

## 输入数据结构

```json
{
  "input_text": "你好",
  "out_text": "",
  "role_text": ""
}
```

## 相关资源

- **DHF 官网：** https://dhf.pub
- **任务市场：** https://dhf.pub/nl/explore

---

**记住：这是任务，使用 `dhf_rpa_task.task_market_run`！** 🤖
