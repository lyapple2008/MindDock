可以。基于目前各项目仓库的架构资料，我建议不要把它们简单理解成“几个类似的 Todo Agent”，它们其实处于**不同的架构层级**。

## 1. 先看整体定位

| 项目           | 核心定位                             | Agent 强度 |  本地优先 | Task/Reminder | Memory |  扩展机制 |
| ------------ | -------------------------------- | -------: | ----: | ------------: | -----: | ----: |
| **Keel**     | Personal AI Workspace            |        中 | ★★★★★ |         ★★★★★ |  ★★★★★ |     中 |
| **Leon**     | Personal Agent Runtime           |    ★★★★★ |  ★★★★ |           ★★★ |  ★★★★★ | ★★★★★ |
| **OpenClaw** | General Agent Gateway/Runtime    |    ★★★★★ |  ★★★★ |          ★★★★ |  ★★★★★ | ★★★★★ |
| **Untask**   | AI Task Manager                  |       ★★ | ★★★★★ |         ★★★★★ |    ★★★ |    ★★ |
| **你的项目**     | Lightweight Local Personal Agent |      ★★★ | ★★★★★ |         ★★★★★ |   ★★★★ |   ★★★ |

这里的星级是**架构特征的相对描述，不是产品评分或优劣排名**。

---

# 2. 五个项目最核心的架构区别

我会把它们画成下面这样：

```text
                         AI Personal Software
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
       Personal Workspace   Agent Runtime    Task Manager
              │                 │                 │
            Keel          Leon / OpenClaw       Untask
              │
              │
              └──────────────┐
                             ▼
                      Your Project
```

你的项目其实不是简单复制其中任何一个，而是：

> **Keel 的 Personal Workspace + Leon 的 Tool/Skill 思想 + OpenClaw 的 Agent Runtime 简化版 + Untask 的 Task UX**

---

# 3. Keel：最接近你的“产品形态”

Keel Labs 的 Keel 目前是一个 local-first desktop assistant。

它把一个很重要的概念放在第一位：

```text
                 Keel
                   │
          ┌────────┴────────┐
          ↓                 ↓
     Local Workspace       Model
          │                 │
     Markdown files    Claude / GPT
          │             Ollama / ...
          ↓
     Projects / Tasks
     Daily Logs / Wiki
```

Keel 的数据核心是一个本地 Markdown workspace，而模型是可以替换的“租户”；它还提供 Tasks、Reminders、Daily Brief、EOD summary、Scheduled Jobs、Whisper 等能力。([GitHub][1])

### 它最值得你学习的地方

**不是 Agent loop，而是“个人上下文系统”。**

例如：

```text
~/Keel/
├── projects/
├── daily/
├── wiki/
├── tasks/
└── ...
```

然后 Agent 每次工作的时候动态构造：

```text
System Prompt
+
Relevant Projects
+
Recent Captures
+
Open Tasks
+
Search Results
```

这与你想做的：

```text
SQLite
  ↓
Memory
  ↓
Relevant Context
  ↓
MiniCPM
```

实际上是同一个问题。

### Keel 和你的项目最大的区别

Keel 更像：

> **“AI + Personal Knowledge Workspace”**

而你的初始目标更像：

> **“AI + Personal Task/Automation Engine”**

所以我会借鉴 Keel 的 **Context / Memory / Daily workflow**，但不一定复制它的 Markdown-first 数据模型。

---

# 4. Leon：最值得研究 Agent Runtime

Leon AI 当前 2.0 Developer Preview 的架构已经非常 Agent-oriented。

官方当前架构大致是：

```text
Leon
 │
 ├── Server
 │    ├── Routing
 │    ├── Context
 │    ├── Memory
 │    └── Agent execution
 │
 ├── Skills
 │    ├── Native skills
 │    └── Agent skills
 │
 ├── Bridges
 │
 ├── Toolkits
 │
 └── Functions / Binaries
```

官方明确把能力组织成：

```text
Skills
   ↓
Actions
   ↓
Tools
   ↓
Functions
   ↓
Binaries
```

同时支持 `smart / controlled / agent` 等不同执行模式，以及分层 Memory。([GitHub][2])

### 这对你的项目特别重要

你之前想做：

```text
MiniCPM
  ↓
Tool Calling
  ↓
create_todo()
create_reminder()
record_work_log()
```

我建议进一步变成：

```text
                 Agent
                   │
          ┌────────┴────────┐
          ↓                 ↓
      Controlled          Agent
          │                 │
          ↓                 ↓
    deterministic       LLM planning
       tools             + tools
```

比如：

```text
“明天提醒我提交周报”
```

完全没必要让 Agent 自主规划。

直接：

```text
Intent
 ↓
create_reminder()
```

而：

```text
“帮我整理一下这周工作，并找出下周最应该处理的事情”
```

才进入：

```text
Agent
 ↓
query_work_logs()
 ↓
query_tasks()
 ↓
summarize()
 ↓
create_tasks()
```

这就是 Leon 的架构思想非常值得借鉴的地方。

---

# 5. OpenClaw：不是你的产品，但非常值得研究 Runtime

OpenClaw 当前更接近：

> **Agent infrastructure / Gateway**

它解决的是：

```text
                 OpenClaw Gateway
                        │
       ┌────────────────┼────────────────┐
       ↓                ↓                ↓
   Channels          Agent Runtime      Tools
       │                │                │
 Discord             Sessions          Browser
 Telegram            Context           Files
 WhatsApp            Memory            Exec
 iMessage             Skills            ...
```

它的核心 Agent runtime 负责：

* model discovery
* tool wiring
* prompt assembly
* session management
* channel delivery

而且 Skill / Tool / Plugin 是明确分层的。([GitHub][3])

它的 Memory 也采用本地 Markdown：

```text
workspace/
├── USER.md
├── MEMORY.md
└── memory/
    ├── 2026-09-17.md
    └── 2026-09-18.md
```

同时通过 `memory_search` / `memory_get` 检索。([GitHub][4])

### 但你不要照搬 OpenClaw

OpenClaw 解决的问题明显更大：

```text
Multi-channel
+
Multi-agent
+
Skills
+
Plugins
+
Browser
+
Automation
+
Gateway
+
Sessions
```

你的 MVP 根本不需要这些。

你的 Runtime 可以只有：

```text
Agent
 │
 ├── Context
 ├── Memory
 ├── Tools
 └── Scheduler
```

这会简单很多。

---

# 6. Untask：最值得学习“数据层 + macOS UX”

Untask 与你的目标也很接近。

它采用：

```text
macOS
  │
  ├── React UI
  │
  ├── Main Process
  │    ├── SQLite
  │    ├── Filesystem
  │    ├── Tray
  │    ├── Shortcuts
  │    └── AI
  │
  └── IPC
```

而且所有任务、Notes、Conversation 都保存在本地 SQLite；数据库写入有 Zod 校验和 audit trail。([GitHub][5])

这其实和你计划的架构非常接近：

```text
TypeScript
     │
     ├── Agent
     ├── Tools
     │
     ↓
   SQLite
```

### Untask 最值得你借鉴

不是 Agent。

而是：

**数据模型 + macOS integration。**

比如：

```text
Task
 ├── id
 ├── title
 ├── status
 ├── priority
 ├── due_at
 └── project_id

Reminder
 ├── id
 ├── task_id
 ├── trigger_at
 └── repeat_rule

WorkLog
 ├── id
 ├── timestamp
 ├── project
 └── content
```

这部分其实比“让 LLM 更聪明”重要得多。

---

# 7. 我建议你的架构

结合这几个项目，我会把你的项目设计成：

```text
┌─────────────────────────────────────────────┐
│                 macOS App                   │
│                                             │
│  Menu Bar / Notification / CLI / UI         │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│              Personal Agent                 │
│                                             │
│  ┌────────────┐    ┌────────────────────┐  │
│  │ Intent     │    │ Agent Loop         │  │
│  │ Router     │    │                    │  │
│  └─────┬──────┘    │ Plan → Tool → ... │  │
│        │            └─────────┬──────────┘  │
│        └──────────────────────┘             │
│                     │                       │
│              ┌──────▼──────┐                │
│              │ Tool System  │                │
│              └──────┬──────┘                │
└─────────────────────┼───────────────────────┘
                      │
       ┌──────────────┼────────────────┐
       ▼              ▼                ▼
  Task Tools      Memory Tools    System Tools
       │              │                │
       ▼              ▼                ▼
    SQLite       SQLite/Files      macOS APIs
       │
       └──────────────┬────────────────┘
                      ▼
                 Scheduler
                      │
              ┌───────┴───────┐
              ▼               ▼
           Reminder        Daily Job
              │               │
              └───────┬───────┘
                      ▼
                 Agent Wakeup

                      │
                      ▼
             ┌─────────────────┐
             │ LLM abstraction  │
             └────────┬────────┘
                      │
              ┌───────┴────────┐
              ▼                ▼
        MiniCPM5 MLX        Ollama
```

---

# 8. 最关键的一点：LLM 不应该成为系统核心

这是我认为你这个项目和很多个人 Agent 项目最应该拉开差异的地方。

不要：

```text
Everything
   ↓
LLM
   ↓
Do something
```

而是：

```text
                User
                  │
                  ▼
             Intent Router
                  │
        ┌─────────┴──────────┐
        ↓                    ↓
 Deterministic            Agent
    Action                   │
        │             ┌──────┴──────┐
        ↓             ↓             ↓
 create_task()    Memory        Tool calls
 reminder()       Search        Reasoning
 log_work()          │
        │             │
        └─────────────┘
```

例如：

**简单任务**

> 明天 9 点提醒我开周会

```text
Intent
 ↓
create_reminder()
 ↓
Scheduler
```

不需要 Agent。

**复杂任务**

> 总结我这周的工作，并整理下周需要继续跟进的事情

```text
Agent
 ↓
query_work_logs()
 ↓
query_tasks()
 ↓
query_project_memory()
 ↓
LLM summarize
 ↓
create_tasks()
```

这时候才使用 Agent。

---

# 9. Memory 我建议采用“SQLite + Markdown”混合方案

这里可以吸收三个项目的优点。

### Keel / OpenClaw

非常适合：

```text
Markdown
 ↓
Human readable
 ↓
Git / Backup / Edit
```

### Untask

非常适合：

```text
SQLite
 ↓
Structured data
 ↓
Fast query
```

### 你的项目

我建议：

```text
              Memory
                 │
        ┌────────┴─────────┐
        ↓                  ↓
 Structured             Semantic
   Memory                Memory
        │                  │
     SQLite             Markdown
        │                  │
 Tasks / Logs         Preferences
 Reminders            Decisions
 Events               Project context
```

**Task / Reminder / WorkLog 不要放 Markdown。**

它们是结构化数据。

而：

```text
“我最近在做 iOS 字幕项目”
“我喜欢先做性能 profiling”
“这个项目采用 OpenSpec”
```

这种长期语义信息，可以放 Memory。

---

# 10. 五个项目对你的启发，可以浓缩成一句话

```text
Keel
  → 学 Context / Workspace / Daily workflow

Leon
  → 学 Agent / Skill / Action / Tool

OpenClaw
  → 学 Runtime / Plugin / Session / Automation

Untask
  → 学 SQLite / Task model / macOS UX

你的项目
  → 把它们缩小到一个 16GB Mac mini 能长期稳定运行的 Local Agent
```

因此我不会建议你 fork 其中任何一个。

**更适合你的路线是自己做一个很小的 Agent Runtime，然后有选择地吸收它们的设计。**

---

## 11. 我会把你的 MVP 定到这个程度

第一版甚至不需要 GUI：

```text
personal-agent/
├── src/
│   ├── agent/
│   │   ├── agent-loop.ts
│   │   ├── context.ts
│   │   └── router.ts
│   │
│   ├── llm/
│   │   ├── llm.ts
│   │   ├── mlx.ts
│   │   └── ollama.ts
│   │
│   ├── tools/
│   │   ├── tasks.ts
│   │   ├── reminders.ts
│   │   ├── worklog.ts
│   │   └── memory.ts
│   │
│   ├── scheduler/
│   │   └── scheduler.ts
│   │
│   ├── memory/
│   │   ├── sqlite.ts
│   │   └── markdown.ts
│   │
│   └── cli.ts
│
├── data/
│   ├── assistant.db
│   └── memory/
│
└── package.json
```

第一阶段只实现：

```text
自然语言
   ↓
MiniCPM5-2B
   ↓
Tool Calling
   ↓
SQLite
   ↓
Scheduler
```

然后再加：

```text
Menu Bar
Notification
Voice
Daily Brief
Proactive Agent
```

这样你的项目就会从一个**非常小、可验证的 Agent Runtime**开始，而不是一上来就变成 OpenClaw 那种大型系统。

另外，Keel、Leon、OpenClaw、Untask 当前的代码和架构都在快速变化，尤其 Leon 2.0 目前仍是 Developer Preview，OpenClaw 也持续演进，因此上面的比较是按它们**当前仓库状态**做的，而不是按历史版本。([GitHub][1])

[1]: https://github.com/Keel-Labs/keel?utm_source=chatgpt.com "GitHub - Keel-Labs/keel: An AI assistant whose memory belongs to you. Local-first Mac app, plain markdown workspace, bring your own model. · GitHub"
[2]: https://github.com/leon-ai/leon?utm_source=chatgpt.com "GitHub - leon-ai/leon: 🧠 Leon is your open-source personal assistant. · GitHub"
[3]: https://github.com/openclaw/openclaw/blob/main/docs/plugins/architecture.md?utm_source=chatgpt.com "openclaw/docs/plugins/architecture.md at main · openclaw/openclaw · GitHub"
[4]: https://github.com/openclaw/openclaw/blob/main/docs/concepts/memory.md?utm_source=chatgpt.com "openclaw/docs/concepts/memory.md at main · openclaw/openclaw · GitHub"
[5]: https://github.com/mbenhard/untask?utm_source=chatgpt.com "GitHub - mbenhard/untask: Local-first personal task manager with optional AI assistant · GitHub"
