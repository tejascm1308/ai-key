# AI KEYBOARD — Complete Solution Documentation

> **Version:** 2.0 — Updated with complete intelligence architecture
> **Date:** 2026-01-29
> **Status:** Production-Ready Design

---

## What is AI Keyboard?

AI Keyboard transforms your keyboard from a passive input device into an **intelligent assistant** that:

- Understands **what app** you're in (Gmail, Slack, VS Code, etc.)
- Knows **what you're trying to do** (writing email, coding, chatting)
- **Suggests** relevant completions based on context, mood, and your style
- **Learns** your writing patterns automatically
- Works **everywhere** — any app, any platform
- Operates **completely automatically** — no manual triggers needed

```
Traditional Keyboard:
You type → Text appears → App receives text
(Keyboard understands nothing)

AI Keyboard:
You type → STM captures context → Mood detected → LLM suggests → You accept
(AI lives at the point of input, understands everything)
```

---

## Quick Glossary (Important Terms)

| Term              | What It Means                                                               |
| ----------------- | --------------------------------------------------------------------------- |
| **STM**           | Short-Term Memory — Tracks current session context (app, text, mood, etc.)  |
| **LTM**           | Long-Term Memory — Learns your patterns over time (MongoDB)                 |
| **MCP**           | Model Context Protocol — Standard way for AI tools to communicate           |
| **UI Automation** | Windows API for reading text from any application                           |
| **LLM**           | Large Language Model — AI that generates text (GPT-4, Claude, local models) |
| **Whisper**       | OpenAI's speech-to-text AI (runs locally using faster-whisper)              |
| **Mood**          | User's current state (focused, rushed, frustrated) inferred from behavior   |

---

## Part 1: Core Intelligence Systems

### The Foundation: STM + LTM Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                    SHORT-TERM MEMORY (STM)                       │
│                  "What's happening right now"                    │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  SESSION DATA:                                                   │
│  • App: Microsoft Word (document_editor)                         │
│  • Document: "Q1 Report.docx" — Conclusion section               │
│  • Text before cursor: "In conclusion, our analysis shows that"  │
│  • Caret position: Line 42, Char 156                             │
│                                                                  │
│  CROSS-APP CONTEXT:                                              │
│  • Recent apps: Chrome (Q1 Data spreadsheet), Outlook (email)    │
│  • Clipboard: "Revenue: $2.4M (+15% YoY)" [relevance: 0.85]     │
│  • Workflow: report_writing_from_data                            │
│                                                                  │
│  BEHAVIORAL SIGNALS:                                             │
│  • Typing speed: 65 WPM (baseline: 72 WPM) — slightly slower     │
│  • Backspace freq: low                                           │
│  • App switches: 3 in last 10 min                                │
│  • Suggestion acceptance: 67% this session                       │
│                                                                  │
│  MOOD ESTIMATION:                                                │
│  • Current mood: "focused" (confidence: 78%)                     │
│  • Contributing factors: steady typing, low multitasking         │
│  • Mood history: neutral → focused (progressing)                 │
│                                                                  │
│  TEMPORAL CONTEXT:                                               │
│  • Time: 3:15 PM Wednesday                                       │
│  • Session phase: mid_session (45 min)                           │
│  • Productivity window: peak_afternoon                           │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
                              ↓
                   Updated every keystroke (debounced)
                   Deleted on session end (privacy)
                              ↓
┌──────────────────────────────────────────────────────────────────┐
│                    LONG-TERM MEMORY (LTM)                        │
│                 "What we learned about you"                      │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  USER PROFILE:                                                   │
│  • Role: software_engineer                                       │
│  • Writing style: professional, concise                          │
│  • Formality default: 0.7 (scale 0-1)                            │
│                                                                  │
│  LEARNED PATTERNS:                                               │
│  • Greeting style: "Hi [Name]," (not "Dear")                     │
│  • Common phrases: "sounds good", "LGTM", "let me know"          │
│  • Per-app tone: Gmail=formal, Slack=casual                      │
│  • Preferred length: medium (20-40 chars)                        │
│  • Rejected patterns: ["too_verbose", "overly_casual"]           │
│                                                                  │
│  VOCABULARY:                                                     │
│  • Frequently used: "analysis", "data", "insights"               │
│  • Never uses: "utilize" (prefers "use")                         │
│                                                                  │
│  PERFORMANCE METRICS:                                            │
│  • Overall acceptance rate: 73%                                  │
│  • Best time: morning (80% acceptance)                           │
│  • Per-app success: Word=85%, Gmail=70%, Slack=60%               │
│                                                                  │
│  STORED IN: MongoDB (encrypted)                                  │
│  PRIVACY: No actual content, only patterns                       │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### How STM Updates in Real-Time

| Event                         | What Updates                             | Latency           |
| ----------------------------- | ---------------------------------------- | ----------------- |
| Keystroke                     | `text_window`, `typing_patterns`         | Debounced (300ms) |
| App switch                    | `current_context`, `cross_app_context`   | Immediate         |
| Clipboard copy                | `clipboard_history` with relevance score | Immediate         |
| Suggestion shown              | `suggestion_history`                     | Immediate         |
| Suggestion accepted/dismissed | `behavioral_signals`, `patterns`         | Immediate         |
| Every 30 seconds              | `mood_estimation`, `temporal_context`    | Background        |

---

## Part 2: Mood Estimation Algorithm

### Why Mood Matters

A rushed user wants quick completions. A focused user prefers less interruption. A frustrated user needs more help.

### The 6 Mood States

| Mood            | User State              | Suggestion Strategy                                 |
| --------------- | ----------------------- | --------------------------------------------------- |
| **focused**     | In flow, deep work      | Minimal interruptions, high-quality only, longer OK |
| **rushed**      | Hurrying, time pressure | Quick, short suggestions, prioritize speed          |
| **exploratory** | Brainstorming, open     | Offer alternatives, creative suggestions            |
| **frustrated**  | Struggling, errors      | Extra helpful, offer corrections, simplify          |
| **casual**      | Relaxed, informal       | Allow informal tone, emoji OK                       |
| **neutral**     | Default state           | Standard balanced approach                          |

### Multi-Signal Detection

```python
# Weighted mood scoring algorithm

SIGNAL_WEIGHTS = {
    "typing_behavior": 0.35,    # Speed vs baseline, rhythm
    "editing_patterns": 0.25,   # Backspace frequency
    "app_behavior": 0.20,       # App switches, focus time
    "temporal": 0.10,           # Time of day
    "session_context": 0.10     # Session duration
}

def calculate_mood(stm):
    # Collect signals
    signals = {
        "typing_speed": classify(stm.typing_speed / stm.baseline_speed),
        "backspace_freq": classify(stm.backspace_frequency),
        "app_switches": classify(stm.app_switches_last_10min),
        "time_of_day": classify(current_time),
        "session_duration": classify(stm.session_duration_min)
    }

    # Calculate weighted scores for each mood
    mood_scores = {
        "focused": 0,
        "rushed": 0,
        "exploratory": 0,
        "frustrated": 0,
        "casual": 0,
        "neutral": 0
    }

    # Apply signal mappings (e.g., much_faster typing → rushed:0.7, focused:0.3)
    for signal, value in signals.items():
        for mood, score in MOOD_SIGNALS[signal][value].items():
            mood_scores[mood] += score * SIGNAL_WEIGHTS[signal]

    # Add momentum (mood shouldn't change rapidly)
    mood_scores[previous_mood] += 0.15

    # Normalize and select
    return max(mood_scores, key=mood_scores.get)
```

### Mood → Suggestion Adaptation

| User Mood      | Debounce Delay | Threshold    | Style             |
| -------------- | -------------- | ------------ | ----------------- |
| **focused**    | 1200ms (calm)  | Higher (0.7) | Less interruption |
| **rushed**     | 500ms (quick)  | Lower (0.5)  | Fast help         |
| **frustrated** | 800ms          | Lower (0.5)  | More helpful      |
| **neutral**    | 900ms          | Normal (0.6) | Balanced          |

---

## Part 3: Clipboard Intelligence

### What We Capture

| Type      | Action                   | Storage                     |
| --------- | ------------------------ | --------------------------- |
| **Text**  | ✅ Analyze for relevance | Preview only (200 chars)    |
| **Image** | ⚠️ Metadata only         | Filename, source, timestamp |
| **File**  | ⚠️ Metadata only         | Filename, extension, source |

### Relevance Scoring (5 Factors)

```python
def score_clipboard_relevance(clip, context) -> float:
    score = 0.5  # Start neutral

    # Factor 1: TIME DECAY
    age_min = (now() - clip.copied_at).minutes
    if age_min < 2:   score += 0.3   # Very fresh
    elif age_min < 10: score += 0.15  # Recent
    elif age_min > 60: score -= 0.25  # Stale

    # Factor 2: SOURCE APP RELEVANCE
    if clip.source_app in context.workflow_apps:
        score += 0.2  # Same workflow

    # Factor 3: CONTENT SIMILARITY
    similarity = text_similarity(clip.content, context.text)
    score += similarity * 0.25

    # Factor 4: TYPE PENALTY
    if clip.type == "image": score -= 0.15

    # Factor 5: ALREADY USED?
    if clip.was_pasted: score -= 0.2

    return clamp(score, 0, 1)

# Decision:
# >= 0.65 → include_full
# >= 0.45 → include_brief
# < 0.45  → exclude
```

**Privacy:** Clipboard content analyzed in RAM, never stored to disk.

---

## Part 4: Automatic LLM Calling Strategy

### Core Principle

**Suggestions are completely automatic.** User never triggers manually. System intelligently decides WHEN to call LLM.

### Smart Trigger Decision Tree

```
User Types Character
       ↓
Reset Debounce Timer
       ↓
Is this a NATURAL PAUSE POINT?
  • Sentence end (. ! ?) → 400ms delay
  • Paragraph break (Enter×2) → 600ms delay
  • Regular typing → Adaptive delay (600-1200ms)
       ↓
Calculate ADAPTIVE DELAY based on:
  • Typing speed (fast typer → shorter wait)
  • Mood (rushed → 500ms, focused → 1200ms)
  • Recent dismissals (back off if rejected)
  • Session acceptance rate (adjust confidence)
       ↓
Wait for delay...
       ↓
PRE-FLIGHT CHECKS:
  ✓ Enough context? (>10 chars)
  ✓ In text field?
  ✓ App allowed?
  ✓ No pending request?
  ✓ User still paused?
       ↓
All pass? → CALL LLM
       ↓
POST-PROCESSING:
  • Validate suggestion quality
  • Check confidence >= threshold
  • Verify user still paused
  • Not duplicate of recent?
       ↓
Quality pass? → SHOW SUGGESTION
```

### No Caching Layer

**Decision:** Removed caching to simplify architecture.

**Rationale:**

- Modern local LLMs (Phi-3, TinyLlama) are fast enough (50-100ms)
- Cloud LLM calls use streaming (tokens arrive immediately)
- Caching adds complexity without significant benefit
- User patterns change frequently, cache would be stale

---

## Part 5: LLM Prompt Design

### Prompt Architecture

```
┌─────────────────────────────────────────┐
│ SYSTEM PROMPT (Cached, rarely changes)  │
│ • Role definition                       │
│ • Output format (JSON)                  │
│ • General guidelines                    │
└─────────────────────────────────────────┘
              +
┌─────────────────────────────────────────┐
│ CONTEXT PROMPT (Dynamic per request)    │
│ • Current app & document                │
│ • User preferences from LTM             │
│ • Mood-based guidance                   │
│ • Cross-app context (if relevant)       │
│ • Clipboard (if high relevance)         │
└─────────────────────────────────────────┘
              +
┌─────────────────────────────────────────┐
│ INPUT (Current text)                    │
│ • Text before cursor (last 500 chars)   │
│ • Text after cursor (if any)            │
└─────────────────────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│ OUTPUT (Suggestion JSON)                │
│ {                                       │
│   "suggestion": "text",                 │
│   "type": "completion|correction",      │
│   "confidence": 0.85                    │
│ }                                       │
└─────────────────────────────────────────┘
```

### Example: Document Writing Prompt

```markdown
## Context

- App: Microsoft Word (writing Q1 Report, Conclusion section)
- Style: professional, concise (formality: 7/10)

## Mood Guidance (focused)

User is in deep work. Provide high-quality completions only.
Avoid interrupting flow with minor corrections.

## Cross-App Context

- Recently viewed: Q1 Financial Data in Google Sheets (2 min ago)
- Clipboard: "Revenue: $2.4M (+15% YoY)"

## Session Patterns

- Acceptance rate: 67%
- Preferred length: medium
- Avoid: overly casual, too verbose

## Current Text

Before cursor: "...financial performance exceeded expectations by 15%.
Revenue grew across all segments. In conclusion, our analysis shows that"

Provide natural completion matching user's professional style.
```

---

## Part 6: Feedback Loop & Learning

### What We Learn From Every Suggestion

```python
class FeedbackCapture:

    def on_suggestion_accepted(self, time_to_decision_ms):
        # Classify acceptance speed
        if time_to_decision_ms < 500:
            speed = "instant"    # Perfect suggestion!
        elif time_to_decision_ms < 1500:
            speed = "quick"      # Good suggestion
        elif time_to_decision_ms < 3000:
            speed = "considered" # Acceptable
        else:
            speed = "hesitant"   # Barely accepted

        # Learn from this
        learn_positive_pattern(suggestion, context, speed)
        update_ltm_preferences(suggestion_length, style)

    def on_suggestion_dismissed(self, method):
        # Classify dismissal strength
        if method == "escape":
            strength = "strong"   # Actively annoyed
        elif method == "click":
            strength = "clear"    # Deliberate rejection
        elif method == "typed_over":
            strength = "passive"  # Ignored it

        # Learn what to avoid
        if strength in ["strong", "clear"]:
            pattern = extract_rejection_pattern(suggestion)
            ltm.rejection_patterns[pattern].count += 1
```

### Learning Signals

| Signal                    | Meaning          | Action                 |
| ------------------------- | ---------------- | ---------------------- |
| Instant accept (<500ms)   | Perfect!         | Increase similar       |
| Quick accept (500-1500ms) | Good             | This is target         |
| Hesitant accept (>3s)     | Barely OK        | Investigate why        |
| Escape dismiss            | Actively annoyed | Strongly avoid pattern |
| Click dismiss             | Not wanted       | Learn mismatch         |
| Typed over                | Ignored          | Wrong timing?          |

---

## Part 7: Technical Implementation (Grammarly-Style)

### Windows UI Automation Strategy

```
┌──────────────────────────────────────────────────────────────┐
│              TEXT CAPTURE (UI Automation)                    │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Uses: Windows UI Automation (pywinauto backend="uia")      │
│                                                              │
│  1. Focus Tracking:                                          │
│     Desktop(backend="uia").windows()                         │
│       → Find element with has_keyboard_focus()               │
│                                                              │
│  2. Text Extraction:                                         │
│     text_pattern = element.get_pattern("Text")              │
│     full_text = text_pattern.DocumentRange.GetText(-1)      │
│                                                              │
│  3. Caret Position:                                          │
│     caret_range = text_pattern.GetCaretRange()              │
│     position = caret_range.GetBoundingRectangles()          │
│                                                              │
│  4. Selection (if any):                                      │
│     selections = text_pattern.GetSelection()                │
│                                                              │
│  Supported: WinForms, WPF, Qt5, browsers with              │
│             --force-renderer-accessibility flag             │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### Overlay Window (Suggestion Display)

```python
# Transparent, always-on-top window
import win32gui, win32con

ex_style = (
    win32con.WS_EX_LAYERED |      # Transparency support
    win32con.WS_EX_TRANSPARENT |  # Click-through
    win32con.WS_EX_TOPMOST |      # Always on top
    win32con.WS_EX_TOOLWINDOW     # No taskbar
)

hwnd = win32gui.CreateWindowEx(
    ex_style,
    "AI Keyboard Overlay",
    None,
    win32con.WS_POPUP,
    x, y, width, height,  # Position at caret
    None, None, None, None
)

# Set transparency
win32gui.SetLayeredWindowAttributes(
    hwnd,
    win32api.RGB(255, 0, 255),  # Magenta = transparent
    0,
    win32con.LWA_COLORKEY
)
```

### Text Insertion (Clipboard + Paste)

```python
def insert_suggestion(text):
    """Most reliable method across all apps"""

    # 1. Save original clipboard
    original = pyperclip.paste()

    try:
        # 2. Copy suggestion to clipboard
        pyperclip.copy(text)

        # 3. Simulate Ctrl+V
        pyautogui.hotkey('ctrl', 'v')

        # 4. Wait for paste to complete
        time.sleep(0.05)

    finally:
        # 5. Restore original clipboard
        pyperclip.copy(original)
```

---

## Part 8: The Complete Flow

### From Keystroke to Suggestion (End-to-End)

```
1. USER TYPES CHARACTER
         ↓
2. KEYBOARD HOOK CAPTURES
   - OS-level hook (desktop app) or Input event (browser)
         ↓
3. UPDATE STM
   - text_window.text_before_cursor updated
   - typing_patterns analyzed
   - mood_estimation recalculated (every 30s)
         ↓
4. DEBOUNCE LOGIC
   - Reset timer
   - Calculate adaptive delay based on mood, typing speed
   - Wait for pause...
         ↓
5. TRIGGER DECISION
   - Pre-flight checks pass?
   - Context stable? User still paused?
         ↓
6. BUILD PROMPT
   - Extract relevant STM data
   - Add LTM preferences
   - Include clipboard if relevant (score >= 0.6)
   - Apply mood-specific guidance
   - Keep within token budget (1500 tokens)
         ↓
7. CALL LLM
   - Local model (Phi-3) for simple completions
   - Cloud (GPT-4) for complex tasks
   - Streaming for perceived speed
         ↓
8. POST-PROCESS RESPONSE
   - Parse JSON
   - Validate suggestion quality
   - Check confidence >= threshold (mood-adjusted)
   - Verify not duplicate
         ↓
9. DISPLAY SUGGESTION
   - Position overlay at caret coordinates
   - Show suggestion with subtle animation
   - Listen for Tab (accept) or Esc (dismiss)
         ↓
10. CAPTURE FEEDBACK
    - Time to decision
    - Accept/dismiss method
    - Learn pattern → Update LTM
```

---

## Part 9: MCP Server Architecture

### Tools Exposed via MCP

```python
# MCP Server exposes these tools to any MCP client

@mcp.tool()
def detect_context() -> dict:
    """Get current app context and user state"""
    return {
        "app": stm.current_context.app,
        "mood": stm.mood_estimation.current_mood,
        "text_before": stm.text_window.text_before_cursor.immediate
    }

@mcp.tool()
def complete_intent(text: str, context: dict) -> dict:
    """Generate intelligent completion"""
    prompt = build_prompt(text, context, stm, ltm)
    suggestion = llm_router.complete(prompt)
    return {"suggestion": suggestion, "confidence": 0.85}

@mcp.tool()
def transcribe_voice(audio_bytes: bytes) -> dict:
    """Convert speech to formatted text with intent"""
    raw_text = whisper_model.transcribe(audio_bytes)
    enhanced = enhance_transcription(raw_text, context=stm)
    return {"text": enhanced}

@mcp.tool()
def enhance_text(text: str, instruction: str) -> dict:
    """Rewrite/improve text per instruction"""
    result = cloud_llm.rewrite(text, instruction, style=ltm.preferences)
    return {"enhanced": result}
```

### Why MCP?

1. **Hackathon requirement:** Integrates with Frai ecosystem
2. **Standardization:** Works with Claude Desktop, other MCP clients
3. **Discovery:** Clients can query "what tools do you have?"
4. **Context sharing:** All tools see same STM/LTM context

---

## Part 10: Privacy & Security

### Data Classification

| Category                | Examples                     | How We Handle                                  |
| ----------------------- | ---------------------------- | ---------------------------------------------- |
| 🔴 **Never Captured**   | Passwords, credit cards, OTP | Auto-excluded (app blocklist)                  |
| 🟠 **Used, Not Stored** | Emails, chats, documents     | Read temporarily (STM), deleted on session end |
| 🟡 **Stored Locally**   | Preferences, patterns        | Encrypted SQLite/MongoDB on device             |
| 🟢 **Cloud Eligible**   | Text for complex rewrite     | Only with explicit user consent                |

### What We Store vs What We Don't

```
STORED (LTM - encrypted):           NOT STORED:
─────────────────────────           ────────────────────
✓ "User prefers 'Hi [Name]'"        ✗ "Hi John, about the..."
✓ "Formal tone in Gmail"            ✗ Actual email content
✓ "Uses LGTM in code reviews"       ✗ Recipient addresses
✓ "Typing speed baseline: 72 WPM"  ✗ Document text
✓ "Rejection pattern: too_verbose"  ✗ Meeting details
```

### Auto-Blocked Applications

```
AI Keyboard automatically disabled in:
├── Banking websites (chase.com, paypal.com, etc.)
├── Password fields (type="password")
├── Login pages (*/login*, */signin/*)
├── Password managers (1Password, LastPass)
└── User-defined blocklist
```

---

## Part 11: Features & Capabilities

### Core Features

| Feature               | How It Works                  | Speed              |
| --------------------- | ----------------------------- | ------------------ |
| **Smart Completions** | STM + Mood + LTM → LLM prompt | 50-200ms           |
| **Auto-Correct**      | Local model, typo patterns    | Instant            |
| **Voice Input**       | Whisper + intent enhancement  | 500ms              |
| **Rewrite/Enhance**   | Cloud LLM with style matching | 1-2s (streaming)   |
| **Multi-Language**    | LLM-based, context-aware      | Same as completion |

### Voice Integration (Whisper)

```
SPOKEN: "email john about pushing the deadline to friday"
    ↓
TRANSCRIPTION (Whisper-Tiny local model):
    "email john about pushing the deadline to friday"
    ↓
INTENT ENHANCEMENT (LLM):
    Detect: Email composition request
    Context: User in Gmail, professional style
    Output:

    "Hi John,

    I wanted to discuss adjusting our timeline. Would it be
    possible to extend the deadline to Friday?

    Best regards,
    [Your Name]"
```

---

## Part 12: Keyboard Shortcuts

```
SUGGESTIONS:
  Tab          → Accept suggestion
  Esc          → Dismiss suggestion
  ↓ / ↑        → Navigate multiple suggestions
  Ctrl+→       → Accept word-by-word

VOICE:
  Ctrl+Shift+V → Hold to speak, release to transcribe

ACTIONS:
  Ctrl+Shift+R → Rewrite selected text
  Ctrl+Shift+E → Enhance selected text
  Ctrl+Shift+T → Cycle tone (formal/casual/friendly)

CONTROL:
  Ctrl+Shift+P → Pause/Resume AI
  Ctrl+Shift+O → Open settings
```

---

## Part 13: Tech Stack

| Component         | Technology                      | Why                                |
| ----------------- | ------------------------------- | ---------------------------------- |
| **Core Engine**   | Python + FastAPI                | Fast async, easy integration       |
| **MCP Server**    | MCP SDK (Python)                | Standard protocol                  |
| **STM**           | In-memory Python dict           | Fast access, temporary             |
| **LTM**           | MongoDB                         | Flexible schema, good for patterns |
| **Local LLM**     | Ollama (Phi-3, TinyLlama)       | Fast, private, free                |
| **Cloud LLM**     | OpenAI GPT-4 / Anthropic Claude | Best quality                       |
| **Voice**         | faster-whisper (local)          | Fast, accurate                     |
| **Browser Ext**   | JavaScript, Chrome APIs         | Web access                         |
| **Desktop App**   | Python + pywin32 + pywinauto    | Native app access                  |
| **UI Automation** | Windows UI Automation (UIA)     | Industry standard                  |
| **Communication** | WebSocket                       | Real-time, low latency             |

---

## Part 14: Hackathon MVP Scope

### What We Build (Priority)

| Component                 | Priority | Estimate |
| ------------------------- | -------- | -------- |
| **Core MCP Server**       | P0       | 2 days   |
| **STM/LTM Architecture**  | P0       | 1 day    |
| **Mood Estimation**       | P0       | 1 day    |
| **LLM Prompt System**     | P0       | 1 day    |
| **Chrome Extension**      | P0       | 2 days   |
| **Desktop App (Windows)** | P0       | 2 days   |
| **Whisper Integration**   | P0       | 1 day    |
| **Feedback Loop**         | P1       | 1 day    |
| **Privacy Controls**      | P1       | 1 day    |

**Total:** ~10 days of focused work

### Post-Hackathon Roadmap

1. Advanced Personalization (pattern extraction from longer usage)
2. Multi-language support (detect language, suggest accordingly)
3. Plugin marketplace (custom templates, integrations)
4. macOS support (Accessibility API)
5. Mobile keyboard (iOS/Android)

---

## Part 15: Success Metrics

### How We Measure "Intelligence"

| Metric                 | Target      | How Measured                       |
| ---------------------- | ----------- | ---------------------------------- |
| **Acceptance Rate**    | >70%        | Suggestions accepted / shown       |
| **Time Saved**         | >30 min/day | Chars suggested × avg typing speed |
| **Context Detection**  | >95%        | Correct app/mode detection         |
| **Mood Accuracy**      | >75%        | User feedback on mood alignment    |
| **Privacy Compliance** | 100%        | Zero sensitive data leaks          |

---

## Summary: What Makes AI Keyboard Special

| Aspect           | Our Approach                                                    |
| ---------------- | --------------------------------------------------------------- |
| **Architecture** | STM (session) + LTM (learned patterns) + MCP (interoperability) |
| **Intelligence** | Mood-aware, context-sensitive, clipboard-intelligent            |
| **Speed**        | Local model → Cloud (no caching, streaming for perceived speed) |
| **Triggering**   | Completely automatic, adaptive debouncing                       |
| **Learning**     | Three-layer: Explicit + Observed + Feedback                     |
| **Privacy**      | USE content, don't STORE content                                |
| **Integration**  | Grammarly-style UI Automation for native apps                   |
| **Ecosystem**    | MCP-based, plugs into Frai, Claude Desktop                      |

---

## Quality Formula

```
SUGGESTION_QUALITY =
    Context(STM)
  × Mood(alignment)
  × Style(LTM_match)
  × Timing(adaptive)
  × Personalization(learning)
  × Feedback(continuous_improvement)
  × Clipboard(relevance)
```

> **"Every keystroke, intelligently enhanced."**

---

_Last Updated: 2026-01-29 15:26 IST_
_Version: 2.0 — Production Architecture_
