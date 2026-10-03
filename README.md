# 🤖 VooDo — Autonomous AI Tech Support Agent

> An AI agent that **sees your screen**, **reasons about your problem**, and **fixes it** — just like a remote IT technician, but instant and available 24/7.

![Chat UI](assets/chat.gif)

---

## ✨ What is VooDo?

VooDo is an autonomous Windows tech-support agent powered by a vision-language model. You describe your problem in a chat UI — the agent takes a screenshot, reasons about what's wrong, and acts with mouse and keyboard to fix it.

No waiting on hold. No step-by-step instructions to follow yourself. The AI just does it.

---

## 🎥 Demo

| Control Mode | Guide Mode | Solutions DB |
|---|---|---|
| ![Mouse & keyboard control](assets/mouse_keyboard_use.gif) | ![Guide mode](assets/show_Bluetooth.gif) | ![Solutions database](assets/solution_database.gif) |
| Agent clicks & types for you | Agent highlights where to click | Shared knowledge base for repeat fixes |

---

## 🚀 Key Features

### 👁️ Real Computer Vision
The agent takes a screenshot before **every single action**. It never assumes what's on screen — it always verifies, just like a human technician.

### 🔄 Autonomous Multi-Step Loop
`screenshot → reason → act → screenshot → repeat`

The agent runs up to 15 steps per session, checking the result of each action before the next.

### 🎛️ Two Operating Modes
- **Control Mode** — The agent fully resolves the issue. Each mouse/keyboard action requires your approval first.
- **Guide Mode** — The agent annotates your screen with spotlight overlays, teaching you where to click. Never touches your machine.

### 🧠 Shared Solutions Knowledge Base
Solved problems are stored as **384-dimensional semantic embeddings** in Postgres + pgvector. When a similar issue comes in, the agent finds the cached fix instantly.

### 🔒 Enterprise Security
- Hard tool allow-list — deny by default, only 40+ safe tools permitted
- Dangerous key blocklist — Win+R, Ctrl+Alt+Delete blocked outright
- Per-session caps — max 5 destructive actions, 500 chars typed
- Prompt injection defense — Unicode normalization + regex screening
- Secret redaction — passwords are never stored in the solutions DB
- Audit log — every action logged in tamper-evident JSON

### 🌐 Firewall-Friendly Architecture
The Windows executor **dials out** to the backend over WebSocket. Zero inbound ports needed — works behind NAT and corporate firewalls.

### 🔌 LLM-Agnostic
Works with any OpenAI-compatible API: OpenAI, Gemini, Anthropic, OpenRouter. Switch models by changing two lines in `.env`.

---

## 🏗️ Architecture

```
┌─ Backend (FastAPI) ─────────────────┐     ┌─ Windows Machine ────────┐
│                                     │     │                          │
│  Agent Loop + Chat UI   :7860       │◀─WS─│  VooDo Executor          │
│  Postgres + pgvector    :5432       │     │  pyautogui / mss         │
│                                     │     │  user's screen           │
└────────┬────────────────────────────┘     └──────────────────────────┘
         ▼
   LLM API (Gemini / OpenAI / OpenRouter)
```

---

## 📁 Project Structure

```
VooDo/
├── server/
│   ├── agent/          # Multi-turn computer-use agent loop
│   ├── app/            # FastAPI server + Chat UI + IT Dashboard
│   └── db/             # Postgres + pgvector + solution seeding
├── client/
│   ├── executor/       # Windows WebSocket client (screenshot, click, type)
│   └── scripts/        # Launcher, floating widget, spotlight overlay
└── shared/             # Wire types, security config, tool allowlist
```

---

## ⚡ Quickstart

### Prerequisites
- Python 3.10+
- A Gemini / OpenAI / OpenRouter API key

### 1. Clone & Configure

```bash
git clone https://github.com/anirbandutta-dev/agent-demo.git
cd agent-demo
```

Create `.env` in the root:

```env
# Gemini (free tier)
LLM_BASE_URL=https://generativelanguage.googleapis.com/v1beta/openai/
LLM_API_KEY=your-gemini-api-key
LLM_MODEL=gemini-flash-lite-latest

SKIP_DB=1
EXECUTOR_TOKEN=your-secret-token-here
IT_PASSWORD=demo123
ADMIN_PASSWORD=demo123
```

### 2. Install & Start Backend

```bash
pip install -r server/app/requirements.txt -r server/agent/requirements.txt
python -m uvicorn server.app.main:app --host 0.0.0.0 --port 7860
```

### 3. Start Windows Executor

```powershell
.\client\scripts\dev_all.ps1 -Backend "ws://localhost:7860" -Token "your-secret-token-here"
```

### 4. Open Chat UI

Go to → **http://localhost:7860**

Describe your Windows problem. The agent takes it from there. 🎯

---

## 🧪 Test Prompts to Try

```
"My Bluetooth is not working"
"Open Notepad and write Hello World"
"Check my disk space"
"My WiFi keeps disconnecting"
"What apps are currently running on my screen?"
"Set my volume to 50%"
```

---

## 🛠️ Dev Flags

| Flag | Effect |
|---|---|
| `SKIP_DB=1` | Skip Postgres (no solution caching) |
| `MOCK_AGENT=1` | UI development — no agent needed |
| `MOCK_LLM=1` | Agent loop with canned LLM responses |

---

## 🔧 IT Dashboard

Access at **http://localhost:7860/it** to review and approve AI-discovered solutions before they enter the shared knowledge base.

---

## 🤝 Built With

- [FastAPI](https://fastapi.tiangolo.com/) — Backend & WebSocket server
- [pyautogui](https://pyautogui.readthedocs.io/) — Mouse & keyboard control
- [mss](https://python-mss.readthedocs.io/) — Fast screen capture
- [PyQt5](https://pypi.org/project/PyQt5/) — Floating widget UI
- [pgvector](https://github.com/pgvector/pgvector) — Semantic solution search
- [Gemini / OpenAI](https://ai.google.dev/) — Vision-language model

---

## 📄 License

MIT License — feel free to use, modify, and build on this project.