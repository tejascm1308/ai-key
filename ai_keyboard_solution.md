# AI KEYBOARD — Complete Solution Documentation

## Project Overview

**Problem Statement:** AI Keyboard — From Input Device to Intelligent Work Interface

**Vision:** Transform the keyboard from a passive input device into an intelligent layer that understands intent, adapts to context, and assists in real-time across all applications.

**Core Principle:**
```
Traditional:  Human Brain → Keyboard → Dumb Text → Application
Our Vision:   Human Brain → Keyboard → AI Layer → Rich Intent → Application
```

The AI layer answers:
- What is the user trying to achieve?
- What app are they in?
- What's their writing style?
- What would help them right now?

---

## The Gap We're Filling

### Current Landscape Analysis

| Existing Tool | What It Does | Critical Limitation |
|---------------|--------------|---------------------|
| **Grammarly** | Grammar, tone, style | Overlay-based, not integrated, writing-only |
| **GitHub Copilot** | Code completion | IDE-specific, code-only context |
| **ChatGPT/Claude** | General AI | Requires context switching, copy-paste |
| **Whisper** | Speech-to-text | Transcription only, no intent layer |
| **iOS/Android Keyboard** | Predictive text | Basic predictions, mobile-only |

### The Core Problem

All existing solutions are **siloed**:
- User must switch apps to access AI
- Context is lost between applications
- No unified intelligence at the input layer
- Each tool rebuilds context from scratch

**Our Solution:** A universal AI layer that lives at the point of input and maintains context across all applications.

---

## Solution Architecture

### High-Level Design: One Brain, Many Tentacles

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              CORE AI ENGINE                                  │
│                    (Runs as local background service)                        │
│  ┌────────────────────────────────────────────────────────────────────────┐  │
│  │                         MCP SERVER                                     │  │
│  │  ┌──────────────────────────────────────────────────────────────────┐  │  │
│  │  │                      CONTEXT MESH                                │  │  │
│  │  │  All tools share unified state and auto-trigger each other       │  │  │
│  │  └──────────────────────────────────────────────────────────────────┘  │  │
│  │                                                                        │  │
│  │  TOOLS:                                                                │  │
│  │  ├── transcribe_voice   → Whisper with confidence routing             │  │
│  │  ├── enhance_text       → Context-aware rewriting                     │  │
│  │  ├── complete_intent    → Predict what user wants to achieve          │  │
│  │  └── detect_context     → Identify app, workflow, mode                │  │
│  │                                                                        │  │
│  │  RESOURCES:                                                            │  │
│  │  ├── user://profile     → Personalization data                        │  │
│  │  ├── context://app      → Current application context                 │  │
│  │  └── history://recent   → Recent inputs and suggestions               │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐  │
│  │                     SPEED ENGINE                                       │  │
│  │  Layer 1: Cache         (0-10ms)    → Exact matches                    │  │
│  │  Layer 2: Local Model   (10-100ms)  → Simple completions               │  │
│  │  Layer 3: Cloud LLM     (100-500ms) → Complex, streamed                │  │
│  │  + Predictive Pre-computation                                          │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐  │
│  │                   PERSONALIZATION ENGINE                               │  │
│  │  Behavioral Fingerprint: Explicit + Observed + Feedback Loop           │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────┘
                                    ↑
                              WebSocket (streaming, persistent)
                                    ↑
            ┌───────────────────────┼───────────────────────┐
            ↓                       ↓                       ↓
    ┌───────────────┐       ┌───────────────┐       ┌───────────────┐
    │ Browser       │       │ Desktop       │       │ IDE           │
    │ Extension     │       │ App           │       │ Extension     │
    │ (Chrome)      │       │ (Electron)    │       │ (VS Code)     │
    │               │       │               │       │               │
    │ • ~50KB       │       │ • Global keys │       │ • Inline ghost│
    │ • Any website │       │ • Any app     │       │ • Code-aware  │
    └───────────────┘       └───────────────┘       └───────────────┘
    
    THIN CLIENTS: Only capture input + display suggestions
    ALL INTELLIGENCE: Lives in Core Engine
```

### Why "Tentacle Architecture"?

- **Single codebase for intelligence** — maintain once, works everywhere
- **Add new platform easily** — new tentacle = 1-2 days work
- **Consistent behavior** — all interfaces share same context pool
- **Lightweight clients** — minimal impact on target apps

---

## Module 1: MCP Architecture — Context Mesh

### The Problem with Traditional MCP

```
Traditional MCP:
  User → Tool A → Response (Tool A forgets)
  User → Tool B → Response (Tool B has no idea about Tool A)
  
  Each tool is isolated. Context is lost between calls.
```

### Our Solution: Context Mesh

```
Our Approach:
  User → Context Mesh → Tool A ←→ Tool B ←→ Tool C
                ↓
         Shared Context Pool
         
  When one tool learns something, ALL tools know it.
  Tools auto-trigger each other based on workflow.
```

### How Context Mesh Works

1. **Shared Memory Pool**
   - All tools read from and write to a unified context object
   - User types in Gmail → `detect_context` writes `{app: gmail, mode: compose, recipient: boss}`
   - When `enhance_text` runs → reads that context → adjusts tone to professional
   - When `complete_intent` runs → knows email patterns + recipient history

2. **Tool Chaining**
   - Tools automatically trigger each other without manual orchestration
   - Voice input → `transcribe_voice` → `detect_context` → `enhance_text` → final output
   - User doesn't manage the flow, system does

3. **Priority Queue**
   - Fast tools (local) run first, provide immediate feedback
   - Expensive tools (cloud LLM) run in background, update when ready
   - User never waits for slow operations

### Context Object Structure

```json
{
  "app": {
    "name": "Gmail",
    "category": "email",
    "mode": "compose"
  },
  "recipient": {
    "email": "manager@company.com",
    "relationship": "supervisor",
    "history": ["previous email threads"]
  },
  "user": {
    "current_goal": "send status update",
    "typing_state": "drafting",
    "time_context": "morning, workday"
  },
  "suggestions": {
    "pending": ["professional greeting", "status summary"],
    "accepted_recently": [...],
    "rejected_recently": [...]
  }
}
```

---

## Module 2: Whisper Integration — Confidence-Based Routing

### The Problem

Whisper API is excellent but:
- Cloud API adds 500-1000ms latency
- Local tiny model may be inaccurate for noisy audio
- No intelligence beyond transcription

### Our Solution: Smart Routing + Intent Enhancement

```
                         AUDIO INPUT
                              ↓
┌─────────────────────────────────────────────────────────────────────────────┐
│                       AUDIO PRE-ANALYZER                                    │
│                     (Runs locally, <50ms)                                   │
│  ─────────────────────────────────────────────────────────────────────────  │
│  Analyzes first 500ms of audio:                                             │
│  • Duration: Short (<5s) vs Long (>5s)                                      │
│  • Noise level: Signal-to-noise ratio calculation                           │
│  • Speech clarity: How clear is the speech?                                 │
│  • Language detection: What language is being spoken?                       │
└─────────────────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────────────────┐
│                       ROUTING DECISION MATRIX                               │
├─────────────────────────────────────────────────────────────────────────────┤
│  Audio Quality    │ Duration │ Route To                │ Expected Latency   │
│  ─────────────────┼──────────┼─────────────────────────┼───────────────────  │
│  Clear            │ <5 sec   │ Local Whisper-Tiny      │ 50-100ms           │
│  Clear            │ >5 sec   │ Local Whisper-Base      │ 200-400ms          │
│  Noisy            │ Any      │ Cloud Whisper API       │ 500-1000ms         │
│  Multiple langs   │ Any      │ Cloud + Language hint   │ 600-1200ms         │
└─────────────────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────────────────┐
│                     INTENT ENHANCEMENT LAYER                                │
│  ─────────────────────────────────────────────────────────────────────────  │
│                                                                             │
│  Raw Transcription:                                                         │
│  "send an email to john about pushing the deadline to friday"               │
│                              ↓                                              │
│  Intent Extraction:                                                         │
│  {                                                                          │
│    action: "compose_email",                                                 │
│    recipient: "john",                                                       │
│    subject_hint: "deadline extension",                                      │
│    key_point: "move deadline to Friday"                                     │
│  }                                                                          │
│                              ↓                                              │
│  Contextual Output:                                                         │
│  "Hi John,                                                                  │
│   I wanted to discuss adjusting our timeline. Would it be possible          │
│   to extend the deadline to Friday? Let me know if this works.              │
│   Best, [User Name]"                                                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Streaming Transcription

- Don't wait for user to finish speaking
- Transcribe in chunks as they speak
- Show progressive text (like live captions)
- User sees words appearing in real-time

### Why This Stands Out

| What Others Do | What We Do |
|----------------|------------|
| Always call Cloud API | Analyze first, route smartly |
| Wait for full audio | Stream progressively |
| Return raw text | Extract intent, format contextually |
| Generic transcription | Context-aware output (email vs code vs chat) |

---

## Module 3: Speed & Latency — Predictive Pre-computation

### The Problem

- LLM API calls: 500-2000ms
- Users notice delays > 150ms
- Typing flow is broken by waiting

### Our Solution: Three-Layer Speed + Prediction

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           USER TYPING                                       │
└─────────────────────────────────────────────────────────────────────────────┘
                                   ↓
┌─────────────────────────────────────────────────────────────────────────────┐
│  LAYER 1: CACHE (0-10ms)                                                    │
│  ─────────────────────────                                                  │
│  • User's common phrases stored locally                                     │
│  • Recent suggestions per app context                                       │
│  • Exact match = instant serve                                              │
│                                                                             │
│  Example: User always ends emails with "Best regards, [Name]"               │
│           → Cached, served in 5ms when email context detected               │
│                                                                             │
│  Cache Structure:                                                           │
│  {                                                                          │
│    "gmail": ["Best regards", "Looking forward to", "Thank you for"],        │
│    "slack": ["sounds good", "let's sync", "LGTM"],                          │
│    "vscode_python": ["def ", "return ", "if __name__"]                      │
│  }                                                                          │
└─────────────────────────────────────────────────────────────────────────────┘
                                   ↓ (if no cache hit)
┌─────────────────────────────────────────────────────────────────────────────┐
│  LAYER 2: LOCAL MODEL (10-100ms)                                            │
│  ────────────────────────────────                                           │
│  • Small LLM runs on user's machine (e.g., Phi, TinyLlama)                  │
│  • No network latency                                                       │
│  • Handles: typo fixes, simple completions, common patterns                 │
│                                                                             │
│  Example: User types "teh" → instantly suggests "the"                       │
│           User types "def calc" → suggests function signature               │
└─────────────────────────────────────────────────────────────────────────────┘
                                   ↓ (if complex request)
┌─────────────────────────────────────────────────────────────────────────────┐
│  LAYER 3: CLOUD LLM (100-500ms, streamed)                                   │
│  ────────────────────────────────────────                                   │
│  • GPT-4/Claude for complex rewrites, deep understanding                    │
│  • Response STREAMED token-by-token                                         │
│  • User sees response building in real-time                                 │
│  • Runs in BACKGROUND while user continues typing                           │
│                                                                             │
│  Example: User requests "make this more professional"                       │
│           → Cloud LLM rewrites, streams back progressively                  │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Predictive Pre-computation

The standout feature: **Compute suggestions BEFORE user asks**

```
User types: "Dear Mr."
                ↓
PREDICTION ENGINE (runs parallel):
├── Likely next: recipient name from context
├── Likely pattern: formal greeting
├── Pre-loading: email templates for formal context
├── Pre-loading: common closings for professional emails
                ↓
By time user types "J", suggestion "Johnson" is ALREADY COMPUTED
                ↓
Feels instant (0ms perceived latency)
```

### Typing Pattern Analysis

- Track typing speed and pauses
- Long pause (>500ms) = user might be thinking = suggest now
- Fast typing = user knows what to write = stay quiet
- Adaptive: wait less for common patterns, more for complex input

### WebSocket Strategy

- Pre-establish 3 WebSocket connections
- One always ready, two warming up
- Zero connection delay
- Persistent connection = no handshake overhead

---

## Module 4: System-Wide Compatibility — Universal Access

### The Challenge

How to work in ANY app on ANY platform without rebuilding everything?

### Our Solution: Input Interception Layer

One core engine + multiple lightweight "tentacles"

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     TENTACLE SPECIFICATIONS                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  🌐 BROWSER EXTENSION (Chrome/Firefox)                                      │
│  ───────────────────────────────────────                                    │
│  Detection: MutationObserver on input/textarea elements                     │
│  Display: Floating div positioned near cursor                               │
│  Size: ~50KB                                                                │
│  Scope: All websites, web apps                                              │
│                                                                             │
│  📱 DESKTOP APP (Electron)                                                  │
│  ─────────────────────────                                                  │
│  Detection: Global keyboard hooks (OS-level via node-global-key-listener)  │
│  Display: Overlay window (always-on-top, click-through)                     │
│  Scope: Any native application                                              │
│                                                                             │
│  💻 IDE EXTENSION (VS Code/JetBrains)                                       │
│  ────────────────────────────────────                                       │
│  Detection: Editor API (onTextChange events)                                │
│  Display: Inline ghost text (like Copilot)                                  │
│  Size: ~30KB                                                                │
│  Scope: All files in IDE                                                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Context Detection Per Platform

| Platform | How We Detect App | What We Capture |
|----------|-------------------|-----------------|
| Windows | `GetForegroundWindow()` API | Process name, window title |
| macOS | `NSWorkspace` notifications | App bundle ID, window title |
| Linux | `_NET_ACTIVE_WINDOW` | Window class, title |
| Browser | `tabs.onActivated` + URL | URL, page title, input field type |

### Unified Protocol

All tentacles send same JSON format to core:

```json
{
  "source": "browser_extension",
  "text": "current input text",
  "cursor_position": 45,
  "app_context": {
    "app": "Gmail",
    "url": "mail.google.com",
    "input_type": "compose"
  },
  "action": "suggest" | "rewrite" | "transcribe",
  "timestamp": 1705312456789
}
```

Core engine doesn't care which tentacle sent it — processes uniformly.

---

## Module 5: Personalization — Behavioral Fingerprint

### The Problem

- Users hate configuring AI
- Generic suggestions feel robotic
- Every user writes differently

### Our Solution: Three-Layer Learning

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       USER PROFILE LAYERS                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  LAYER 1: EXPLICIT SETTINGS (User configures once)                          │
│  ─────────────────────────────────────────────────                          │
│  • Role: "Software Developer" / "Marketing Manager" / "Student"             │
│  • Preferred tone: "Direct" / "Friendly" / "Formal"                         │
│  • Language: "English" / "Hindi" / "Both"                                   │
│                                                                             │
│  Set during onboarding, rarely changed.                                     │
│                                                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  LAYER 2: OBSERVED PATTERNS (System learns automatically)                   │
│  ──────────────────────────────────────────────────────                     │
│  Tracked silently:                                                          │
│  • Typing speed: 65 WPM average                                             │
│  • Common phrases: ["LGTM", "sounds good", "let's sync"]                    │
│  • Active hours: 10am-6pm weekdays                                          │
│  • Email length: Short (< 100 words avg)                                    │
│  • Emoji usage: Rare in email, frequent in Slack                            │
│  • Punctuation style: Oxford comma, em-dashes                               │
│                                                                             │
│  Context-specific patterns:                                                 │
│  • Gmail to manager: Formal, no emoji                                       │
│  • Slack to team: Casual, uses ✅ and 👍                                    │
│  • Code comments: Technical, concise                                        │
│                                                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  LAYER 3: FEEDBACK LOOP (Continuous improvement)                            │
│  ─────────────────────────────────────────────────                          │
│  Tracked per suggestion:                                                    │
│  • Accepted: Add to positive patterns                                       │
│  • Rejected: Add to negative patterns                                       │
│  • Edited after accept: Learn the transformation                            │
│                                                                             │
│  ANTI-PATTERNS (things to never suggest):                                   │
│  • User always changes "utilize" → "use"                                    │
│    → Never suggest "utilize" again                                          │
│  • User always removes "I think" from sentences                             │
│    → Never suggest hedging language                                         │
│  • User never uses 🎉 emoji                                                 │
│    → Remove from suggestion pool                                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Profile Storage Structure

```json
{
  "user_id": "local_user_001",
  "explicit": {
    "role": "Software Developer",
    "tone": "direct",
    "language": "en"
  },
  "observed": {
    "typing_speed_wpm": 65,
    "common_phrases": ["LGTM", "sounds good", "let me check"],
    "active_hours": {"start": 10, "end": 18},
    "avg_message_length": {"email": 87, "slack": 23, "code_comment": 45}
  },
  "context_profiles": {
    "gmail_professional": {
      "tone": "formal",
      "emoji": false,
      "greeting": "Hi [Name],"
    },
    "slack_team": {
      "tone": "casual",
      "emoji": true,
      "greeting": "hey"
    }
  },
  "anti_patterns": [
    {"find": "utilize", "user_prefers": "use"},
    {"find": "I think", "user_action": "remove"},
    {"find": "🎉", "user_action": "never_suggest"}
  ],
  "suggestion_stats": {
    "total_shown": 1543,
    "accepted": 1112,
    "rejected": 289,
    "edited_after_accept": 142
  }
}
```

---

## Module 6: Security & Privacy

### The Challenge

An AI keyboard captures **everything** the user types — this is sensitive data that includes:
- Passwords and credentials
- Personal messages
- Financial information
- Confidential work documents

**Trust is essential. Privacy must be built into the architecture.**

### Privacy-First Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         PRIVACY-FIRST DESIGN                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  PRINCIPLE 1: LOCAL-FIRST PROCESSING                                       │
│  • Core engine runs entirely on user's device                               │
│  • Context cache stored locally only                                        │
│  • Personalization data never leaves machine                                │
│                                                                             │
│  PRINCIPLE 2: MINIMAL CLOUD EXPOSURE                                        │
│  • Cloud used only when local can't handle                                  │
│  • Only necessary text sent, never full context                             │
│  • No persistent storage on cloud                                           │
│  • Encrypted transmission (TLS 1.3)                                         │
│                                                                             │
│  PRINCIPLE 3: USER CONTROL                                                  │
│  • User decides what goes to cloud                                          │
│  • One-click data deletion                                                  │
│  • Pause/disable anytime                                                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Data Classification

| Category | Examples | Handling |
|----------|----------|----------|
| 🔴 **Never Captured** | Password fields, credit cards, OTP | Automatic exclusion |
| 🟡 **Local Only** | User profile, typing patterns, context cache | Never sent to cloud |
| 🟢 **Cloud Eligible** | Text for rewriting, audio for transcription | With user consent only |

### Sensitive App Detection

```
AUTOMATIC BLOCKLIST:
├── Banking Apps (Chase, PayPal, etc.) → AI disabled
├── Password Managers (1Password, LastPass) → AI disabled
├── Login Pages (*/login*, */signin/*) → AI disabled for password fields
└── User-Defined Apps/Websites → AI disabled
```

### Encryption & Data Protection

| Aspect | Implementation |
|--------|----------------|
| **Data at Rest** | AES-256 encrypted local database |
| **Data in Transit** | TLS 1.3 for all connections |
| **Cloud Processing** | Stateless, immediate deletion, zero retention |
| **Key Storage** | Device-specific, never leaves machine |

### User Privacy Controls

| Control | Function |
|---------|----------|
| **Pause Mode** | Global hotkey to instantly disable AI |
| **Offline Mode** | Force local-only, no network calls |
| **App Blocklist** | Add apps/websites where AI never activates |
| **Data Wipe** | One-click delete all local data |
| **Export Data** | GDPR-compliant data export |

### Privacy Settings Structure

```json
{
  "privacy": {
    "mode": "balanced",
    "offline_only": false,
    "cloud": {
      "allow_llm": true,
      "allow_whisper": true,
      "allow_analytics": false
    },
    "blocklist": {
      "apps": ["1Password", "LastPass", "Chase"],
      "urls": ["*bank*", "*paypal*", "*/login*"]
    },
    "data_retention": {
      "context_cache_days": 7,
      "auto_delete_on_exit": false
    }
  }
}
```

---

## Seamless App Switching — The Complete Flow

### Detection to Adaptation in <50ms

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        USER SWITCHES APP                                    │
│                     (e.g., Alt+Tab or click)                                │
└─────────────────────────────────────────────────────────────────────────────┘
                                 ↓ [<10ms]
┌─────────────────────────────────────────────────────────────────────────────┐
│                     WINDOW MONITOR                                          │
│         Captures: Process name, Window title, URL (if browser)              │
└─────────────────────────────────────────────────────────────────────────────┘
                                 ↓ [<10ms]
┌─────────────────────────────────────────────────────────────────────────────┐
│                    CONTEXT CLASSIFIER                                       │
│                                                                             │
│  1. Check Context Cache (instant lookup)                                    │
│     → Found: "Gmail" → Load gmail_professional profile                      │
│                                                                             │
│  2. Pattern match window info (if not cached)                               │
│     → "Compose Mail - john@company.com - Gmail"                             │
│     → Category: email, Mode: compose, Recipient: colleague                  │
│                                                                             │
│  3. Merge: cached context + current signals                                 │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                 ↓ [<10ms]
┌─────────────────────────────────────────────────────────────────────────────┐
│                  BEHAVIOR ENGINE UPDATED                                    │
│                                                                             │
│  • Suggestion style: formal                                                 │
│  • Vocabulary bank: professional email vocabulary                           │
│  • Formality level: high                                                    │
│  • Common patterns: greetings, signoffs, status update templates            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                 ↓
                        USER STARTS TYPING
                                 ↓
                     AI RESPONDS WITH NEW CONTEXT
                     (User notices nothing, it just works)
```

### Context Cache for Instant Switching

```
USER'S CONTEXT CACHE (stored locally, updates in background)
├── vscode
│   ├── last_file: "main.py"
│   ├── language: "python"
│   ├── recent_suggestions: ["def ", "async ", "return "]
│   └── behavior_preset: CODING_MODE
│
├── gmail
│   ├── last_recipient: "manager@company.com"
│   ├── draft_in_progress: true
│   ├── recent_suggestions: ["Best regards", "Thank you for"]
│   └── behavior_preset: FORMAL_EMAIL_MODE
│
├── slack
│   ├── last_channel: "#dev-team"
│   ├── recent_mentions: ["@john", "@sarah"]
│   ├── common_reactions: ["✅", "👍", "🎉"]
│   └── behavior_preset: CASUAL_CHAT_MODE
│
└── terminal
    ├── last_cwd: "/home/user/project"
    ├── recent_commands: ["git status", "npm run dev"]
    └── behavior_preset: COMMAND_MODE
```

---

## Module 7: Error Handling & Recovery

### The Challenge

AI systems fail. Networks go down. Models hallucinate. The keyboard must handle failures gracefully without disrupting user's work.

### Failure Scenarios & Responses

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        GRACEFUL DEGRADATION STRATEGY                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  SCENARIO 1: Cloud LLM Unavailable                                          │
│  ─────────────────────────────────                                          │
│  Detection: API timeout (>3s) or error response                             │
│  Response:                                                                  │
│    1. Silently fall back to local model                                     │
│    2. Cache common requests for offline use                                 │
│    3. Show subtle indicator "Offline mode"                                  │
│    4. Retry cloud in background every 30s                                   │
│                                                                             │
│  SCENARIO 2: Whisper Transcription Fails                                    │
│  ─────────────────────────────────────────                                  │
│  Detection: Empty response or confidence < 30%                              │
│  Response:                                                                  │
│    1. If local failed → try cloud                                           │
│    2. If cloud failed → show "Couldn't hear that"                           │
│    3. Offer to retry or type instead                                        │
│                                                                             │
│  SCENARIO 3: Context Detection Fails                                        │
│  ───────────────────────────────────                                        │
│  Detection: Unknown app, no pattern match                                   │
│  Response:                                                                  │
│    1. Apply neutral/generic behavior preset                                 │
│    2. Start learning mode for this app                                      │
│    3. After 10 interactions, build custom profile                           │
│                                                                             │
│  SCENARIO 4: Suggestion Rejected Multiple Times                             │
│  ───────────────────────────────────────────────                            │
│  Detection: 3+ consecutive rejections                                       │
│  Response:                                                                  │
│    1. Reduce suggestion frequency                                           │
│    2. Add to "uncertain" pool                                               │
│    3. Ask user preference silently via behavior tracking                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Recovery Mechanisms

| Failure Type | Auto-Recovery | User Notification |
|--------------|---------------|-------------------|
| Network timeout | Retry 3x with backoff | Subtle icon change |
| LLM error | Fall back to cache/local | None (silent) |
| Corrupted cache | Rebuild from scratch | "Relearning your preferences" |
| Extension crash | Auto-restart within 2s | None if quick |
| Core engine down | Tentacles show "Reconnecting" | Notification after 10s |

### Error Logging

```json
{
  "error_log": {
    "storage": "local_only",
    "retention_days": 7,
    "captured": [
      "error_type",
      "timestamp",
      "app_context",
      "recovery_action"
    ],
    "excluded": [
      "user_text",
      "suggestions",
      "personal_data"
    ]
  }
}
```

---

## Module 8: Onboarding & First-Time User Experience

### Design Philosophy

**Goal:** User gets value in < 2 minutes. No lengthy tutorials.

### Onboarding Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           ONBOARDING STEPS                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  STEP 1: Install & Launch (30 seconds)                                      │
│  ─────────────────────────────────────                                      │
│  • One-click install (browser extension / desktop app)                      │
│  • Automatic startup, no configuration needed                               │
│  • Background service starts silently                                       │
│                                                                             │
│  STEP 2: Quick Setup (60 seconds)                                           │
│  ────────────────────────────────                                           │
│  • Simple onboarding modal:                                                 │
│    ┌─────────────────────────────────────────┐                              │
│    │  Welcome to AI Keyboard!               │                              │
│    │                                         │                              │
│    │  What's your role?                      │                              │
│    │  [ Developer ] [ Writer ] [ Other ]     │                              │
│    │                                         │                              │
│    │  Preferred tone?                        │                              │
│    │  [ Direct ] [ Friendly ] [ Formal ]     │                              │
│    │                                         │                              │
│    │  [Skip - I'll let AI learn naturally]   │                              │
│    └─────────────────────────────────────────┘                              │
│                                                                             │
│  STEP 3: First Interaction (30 seconds)                                     │
│  ───────────────────────────────────────                                    │
│  • Guided first suggestion in current app                                   │
│  • Show: "Press Tab to accept, Esc to dismiss"                              │
│  • Celebrate first accepted suggestion                                      │
│                                                                             │
│  STEP 4: Learn by Using                                                     │
│  ──────────────────────                                                     │
│  • Progressive disclosure of features                                       │
│  • Subtle hints for new features after 1 day, 3 days, 7 days                │
│  • Never overwhelm with all features at once                                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Progressive Feature Discovery

| Day | Features Introduced |
|-----|---------------------|
| Day 1 | Basic suggestions, accept/reject |
| Day 3 | Voice input capability |
| Day 7 | Custom shortcuts, pause mode |
| Day 14 | Advanced settings, blocklist |

### Onboarding Metrics

```
Track silently:
├── Time to first suggestion accepted
├── First-week retention rate
├── Feature discovery percentage
└── Support/help requests
```

---

## Module 9: Keyboard Shortcuts

### Core Shortcuts

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         KEYBOARD SHORTCUTS                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  SUGGESTION INTERACTION                                                     │
│  ───────────────────────                                                    │
│  Tab          → Accept current suggestion                                   │
│  Esc          → Dismiss suggestion                                          │
│  ↓ / ↑        → Navigate between multiple suggestions                       │
│  Ctrl+→       → Accept word-by-word                                         │
│                                                                             │
│  VOICE INPUT                                                                │
│  ───────────                                                                │
│  Ctrl+Shift+V → Hold to speak, release to transcribe                        │
│  Ctrl+Shift+M → Toggle voice mode on/off                                    │
│                                                                             │
│  REWRITE & ENHANCE                                                          │
│  ─────────────────                                                          │
│  Ctrl+Shift+R → Rewrite selected text                                       │
│  Ctrl+Shift+E → Enhance/improve selected text                               │
│  Ctrl+Shift+T → Change tone (cycle: formal/casual/friendly)                 │
│                                                                             │
│  CONTROL                                                                    │
│  ───────                                                                    │
│  Ctrl+Shift+P → Pause/Resume AI (toggle)                                    │
│  Ctrl+Shift+O → Open settings                                               │
│  Ctrl+Shift+? → Show all shortcuts                                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Customizable Shortcuts

```json
{
  "shortcuts": {
    "accept_suggestion": "Tab",
    "dismiss_suggestion": "Escape",
    "voice_input": "Ctrl+Shift+V",
    "rewrite_selection": "Ctrl+Shift+R",
    "pause_toggle": "Ctrl+Shift+P",
    "custom": {
      "insert_signature": "Ctrl+Shift+S",
      "quick_reply": "Ctrl+Shift+Q"
    }
  }
}
```

### Context-Aware Shortcuts

| Context | Extra Shortcuts |
|---------|-----------------|
| **Email** | Ctrl+Shift+1 = Professional template, Ctrl+Shift+2 = Casual template |
| **Code** | Ctrl+Shift+D = Generate docstring, Ctrl+Shift+C = Add comments |
| **Slack** | Ctrl+Shift+G = Insert reaction emoji |

---

## Module 10: Offline Capabilities

### Offline Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         OFFLINE MODE ARCHITECTURE                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  WHAT WORKS OFFLINE (100%)                                                  │
│  ─────────────────────────────                                              │
│  ✓ Context detection (window monitoring is local)                           │
│  ✓ App switching and behavior adaptation                                    │
│  ✓ Cached suggestions (user's common phrases)                               │
│  ✓ Local LLM completions (typos, simple completions)                        │
│  ✓ Local Whisper transcription (Whisper-Tiny model)                         │
│  ✓ Personalization (all data stored locally)                                │
│  ✓ Keyboard shortcuts                                                       │
│                                                                             │
│  WHAT REQUIRES NETWORK                                                      │
│  ───────────────────────                                                    │
│  ✗ Complex rewrites (needs Cloud LLM)                                       │
│  ✗ High-quality transcription for noisy audio                               │
│  ✗ Advanced intent understanding                                            │
│                                                                             │
│  GRACEFUL DEGRADATION                                                       │
│  ────────────────────                                                       │
│  When network unavailable:                                                  │
│  • All local features continue working                                      │
│  • Cloud-dependent features show "Offline" hint                             │
│  • Requests queued for when connection returns                              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Local Model Requirements

| Model | Size | Purpose | RAM Required |
|-------|------|---------|--------------|
| Whisper-Tiny | 39 MB | Voice transcription | ~200 MB |
| TinyLlama (1.1B) | 600 MB | Text completions | ~2 GB |
| Phi-2 (2.7B) | 1.5 GB | Better completions | ~4 GB |

### Offline-First Sync Strategy

```
SYNC STRATEGY:
├── Cache Layer
│   ├── Pre-cache user's top 100 phrases per context
│   ├── Store recent suggestions for quick re-use
│   └── Persist context profiles locally
│
├── Background Sync (when online)
│   ├── Send batched analytics (opt-in)
│   ├── Download model updates
│   └── Sync user preferences across devices (opt-in)
│
└── Conflict Resolution
    ├── Local changes always win
    ├── Cloud is secondary/backup
    └── User can force cloud restore if needed
```

### Forced Offline Mode

```
User can enable "Airplane Mode" for AI Keyboard:
├── Zero network calls
├── All processing local
├── Full privacy guarantee
└── Reduced capability (no complex rewrites)
```

---

## Module 11: Plugin & Extension System

### Why Plugins?

Allow users and developers to extend AI Keyboard without modifying core.

### Plugin Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         PLUGIN SYSTEM ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│                        ┌─────────────────────┐                              │
│                        │    CORE ENGINE      │                              │
│                        │  (Immutable Core)   │                              │
│                        └─────────────────────┘                              │
│                                 ↑                                           │
│                           Plugin API                                        │
│                                 ↑                                           │
│   ┌────────────┬────────────┬────────────┬────────────┬────────────┐       │
│   │  Plugin 1  │  Plugin 2  │  Plugin 3  │  Plugin 4  │  Plugin 5  │       │
│   │ Email      │ Code       │ Grammar    │ Translate  │ Custom     │       │
│   │ Templates  │ Snippets   │ Pro        │            │            │       │
│   └────────────┴────────────┴────────────┴────────────┴────────────┘       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Plugin Types

| Type | Purpose | Example |
|------|---------|---------|
| **Context Plugin** | Custom app detection | "Detect Figma and enable design vocab" |
| **Suggestion Plugin** | Custom suggestion logic | "Insert company-specific templates" |
| **Command Plugin** | Custom shortcuts/actions | "Quick insert code snippet" |
| **Integration Plugin** | Connect external services | "Fetch data from Notion" |

### Plugin API

```python
# Plugin Interface
class AIKeyboardPlugin:
    def on_context_change(self, context: Context) -> None:
        """Called when user switches apps"""
        pass
    
    def on_text_input(self, text: str, context: Context) -> List[Suggestion]:
        """Return custom suggestions"""
        pass
    
    def on_command(self, command: str, context: Context) -> str:
        """Handle custom command"""
        pass
    
    def get_shortcuts(self) -> Dict[str, Callable]:
        """Register custom keyboard shortcuts"""
        pass
```

### Example Plugins

```
EXAMPLE 1: Email Template Plugin
─────────────────────────────────
Trigger: User types "##meeting" in email context
Action: Insert pre-defined meeting follow-up template
Customizable: User can define their own templates

EXAMPLE 2: Code Snippet Plugin
──────────────────────────────
Trigger: User types "!!react-component" in VS Code
Action: Insert React component boilerplate
Customizable: User can add snippets for any language

EXAMPLE 3: Translation Plugin
─────────────────────────────
Trigger: Ctrl+Shift+L to translate selection
Action: Translate selected text using LibreTranslate (local) or cloud API
Customizable: Default language pairs
```

### Plugin Security

```
PLUGIN SANDBOXING:
├── Plugins run in isolated context
├── No direct file system access
├── No network access without permission
├── Limited API surface exposed
└── User must approve plugin permissions

PERMISSION LEVELS:
├── Basic: Read context, provide suggestions
├── Standard: + Custom shortcuts, store data
├── Extended: + Network access, external integrations
└── Admin: + System-level access (rare, needs review)
```

### Plugin Distribution (Post-Hackathon)

```
PLUGIN MARKETPLACE:
├── Curated plugins reviewed for security
├── User ratings and reviews
├── Auto-updates for installed plugins
└── Source code visible for open-source plugins
```

---

## Standout Strategies Summary

| Module | What Others Do | Our Standout Approach |
|--------|---------------|----------------------|
| **MCP** | Isolated tool calls | **Context Mesh** — tools share state and auto-chain |
| **Whisper** | Always call same API | **Confidence Routing** — analyze audio, pick optimal model |
| **Speed** | Wait for API response | **Predictive Pre-compute** — suggestions ready before needed |
| **Platform** | Rebuild for each platform | **Tentacle Architecture** — one brain, many lightweight interfaces |
| **Personalization** | Ask user to configure | **Behavioral Fingerprint** — silent learning from usage |

---

## Hackathon MVP Scope

### What We'll Build

| Component | Priority | Description |
|-----------|----------|-------------|
| Core MCP Server | P0 | Context Mesh with basic tools |
| Chrome Extension | P0 | First tentacle, works on all websites |
| Whisper Integration | P0 | Local + API routing |
| Real-time Suggestions | P0 | Three-layer speed system |
| Basic Personalization | P1 | Layer 1 (explicit) + Layer 2 (observed) |
| WebSocket Streaming | P1 | For low-latency communication |

### Tech Stack

| Layer | Technology |
|-------|------------|
| Core Engine | Python + FastAPI |
| MCP Server | MCP SDK |
| Voice | OpenAI Whisper (local model) + API fallback |
| LLM | OpenAI GPT-4 / Claude (cloud) + local small model |
| Browser Extension | JavaScript, Chrome Extension API |
| Communication | WebSocket |
| Storage | Local JSON/SQLite for context cache |

### Post-Hackathon Roadmap

1. Desktop App (Electron) — system-wide access
2. VS Code Extension — deep IDE integration
3. Layer 3 Personalization — feedback loop
4. Multi-language support
5. Team/org profiles

---

## Conclusion

AI Keyboard is not just "AI + typing". It's a fundamental rethinking of the input layer:

1. **Universal** — One solution for all apps, all platforms
2. **Invisible** — Works in background, user doesn't switch contexts
3. **Intelligent** — Understands intent, not just words
4. **Personal** — Learns and adapts to each user
5. **Fast** — Predicts ahead, feels native

> **"Making every keystroke intelligent."**
