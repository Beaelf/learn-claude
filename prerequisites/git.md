# Git 指南

> [← 返回知识库首页](../README.md)

---

## 1. Git 是什么？

想象你在用 Word 写一份重要报告。你可能会手动保存多个版本：

```
报告_初稿.docx
报告_修改版1.docx
报告_修改版2_老板审阅后.docx
报告_终稿_最终版_这次是真的终稿.docx
```

**Git 做的就是这件事，但更聪明：**
- 自动记录每一次"保存"的时间和改了什么
- 可以多人同时修改同一份文件，不会互相覆盖
- 出了问题可以精确回滚到任意历史版本
- 可以创建"平行宇宙"（分支），安全实验新想法

Git 是目前全球最流行的**版本控制系统**，几乎所有技术团队都在用它。

---

## 2. 基本概念

### Repository（仓库）— 项目的"档案柜"

一个 Repository（简称 Repo）就是一个**包含了所有文件和完整历史记录的项目目录**。

类比：把整个项目想象成一本书，Repo 就是这本书加上它所有历史草稿、修改记录的完整档案盒。

### Commit（提交）— 一次"存档"

Commit 是你主动触发的一次保存动作。每次 Commit 都会记录：
- 这次改了什么内容
- 谁改的
- 什么时候改的
- 这次改动的说明（你自己写的）

类比：打电子游戏时的"存档点"。你可以随时回到任何一个存档点。

### Branch（分支）— "平行宇宙"

Branch 允许你从主线分叉出去，在一个**独立的副本**上工作，不影响主线。完成后，再把改动合并回主线。

类比：你在写一份报告，突然有个大胆的新想法，但不确定好不好。你复印一份，在复印件上尝试，原件保持不动。如果新想法好，就把复印件的内容合并回原件。

### Remote（远端）— "云上备份"

Remote 是存放在服务器上的仓库副本（比如 GitHub）。你的电脑上是本地仓库，服务器上是远端仓库，两者可以同步。

类比：本地仓库是你家里的文件，Remote 是你存在网盘上的备份，同时也是团队共享的版本。

---

## 3. Git 工作原理（简版）

Git 把你的文件管理分为**四个区域**：

```
┌─────────────────────────────────────────────────────────┐
│                        你的电脑                           │
│                                                          │
│  ┌──────────┐   git add   ┌──────────┐   git commit     │
│  │          │ ──────────► │          │ ──────────────►  │
│  │  工作区   │             │  暂存区   │                  │
│  │ Working  │ ◄────────── │  Staging │                  │
│  │  Area    │git restore  │  Area    │                  │
│  │          │--staged     └──────────┘                  │
│  └──────────┘                       │                   │
│                                     │ git commit        │
│                                     ▼                   │
│                           ┌──────────────┐              │
│                           │   本地仓库    │              │
│                           │    Local     │              │
│                           │   Repo       │              │
│                           └──────────────┘              │
└───────────────────────────────────┬─────────────────────┘
                                    │ git push / git pull
                                    ▼
                           ┌──────────────┐
                           │   远端仓库    │
                           │    Remote    │
                           │   (GitHub)   │
                           └──────────────┘
```

**四个区域解释：**

| 区域 | 说明 |
|------|------|
| **工作区**（Working Area）| 你实际看到和编辑的文件，就是你的项目目录 |
| **暂存区**（Staging Area）| 准备好"要提交"的文件的临时区域，像是购物车 |
| **本地仓库**（Local Repo）| 存在你电脑上的完整历史记录库 |
| **远端仓库**（Remote Repo）| 存在服务器（GitHub）上的仓库 |

**典型工作流：**
1. 修改文件（在工作区）
2. `git add` — 把改动放入购物车（暂存区）
3. `git commit` — 结账，把购物车的内容存入本地仓库，并附上备注
4. `git push` — 把本地仓库同步到远端

---

## 4. 安装与初始配置

### 安装 Git（macOS）

macOS 自带 Git，但版本可能较旧。推荐通过 Homebrew 安装最新版：

**第一步：安装 Homebrew（macOS 的软件包管理器）**
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**第二步：用 Homebrew 安装 Git**
```bash
brew install git
```

**验证安装成功：**
```bash
git --version
# 输出类似：git version 2.43.0
```

---

### 配置用户名和邮箱

Git 每次 Commit 都会记录"谁做的"，所以先告诉 Git 你是谁：

```bash
# 设置你的名字（会显示在 commit 记录里）
git config --global user.name "你的名字"

# 设置你的邮箱（用公司邮箱）
git config --global user.email "your.name@aftership.com"

# 验证配置
git config --global --list
```

> `--global` 表示这是全局配置，对你电脑上所有 Git 项目都生效。

---

### 配置 SSH Key（连接 GitHub）

SSH Key 就像一把"数字钥匙"，让你不用每次操作都输密码。

**第一步：生成 SSH Key**
```bash
ssh-keygen -t ed25519 -C "your.name@aftership.com"
```
- 系统会问你保存位置，直接按回车用默认位置
- 系统会问你设置密码，可以直接回车跳过（或设置一个密码）

**第二步：查看并复制公钥**
```bash
cat ~/.ssh/id_ed25519.pub
```
复制输出的内容（以 `ssh-ed25519` 开头的一长串文字）。

**第三步：把公钥添加到 GitHub**

打开 GitHub → 右上角头像 → `Settings` → `SSH and GPG keys` → `New SSH key`，粘贴公钥，保存。

**第四步：测试连接**
```bash
ssh -T git@github.com
# 成功时输出：Hi username! You've successfully authenticated...
```

---

## 5. 公司协作流程

这是你日常最常用的工作流程。假设公司有一个仓库，你需要在上面修改一些东西。

```
公司主仓库                        你的 Fork（个人副本）
(company/project)                (yourname/project)
      │                                  │
      │  Step 1: Fork                    │
      │──────────────────────────────────►│
      │                                  │
      │                                  │ Step 2: Clone
      │                                  │──────────────► 你的电脑
      │                                  │               (本地仓库)
      │                                  │
      │  Step 3: 添加 upstream remote     │
      │◄─────────────────────────────────┤
```

### Step 1：Fork 公司仓库到个人账号

在 GitHub 上找到公司仓库，点击右上角的 **Fork** 按钮，把仓库复制一份到你的个人账号下。

这样你就有了自己的"副本"，可以随意修改，不会影响公司原始仓库。

---

### Step 2：Clone 个人 Fork 到本地

在你自己的 Fork 仓库页面，点击 **Code** 按钮，复制 SSH 地址（类似 `git@github.com:yourname/project.git`）。

```bash
# 克隆仓库到本地（会在当前目录创建一个新文件夹）
git clone git@github.com:yourname/project.git

# 进入项目目录
cd project
```

---

### Step 3：添加上游 upstream 远端

默认情况下，Clone 下来的仓库只知道你的 Fork（称为 `origin`）。你还需要告诉 Git 公司的原始仓库在哪里（称为 `upstream`）：

```bash
# 添加公司原始仓库为 upstream
git remote add upstream git@github.com:company/project.git

# 验证远端配置
git remote -v
# 输出：
# origin    git@github.com:yourname/project.git (fetch)
# origin    git@github.com:yourname/project.git (push)
# upstream  git@github.com:company/project.git (fetch)
# upstream  git@github.com:company/project.git (push)
```

---

### Step 4：新建个人分支

不要直接在 `master` 分支上工作，养成好习惯：**每个任务开一个新分支**。

```bash
# 确保你在最新的 master 分支上
git checkout master
git pull upstream master

# 新建并切换到你的工作分支
# 分支名建议用 "类型/简短描述" 格式
git checkout -b feature/update-readme

# 查看当前分支
git branch
# 输出（* 号表示当前分支）：
# * feature/update-readme
#   master
```

---

### Step 5：修改文件并提交

修改你需要改的文件后，告诉 Git 你改了什么：

```bash
# 查看哪些文件被修改了
git status
# 输出：
# Changes not staged for commit:
#   modified:   README.md

# 把改动的文件加入暂存区
# 方式一：添加指定文件
git add README.md

# 方式二：添加当前目录所有改动（谨慎使用）
git add .

# 再次查看状态，确认文件已进入暂存区
git status
# 输出：
# Changes to be committed:
#   modified:   README.md

# 提交，并写清楚这次改动的说明
git commit -m "docs: update README with installation steps"
```

**写好 Commit Message 的小技巧：**

用一句简短的话说清楚"**做了什么**"，而不是"改了什么文件"。

| 好的示例 | 不好的示例 |
|---------|----------|
| `docs: add installation guide` | `update file` |
| `fix: correct typo in homepage` | `修改` |
| `feat: add dark mode support` | `改了一些东西` |

---

### Step 6：推送分支到个人 Fork

把本地的改动推送到你 GitHub 上的 Fork：

```bash
# 推送分支到你的 Fork（origin）
# 如果是第一次推送这个分支，加 -u 参数建立追踪关系
git push -u origin feature/update-readme
```

---

### Step 7：发起 Pull Request

登录 GitHub，进入你的 Fork 仓库（`github.com/你的用户名/仓库名`）。

**方式一：点击提示条（push 后短时间内才会出现）**

刚推送完分支后，页面顶部有时会出现黄色提示条：
`"feature/update-readme had recent pushes — Compare & pull request"`，直接点击即可。

> 注意：这个提示条不是每次都有，超过一段时间或刷新后会消失。

**方式二：手动发起（推荐，更稳定）**

1. 进入你的 Fork 仓库页面
2. 点击顶部的 **Pull requests** 标签
3. 点击右上角绿色按钮 **New pull request**
4. 页面会让你选择"从哪个仓库的哪个分支" → "合并到哪个仓库的哪个分支"
   - base repository：公司主仓库，base branch：`master`
   - head repository：你的 Fork，compare branch：`feature/update-readme`
5. 点击 **Create pull request**

填写 PR 信息：
- **标题**：简短说明这个 PR 做了什么
- **描述**：详细说明改动原因、改了什么、如何测试
- **Reviewers**：指派需要审阅的同事

确认目标是**公司主仓库的 `master` 分支**，点击 **Create Pull Request**。

---

### Step 8：同步上游最新代码

工作一段时间后，公司主仓库可能已经更新了。你需要把最新代码同步到本地：

**方式一：fetch + merge（分步骤，更清晰）**

```bash
git fetch upstream                  # 拉取 upstream 的最新状态
git checkout master                 # 切换到本地 master
git merge upstream/master           # 合并 upstream 最新代码
git push origin master              # 同步到你的 Fork
```

**方式二：直接 pull（命令更少，效果一样）**

```bash
git checkout master
git pull upstream master            # fetch + merge 一步完成
git push origin master
```

如果你的工作分支也需要最新代码，再做一步 rebase：

```bash
git checkout feature/update-readme
git rebase master
```

---

## 6. 常用命令速查表

| 命令 | 作用 |
|------|------|
| `git status` | 查看当前状态（哪些文件改了，哪些在暂存区）|
| `git log` | 查看提交历史 |
| `git log --oneline` | 一行一条的简洁历史 |
| `git diff` | 查看具体改了哪些内容 |
| `git stash` | 临时保存未提交的改动（切分支前用）|
| `git stash pop` | 恢复之前暂存的改动 |
| `git branch` | 查看所有本地分支 |
| `git branch -d 分支名` | 删除已合并的分支 |
| `git pull` | 拉取并合并远端最新代码 |

---

## 7. Stash：临时搁置改动

**使用场景**：你正在改东西，改了一半突然要切到另一个分支处理紧急任务，但现在的改动还没到能提交的程度。

`git stash` 就是把当前未提交的改动"压入抽屉"，让工作区变干净，之后再取出来继续。

```bash
# 把当前所有未提交的改动压入抽屉（工作区变干净）
git stash

# 给这次 stash 加个备注，方便后面认出来
git stash push -m "半成品：正在重构登录流程"

# 查看抽屉里有几份存档
git stash list
# 输出：
# stash@{0}: On feature/login: 半成品：正在重构登录流程
# stash@{1}: On feature/ui: 调整了按钮颜色

# 取出最近一份并删除记录（最常用）
git stash pop

# 取出指定的一份（编号从 0 开始）
git stash pop stash@{1}

# 只查看不取出
git stash apply stash@{0}

# 删除所有 stash 存档
git stash clear
```

> 💡 stash 是本地的，不会被 push 到远端，换电脑就没了。

---

## 8. 回滚：撤销错误改动

回滚分三种情况，对应不同的命令：

**情况一：文件改了但还没 add（工作区撤销）**

```bash
# 撤销单个文件的改动，恢复到上次 commit 的状态
git restore README.md

# 撤销所有文件的改动
git restore .
```

**情况二：已经 add 但还没 commit（从暂存区退回工作区）**

```bash
# 把文件从暂存区退回工作区（改动还在，只是取消了 add）
git restore --staged README.md
```

**情况三：已经 commit，想撤销这次提交**

```bash
# 查看历史，找到要回滚到的 commit ID
git log --oneline
# 输出：
# a3f2c1d docs: update README        ← 这是最新的，想撤销它
# b7e9a2f feat: add login page       ← 想回到这个状态

# 方式一：撤销最近一次 commit，改动退回工作区（安全，可以重新改）
git reset --soft HEAD~1

# 方式二：撤销最近一次 commit，改动也一起丢弃（慎用！）
git reset --hard HEAD~1

# 方式三：生成一个"反操作"commit，不改历史（推荐用于已推送的分支）
git revert HEAD
```

> ⚠️ `git reset --hard` 会**永久丢弃**改动，执行前确认你真的不需要这些内容。
>
> 如果这个 commit 已经 push 到远端，优先用 `git revert`，它不改历史，对团队更安全。
