# AI Keyboard
## Making Every Keystroke Intelligent

---

# Slide 1: Problem Statement

### Current Keyboard Limitations

**Definition:** Traditional keyboards serve as passive input devices with no contextual awareness or intelligent assistance.

**Identified Gaps:**

1. **No Context Awareness** — Keyboard behavior remains static regardless of application
2. **No Assistance** — Users receive no help with tone, style, or content
3. **No Learning** — Repetitive phrases require manual retyping
4. **Fragmented Tools** — Grammarly (grammar only), Copilot (code only), ChatGPT (copy-paste required)

**Opportunity:** A unified AI layer at the input level that works universally across all applications.

---

# Slide 2: Proposed Solution

### AI Keyboard — System Definition

**Definition:** An AI-powered middleware layer positioned between keyboard input and application receipt, providing context-aware suggestions, voice input, and personalized assistance.

**Core Capabilities:**

1. **Context-Aware Suggestions** — Adaptive completions based on detected application
2. **Voice-to-Formatted-Text** — Speech input with intent enhancement
3. **Automatic Personalization** — Behavioral learning from user patterns
4. **Universal Compatibility** — Functions across browsers and native applications

---

# Slide 3: System Architecture

### Component Overview

```
┌────────────────────────────────────────────────────────────────┐
│                      CORE AI ENGINE                            │
│                   (Local Background Service)                   │
│                                                                │
│   ┌────────────────────────────────────────────────────────┐   │
│   │                   MCP SERVER                           │   │
│   │                                                        │   │
│   │   Tools:                     Shared Context:           │   │
│   │   • detect_context           • Application state       │   │
│   │   • complete_intent          • User profile            │   │
│   │   • enhance_text             • Session history         │   │
│   │   • transcribe_voice         • Learned patterns        │   │
│   └────────────────────────────────────────────────────────┘   │
│                                                                │
│   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    │
│   │  LLM Router  │    │Personalization│   │    Cache     │    │
│   │              │    │    Engine     │   │    Layer     │    │
│   └──────────────┘    └──────────────┘    └──────────────┘    │
└────────────────────────────────────────────────────────────────┘
                              ↑
                     WebSocket Protocol
                              ↑
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
    ┌───────────┐       ┌───────────┐       ┌───────────┐
    │  Browser  │       │  Desktop  │       │    IDE    │
    │ Extension │       │Application│       │ Extension │
    └───────────┘       └───────────┘       └───────────┘
                     (Lightweight Clients)
```

**Architecture Pattern:** Centralized intelligence with distributed lightweight clients.

---

# Slide 4: Speed Optimization

### Three-Layer Response System

**Objective:** Achieve sub-100ms response time for suggestions.

```
┌─────────────────────────────────────────────────────────────┐
│  LAYER 1: CACHE                              [0-10ms]       │
│  • Pre-stored common phrases                                │
│  • User-specific frequent patterns                          │
│  • Instant retrieval                                        │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  LAYER 2: LOCAL LLM                          [10-100ms]     │
│  • Ollama with TinyLlama/Phi-3                              │
│  • Handles simple completions                               │
│  • No network latency                                       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  LAYER 3: CLOUD LLM                          [100-500ms]    │
│  • GPT-4 for complex requests                               │
│  • Streaming response                                       │
│  • Full rewrite capabilities                                │
└─────────────────────────────────────────────────────────────┘
```

**Performance Target:** 70% requests served from Layer 1-2.

---

# Slide 5: Context Detection

### Application-Specific Adaptation

**Mechanism:** Real-time detection of active application, input field type, and context signals.

**Detection Sources:**
- URL patterns (browser)
- Window title (desktop)
- Input field attributes (DOM)

**Behavior Mapping:**

| Context | Detected Formality | AI Behavior |
|---------|-------------------|-------------|
| Gmail Compose | Formal | Professional suggestions |
| Slack Message | Casual | Brief, emoji-friendly |
| VS Code Editor | Technical | Code-aware completions |

**Switching Latency:** <50ms

---

# Slide 6: Voice Integration

### Speech-to-Formatted-Text Pipeline

**Process Flow:**

```
Audio Input
     ↓
┌─────────────────────────────────────────────────────────────┐
│  AUDIO ANALYSIS                                             │
│  • Quality assessment                                       │
│  • Duration measurement                                     │
│  • Route decision (local vs cloud)                          │
└─────────────────────────────────────────────────────────────┘
     ↓
┌─────────────────────────────────────────────────────────────┐
│  TRANSCRIPTION (Whisper)                                    │
│  Raw output: "email john about deadline friday"             │
└─────────────────────────────────────────────────────────────┘
     ↓
┌─────────────────────────────────────────────────────────────┐
│  INTENT ENHANCEMENT                                         │
│  Formatted output:                                          │
│  "Hi John, Could we extend the deadline to Friday?"         │
└─────────────────────────────────────────────────────────────┘
```

**Routing Logic:** Clear audio → Local Whisper | Noisy audio → Cloud API

---

# Slide 7: Personalization Engine

### Behavioral Learning System

**Learning Layers:**

1. **Explicit Layer** — User-provided preferences (role, tone, language)
2. **Observed Layer** — Pattern extraction from usage (greeting styles, common phrases)
3. **Feedback Layer** — Accept/reject signal processing (anti-patterns, preferences)

**Stored Data:**
- Greeting patterns per application
- Frequently used phrases
- Suggestion acceptance patterns
- User-specific anti-patterns

**Privacy Constraint:** Patterns stored, content discarded.

---

# Slide 8: Security Architecture

### Privacy-First Design

**Core Principles:**

1. **Local-First Processing** — Core engine executes on user device
2. **Ephemeral Content Handling** — Content processed in memory, not persisted
3. **User Control** — Granular settings for pause, blocklist, data deletion

**Data Classification:**

| Category | Examples | Handling |
|----------|----------|----------|
| Excluded | Passwords, OTP | Never captured |
| Ephemeral | Email content | Memory only |
| Persistent | User preferences | Encrypted local storage |

**Auto-Block Triggers:** Banking URLs, password managers, login pages

---

# Slide 9: MCP Integration

### Frai Ecosystem Compatibility

**Protocol:** Model Context Protocol (MCP) — Standardized AI tool communication.

**Exposed Tools:**

```
┌─────────────────────────────────────────────────────────────┐
│  AI KEYBOARD MCP SERVER                                     │
│                                                             │
│  • detect_context   — Returns application context           │
│  • complete_intent  — Returns text suggestions              │
│  • enhance_text     — Returns improved text                 │
│  • transcribe_voice — Returns transcribed + formatted text  │
└─────────────────────────────────────────────────────────────┘
                              ↑
                    Standard MCP Protocol
                              ↑
┌─────────────────────────────────────────────────────────────┐
│  Compatible Clients: Frai Platform, Claude Desktop          │
└─────────────────────────────────────────────────────────────┘
```

**Benefit:** Single implementation, multiple integrations.

---

# Slide 10: Technology Stack

### Implementation Components

| Component | Technology |
|-----------|------------|
| Core Server | Python, FastAPI, MCP SDK |
| Local LLM | Ollama (TinyLlama, Phi-3) |
| Cloud LLM | OpenAI GPT-4 API |
| Voice Processing | Whisper (local + API) |
| Browser Extension | JavaScript, Chrome Extension APIs |
| Communication | WebSocket |
| Storage | SQLite with AES-256 encryption |

---

# Slide 11: Implementation Status

### MVP Deliverables

**Completed:**
- Core MCP Server with four tools
- Chrome Browser Extension
- Three-layer speed system
- Context detection module
- Personalization engine
- Privacy controls

**Roadmap:**
- Phase 1: Desktop application (native apps)
- Phase 2: IDE extensions
- Phase 3: Enterprise features

---

# Slide 12: Value Proposition

### Key Differentiators

1. **Universal** — Single solution for all applications
2. **Contextual** — Adapts to application automatically
3. **Fast** — Sub-100ms response time
4. **Personal** — Learns user patterns silently
5. **Private** — Local-first, user-controlled
6. **Extensible** — MCP-compatible for ecosystem integration

---

# Thank You

**AI Keyboard**
Making Every Keystroke Intelligent
