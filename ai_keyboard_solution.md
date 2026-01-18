# AI KEYBOARD — COMPLETE SOLUTION DOCUMENTATION
## From Input Device to Intelligent Work Interface

**Status:** Ready for 48-hour hackathon sprint  
**Date:** January 18, 2026  
**Team Size:** 4 people  
**Timeline:** 48 hours

---

## EXECUTIVE SUMMARY

### The Problem
The keyboard is the most used interface in modern work yet fundamentally passive. Users switch between "thinking" (Frai) and "doing" (external apps), losing context constantly. AI assistance requires explicit action—context switches, copy-paste, manual sync.

### Our Solution
**An OS-level intelligent input layer** that:
- Works system-wide across ALL applications (Gmail, VS Code, Slack, browser, Frai, etc.)
- Understands context automatically (app, mode, tone)
- Suggests naturally (<150ms latency) with progressive refinement
- Learns from user feedback and patterns
- Works offline (local models, local-first)
- Optional Frai integration for interview prep context

### Why This Wins
1. **System-wide** (not app-specific like Grammarly/Copilot)
2. **Ambient** (suggestions appear without asking)
3. **Context-aware** (different tone for email vs code vs chat)
4. **Built from scratch** (custom models, custom engine)
5. **Local-first** (privacy-first, offline-capable)
6. **Achievable** (focused MVP, proven tech stack)

### The Hero Demo
User practices interview in Frai → takes notes in VS Code → messages friend in Slack → all with AI suggestions understanding interview context. Seamless. Continuous. Intelligent.

---

## ARCHITECTURE OVERVIEW

### 4-Layer System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│ LAYER 1: OS-Level Input Interception                       │
│ • Global keyboard hooks (Windows/macOS/Linux)              │
│ • Browser extension (Chrome/Firefox)                       │
│ • Electron desktop app for any application                 │
│ • Voice input (Whisper-Tiny local transcription)           │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ LAYER 2: Context Detection & Classification                │
│ • Identify active application (Gmail, VS Code, Slack)      │
│ • Classify mode (email/code/chat/interview)                │
│ • Determine formality (formal/casual/technical)            │
│ • Detect urgency & time context                            │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ LAYER 3: Multi-Layer Suggestion Engine                     │
│ • Cache Layer (0-10ms) - User's common patterns            │
│ • Local Model (10-100ms) - Phi-2 quantized (2.7B)         │
│ • Cloud Layer (async) - GPT-4/Claude in background         │
│ • Predictive pre-computation (compute before asked)        │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ LAYER 4: Optional Frai Integration (Bonus Feature)         │
│ • Fetch interview practice context (if available)          │
│ • Log practice sessions automatically                      │
│ • Enrich suggestions with Frai data                        │
│ • Works perfectly without Frai (local-only mode)           │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow

```
User Types in Any App
        ↓
LAYER 1: Capture input + app context
        ↓
LAYER 2: Detect context (what is user doing?)
        ↓
LAYER 3: Generate suggestions (cache → local → cloud async)
        ↓
LAYER 4: Optional Frai context enrichment
        ↓
Display suggestion overlay to user
        ↓
User accepts (Tab) or rejects (typing)
        ↓
Log for personalization & feedback
```

---

## DETAILED LAYER DESCRIPTIONS

### LAYER 1: OS-Level Input Interception

**Windows Implementation:**
- SetWindowsHookEx(WH_KEYBOARD_LL) for global keyboard capture
- GetForegroundWindow() for active app detection
- Process name mapping to app identification

**macOS Implementation:**
- NSWorkspace.frontmostApplication for active app
- CGEvent.tapCreate with accessibility permissions
- App bundle ID detection

**Linux Implementation:**
- xdotool getactivewindow for active window
- XRecord extension for keyboard events
- Window class tracking

**Browser Extension (Chrome/Firefox):**
- Manifest V3 compliance (~60KB)
- Content script injected into all pages
- MutationObserver for input field detection
- Floating overlay for suggestion display

**Voice Input:**
- Global hotkey to activate recording
- Whisper-Tiny local inference (~200MB)
- Auto-punctuation and casing
- Intent extraction from transcription

### LAYER 2: Context Detection

**Classification Rules:**

```json
{
  "email": {
    "apps": ["Gmail", "Outlook", "Apple Mail"],
    "markers": ["To:", "Subject:", "compose"],
    "mode": "compose|reply|forward",
    "formality": "professional"
  },
  "code": {
    "apps": ["VS Code", "IntelliJ", "PyCharm"],
    "markers": ["def ", "class ", "import ", "{"],
    "mode": "coding|commenting",
    "formality": "technical"
  },
  "chat": {
    "apps": ["Slack", "Discord", "Teams"],
    "markers": ["@", "emoji", "short phrases"],
    "mode": "channel|DM",
    "formality": "casual"
  },
  "interview": {
    "apps": ["Frai web app", "text editors"],
    "markers": ["Q:", "Answer:", "practice"],
    "mode": "question|answer",
    "formality": "professional"
  }
}
```

**Context Object:**
```json
{
  "app": "Gmail",
  "category": "email",
  "mode": "compose",
  "recipient_hint": "manager@company.com",
  "formality": "professional",
  "urgency": "normal",
  "time_context": "9:30 AM (business hours)",
  "typing_state": "thinking (pause detected)",
  "frai_context": null  // or interview data if available
}
```

### LAYER 3: Multi-Layer Suggestion Engine

**Layer 1 - Cache (0-10ms):**
- User's common phrases stored per context
- Recent suggestions per app context
- Exact pattern matching
- Example: User always ends emails with "Best regards, [Name]"

**Layer 2 - Local Model (10-100ms):**
- Phi-2 (2.7B parameters, quantized to GGML format)
- Runs on user's machine (GPU optional)
- No network latency
- Handles typo fixes, completions, common patterns

**Layer 3 - Cloud LLM (100-500ms, async):**
- GPT-4 or Claude for complex rewrites
- Runs in background while user continues typing
- Streamed token-by-token for perceived speed
- Non-blocking (user doesn't wait)

**Predictive Pre-computation:**
```
User types: "Dear Mr."
├── Prediction engine (runs in parallel):
│   ├── Likely next: recipient name
│   ├── Pre-loading: email templates
│   ├── Pre-loading: common closings
│   └── Pre-computing: formal greeting patterns
│
├── By time user types "J":
│   └── Suggestion already computed: "Johnson"
│
└── Result: Feels instant (0ms perceived latency)
```

### LAYER 4: Optional Frai Integration

**How It Works:**
- User enables in settings (optional, default OFF)
- If Frai is running, API calls fetch context
- Non-blocking (2-second timeout, then continues)
- Enriches suggestions but doesn't break without it

**Data Available from Frai:**
- Current interview question being practiced
- Key concepts user is studying
- Previous answers and feedback
- User's interview goals and timeline
- Common mistakes and patterns

**Example Usage:**
```
User typing in Slack about interview prep:
├── Has Frai context: "Practicing URL shortener design"
├── Local knowledge: "User just wrote system design notes"
└── Generates: "Been working on system design for URLs...
               focusing on scalability and database design..."
```

---

## TECHNICAL STACK

### Backend (Core Intelligence)
- **Language:** Python 3.11+
- **Framework:** FastAPI (async-first, WebSocket support)
- **Local Model:** Phi-2 (2.7B, quantized to GGML)
- **Voice:** Whisper-Tiny (39M, ~200MB)
- **Intent Classifier:** BERT-tiny fine-tuned
- **Storage:** SQLite (local machine only)
- **Communication:** WebSocket (real-time, persistent)
- **Optional Cloud:** OpenAI Whisper API (user's key)

### Frontend Clients

**Browser Extension:**
- Manifest V3 (Chrome/Firefox compatible)
- Service Worker (background processing)
- Content Script (input detection)
- ~60KB total size
- Floating suggestion overlay

**Desktop App (Electron):**
- Windows/macOS/Linux support
- Global keyboard hook registration
- Settings UI
- ~50MB footprint
- Tray menu integration

### Deployment
- Runs as background daemon (OS service)
- No cloud required (optional only)
- Graceful degradation if offline
- <200MB memory typical usage
- Data stored locally (SQLite)

---

## 48-HOUR EXECUTION PLAN

### Team Structure (4 People)
- **Person A (Backend Lead):** FastAPI, models, context engine
- **Person B (Desktop):** Electron app, global hooks
- **Person C (Browser):** Chrome extension, content script
- **Person D (Integration):** Testing, demo, final assembly

### Day 1: Foundation (Hours 0-24)

**Hours 0-2: Setup**
- Create git repo with folder structure
- Set up FastAPI project skeleton
- Install dependencies (from requirements.txt)
- Test OS keyboard hooks on target platform

**Hours 2-5: Model Loading**
- Download Phi-2 model (2.5GB)
- Quantize to GGML format
- Load Whisper-Tiny locally
- Test inference latency

**Hours 5-8: Context Detection**
- Implement app detection (Windows/macOS/Linux)
- Classification logic (email/code/chat)
- Context object creation
- Test with real windows/apps

**Hours 8-12: Browser Extension**
- Create manifest.json (Manifest V3)
- Content script with input detection
- MutationObserver for dynamic elements
- WebSocket connection to backend

**Hours 12-16: Electron Desktop App**
- Global keyboard hook registration
- Floating suggestion overlay
- Settings storage (electron-store)
- Tray menu integration

**Hours 16-20: Optional Frai Integration**
- HTTP client for Frai API
- Context fetching with 2s timeout
- Non-blocking background requests
- Graceful fallback if unavailable

**Hours 20-24: Testing & Integration**
- End-to-end test: Browser → Backend → Suggestion
- End-to-end test: Desktop → Backend → Suggestion
- Test with multiple apps simultaneously
- Performance profiling (latency measurement)

### Day 2: Polish & Demo (Hours 24-48)

**Hours 24-28: UI Polish**
- Smooth overlay animations (CSS)
- No lag or visual jank
- Responsive positioning
- Multiple suggestion display

**Hours 28-32: Settings UI**
- Role selection (developer, manager, etc.)
- Tone preference (formal, casual, direct)
- Frai integration toggle
- Enable/disable per-app
- Clear and reset personalization

**Hours 32-36: Voice Input**
- Voice recording UI
- Local Whisper inference
- Intent extraction
- Formatted output insertion
- Auto-punctuation

**Hours 36-42: Comprehensive Testing**
- Test all 3 platforms (Windows, macOS, Linux if possible)
- Test with real apps (Gmail, VS Code, Slack)
- Test voice input end-to-end
- Stress test (concurrent requests)
- Error handling verification

**Hours 42-48: Demo Preparation**
- Record demo video (5 minutes)
- Prepare live demo backup
- Write demo script
- Practice presentation
- Clean up code and comments
- Final git push

### Success Criteria Checklist
- ✅ Suggestion latency < 150ms
- ✅ Works in ≥3 apps simultaneously
- ✅ Context-aware suggestions (tone changes per app)
- ✅ Optional Frai (works without it)
- ✅ Local-first (works offline)
- ✅ Clean, commented code
- ✅ Smooth, compelling demo

---

## HERO DEMO FLOW (5 Minutes)

### Setup
- Frai web app open in browser (interview prep mode)
- VS Code open with scratch notes file
- Slack open with friend in DM

### Scene 1: Frai Practice (1 minute)
```
Show: User practicing "Design a URL shortener" question
AI Keyboard: Detects interview mode, captures context
Caption: "AI Keyboard recognizes this is interview prep"
```

### Scene 2: Context Switches to VS Code (1 minute)
```
Show: User opens VS Code, starts typing notes
Type: "# URL Shortener Design"
AI Keyboard: Suggests (auto-filled from Frai context)
Suggest: "## Key Requirements:
          - High read/write ratio
          - Fast lookup by short URL
          - Globally distributed"
User: Presses Tab to accept
Caption: "Same AI engine, different app, context adapted"
```

### Scene 3: Messages Friend (1 minute)
```
Show: Switch to Slack, user types message
Type: "Hey, just finished my system design session..."
AI Keyboard: Suggestion appears (Frai context flows)
Suggest: "...practicing URL shortener design. Focused on
         understanding scalability patterns and database
         optimization..."
User: Accepts suggestion
Caption: "Cross-app continuity: context from Frai → Slack"
```

### Scene 4: Voice Input (0.5 minute)
```
Show: Global hotkey triggers voice recording
User: Speaks "Email about the system design project"
Result: Transcribed and formatted intelligently
Caption: "Voice as first-class input, contextually aware"
```

### Scene 5: Auto-Logging (0.5 minute)
```
Show: Back in Frai
Caption: "System auto-logged: practiced URL shortener,
         explained to peer, 30 minutes"
Message: "No manual logging. Context maintained automatically."
```

### Closing Statement
"That's AI living where work actually happens—at the keyboard, across all applications, maintaining continuity without context switching."

---

## KEY DIFFERENTIATORS

| Aspect | Others | AI Keyboard |
|--------|--------|-------------|
| **Scope** | App-specific (email only, code only) | System-wide (any app) |
| **Context** | Single context | Cross-app with optional Frai |
| **Speed** | Waits for API calls | Cache + local model |
| **Privacy** | Cloud-first | Local-first, optional cloud |
| **Learning** | Generic for everyone | Personal + context-aware |
| **Integration** | Siloed features | Unified input layer |
| **Built-from-scratch** | Claims but uses wrappers | Actual custom models + engine |
| **Innovation** | Incremental | Transformative (HCI shift) |

---

## IMPLEMENTATION HIGHLIGHTS

### Why This Architecture Works

1. **OS-Level Interception:** Catches input before any app sees it
2. **Context Mesh:** All tools share unified context state
3. **Speed Layers:** Typical case fast (<50ms), complex case async
4. **Optional Frai:** Adds value without creating dependency
5. **Local-First:** Privacy respected, offline-capable
6. **Scalable:** Add new app via thin client, backend unchanged

### Built From Scratch (Not Wrappers)

❌ NOT using: Grammarly API, Copilot SDK, SwiftKey, Gboard  
✅ YES building: Custom suggestion engine, custom context detection, custom OS interception

### Risk Mitigation

- **Behind schedule:** Cut Frai integration (still works)
- **Model too slow:** Use smaller quantized version
- **Keyboard hook fails:** Use browser-only subset
- **Feature bloat:** Cut voice, keep text
- **Demo failure:** Have pre-recorded backup ready

---

## PERSONALIZATION ENGINE

### Three-Layer Learning

**Layer 1: Explicit Settings**
- Role: "Software Developer" / "Manager" / "Student"
- Preferred tone: "Direct" / "Friendly" / "Formal"
- Language preference
- App-specific rules

**Layer 2: Observed Patterns**
- Typing speed: Words per minute
- Common phrases per context
- Active hours: When user types most
- Message length: Typical email/message lengths
- Emoji usage: Frequent vs rare
- Punctuation style: Oxford comma preference, em-dashes

**Layer 3: Feedback Loop**
- Tracked per suggestion: Accepted vs rejected
- Learned transformations: If user changes suggestion, learn the pattern
- Anti-patterns: Never suggest again if always rejected

---

## DEPLOYMENT & OPERATIONS

### Installation
1. Download AI Keyboard app (desktop) or extension (browser)
2. Grant OS permissions (accessibility on macOS, keyboard on Windows)
3. Download models (~2.5GB first time)
4. Configure settings (optional, can use defaults)

### Local Operation
- All data stored locally (SQLite)
- No internet required for basic operation
- Cloud features (cloud LLM) optional with user consent

### Privacy & Control
- User can pause AI Keyboard anytime
- User can see what data is captured
- No data sent to cloud by default
- Can enable Frai integration or keep local-only
- Can clear personalization data anytime

---

## CONTINGENCY PLANNING

**If Behind at Hour 40:**
- Cut Frai integration (mark as TODO)
- Focus on 2 apps instead of 3
- Use hardcoded test data instead of real OS hooks

**If One Person Out:**
- Redistribute their critical path tasks
- Others absorb their hours
- Narrow scope to MVP

**Must-Haves (Never Cut):**
- Backend suggestion engine works
- At least one frontend (pick browser OR desktop)
- End-to-end WebSocket communication
- Smooth demo showing context awareness

---

## SUCCESS CRITERIA

At demo day, show:

✅ **"That's useful"** — User typing in multiple apps, suggestions adapt automatically  
✅ **"That's novel"** — First time seeing ambient AI that doesn't break context  
✅ **"That scales"** — Clear architecture for any app, not just 3  
✅ **"Built real"** — Code is clean, production-like, not hacky  
✅ **"It works"** — Fast (<150ms), smooth, no crashes  
✅ **"That's powerful"** — Demo shows real value (interview continuity)

---

## YOUR 30-SECOND PITCH

> "The keyboard is where thinking becomes action. Right now that moment is broken—context switches everywhere. We built AI Keyboard: an OS-level intelligent layer that transforms every keystroke into a contextual moment of AI assistance. Not a plugin for one app. A system-wide service working everywhere—email, code, chat, browser, anywhere. It understands your context, adapts suggestions, learns from you. Optional Frai integration adds interview prep awareness. The demo shows: practice interview in Frai → take notes in VS Code → message friend in Slack, all with context flowing seamlessly. No switching. No copy-paste. That's AI living where work actually happens."

---

## NEXT STEPS

### Before Hackathon Starts
- [ ] Form 4-person team
- [ ] Assign roles (backend, desktop, browser, integration)
- [ ] Download Phi-2 model (~2.5GB)
- [ ] Create git repo with folder structure
- [ ] Test OS keyboard hooks on target platform
- [ ] Memorize the 30-second pitch

### During Hackathon
- [ ] Follow 48-hour plan hour by hour
- [ ] Test end-to-end regularly
- [ ] Keep demo simple (don't overthink)
- [ ] Focus on latency (most critical)
- [ ] Record demo video daily (backup)

### Demo Day
- [ ] Practice pitch
- [ ] Run through demo 3x minimum
- [ ] Have live demo backup ready
- [ ] Show code (it's clean)
- [ ] Answer judges' questions with confidence

---

**You have everything you need. Now execute it. 🚀**

Built from scratch. System-wide. Context-aware. Local-first. That's the future of human-computer interaction.