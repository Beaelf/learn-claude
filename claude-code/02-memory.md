# Claude Code · Memory 机制

> [← 返回知识库首页](../README.md)

---

## 什么是 Memory？

Claude Code 本身是无状态的——每次对话结束，它就"忘记"了你们聊了什么。

**Memory 机制**通过让你在项目里维护一个 `CLAUDE.md` 文件，让 Claude 在每次对话开始时自动读取里面的内容，作为"背景知识"注入对话。

---

## 关键文件：CLAUDE.md

这是 Claude Code 官方内置的标准 Memory 文件，**不需要任何配置**，放在对应目录就自动生效。

**两种作用范围：**

| 文件位置 | 作用范围 |
|---------|---------|
| `~/.claude/CLAUDE.md` | **全局**：对你电脑上所有项目生效 |
| `<项目根目录>/CLAUDE.md` | **项目级**：只对当前项目生效 |

两者可以同时存在，都会被加载。优先级：项目级 > 全局级（如有冲突，项目级优先）。

**创建一个项目级的 CLAUDE.md：**

```bash
# 进入你的项目目录
cd ~/my-project

# 创建 CLAUDE.md
touch CLAUDE.md
```

然后用编辑器填入项目信息，示例内容：

```markdown
# 产品背景

## 产品简介
AfterShip Returns 是一个退货管理 SaaS 产品，帮助电商品牌自动化处理退货流程。
目标用户是中大型 DTC 品牌的 Operations 和 Customer Service 团队。

## 当前迭代重点
- Q2 主题：提升退货门店网络覆盖率
- 当前 Sprint：自助退货流程优化（截止 4 月 18 日）

## 竞品参考
- 主要竞品：Loop Returns、Happy Returns
- 我们的差异化：价格更低、与 AfterShip 生态集成深

## 写作风格约定
- PRD 用中文写，但字段名/功能名保留英文原词
- 用户故事格式：As a [角色], I want to [目标], so that [价值]
- 避免用"优化"这种模糊词，要写具体的用户问题是什么

## 常用干系人
- 研发 PM：@david（负责平台侧）
- 设计：@lisa
- 数据：找 #data-team 频道
```

---

## Claude Code 如何读取 CLAUDE.md

**自动读取**：每次在项目目录启动 `claude`，它会自动查找并读取 `CLAUDE.md`，无需任何操作。

**让 Claude 帮你维护**：你可以直接告诉 Claude 把某条信息写入：

```
请把"所有 PR 都需要至少 2 个 reviewer 审批"这条规定记录到 CLAUDE.md 里
```

Claude 会直接编辑 `CLAUDE.md` 文件，下次对话时自动生效。

> 💡 **实用建议**：项目初期让 Claude 扫描你的项目结构，然后问它"帮我生成一个合适的 CLAUDE.md"，它会自动整理关键信息写入文件，后续每次对话都不需要重复解释背景。

---

## 加载优先级

Claude Code 按以下顺序加载 CLAUDE.md，所有层级都会加载（不是覆盖）：

| 优先级 | 文件位置 | 用途 |
|--------|---------|------|
| 最高 | 系统级（企业统一配置）| 公司强制规范，不可绕过 |
| 中 | 项目根目录 `CLAUDE.md` | 团队共享的项目规范，提交到 Git |
| 低 | `~/.claude/CLAUDE.md` | 个人偏好，只在自己电脑生效 |

**注意**：CLAUDE.md 每次对话都**全量加载**，建议控制在 200 行内，超长内容会影响上下文效率。

---

> **官方文档**：[Memory · CLAUDE.md 指南](https://code.claude.com/docs/en/memory)
