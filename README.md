# Claude Code 知识库

> 面向非研发同学的 AI 工具使用指南，涵盖环境基础到 Claude Code 进阶用法。

---

## 前置知识

在使用 Claude Code 之前，需要掌握两个基础工具。

| 文档 | 内容 | 适合谁 |
|------|------|--------|
| [终端入门](./prerequisites/terminal.md) | 终端是什么、文件目录、常用命令 | 完全没用过命令行的同学 |
| [Git 指南](./prerequisites/git.md) | 版本控制概念、安装配置、公司协作流程、Stash 与回滚 | 需要提交代码或协作改文档的同学 |

---

## Claude Code 知识库

| 文档 | 内容 |
|------|------|
| [01 · 安装入门](./claude-code/01-getting-started.md) | 安装 Node.js、安装 Claude Code、首次启动、三大核心组件概览 |
| [02 · Memory 机制](./claude-code/02-memory.md) | CLAUDE.md 是什么、如何创建、优先级规则、PM 场景示例 |
| [03 · Skills 机制](./claude-code/03-skills.md) | Skill 是什么、触发方式、目录结构、自定义 Skill 完整示例 |
| [04 · Subagents 机制](./claude-code/04-subagents.md) | Subagent 是什么、触发方式、内置类型、自定义、与 Skill 的对比和选择指引 |
| [05 · 构建本地 Agent](./claude-code/05-agent-patterns.md) | 本地 Agent 目录结构、运作全景图、串行编排、并行编排、实用技巧 |
| [06 · 常见问题](./claude-code/06-faq.md) | 产品对比、基础使用、CLAUDE.md、Skills、Subagents、安全与权限 |

---

官方文档：[Claude Code Docs](https://code.claude.com/docs/en/)

---

## 贡献指南

- 新文档放入对应目录，并在本 README 的表格中补充链接和说明
- Claude Code 相关文档统一放 `claude-code/`，按编号递增命名
- 前置工具文档放 `prerequisites/`
