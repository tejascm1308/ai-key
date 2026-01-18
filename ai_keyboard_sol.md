# AI KEYBOARD — Complete Solution Documentation

## What is AI Keyboard?

AI Keyboard transforms your keyboard from a passive input device into an **intelligent assistant** that:
- Understands **what app** you're in (Gmail, Slack, VS Code, etc.)
- Knows **what you're trying to do** (writing email, coding, chatting)
- **Suggests** relevant completions based on context
- **Learns** your writing style automatically
- Works **everywhere** — any app, any platform

```
Traditional Keyboard:
You type → Text appears → App receives text
(Keyboard understands nothing)

AI Keyboard:
You type → AI Layer → Understands intent → Suggests help
(AI lives at the point of input)
```

---

## Quick Glossary (Important Terms)

| Term | What It Means |
|------|---------------|
| **MCP** | Model Context Protocol — A standard way for AI tools to communicate and share data |
| **Context Mesh** | Our system where all AI tools share information with each other |
| **Tentacle** | A lightweight client (browser extension, desktop app) that connects to the main AI engine |
| **LLM** | Large Language Model — AI that understands and generates text (like GPT-4, Claude) |
| **Whisper** | OpenAI's speech-to-text AI model |
| **Local Model** | Small AI model that runs on your computer (no internet needed) |
| **Cloud Model** | Large AI model that runs on servers (needs internet, more powerful) |

---

## Part 1: The Problem We're Solving

### Current State

The keyboard is the most-used interface in modern work, but it understands nothing about what you're doing.

| Current Situation | What's Missing |
|-------------------|----------------|
| You switch from Gmail to Slack | Keyboard doesn't know |
| You're writing a formal email | Keyboard can't help with tone |
| You want to dictate a message | You get raw text, no formatting |
| You type the same phrases daily | No learning, no shortcuts |

### Existing Tools & Their Limitations

| Tool | What It Does | Why It's Not Enough |
|------|--------------|---------------------|
| **Grammarly** | Grammar & spelling | Works in limited apps, adds overlay |
| **GitHub Copilot** | Code completion | Only in code editors |
| **ChatGPT** | General AI | Requires copy-paste, breaks your flow |
| **Voice Assistants** | Speech-to-text | Just transcription, no understanding |

**The Gap:** No tool provides universal AI assistance at the input layer, across all apps.

---

## Part 2: What is MCP and Why We Use It

### MCP Explained Simply

**MCP (Model Context Protocol)** is a standard way for AI tools to talk to each other.

Think of it like USB:
- Before USB, every device had a different connector
- USB = one standard that works everywhere

MCP is the same idea for AI:
- Without MCP, every AI tool has its own API
- With MCP, any MCP-compatible system can use any MCP tool

### How MCP Works

```
┌─────────────────────────────────────────────────────────────┐
│  MCP SERVER (Our AI Keyboard Core)                         │
│                                                             │
│  Exposes "tools" that any MCP client can use:               │
│  • detect_context   → "What app is the user in?"            │
│  • complete_intent  → "Suggest text completion"             │
│  • transcribe_voice → "Convert speech to text"              │
│  • enhance_text     → "Improve this text"                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
                            ↑
                    Standard MCP Protocol
                            ↑
┌─────────────────────────────────────────────────────────────┐
│  MCP CLIENTS (Who can use our tools)                        │
│                                                             │
│  • Frai Platform (hackathon requirement)                    │
│  • Claude Desktop                                           │
│  • Our own browser extension                                │
│  • Any future MCP-compatible app                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Why MCP for This Hackathon

1. **Hackathon Requirement**: Must be a "plugin for Frai ecosystem" — MCP is how Frai integrates plugins
2. **Standardization**: We define tools once, they work everywhere
3. **Ecosystem**: Claude Desktop, Frai, future apps can all use our AI Keyboard

### MCP vs Regular API

| Aspect | Regular API | MCP |
|--------|-------------|-----|
| Integration | Custom code per app | Standard protocol |
| Discovery | Read docs, figure out | "Show me your tools" |
| Context | Pass manually each call | Shared automatically |
| Ecosystem | Standalone | Plugs into MCP hosts |

---

## Part 3: Solution Architecture

### The Big Picture: One Brain, Many Tentacles

```
┌──────────────────────────────────────────────────────────────┐
│                    CORE AI ENGINE                            │
│              (Runs locally on your computer)                 │
│                                                              │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  MCP SERVER                                            │  │
│  │                                                        │  │
│  │  TOOLS (what we can do):                               │  │
│  │  • detect_context   → Know which app you're in         │  │
│  │  • complete_intent  → Suggest completions              │  │
│  │  • transcribe_voice → Convert speech to text           │  │
│  │  • enhance_text     → Improve your writing             │  │
│  │  • search_web       → Fetch real-time information      │  │
│  │                                                        │  │
│  │  SHARED CONTEXT:                                       │  │
│  │  All tools see the same context (app, user, history)   │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │ LLM Router   │  │Personalize   │  │ Privacy      │       │
│  │Local + Cloud │  │Engine        │  │ Controls     │       │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
└──────────────────────────────────────────────────────────────┘
                            ↑
                      WebSocket Connection
                      (real-time, fast)
                            ↑
        ┌───────────────────┼───────────────────┐
        ↓                   ↓                   ↓
  ┌───────────┐       ┌───────────┐       ┌───────────┐
  │  Browser  │       │  Desktop  │       │    IDE    │
  │ Extension │       │    App    │       │ Extension │
  │           │       │           │       │           │
  │ Captures  │       │ Captures  │       │ Captures  │
  │ web input │       │ any app   │       │ code      │
  └───────────┘       └───────────┘       └───────────┘
  
  "TENTACLES" = Thin clients that only:
  1. Capture what you type
  2. Send to Core Engine
  3. Display suggestions
  
  All intelligence lives in Core Engine
```

### Why This Architecture?

| Benefit | Explanation |
|---------|-------------|
| **One codebase** | AI logic written once, works everywhere |
| **Easy to add platforms** | New "tentacle" = few days work |
| **Consistent behavior** | Same context in browser, desktop, IDE |
| **Lightweight clients** | Extensions are small, fast |

---

## Part 4: How LLM Connection Works

### Two Types of AI Models

| Type | Where It Runs | Speed | Quality | When Used |
|------|---------------|-------|---------|-----------|
| **Local LLM** | Your computer | Very fast (50ms) | Good for simple tasks | Typos, short completions |
| **Cloud LLM** | Remote servers | Slower (500ms) | Best quality | Complex rewrites |

### How We Connect to LLMs

```
┌─────────────────────────────────────────────────────────────┐
│  OUR CORE ENGINE                                            │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  LLM ROUTER                                         │    │
│  │                                                     │    │
│  │  Input: "Make this email more professional"         │    │
│  │                       ↓                             │    │
│  │  Decision: Complex task → Use Cloud LLM             │    │
│  │                       ↓                             │    │
│  │  Call: OpenAI GPT-4 API or Claude API               │    │
│  │                       ↓                             │    │
│  │  Return: Improved text                              │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  LOCAL LLM (runs on your machine):                          │
│  • Ollama (runs TinyLlama, Phi-3 models)                    │
│  • No internet needed                                        │
│  • Free, private                                             │
│                                                             │
│  CLOUD LLM (runs on servers):                               │
│  • OpenAI GPT-4 API                                          │
│  • Anthropic Claude API                                      │
│  • Needs internet, API key                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Three-Layer Speed System

```
USER STARTS TYPING
        ↓
┌─────────────────────────────────────────────────────────────┐
│  LAYER 1: CACHE (0-10ms)                                    │
│  Your common phrases stored locally                         │
│  "Best regards," "Sounds good," "LGTM"                      │
│  → If match found, return instantly                         │
└─────────────────────────────────────────────────────────────┘
        ↓ (no cache hit)
┌─────────────────────────────────────────────────────────────┐
│  LAYER 2: LOCAL MODEL (10-100ms)                            │
│  Small AI on your computer                                  │
│  Typo fixes, simple completions                             │
│  → Fast, no internet needed                                 │
└─────────────────────────────────────────────────────────────┘
        ↓ (complex request)
┌─────────────────────────────────────────────────────────────┐
│  LAYER 3: CLOUD LLM (100-500ms)                             │
│  GPT-4 or Claude for complex tasks                          │
│  Full rewrites, nuanced suggestions                         │
│  → Streamed token-by-token (feels fast)                     │
└─────────────────────────────────────────────────────────────┘
```

**Result:** 70% of suggestions served instantly from cache/local.

---

## Part 5: Browser Extension vs Desktop App

### Why We Need Both

| Platform | What It Handles | Why Needed |
|----------|-----------------|------------|
| **Browser Extension** | Websites (Gmail, Slack, LinkedIn) | Full access to webpage input fields |
| **Desktop App** | Native apps (Word, Outlook, Notepad) | OS-level keyboard access |

### Browser Extension (How Grammarly Does It)

```javascript
// 1. Content script injected into every webpage
// 2. Watches for text input fields

const observer = new MutationObserver(() => {
  // Find all editable elements
  const inputs = document.querySelectorAll(
    'input, textarea, [contenteditable="true"]'
  );
  
  // Attach listeners
  inputs.forEach(input => {
    input.addEventListener('input', handleTyping);
  });
});

// 3. When user types, capture and send to Core Engine
function handleTyping(event) {
  const text = event.target.value;
  websocket.send({ text, context: detectContext() });
}

// 4. Display suggestion overlay near cursor
```

### Desktop App (For Native Apps)

```
WINDOWS:
├── GetForegroundWindow() → Detect active app
├── SetWindowsHookEx() → Global keyboard hook
└── Overlay window → Display suggestions

macOS:
├── NSWorkspace → Detect active app  
├── CGEvent → Keyboard events
└── Requires Accessibility permission
```

### Can Desktop App Handle Browser?

**Partially.** Desktop app can capture keystrokes, but:
- Cannot see which input field on webpage
- Cannot inject suggestions inline
- Cannot read page context (URL, recipient)

**Recommendation:** Use browser extension for web, desktop app for native apps.

---

## Part 6: Context Detection

### How We Know What You're Doing

```
┌─────────────────────────────────────────────────────────────┐
│  CONTEXT DETECTION                                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  WHAT WE DETECT:                                            │
│  ├── App: Gmail, Slack, VS Code, Word                       │
│  ├── Mode: Compose email, Chat, Coding                      │
│  ├── Recipient: john@company.com (for emails)               │
│  ├── Tone hint: Professional, Casual                        │
│  └── URL: mail.google.com/compose                           │
│                                                             │
│  HOW WE DETECT (Browser):                                   │
│  ├── URL pattern matching                                   │
│  ├── DOM element analysis                                   │
│  └── Input field attributes                                 │
│                                                             │
│  HOW WE DETECT (Desktop):                                   │
│  ├── Window title parsing                                   │
│  ├── Process name                                           │
│  └── Accessibility APIs                                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Example: Gmail Detection

```
URL: mail.google.com/mail/u/0/#compose
DOM: div[aria-label="Message Body"] is focused

Context Object Generated:
{
  "app": "Gmail",
  "mode": "compose",
  "recipient": "john@company.com",
  "subject": "Project Update",
  "formality": "professional"
}

→ AI now suggests formal email completions
```

### Context Switching (<50ms)

When you Alt+Tab to a different app:
1. Window monitor detects change (10ms)
2. Context classifier identifies new app (10ms)
3. Behavior engine loads new profile (10ms)
4. AI ready with new suggestions (30ms total)

---

## Part 7: Features

### Core Features

| Feature | What It Does | How It Works |
|---------|--------------|--------------|
| **Smart Suggestions** | Context-aware completions | Based on app, tone, your style |
| **Auto-Correct** | Fix typos automatically | Local model, instant |
| **Voice Input** | Speak, get formatted text | Whisper + intent enhancement |
| **Rewrite/Enhance** | Improve selected text | Cloud LLM for quality |
| **Personalization** | Learn your style | Patterns extracted, content discarded |

### Voice Integration (Whisper)

```
AUDIO INPUT: "email john about pushing the deadline to friday"
        ↓
┌─────────────────────────────────────────────────────────────┐
│  AUDIO ANALYZER                                             │
│  • Quality check: Clear? Noisy?                             │
│  • Duration: Short? Long?                                   │
│  • Route decision: Local or Cloud Whisper?                  │
└─────────────────────────────────────────────────────────────┘
        ↓
┌─────────────────────────────────────────────────────────────┐
│  TRANSCRIPTION                                              │
│  Raw text: "email john about pushing the deadline..."       │
└─────────────────────────────────────────────────────────────┘
        ↓
┌─────────────────────────────────────────────────────────────┐
│  INTENT ENHANCEMENT                                         │
│  Detect: This is an email request                           │
│  Output:                                                    │
│  "Hi John,                                                  │
│   I wanted to discuss adjusting our timeline. Would it be   │
│   possible to extend the deadline to Friday?                │
│   Best regards, [Your Name]"                                │
└─────────────────────────────────────────────────────────────┘
```

### Emoji Integration

Emojis help convey tone. Our approach:
- **LLM decides** when to include emoji (not rule-based)
- **Context-aware**: Slack gets emoji, formal email doesn't
- **User toggle**: Can disable emoji entirely

```json
{
  "emoji_enabled": true,
  "emoji_contexts": ["slack", "whatsapp", "messenger"]
}
```

### Expert Tools (Beyond LLM)

LLMs are great at language, but sometimes need external data:

```
┌─────────────────────────────────────────────────────────────┐
│  INTELLIGENT ROUTING                                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Input: "What's the current stock price of Reliance?"       │
│  → Detect: Needs real-time data                             │
│  → Route: Web Search Tool                                   │
│  → LLM: Format response naturally                           │
│                                                             │
│  Input: "Make this email more professional"                 │
│  → Detect: Language task                                    │
│  → Route: LLM directly (no external tool)                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 8: Personalization

### Three-Layer Learning

```
┌─────────────────────────────────────────────────────────────┐
│  LAYER 1: EXPLICIT (You tell us once)                       │
│  • Role: Developer / Writer / Manager                       │
│  • Tone preference: Direct / Friendly / Formal              │
│  • Set during onboarding                                    │
└─────────────────────────────────────────────────────────────┘
        +
┌─────────────────────────────────────────────────────────────┐
│  LAYER 2: OBSERVED (We learn automatically)                 │
│  • Greeting style: "Hi [Name]" vs "Dear Sir"                │
│  • Common phrases: "sounds good", "LGTM"                    │
│  • Tone per app: Formal in Gmail, Casual in Slack           │
│  • Typing patterns                                          │
└─────────────────────────────────────────────────────────────┘
        +
┌─────────────────────────────────────────────────────────────┐
│  LAYER 3: FEEDBACK (We learn from your actions)             │
│  • Which suggestions you accept                             │
│  • Which you reject                                         │
│  • How you edit accepted suggestions                        │
│  • Anti-patterns: Things you never want ("utilize"→"use")   │
└─────────────────────────────────────────────────────────────┘
```

### What We Learn vs What We Don't Store

```
STORED (for personalization):              NOT STORED (your privacy):
─────────────────────────────              ─────────────────────────
✓ "User prefers Hi [Name]"                 ✗ "Hi John, about the..."
✓ "Formal tone in Gmail"                   ✗ Actual email content
✓ "Uses LGTM in code reviews"              ✗ Recipient addresses
✓ "Never uses 🎉 emoji"                    ✗ Document text
```

---

## Part 9: Privacy & Security

### Core Principles

1. **Local-First**: All processing on your device when possible
2. **USE but Don't STORE**: We read content temporarily, don't save it
3. **User Control**: You decide what goes to cloud

### What Happens to Your Email/Chat Content

```
You type: "Hi John, I wanted to follow up on our meeting..."
        ↓
┌─────────────────────────────────────────────────────────────┐
│  STEP 1: READ (temporary)                                   │
│  AI sees your text to understand context                    │
│                                                             │
│  STEP 2: LEARN PATTERNS (stored)                            │
│  • "User prefers Hi [Name] greeting"                        │
│  • "User writes professional emails"                        │
│  • "Gmail context = formal tone"                            │
│                                                             │
│  STEP 3: SUGGEST                                            │
│  "...and wanted to share the action items."                 │
│                                                             │
│  STEP 4: DISCARD CONTENT (not stored)                       │
│  • "Hi John, I wanted to follow up..." → DELETED            │
│  • Recipient "John" → NOT SAVED                             │
│  • Meeting details → NOT SAVED                              │
└─────────────────────────────────────────────────────────────┘
```

### Data Classification

| Category | Examples | How We Handle |
|----------|----------|---------------|
| 🔴 **Never Captured** | Passwords, credit cards, OTP | Automatic exclusion |
| 🟠 **Used, Not Stored** | Emails, chats, documents | Read temporarily, discard |
| 🟡 **Stored Locally** | Preferences, patterns | Encrypted on your device |
| 🟢 **Cloud Eligible** | Text for rewrite | Only with your consent |

### Auto-Blocked Apps

```
These apps automatically disable AI Keyboard:
├── Banking apps (Chase, PayPal, etc.)
├── Password managers (1Password, LastPass)
├── Login pages (*/login*, */signin/*)
└── User-defined blocklist
```

### User Controls

| Control | What It Does |
|---------|--------------|
| **Pause Mode** | Ctrl+Shift+P → Instantly disable AI |
| **Offline Mode** | No internet calls, 100% local |
| **App Blocklist** | Disable AI in specific apps |
| **Data Wipe** | One-click delete all learned patterns |

---

## Part 10: Per-App Configuration

### Users Control Where AI Works

```
┌─────────────────────────────────────────────────────────────┐
│  AI KEYBOARD SETTINGS                                       │
│                                                             │
│  WEB APPS:                                                  │
│  ├── Gmail               [████████] ON                      │
│  ├── Slack               [████████] ON                      │
│  ├── LinkedIn            [████████] ON                      │
│  ├── ChatGPT             [░░░░░░░░] OFF                     │
│  └── + Add website...                                       │
│                                                             │
│  DESKTOP APPS:                                              │
│  ├── VS Code             [████████] ON                      │
│  ├── Microsoft Word      [████████] ON                      │
│  ├── Terminal            [░░░░░░░░] OFF                     │
│  └── + Add app...                                           │
│                                                             │
│  BLOCKED (Auto-disabled):                                   │
│  ├── 🔒 Banking websites                                    │
│  └── 🔒 Password managers                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Per-App Feature Toggle

For each app, user can control:
```json
{
  "gmail.com": {
    "enabled": true,
    "features": {
      "suggestions": true,
      "auto_correct": true,
      "voice": false,
      "emoji": false,
      "rewrite": true
    }
  }
}
```

---

## Part 11: Keyboard Shortcuts

```
┌─────────────────────────────────────────────────────────────┐
│  KEYBOARD SHORTCUTS                                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  SUGGESTIONS:                                               │
│  Tab          → Accept suggestion                           │
│  Esc          → Dismiss suggestion                          │
│  ↓ / ↑        → Navigate multiple suggestions               │
│  Ctrl+→       → Accept word-by-word                         │
│                                                             │
│  VOICE:                                                     │
│  Ctrl+Shift+V → Hold to speak, release to transcribe        │
│                                                             │
│  ACTIONS:                                                   │
│  Ctrl+Shift+R → Rewrite selected text                       │
│  Ctrl+Shift+E → Enhance selected text                       │
│  Ctrl+Shift+T → Cycle tone (formal/casual/friendly)         │
│                                                             │
│  CONTROL:                                                   │
│  Ctrl+Shift+P → Pause/Resume AI                             │
│  Ctrl+Shift+A → Toggle Auto-Correct                         │
│  Ctrl+Shift+O → Open settings                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 12: Offline Capabilities

### What Works Without Internet

| Feature | Offline? | How |
|---------|----------|-----|
| Context detection | ✅ Yes | Local window monitoring |
| Cached suggestions | ✅ Yes | Your common phrases |
| Local LLM completions | ✅ Yes | TinyLlama, Phi-3 |
| Basic voice transcription | ✅ Yes | Whisper-Tiny model |
| Personalization | ✅ Yes | Data stored locally |
| Complex rewrites | ❌ No | Needs cloud LLM |
| High-quality transcription | ❌ No | Needs cloud Whisper |

### Local Model Requirements

| Model | Size | RAM Needed | Purpose |
|-------|------|------------|---------|
| Whisper-Tiny | 39 MB | ~200 MB | Voice transcription |
| TinyLlama | 600 MB | ~2 GB | Basic completions |
| Phi-3 | 1.5 GB | ~4 GB | Better completions |

---

## Part 13: Plugin System

### Why Plugins?

Allow extensions without modifying core:
- Custom templates
- Domain-specific features
- Third-party integrations

### Plugin Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  CORE ENGINE (never changes)                                │
│                    ↑                                        │
│              Plugin API                                     │
│                    ↑                                        │
│  ┌─────────┬─────────┬─────────┬─────────┐                 │
│  │ Email   │ Code    │ Translate│ Custom  │                 │
│  │ Template│ Snippets│ Plugin   │ Plugin  │                 │
│  └─────────┴─────────┴─────────┴─────────┘                 │
└─────────────────────────────────────────────────────────────┘
```

### Example Plugins

| Plugin | Trigger | Action |
|--------|---------|--------|
| Email Templates | Type "##meeting" | Insert meeting follow-up template |
| Code Snippets | Type "!!react" | Insert React component boilerplate |
| Translate | Ctrl+Shift+L | Translate selected text |

### Plugin Security

- Plugins run in sandbox (isolated)
- Must request permissions
- User approves before install

---

## Part 14: Error Handling

### When Things Go Wrong

| Failure | Response |
|---------|----------|
| Cloud LLM unavailable | Silently use local model |
| Whisper transcription fails | "Couldn't hear that, try again" |
| Unknown app detected | Use neutral suggestions, start learning |
| Multiple rejections | Reduce suggestion frequency |

### Graceful Degradation

```
NETWORK DOWN?
├── Local features continue working (70% of functionality)
├── Cloud features show "Offline" indicator
├── Requests queued for when connection returns
└── User experience barely interrupted
```

---

## Part 15: Onboarding

### Goal: User Gets Value in 2 Minutes

```
STEP 1: INSTALL (30 seconds)
├── One-click install (extension or desktop app)
├── Background service starts automatically
└── No configuration needed

STEP 2: QUICK SETUP (60 seconds)
├── "What's your role?" [Developer | Writer | Other]
├── "Preferred tone?" [Direct | Friendly | Formal]
└── [Skip - Let AI learn naturally]

STEP 3: FIRST INTERACTION (30 seconds)
├── Guided first suggestion
├── "Press Tab to accept, Esc to dismiss"
└── Celebrate first accepted suggestion 🎉

STEP 4: LEARN BY USING
├── Progressive feature discovery
├── New hints on Day 3, Day 7, Day 14
└── Never overwhelm with all features at once
```

---

## Part 16: Tech Stack

| Component | Technology |
|-----------|------------|
| **Core Engine** | Python + FastAPI |
| **MCP Server** | MCP SDK (Python) |
| **Local LLM** | Ollama (TinyLlama, Phi-3) |
| **Cloud LLM** | OpenAI GPT-4 / Claude API |
| **Voice** | Whisper (local + API) |
| **Browser Extension** | JavaScript, Chrome APIs |
| **Desktop App** | Python + PyQt or Electron |
| **Communication** | WebSocket |
| **Storage** | SQLite (local, encrypted) |
| **OS Interaction** | Python (pywin32, pynput) |

---

## Part 17: Hackathon MVP Scope

### What We Build

| Component | Priority | Description |
|-----------|----------|-------------|
| Core MCP Server | P0 | Context mesh, tools |
| Chrome Extension | P0 | First tentacle, all websites |
| Whisper Integration | P0 | Voice with smart routing |
| Three-Layer Speed | P0 | Cache → Local → Cloud |
| Personalization | P1 | Explicit + Observed layers |
| Privacy Controls | P1 | Blocklist, pause mode |

### Post-Hackathon Roadmap

1. Desktop App (system-wide native apps)
2. VS Code Extension (deep IDE integration)
3. Advanced Personalization (feedback loop)
4. Multi-language support
5. Plugin marketplace

---

## Summary: What Makes AI Keyboard Special

| Aspect | Our Approach |
|--------|--------------|
| **Architecture** | One brain (MCP server) + many tentacles (extensions) |
| **Speed** | Three-layer: Cache → Local → Cloud |
| **Context** | Knows which app, adapts instantly |
| **Voice** | Not just transcription — intent enhancement |
| **Personalization** | Learns automatically, no configuration |
| **Privacy** | USE content, don't STORE content |
| **Ecosystem** | MCP-based, plugs into Frai, Claude Desktop |

> **"Making every keystroke intelligent."**
