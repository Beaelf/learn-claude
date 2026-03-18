# Claude Code · Skills 机制

> [← 返回知识库首页](../README.md)

---

## Skill 是什么？

**Skill** 是一段预先写好的"专项指令包"。当你触发某个 Skill 时，它会把特定的知识、流程模板或操作规范注入到当前对话，让 Claude 立刻"变身"为该领域的专家。

**类比**：Skill 就像给 Claude 配了一本特定的"操作手册"。平时它是通用助手，触发 Skill 后，它就按照这本手册工作。

---

## 触发方式

**方式一：关键词匹配（自动触发）**

在对话中说出包含 Skill 配置关键词的句子，Claude 会自动识别并加载对应 Skill：

```
帮我 clone 一个 git 仓库   → 触发 aftership-git skill
有哪些 skill 可以用？       → 触发 find-skills skill
```

**方式二：斜杠命令（手动触发）**

直接输入 `/skill名称` 来精确触发：

```
/commit          → 执行提交代码的 skill
/review-pr 123   → 对 PR #123 执行代码审查
```

---

## 目录结构

每个 Skill 是一个**独立的文件夹**（不是单个文件），文件夹里有主说明文件 `SKILL.md`，还可以附带脚本、模板等资源文件。

**Skill 的存放位置：**

| 路径 | 作用范围 |
|------|---------|
| `~/.claude/skills/<skill名>/` | 个人全局，所有项目可用 |
| `.claude/skills/<skill名>/` | 项目级，只对当前项目生效，可提交 Git 共享 |

**标准目录结构：**

```
~/.claude/skills/
└── prd-writer/                ← Skill 文件夹（即 skill 名称）
    ├── SKILL.md               ← 必须有，主说明文件
    ├── template.md            ← 可选，PRD 模板
    ├── examples/              ← 可选，示例输出
    │   └── sample-prd.md
    └── scripts/               ← 可选，辅助脚本
        └── fetch_jira.py
```

---

## 自定义 Skill：完整示例

**SKILL.md 文件格式：**

文件分两部分：顶部是 frontmatter（用 `---` 包裹的配置项），下面是给 Claude 看的指令正文。

```markdown
---
name: prd-writer
description: >
  PRD 写作助手。触发场景：
  - 用户说"帮我写 PRD"、"起草需求文档"
  - 用户描述一个新功能需要整理成文档
argument-hint: [功能名称]
allowed-tools: Read, Write
---

# PRD 写作助手

收到请求后，按以下步骤工作：

1. 先问清楚：目标用户是谁？要解决什么问题？
2. 参考模板：见 [template.md](template.md)
3. 按模板结构起草 PRD，语言用中文，字段名保留英文
4. 起草完成后列出 3 个你不确定的地方，请用户确认

## 写作规范
- 用户故事格式：As a [角色], I want to [目标], so that [价值]
- 避免模糊词（"优化"、"提升体验"），要写具体的用户问题
- 每个需求点都要有验收标准（Acceptance Criteria）
```

**常用 frontmatter 字段说明：**

| 字段 | 作用 |
|------|------|
| `name` | Skill 的名称（用于 `/skill-name` 调用） |
| `description` | 描述触发场景，Claude 靠这个判断何时自动加载 |
| `argument-hint` | 斜杠命令的参数提示，显示在自动补全里 |
| `allowed-tools` | 该 Skill 激活时 Claude 可免审批使用的工具 |
| `disable-model-invocation` | 设为 `true` 则禁止 Claude 自动触发，只允许手动 `/skill-name` |
| `context` | 设为 `fork` 则在隔离的子 Agent 中运行 |

**在 SKILL.md 里引用脚本：**

```markdown
## 使用方法

拉取最新 Jira 数据后再分析：

运行脚本：!`python ${CLAUDE_SKILL_DIR}/scripts/fetch_jira.py`
```

> `!`命令`` 语法会在 Claude 读取 SKILL.md 时**先执行该命令**，把输出结果注入内容。`${CLAUDE_SKILL_DIR}` 是内置变量，指向当前 Skill 所在目录。

---

## 典型使用场景

| 场景 | Skill 功能 |
|------|-----------|
| 文档生成 | 按公司模板生成 PRD、技术方案 |
| 代码提交规范 | 自动生成符合团队规范的 commit message |
| 代码 Review | 按团队标准检查代码风格和潜在问题 |
| 周报生成 | 根据工作记录自动生成周报 |
| 部署流程 | 引导完成标准化部署检查清单 |

---

## 加载机制

Skill 采用"描述先行，内容按需"的策略：

- **启动时**：只把每个 Skill 的 `description` 字段（几行文字）加载进上下文，不占用主要 token
- **触发时**：把 `SKILL.md` 全文 + 脚本/模板内容注入当前主对话

结论：**装多少个 Skill 都不影响启动速度**，SKILL.md 写很长也没关系，不触发就不加载。
