有，而且**已经出现了与你的目标非常接近的开源项目**。我查了一轮后，比较值得重点研究的不是传统的“聊天机器人”，而是下面几类。

### 1. Keel —— 和你的目标最接近

[Keel GitHub](https://github.com/Keel-Labs/keel?utm_source=chatgpt.com)

它的定位就是 **local-first AI assistant**，目前支持 macOS/Windows，而且功能与你计划的第一版高度重合：

* 本地 Markdown 作为长期记忆
* Tasks / Reminders
* Daily Brief
* End-of-day 总结
* Scheduled Jobs
* 工作日志/项目上下文
* 本地 Whisper 会议转录
* Ollama / Claude / OpenAI 等模型
* SQLite 全文搜索
* 可选向量搜索
* 桌面通知

项目明确强调数据留在用户自己的机器上。([GitHub][1])

**这应该是你首先值得研究的项目。**

---

### 2. Leon —— 更偏“通用个人 Agent”

[Leon GitHub](https://github.com/leon-ai/leon?utm_source=chatgpt.com)

Leon 已经发展很多年，目前 2.0 正在向 Agent 架构重构。

它现在的核心概念已经非常接近你前面设计的：

```text
LLM
 ↓
Context
 ↓
Memory
 ↓
Skills
 ↓
Tools
 ↓
Actions
```

而且支持：

* 本地模型 / 远程模型
* Memory
* Skills
* Tool calling
* Agent execution
* Desktop / browser 操作
* macOS / Linux / Windows
* Node.js
* 本地运行

目前 GitHub 上约有 **17.5k stars**，所以它很适合作为一个成熟的开源个人 Agent 架构来研究。([GitHub][2])

不过它的目标已经比你的第一版大很多。

---

### 3. OpenClaw —— “个人 Agent 操作系统”方向

[OpenClaw GitHub](https://github.com/openclaw/openclaw?utm_source=chatgpt.com)

这个你之前其实已经接触过。

OpenClaw 的方向不是简单的 Todo Assistant，而是：

```text
                   OpenClaw
                       │
       ┌───────────────┼───────────────┐
       ↓               ↓               ↓
    Channels         Skills           Tools
       │               │               │
 WhatsApp          Calendar          Browser
 Telegram          Tasks             Files
 Discord            ...              ...
 iMessage
 ...
```

它可以运行在用户自己的设备上，并把状态、记忆、凭据放在本地，同时支持本地模型和不同 Agent harness。([GitHub][3])

**对你来说，它更适合研究 Agent Runtime，而不是直接作为你的项目蓝本。**

---

### 4. Untask —— 偏“AI Todo + Personal Memory”

[Untask GitHub](https://github.com/mbenhard/untask?utm_source=chatgpt.com)

这个也非常值得看。

它定位为：

> Local-first personal task manager with optional AI assistant

主要功能包括：

* Tasks
* Notes
* Chat
* 本地 SQLite
* AI assistant
* Memory
* Proactive nudges
* macOS menu bar
* Desktop notifications

它甚至已经有类似：

```text
AI
 ↓
了解你的 Tasks
 ↓
帮助整理 / 拆分任务
 ↓
Memory
 ↓
根据任务状态主动提醒
```

这样的设计。([GitHub][4])

它和你的第一阶段目标非常接近，但范围比你目前设想的个人助理更偏 **Task Manager**。

---

# 放在一起看

我会把目前这些项目分成四个方向：

| 项目           | 定位                             | 与你的项目重合度 | 值得研究什么                           |
| ------------ | ------------------------------ | -------: | -------------------------------- |
| **Keel**     | Local-first Personal Assistant |    ⭐⭐⭐⭐⭐ | 产品功能、Memory、Tasks、Daily workflow |
| **Leon**     | Open-source Personal Agent     |     ⭐⭐⭐⭐ | Agent / Skill / Tool 架构          |
| **OpenClaw** | General-purpose Agent Runtime  |      ⭐⭐⭐ | Agent Runtime、Channels、Skills    |
| **Untask**   | AI Task Manager                |     ⭐⭐⭐⭐ | Todo、Reminder、Memory、macOS UX    |

---

# 但有一个很有意思的发现

你原来设计的东西：

```text
Mac mini
   │
   ↓
TypeScript Agent
   │
   ├── Todo
   ├── Reminder
   ├── Work Log
   ├── Daily Summary
   └── Memory
        │
        ↓
     SQLite
        │
        ↓
 MiniCPM5-2B MLX
```

其实**已经不是一个“聊天机器人项目”了**。

它更接近：

> **Local-first Personal Agent / Personal Operating System**

而目前开源项目大致存在三个明显方向：

```text
                Personal AI
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
   Task Manager   Assistant    Agent Runtime
       │            │            │
    Untask        Keel         OpenClaw
                    │
                  Leon
```

你的项目恰好可以放在中间：

```text
                 Your Project
                     │
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
     Tasks         Memory        Agent
       │             │             │
    Reminder      Work Log       Tools
       │             │             │
       └─────────────┼─────────────┘
                     ↓
                  SQLite
                     ↓
              Local MiniCPM
```

### 我反而建议你不要马上开始写代码

先把 **Keel、Leon、OpenClaw、Untask** 的架构和功能做一次系统拆解，然后回答一个问题：

> **“我的项目到底和这些项目有什么不同？”**

尤其值得重点比较：

1. **Memory 怎么做**
2. **Task / Reminder 数据模型**
3. **Agent Tool Calling**
4. **Scheduled Job**
5. **Daily Summary**
6. **Local LLM**
7. **SQLite / Markdown 的选择**
8. **macOS Menu Bar / Notification**
9. **长期运行机制**
10. **Agent 是否允许主动执行任务**

其中 **Keel 是最值得你直接拿来做功能对照的项目**，而 **Leon / OpenClaw 更值得你研究 Agent 架构**。Keel 的功能集合甚至已经覆盖了你最初设想的大部分 MVP，因此你的差异化最好放在**更轻量、本地 MiniCPM、Mac-native、低资源长期运行，以及工程上可控的 deterministic tools**上。([GitHub][1])

如果你愿意，我下一步可以直接给你做一份 **“Keel vs Leon vs OpenClaw vs Untask vs 你的项目”架构级对比**，包括目录结构、Agent loop、Memory、Tool、Scheduler、SQLite/Markdown、模型层，并据此确定你这个项目应该怎么设计。

[1]: https://github.com/Keel-Labs/keel?utm_source=chatgpt.com "GitHub - Keel-Labs/keel: An AI assistant whose memory belongs to you. Local-first Mac app, plain markdown workspace, bring your own model. · GitHub"
[2]: https://github.com/leon-ai/leon?utm_source=chatgpt.com "GitHub - leon-ai/leon: 🧠 Leon is your open-source personal assistant. · GitHub"
[3]: https://github.com/openclaw/openclaw?utm_source=chatgpt.com "GitHub - openclaw/openclaw: The AI that really does things. Any OS. Any Platform. The lobster way. 🦞 · GitHub"
[4]: https://github.com/mbenhard/untask?utm_source=chatgpt.com "GitHub - mbenhard/untask: Local-first personal task manager with optional AI assistant · GitHub"
