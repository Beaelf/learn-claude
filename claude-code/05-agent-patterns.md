# Claude Code · 构建本地 Agent

> [← 返回知识库首页](../README.md)

---

## 本地 Agent 的目录结构

把 CLAUDE.md、Skills、Subagents 放在一起，就是一个完整的本地 Agent 配置。以一个 PM 工作场景为例：

```
my-pm-workspace/                        ← 你的工作目录
├── CLAUDE.md                           ← 项目背景、写作规范（每次对话自动加载）
│
└── .claude/
    ├── settings.json                   ← 权限、模型等配置
    │
    ├── skills/                         ← Skills 目录
    │   ├── prd-writer/                 ← PRD 写作 Skill
    │   │   ├── SKILL.md
    │   │   ├── template.md             ← PRD 模板文件
    │   │   └── examples/
    │   │       └── sample-prd.md
    │   │
    │   ├── weekly-report/              ← 周报生成 Skill
    │   │   ├── SKILL.md
    │   │   └── template.md
    │   │
    │   └── meeting-notes/              ← 会议纪要整理 Skill
    │       └── SKILL.md
    │
    └── agents/                         ← Subagents 目录
        ├── competitive-analyst.md      ← 竞品分析 Subagent
        ├── user-research.md            ← 用研分析 Subagent
        └── data-digest.md              ← 数据报告 Subagent
```

**说明：**
- `CLAUDE.md` 放在**项目根目录**（不是 `.claude/` 里），每次启动自动读取
- Skills 每个一个**文件夹**，里面必须有 `SKILL.md`，可以附带模板和脚本
- Subagents 每个一个**单独的 `.md` 文件**，直接放在 `agents/` 目录下

**个人全局配置**（适用所有项目，放在 home 目录）：

```
~/.claude/
├── CLAUDE.md                           ← 个人偏好，对所有项目生效
├── skills/
│   └── my-personal-skill/
│       └── SKILL.md
└── agents/
    └── my-personal-agent.md
```

> 项目级（`.claude/`）和个人级（`~/.claude/`）可以同时存在，两者都会加载。项目级通常提交到 Git，供团队共享；个人级只在自己机器上生效。

---

## Local Agent 运作全景图

下图展示了一次完整对话中，上下文如何加载、Skill 和 Subagent 如何被触发：

```
你输入一条消息
        │
        ▼
┌───────────────────────────────────────────────────────────────┐
│                     启动时加载进上下文                          │
│                                                               │
│  CLAUDE.md 全文        Skill descriptions    Agent metadata   │
│  （项目背景/规范）       （每个 Skill 的几行   （名称 + 描述，   │
│                          description 字段）    不含正文）       │
└───────────────────────┬───────────────────────────────────────┘
                        │  Claude 理解你的请求
                        ▼
              ┌─────────────────────────┐
              │     Claude 判断如何响应   │
              └──────────┬──────────────┘
                         │
           ┌─────────────┴──────────────┐
           │                            │
           ▼                            ▼
   ─────────────────           ───────────────────────
   触发 Skill                  派遣 Subagent
   ─────────────────           ───────────────────────
   把 SKILL.md 全文            新建独立上下文窗口
   + 脚本/模板                  注入 agent .md 全文
   注入当前主对话               在子进程中执行任务
                               ↓
                               只把结论汇报回主对话
   ─────────────────           ───────────────────────
   输出直接显示在              主对话收到汇报结果
   当前对话里                  继续下一步
```

---

## 串行编排：任务依赖链

**场景：竞品调研 → 分析 → 输出报告**

对应目录结构：
```
product-research/
├── CLAUDE.md                        ← 产品背景、分析框架、输出格式要求
└── .claude/
    ├── skills/
    │   └── research-report/         ← 报告写作 Skill
    │       ├── SKILL.md             ← 触发后注入报告写作规范
    │       └── template.md          ← 报告模板
    └── agents/
        ├── web-researcher.md        ← 专门负责网络搜索的 Subagent
        └── data-analyst.md          ← 专门负责结构化分析的 Subagent
```

**CLAUDE.md 内容示例：**
```markdown
# 产品背景

## 我们是谁
AfterShip Returns —— 帮助电商品牌自动化退货流程的 SaaS 产品。
目标客户：年 GMV 500 万美元以上的 DTC 品牌。

## 当前定价（内部参考，勿外传）
- Starter：$99/月，500 笔退货
- Growth：$299/月，2000 笔退货
- Enterprise：定制报价

## 竞品分析框架
每次分析竞品时，必须覆盖以下维度：
1. 定价结构（按量 / 按功能 / 混合）
2. 核心功能差异
3. 与主流电商平台的集成深度（Shopify / WooCommerce / Salesforce）
4. 目标客户规模（SMB / Mid-market / Enterprise）

## 输出规范
- 报告语言：中文，专有名词保留英文
- 数据来源需注明（官网 / 新闻 / 用户评论）
- 结尾必须有"对我们的启示"一节
```

流程图：
```
用户输入："帮我调研 Loop Returns 最新的定价策略"
    │
    ▼
主 Agent（读取 CLAUDE.md，知道产品背景）
    │
    ├─► Step 1: 派遣 web-researcher Subagent（独立上下文）
    │           任务：搜索 Loop Returns 官网、定价页、近期公告
    │           汇报：收集到的原始定价信息
    │
    ├─► Step 2: 派遣 data-analyst Subagent（独立上下文）
    │           任务：结构化整理 Step 1 的数据，对比我方定价
    │           依赖：Step 1 的结果
    │           汇报：对比表格 + 差距分析
    │
    └─► Step 3: 触发 research-report Skill（注入主对话）
                任务：按 template.md 格式生成最终报告
                依赖：Step 2 的结果
                输出：标准格式的竞品定价报告
```

**Prompt 示例：**
```
帮我调研 Loop Returns 最新的定价策略，分析对我们的影响。

步骤：
1. 先搜索 Loop Returns 官网和近期新闻，收集定价信息
2. 结合我们当前的定价（见 CLAUDE.md），做对比分析
3. 用 /research-report 模板生成一份报告，保存到 ./output/loop-pricing.md
```

---

## 并行编排：多任务同时处理

**场景：同时调研三个竞品**

对应目录结构：
```
competitive-intel/
├── CLAUDE.md                        ← 产品定位、分析维度定义
└── .claude/
    ├── skills/
    │   └── competitive-summary/     ← 汇总报告 Skill
    │       ├── SKILL.md
    │       └── template.md          ← 竞品对比表模板
    └── agents/
        └── competitive-analyst.md   ← 单个竞品分析 Subagent（复用）
```

**CLAUDE.md 内容示例：**
```markdown
# 竞品情报工作台

## 我们的产品定位
AfterShip Returns，专注退货体验管理，核心优势：
- 与 AfterShip Tracking 生态打通
- 价格比 Loop 低 30-40%
- 支持更多亚太地区承运商

## 本次分析的三个竞品
- Loop Returns：北美市场领导者，主打 Shopify 生态
- Happy Returns：主打线下退货网点，已被 PayPal 收购
- Narvar：偏 Enterprise，以追踪和通知见长

## 统一分析维度（每个竞品都必须覆盖）
1. 退货政策灵活度（规则自定义能力）
2. 退货时效（从发起到退款到账）
3. 费用结构（商家承担 vs 消费者承担）
4. 支持的退货方式（上门取件 / 自寄 / 线下网点）
5. 与 Shopify 集成深度（原生 App / API / 插件）

## 汇总报告格式
- 每个维度一列，每个竞品一行
- 我们的产品作为基准列放在最左边
- 表格下方写"综合结论"，不超过 200 字
```

流程图：
```
用户输入："同时分析 Loop、Happy Returns、Narvar 三家的退货政策"
    │
    ▼
主 Agent 拆分任务（读取 CLAUDE.md 中定义的分析维度）
    │
    ├─► [并行] competitive-analyst → 分析 Loop Returns
    ├─► [并行] competitive-analyst → 分析 Happy Returns
    └─► [并行] competitive-analyst → 分析 Narvar
                    │
                    ▼（三个子进程同时运行，互不干扰）
                    │
              主 Agent 收到三份汇报
                    │
                    ▼
         触发 competitive-summary Skill
         按模板生成三列对比表
                    │
                    ▼
         输出：完整竞品对比报告
```

**Prompt 示例：**
```
请并行分析以下三家竞品的退货政策，最后汇总成对比报告：
- Loop Returns
- Happy Returns
- Narvar

分析维度：退货时效、费用结构、支持的退货方式、与 Shopify 的集成深度

完成后用 /competitive-summary 生成对比表，保存到 ./output/returns-comparison.md
```

---

## 实用技巧

1. **给 Claude 明确的"完成标准"**：告诉它什么算完成，不要模糊
   ```
   不好："帮我分析代码"
   好："分析 ./src/utils.js，找出所有没有错误处理的异步函数，列出行号"
   ```

2. **分解复杂任务**：如果任务很大，先让 Claude 列出执行计划，确认后再执行
   ```
   "在开始修改代码之前，先列出你的实现计划，等我确认后再动手"
   ```

3. **善用 Memory 保存规范**：把团队约定存入 CLAUDE.md，每次对话都自动生效
   ```
   "请记住：我们的代码必须通过 ESLint 检查，提交前需要运行 npm run lint"
   ```

4. **让 Claude 在隔离环境工作**：对于风险较高的操作，让它在 worktree 中工作
   ```
   "请在一个新的 git worktree 中完成这个重构，完成后我来 review"
   ```

---

## 附录：常见问题

### Q：Claude Code 和网页版 Claude 有什么区别？

**Claude Code** 是运行在终端里的命令行工具，可以：
- 直接读取和修改你本地的文件
- 执行终端命令（git、npm 等）
- 通过 CLAUDE.md / Skill / Subagent 系统实现更复杂的自动化

**网页版 Claude** 只能在对话框里交流，无法直接操作本地文件。

---

### Q：如何查看 Claude Code 当前加载了哪些 Skills？

在 Claude Code 对话中输入：
```
/help
```
或者直接问：
```
你现在有哪些可用的 skills？
```

---

### Q：Commit Message 应该用中文还是英文？

看团队约定。如果你不确定，可以把规范写入 CLAUDE.md：
```
请把"我们团队的 commit message 用中文写，格式是'类型: 简短说明'"记录到 CLAUDE.md 里
```

---

### Q：我不小心 `rm` 了一个重要文件怎么办？

如果文件在 Git 仓库里，有救：
```bash
# 查看文件的历史记录
git log -- 文件路径

# 恢复最近一次提交的版本
git checkout HEAD -- 文件路径
```

如果文件不在 Git 仓库里，可以试试从时间机器（Time Machine）恢复，或联系 IT。

**这也是为什么要用 Git 管理所有重要文件的原因之一。**
