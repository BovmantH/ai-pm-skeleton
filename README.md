# AI 项目管理骨架（ai-pm-skeleton）

[English](README.en.md) | 简体中文

一套**工具无关**的项目管理规则骨架，用于让 Claude Code / Codex / Gemini 等 AI 编程助手按统一约定管理你所有的项目（寻址、初始化、索引、模板、红线）。

骨架本体只有一个 `Project/` 目录，把它放到任何位置都能用——它就是你所有 AI 管理项目的"起点"（工作区根）。

---

## 命名说明（重要）

项目代号 `ai-pm-skeleton` 拆开是：

- **ai** — AI 编程助手（Claude Code / Codex / Gemini 等）
- **pm** — **Project Management（项目管理）**，**不是 Product Manager（产品经理）**
- **skeleton** — 骨架（只含规则、模板、索引；不含任何业务代码）

> ⚠️ 本项目与"产品经理 / 产品需求 / PRD / 用户故事"等产品岗位职能**没有任何关系**。它解决的是「AI 助手在多项目环境下如何被统一约束、如何寻址、如何按模板初始化项目」这类**工程协作流程**问题。如果你在找产品经理协作工具，这里不是。

---

## 首次使用（必做两步）

```bash
# 1. 把仅本机使用的外部项目索引拷一份(原文件已被 .gitignore)
cp Project/PROJECTS_LOCAL.md.example Project/PROJECTS_LOCAL.md

# 2. 按本机环境改写 DESC_ENV.md(里面是占位模板,不改 AI 会一直给错命令)
#    Windows 用 notepad / VS Code,macOS/Linux 用 vim / nano 都行
```

完成后把 AI 工具(Claude Code / Codex / Gemini)的工作目录指向 `Project/`,直接对话即可。

---

## 使用示例

### 示例一：在骨架中创建一个新项目并完成初始化

> 场景：今天要新开一个项目叫 `aiBovmantH`，让 AI 在骨架内按规范开出一个项目文件夹。

1. 把 `Project/` 放到你想用的任意位置（例如 `D:\Project\` 或 `~/workspace/`），并把 AI 工具的工作目录指向它。
2. 直接和 AI 说：

   > 我要新建一个项目 `aiBovmantH`，用于计划收购OpenAI，并完成初始化。

3. AI 的预期行为（来自 `CLAUDE.md` / `AGENTS.md` / `DESC_DIR.md`）：
   - 启动时自动读取 `RULES_GLOBAL.md` / `PROJECTS_INDEX.md` / `DESC_ENV.md` / `DESC_DIR.md` / `PROJECTS_LOCAL.md`。
   - 在 `PROJECTS_INDEX.md` 中确认没有同名项目，向你确认建议的文件夹名（形如 `20260509_aiBovmantH`，日期取当天系统日期）。
   - 你确认后，AI 会读取 `_template/PROJECT.md` 和 `_template/PROGRESS.md`，把占位符（`{项目名}`、`{YYYY-MM-DD}` 等）替换为实际值后写入新文件夹。
   - 在 `PROJECTS_INDEX.md` 的「活跃项目」表里追加一行项目索引。
   - 后续所有该项目的文件操作都会被锁定在这个文件夹内（`DESC_DIR.md` 第 4 条）。

最终目录形如：

```
Project/
├── PROJECTS_INDEX.md          ← 表里多一行 20260509_aiBovmantH
└── 20260509_aiBovmantH/
    ├── PROJECT.md             ← 已按模板填充
    └── PROGRESS.md
```

### 示例二：把本机已存在的项目关联进来并完成初始化

> 场景：本机已有一个老项目（Windows 例：`D:\develop\workspace\my-old-project`；macOS/Linux 例：`~/code/my-old-project`），希望今后也按这套规则管理它，但**不把它物理迁移**到骨架目录里。

1. 把 `Project/` 放到任意位置作为工作区根，AI 工具工作目录指向它。
2. 直接和 AI 说：

   > 请把 `D:\develop\workspace\my-old-project` 关联到当前工作区作为一个外部项目，并完成初始化。

3. AI 的预期行为：
   - 启动时自动读取上述 5 个规则文件。
   - 在 `PROJECTS_LOCAL.md` 的「外部项目」表里追加一行（绝对路径 + 项目名 + 状态 + 说明）。
     之所以写 `PROJECTS_LOCAL.md` 而不是 `PROJECTS_INDEX.md`，是因为前者**仅本机使用、不跟随同步**（适合机器路径）；后者跟随仓库同步，登记的是骨架内项目。
   - 进入外部项目目录读取（或为其创建）`PROJECT.md`，按 `_template/` 补齐缺失的文档骨架。
   - 后续所有针对该项目的修改都会被锁定在 `D:\develop\workspace\my-old-project` 内，受同一套红线约束。

`PROJECTS_LOCAL.md` 中追加后形如：

```
| `D:\develop\workspace\my-old-project` | My Old Project | 活跃 | 外部引用,受本工作区规则约束 |
```

---

## 这套骨架是怎么工作的

- **统一入口**：无论你用 Claude Code、Codex 还是 Gemini，每次会话开始都会被指引先读完 5 个规则文件（`CLAUDE.md` 给 Claude Code，`AGENTS.md` 给 Codex，Gemini 用户可在首条提示里让其先读这几个文件）。
- **状态唯一落点**：所有项目状态、变更、约定只落到 `PROJECTS_INDEX.md`（骨架内）/ `PROJECTS_LOCAL.md`（本机外部）/ 子项目 `PROJECT.md`。工具自身的配置文件（`.cursorrules` / `~/.claude/CLAUDE.md` 等）一律只读不写——这样规则跨工具 100% 可移植。
- **目录红线**：`plan/` 与 `archive/` 默认静默，AI 不主动读；新项目必须按 `[YYYYMMDD]_[名]` 命名；严禁跨项目修改、严禁向工作区根写业务代码。详见 `DESC_DIR.md`。

---

## 目录树解释

```
Project/                       ← 工作区根,放到任意目录都能用
│
├── CLAUDE.md                  ← Claude Code 启动协议:进入会话立即读取下面 5 个规则文件
├── AGENTS.md                  ← Codex / OpenAI 系工具启动协议(内容与 CLAUDE.md 等价)
│
├── RULES_GLOBAL.md            ← 全局行为准则:中文/前置确认/上下文优先/握手协议/反碎片化/代理降级
├── DESC_ENV.md                ← 本机环境描述:OS、PowerShell/Bash、Python/Node 版本等
├── DESC_DIR.md                ← 目录红线:寻址规则、静默区、新项目流程、模板机制、操作边界
│
├── PROJECTS_INDEX.md          ← 跟随仓库的全局项目索引(骨架内项目登记表)
├── PROJECTS_LOCAL.md          ← 仅本机使用的外部项目索引(绝对路径,不同步)
│
├── _template/                 ← 新项目模板源,只读保护
│   ├── PROJECT.md             ← 项目主文档骨架(概述/结构/资源/核心内容/进度记录)
│   └── PROGRESS.md            ← 进度追踪骨架(阶段、任务、状态、时间轴、验证清单)
│
├── plan/                      ← 规划/构思区,AI 默认静默不主动读
│
└── archive/                   ← 归档区,存放已完成或弃用的项目,AI 默认静默不主动读
```

各文件作用速查：

| 文件 / 目录 | 谁读 | 作用 |
|------------|------|------|
| `CLAUDE.md` | Claude Code 自动 | 启动时强制读 5 个规则文件 |
| `AGENTS.md` | Codex 自动 | 同上,Codex 版本 |
| `RULES_GLOBAL.md` | AI 启动时读 | 行为准则:语言、前置确认、代理、反碎片化等 |
| `DESC_ENV.md` | AI 启动时读 | 让 AI 给出和你本机匹配的命令(PowerShell vs Bash 等) |
| `DESC_DIR.md` | AI 启动时读 | 目录红线和工作流(寻址、静默、新项目、模板) |
| `PROJECTS_INDEX.md` | AI 启动时读 | 项目地图,新建/归档骨架内项目时同步 |
| `PROJECTS_LOCAL.md` | AI 启动时读 | 关联本机外部路径下的已有项目 |
| `_template/` | AI 创建新项目时读 | 项目骨架源,严禁日常修改 |
| `plan/` | 默认不读 | 仅在你明确要求时 AI 才会进去 |
| `archive/` | 默认不读 | 同上 |

---

## 注意事项

- **工具专属配置只读不写**：你机器上可能已有 `~/.claude/CLAUDE.md`、`.cursorrules`、Gemini 的隐藏配置等,本骨架严禁向那些文件写状态/规则,保证可移植。
- **状态只能落到三个地方**:`PROJECTS_INDEX.md`、`PROJECTS_LOCAL.md`、子项目 `PROJECT.md`。
- **新项目命名**:`[YYYYMMDD]_[项目名]`,日期必须用当天真实系统日期,不能编。
- **代理端口**:默认骨架假设本机有 `127.0.0.1:10808` 的 HTTP/SOCKS5 混合代理,且仅在网络异常时降级使用。如果你本机不是这个端口,或不需要代理,请改 `RULES_GLOBAL.md` 第 6 条。
- **环境描述**:`DESC_ENV.md` 入库的是占位模板,**到新机器第一件事就是按本机情况改它**,否则 AI 会一直按错误的 shell 给命令。
- **本机外部索引**:`PROJECTS_LOCAL.md` 已被 `.gitignore`,入库的是 `PROJECTS_LOCAL.md.example`。首次使用 `cp` 一份即可(见上方"首次使用")。
- **首次启用**:`PROJECTS_INDEX.md` 和 `PROJECTS_LOCAL.md` 留的是空表头,等你的第一个项目按上面两个示例自然填进去。
