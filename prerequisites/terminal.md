# 终端入门

> [← 返回知识库首页](../README.md)

---

## 1. 什么是终端？

**终端**（Terminal）就是一个"纯文字版的 Finder"。

你在 Finder 里可以用鼠标点来点去，打开文件夹、移动文件。而终端做的是同样的事情，只不过你要**用文字命令代替鼠标点击**。

| 操作 | Finder（图形界面） | 终端（命令行） |
|------|------------------|--------------|
| 进入文件夹 | 双击 | `cd 文件夹名` |
| 查看文件列表 | 看到图标列表 | `ls` |
| 新建文件夹 | 右键 → 新建文件夹 | `mkdir 文件夹名` |
| 删除文件 | 拖到废纸篓 | `rm 文件名` |

**为什么要用终端？**
- 很多工具（Git、Node.js、Claude Code）只能通过终端使用
- 批量操作比点鼠标快得多
- 开发者之间分享操作步骤更精确

**怎么打开终端？**

按 `Command + 空格` 打开 Spotlight，输入 `Terminal`，回车即可。

---

## 2. 文件目录知识

### 目录树结构

macOS 的文件系统就像一棵倒过来的树，根部是 `/`（读作"根目录"），所有文件都从这里延伸出去：

```
/                          ← 根目录（整棵树的起点）
├── Users/
│   └── cd.l/              ← 你的家目录（用 ~ 代替）
│       ├── Documents/
│       │   └── work/
│       ├── Downloads/
│       └── Desktop/
├── Applications/          ← 应用程序
└── System/                ← 系统文件
```

### 两个最重要的路径概念

| 符号 | 含义 | 示例 |
|------|------|------|
| `/` | 根目录，从最顶层开始 | `/Users/cd.l/Documents` |
| `~` | 家目录，等同于 `/Users/你的用户名` | `~/Documents` |

**绝对路径 vs 相对路径**

- **绝对路径**：从根目录 `/` 出发，写完整地址。就像写信填"广东省深圳市南山区某街道某号"，不会有歧义。
  - 例：`/Users/cd.l/Documents/work/report.md`

- **相对路径**：从**当前所在位置**出发的路径。就像跟朋友说"往前走两个路口左转"，只对站在同一位置的人有效。
  - 例：如果你当前在 `Documents/`，写 `work/report.md` 就够了

**两个特殊符号**

| 符号 | 含义 |
|------|------|
| `.` | 当前目录 |
| `..` | 上一级目录 |

```bash
cd ..          # 回到上一级目录
cd ../sibling  # 去上一级目录里的 sibling 文件夹
```

---

## 3. 常用文件操作命令

### 查看位置和文件

| 命令 | 作用 | 示例 |
|------|------|------|
| `pwd` | 显示当前所在目录（Print Working Directory）| `pwd` |
| `ls` | 列出当前目录的文件和文件夹 | `ls` |
| `ls -la` | 列出所有文件（含隐藏文件），显示详细信息 | `ls -la` |
| `cd 路径` | 跳转到指定目录（Change Directory）| `cd ~/Documents` |
| `cd ..` | 返回上一级目录 | `cd ..` |
| `cd ~` | 回到家目录 | `cd ~` |

**示例实操：**
```bash
# 查看当前在哪里
pwd
# 输出：/Users/cd.l

# 查看当前目录有什么
ls
# 输出：Desktop  Documents  Downloads  Music  Pictures

# 进入 Documents 目录
cd Documents

# 再次查看位置
pwd
# 输出：/Users/cd.l/Documents
```

---

### 创建目录和文件

| 命令 | 作用 | 示例 |
|------|------|------|
| `mkdir 目录名` | 创建新目录 | `mkdir my-project` |
| `mkdir -p a/b/c` | 连同父目录一起创建（如果不存在）| `mkdir -p projects/2024/q1` |
| `touch 文件名` | 创建空文件（如果文件已存在，则更新修改时间）| `touch README.md` |

**示例实操：**
```bash
# 在家目录下创建一个项目目录
cd ~
mkdir my-first-project

# 进入这个目录
cd my-first-project

# 创建一个 README 文件
touch README.md

# 确认文件创建成功
ls
# 输出：README.md
```

---

### 复制、移动、删除

| 命令 | 作用 | 示例 |
|------|------|------|
| `cp 源 目标` | 复制文件 | `cp report.md report-backup.md` |
| `cp -r 源目录 目标` | 复制整个目录（`-r` 表示递归）| `cp -r project/ project-backup/` |
| `mv 源 目标` | 移动文件或重命名 | `mv old-name.md new-name.md` |
| `rm 文件名` | 删除文件（**不可撤销，慎用！**）| `rm temp.txt` |
| `rm -r 目录名` | 删除整个目录（**极度谨慎！**）| `rm -r old-project/` |

> ⚠️ **警告**：终端里删除的文件**不会进废纸篓**，直接永久消失。删除前务必确认。

**示例实操：**
```bash
# 复制一个文件作为备份
cp report.md report-backup.md

# 重命名文件（mv 用来移动，但也可以用来重命名）
mv report-backup.md report-v1.md

# 查看结果
ls
# 输出：report.md  report-v1.md

# 删除旧备份
rm report-v1.md
```

---

### 查看文件内容

| 命令 | 作用 | 示例 |
|------|------|------|
| `cat 文件名` | 在终端里打印文件全部内容 | `cat README.md` |
| `open 文件名` | 用系统默认程序打开文件 | `open report.pdf` |
| `open .` | 用 Finder 打开当前目录 | `open .` |
| `open -a "App名" 文件` | 用指定应用打开 | `open -a "Visual Studio Code" .` |

**示例实操：**
```bash
# 查看 README 文件内容
cat README.md

# 在 Finder 中打开当前目录（方便查看文件）
open .

# 用 VS Code 打开整个项目目录
open -a "Visual Studio Code" .
```

---

### 小技巧

1. **Tab 补全**：输入路径或命令时，按 `Tab` 键可以自动补全，减少手误
2. **历史命令**：按 `↑` 键可以回调上一条命令，反复按可以查看历史
3. **中断命令**：如果命令卡住了，按 `Control + C` 强制中止
4. **清屏**：输入 `clear` 或按 `Control + L` 清空终端界面（历史记录仍在）
