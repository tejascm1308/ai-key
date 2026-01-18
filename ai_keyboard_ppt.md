# AI Keyboard
## Making Every Keystroke Intelligent

---

# Slide 1: The Problem

### The Keyboard is Dumb

You use your keyboard 8+ hours daily, yet it understands **nothing** about what you're doing.

**Pain Points:**
- Writing a formal email → Keyboard doesn't help with tone
- Switching to Slack → No change in behavior
- Typing same phrases daily → No shortcuts, no learning
- Want to dictate a message → Get raw text, no formatting

**Current tools are fragmented:**
- Grammarly → Only grammar, limited apps
- Copilot → Only code editors
- ChatGPT → Requires copy-paste, breaks flow

> **Gap:** No universal AI assistant at the input layer.

---

# Slide 2: Our Solution

### AI Keyboard — Universal Intelligence at Input

An AI layer that lives **between your keyboard and every app**.

```
┌─────────────────────────────────────────────────────────────┐
│  TRADITIONAL:  Keyboard → App                               │
│                (No intelligence)                            │
│                                                             │
│  AI KEYBOARD:  Keyboard → AI Layer → App                    │
│                     ↓                                       │
│              Understands context                            │
│              Suggests completions                           │
│              Learns your style                              │
│              Works everywhere                               │
└─────────────────────────────────────────────────────────────┘
```

**Key Capabilities:**
- ✅ Context-aware suggestions
- ✅ Voice-to-formatted-text
- ✅ Automatic personalization
- ✅ Works in any app, any browser

---

# Slide 3: System Architecture

### One Brain, Many Tentacles

```
┌────────────────────────────────────────────────────────────────┐
│                      CORE AI ENGINE                            │
│                   (Runs on your device)                        │
│                                                                │
│   ┌────────────────────────────────────────────────────────┐   │
│   │                   MCP SERVER                           │   │
│   │                                                        │   │
│   │   TOOLS:                     SHARED CONTEXT:           │   │
│   │   • detect_context           • Current app             │   │
│   │   • complete_intent          • User preferences        │   │
│   │   • enhance_text             • Recent history          │   │
│   │   • transcribe_voice         • Learned patterns        │   │
│   └────────────────────────────────────────────────────────┘   │
│                                                                │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    │
│   │  LLM Router  │    │ Personalize  │    │    Cache     │    │
│   │ Local/Cloud  │    │   Engine     │    │   Layer      │    │
│   └──────────────┘    └──────────────┘    └──────────────┘    │
└────────────────────────────────────────────────────────────────┘
                              ↑
                        WebSocket (Real-time)
                              ↑
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
    ┌───────────┐       ┌───────────┐       ┌───────────┐
    │  Browser  │       │  Desktop  │       │    IDE    │
    │ Extension │       │    App    │       │ Extension │
    │  (Web)    │       │  (Native) │       │  (Code)   │
    └───────────┘       └───────────┘       └───────────┘
```

**Why this architecture?**
- AI logic written once, works everywhere
- Extensions are lightweight (just capture input, show suggestions)
- Easy to add new platforms

---

# Slide 4: Three-Layer Speed System

### How We Achieve <100ms Response Time

```
USER TYPES: "Hi, I wanted to"
              ↓
┌─────────────────────────────────────────────────────────────┐
│  LAYER 1: CACHE                              [0-10ms]       │
│  Your common phrases stored locally                         │
│  → "Hi, I wanted to follow up on our meeting"               │
│  → If found, return instantly ✓                             │
└─────────────────────────────────────────────────────────────┘
              ↓ (no match)
┌─────────────────────────────────────────────────────────────┐
│  LAYER 2: LOCAL LLM (Ollama)                 [10-100ms]     │
│  Small model on your computer                               │
│  → Fast, no internet needed                                 │
│  → Handles simple completions                               │
└─────────────────────────────────────────────────────────────┘
              ↓ (complex request)
┌─────────────────────────────────────────────────────────────┐
│  LAYER 3: CLOUD LLM (GPT-4)                  [100-500ms]    │
│  Powerful model for complex tasks                           │
│  → Full rewrites, nuanced suggestions                       │
│  → Streamed token-by-token                                  │
└─────────────────────────────────────────────────────────────┘
```

**Result:** 70% of suggestions served from cache/local = instant feel.

---

# Slide 5: Context Detection

### AI That Knows Where You Are

```
GMAIL (compose)     →  Professional tone, sign-offs, formal
SLACK (chat)        →  Casual, emoji allowed, brief
VS CODE (coding)    →  Technical, concise, code-aware
LINKEDIN (message)  →  Professional networking, warm
```

**How it works:**
1. Extension detects URL / window title / input field
2. Classifies into: App → Mode → Category → Formality
3. Loads appropriate behavior profile
4. AI adjusts suggestions in real-time

**Context switch time:** <50ms (user doesn't notice)

---

# Slide 6: Voice Integration

### Speak Naturally, Get Formatted Text

```
USER SPEAKS: "email john about pushing the deadline to friday"

              ↓ Audio Analysis + Transcription
┌─────────────────────────────────────────────────────────────┐
│  Raw: "email john about pushing the deadline to friday"     │
└─────────────────────────────────────────────────────────────┘
              ↓ Intent Enhancement
┌─────────────────────────────────────────────────────────────┐
│  Hi John,                                                   │
│                                                             │
│  I wanted to discuss adjusting our timeline. Would it be    │
│  possible to extend the deadline to Friday?                 │
│                                                             │
│  Best regards,                                              │
│  [Your Name]                                                │
└─────────────────────────────────────────────────────────────┘
```

**Smart Routing:** Clear audio → Local Whisper (fast), Noisy → Cloud (accurate)

---

# Slide 7: Personalization

### AI That Learns Your Style

**Three Layers of Learning:**

- **Explicit** — You tell us once (Role: Developer, Tone: Direct)
- **Observed** — We learn automatically ("Hi [Name]" greeting, uses "LGTM")
- **Feedback** — Accept/reject actions (Never suggest "utilize" → prefers "use")

**What we learn:**
- Greeting style per app (formal in Gmail, casual in Slack)
- Common phrases you type repeatedly
- Patterns in how you edit suggestions

**Privacy guarantee:** We learn **patterns**, not store **content**. All data stays on device.

---

# Slide 8: Privacy & Security

### Privacy-First Architecture

**Three Principles:**
1. **Local-First** — Core engine runs on your device
2. **USE, Don't STORE** — Read content temporarily, discard after
3. **User Control** — Pause mode, app blocklist, data wipe

**Data Handling:**

| Type | Handling |
|------|----------|
| 🔴 Passwords, OTP | Never captured |
| 🟠 Email/chat content | Used in memory, then discarded |
| 🟡 Your preferences | Encrypted on your device |

**Auto-blocked:** Banking apps, password managers, login pages

---

# Slide 9: MCP Integration

### Built for the Frai Ecosystem

**What is MCP?**
Model Context Protocol — a standard way for AI tools to communicate.

```
┌─────────────────────────────────────────────────────────────┐
│  AI KEYBOARD (MCP Server)                                   │
│                                                             │
│  Exposes tools:                                             │
│  • detect_context   → "What app is the user in?"            │
│  • complete_intent  → "Suggest text completion"             │
│  • enhance_text     → "Improve this text"                   │
│  • transcribe_voice → "Convert speech to text"              │
└─────────────────────────────────────────────────────────────┘
                              ↑
                    Standard MCP Protocol
                              ↑
┌─────────────────────────────────────────────────────────────┐
│  MCP CLIENTS: Frai Platform, Claude Desktop, Any MCP app    │
└─────────────────────────────────────────────────────────────┘
```

**Benefit:** Build once, integrate everywhere.

---

# Slide 10: Tech Stack

### What We Used

| Layer | Technology |
|-------|------------|
| Backend | Python, FastAPI, MCP SDK |
| LLM | Ollama (local), OpenAI GPT-4 (cloud) |
| Voice | Whisper |
| Extension | JavaScript, Chrome APIs, WebSocket |

---

# Slide 11: Demo & Roadmap

### What We Built (MVP)

- ✅ Core MCP Server with 4 tools
- ✅ Chrome Extension (works on all websites)
- ✅ Three-layer speed system
- ✅ Context detection (Gmail, Slack, GitHub, etc.)
- ✅ Personalization engine
- ✅ Privacy controls

### What's Next

- **Phase 1:** Desktop app (Word, Outlook, native apps)
- **Phase 2:** VS Code extension, advanced voice
- **Phase 3:** Team profiles, plugin marketplace

---

# Slide 12: Summary

### AI Keyboard — Why It Matters

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   UNIVERSAL    — Works in every app, every browser          │
│                                                             │
│   INVISIBLE    — No context switching, flows naturally      │
│                                                             │
│   INTELLIGENT  — Understands context, not just words        │
│                                                             │
│   PERSONAL     — Learns your style automatically            │
│                                                             │
│   FAST         — <100ms response, feels native              │
│                                                             │
│   PRIVATE      — Local-first, you own your data             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

> **"Making every keystroke intelligent."**

---

# Thank You

**AI Keyboard**
Making every keystroke intelligent.

Questions?
