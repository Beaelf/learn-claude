# Claude Code · 安装入门

> [← 返回知识库首页](../README.md)

---

## 1. 安装 Claude Code

### 前置要求：安装 Node.js

Claude Code 需要 Node.js 运行环境。推荐通过 Homebrew 安装：

```bash
# 安装 Node.js
brew install node

# 验证安装
node --version    # 输出类似：v22.0.0
npm --version     # 输出类似：10.0.0
```

### 安装 Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

- `-g` 表示全局安装，安装后在任何目录都可以使用

### 初次启动与认证

在终端任意目录输入：

```bash
claude
```

首次运行会引导你完成认证（登录 Anthropic 账号或输入 API Key）。完成后，你就进入了 Claude Code 的交互界面。

**常用启动方式：**
```bash
# 在当前项目目录启动
cd ~/my-project
claude

# 直接提问（非交互模式）
claude "帮我看看这个目录里有什么文件"
```

---

## 2. 三大核心组件概览

Claude Code 有三个核心机制，让它变得更聪明、更高效：

| 组件 | 一句话定义 | 详细文档 |
|------|-----------|---------|
| **Memory** | Claude 的"长期记忆"，跨会话保存你的项目信息和偏好 | [02-memory.md](./02-memory.md) |
| **Skill** | 可触发的"专项能力包"，注入特定知识和流程模板 | [03-skills.md](./03-skills.md) |
| **Subagent** | 在独立上下文中运行的专项助手，由用户或 Claude 触发，完成后汇报结果 | [04-subagents.md](./04-subagents.md) |

三者协同工作的模式，见 [05-agent-patterns.md](./05-agent-patterns.md)。
