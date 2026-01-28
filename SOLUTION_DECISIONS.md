# AI Keyboard — Solution Decisions & Discussion Log

> This document tracks all decisions made during solution planning.
> Last Updated: 2026-01-22

---

## 🎯 Problem Statement (Hackathon Theme)

**Theme:** From input device to intelligent work interface.

> "The keyboard is the most used interface in modern work yet it is fundamentally passive. It captures keystrokes but understands nothing about intent, context, or outcomes. This track explores what happens when **AI is embedded directly into the keyboard itself**."

**Key exploration areas:**

- Context-aware input that adapts across apps and workflows
- Inline suggestions, rewrites, and intent completion
- Voice, text, and hybrid input (Whisper-style flows)
- Personalization based on role, goals, and feedback
- Low-latency, always-on AI without breaking flow

**Prompt to Builders:** "If AI lived where work actually happens — at the point of input — how would human-computer interaction change?"

---

## ✅ Confirmed Decisions

### Decision 1: LLM Processing Strategy

**Date:** 2026-01-22

| Priority | Approach  | When Used                                |
| -------- | --------- | ---------------------------------------- |
| 1st      | Cloud LLM | Default when online (best quality)       |
| 2nd      | Local LLM | Fallback when offline (Ollama/TinyLlama) |

**Rationale:** Cloud-first gives best suggestion quality. Local is fallback for offline scenarios.

---

### Decision 2: Platform Focus — Mobile + Desktop

**Date:** 2026-01-22

| Platform    | Implementation                            | Why                                               |
| ----------- | ----------------------------------------- | ------------------------------------------------- |
| **Mobile**  | Custom AI Keyboard App (Android)          | True "AI in keyboard" — works in ALL apps         |
| **Desktop** | System-tray App with Global Keyboard Hook | Covers Word, VS Code, Slack, browsers, everything |

**NOT building separately:** Browser extension (desktop app covers browser too via OS-level keyboard hook)

**Why this approach:**

1. ✅ Matches hackathon theme: "AI embedded directly into the keyboard itself"
2. ✅ Differentiates from Grammarly (they need separate browser extension + desktop app)
3. ✅ One install = works everywhere (no per-browser/per-app setup)
4. ✅ Mobile keyboard = truly "AI in the keyboard" experience

---

## 🎨 Features & UX Decisions

### Feature 1: Context-Aware AI Visibility (Like Grammarly)

**Date:** 2026-01-22

**Observation from Grammarly:**

- Grammarly icon **appears** only when cursor is in a text input field
- Icon **vanishes** when not in a typing context
- AI is only active when typing is needed — not always visible

**Our Implementation:**
| Platform | Behavior |
|----------|----------|
| **Desktop** | Floating icon appears near cursor only when in editable text field |
| **Mobile** | AI features integrated directly into keyboard UI (always available when keyboard is open) |

**Why this matters:**

- Non-intrusive — doesn't clutter screen when not needed
- Context-aware — AI knows when you're typing
- Focus on typing = AI appears; not typing = AI disappears

---

### Feature 2: Core AI Capabilities

**Date:** 2026-01-22

| Feature               | Description                                          | Priority |
| --------------------- | ---------------------------------------------------- | -------- |
| **Smart Suggestions** | Inline text completions based on context             | P0 (MVP) |
| **Context Detection** | Knows which app you're in (Gmail, Slack, Code)       | P0 (MVP) |
| **Voice Input**       | Whisper-based speech-to-text with intent enhancement | P0 (MVP) |
| **Text Enhancement**  | Rewrite selected text (formal/casual/concise)        | P1       |
| **Personalization**   | Learn user's writing style over time                 | P1       |
| **Emoji Suggestions** | Context-aware emoji for casual apps                  | P2       |

---

### Feature 3: Keyboard Shortcuts (Desktop)

**Date:** 2026-01-22

| Shortcut       | Action                      |
| -------------- | --------------------------- |
| `Tab`          | Accept suggestion           |
| `Esc`          | Dismiss suggestion          |
| `Ctrl+Shift+V` | Voice input (hold to speak) |
| `Ctrl+Shift+R` | Rewrite selected text       |
| `Ctrl+Shift+P` | Pause/Resume AI             |

---

### Feature 4: Mobile Keyboard UI

**Date:** 2026-01-22

| Element               | Description                                 |
| --------------------- | ------------------------------------------- |
| **Suggestion Bar**    | Above keyboard, shows inline completions    |
| **Voice Button**      | Mic icon for voice input                    |
| **AI Action Row**     | Quick actions (rewrite, tone change, emoji) |
| **Context Indicator** | Shows detected app context                  |

---

### Feature 5: Privacy Controls

**Date:** 2026-01-22

| Control           | Description                                 |
| ----------------- | ------------------------------------------- |
| **Auto-Block**    | Banking apps, password fields auto-disabled |
| **Pause Mode**    | Instantly disable AI (Ctrl+Shift+P)         |
| **App Blocklist** | User can disable AI for specific apps       |
| **Data Wipe**     | One-click delete all learned patterns       |

**Privacy Principle:** USE content (temporarily for suggestions), don't STORE content (actual text never saved)

---

## 🔄 Open Discussions (Resolved)

### Discussion: Browser + Desktop vs Embedded Keyboard Approach

**Question:** Should we follow Grammarly's browser extension + desktop app approach, or do something different?

**Problem Statement Emphasis:**

> "AI is embedded **directly into the keyboard itself**"

This suggests the hackathon wants something more integrated than separate apps.

#### Option A: Grammarly-Style (Browser Extension + Desktop App)

**Pros:**

- Proven approach (Grammarly, Copilot use this)
- Easier to implement
- Works across all apps

**Cons:**

- Not "embedded in keyboard" — it's a layer on top
- Requires installing separate components
- Doesn't match hackathon's vision of "keyboard as intelligent interface"

#### Option B: Actual Keyboard Integration

**Possible approaches:**

1. **Custom Mobile Keyboard (Android/iOS)**
   - Replace system keyboard with our AI keyboard
   - True "AI in the keyboard" experience
   - Works in ALL apps automatically
   - Example: Gboard, SwiftKey, but with LLM integration

2. **Hardware Keyboard Companion**
   - Physical keyboard with embedded AI
   - Dedicated keys for AI functions (voice, enhance, accept)
   - Not practical for hackathon

3. **IME (Input Method Editor)**
   - System-level text input replacement on desktop
   - Very complex to implement

#### Option C: Hybrid Approach

- **Desktop:** One system-tray app that hooks into keyboard globally
- **Mobile:** Custom keyboard app (like Gboard replacement)
- **Browser:** Extension for enhanced web features

---

## 💡 Analysis: What Does "Embedded in Keyboard" Really Mean?

The problem statement says:

> "Instead of switching between apps and prompts, the keyboard becomes an intelligent layer"

This suggests:

1. **No app switching** — AI should be available without opening a separate app
2. **At the point of input** — Where you type, AI helps immediately
3. **Invisible integration** — Should feel like the keyboard itself is smart

### Interpretation Options:

| Interpretation | Implementation                                            |
| -------------- | --------------------------------------------------------- |
| Literal        | Build an actual custom keyboard (mobile app)              |
| Conceptual     | Build invisible layer that intercepts all keyboard input  |
| Practical      | Grammarly-style but positioned as "keyboard intelligence" |

---

## 📝 Questions to Decide

1. ~~**Primary Platform:** Mobile keyboard app vs Desktop system vs Both?~~ ✅ DECIDED: Both
2. **MVP Scope:** What's achievable in hackathon timeline?
3. ~~**Differentiator:** How do we stand out from Grammarly?~~ ✅ DECIDED: See features below

---

## 🆕 Advanced Features (2026-01-22 Update)

### Feature 6: Tooltip-Style Suggestions (Not Inline)

**Date:** 2026-01-22

**Observation from Grammarly:**

- Grammarly uses **tooltip/popup** style suggestions (not inline ghost text)
- Shows "Replace the phrase" with strikethrough + suggestion
- User can Dismiss or "See more in Grammarly"

**Our Implementation:**

- Follow same **tooltip approach** for suggestions
- Works for: text writing, code writing, reports, casual chats
- **Difference from Grammarly:** We provide **corrections + auto-suggestions** (not just corrections)
- User clicks tooltip to accept/dismiss

---

### Feature 7: Content Window Analysis

**Date:** 2026-01-22

**How Grammarly Does It:**

- Analyzes ~10 pages around cursor position
- If user needs other pages analyzed, must move cursor there

**Our Implementation:**

| Context            | Analysis Window                                           |
| ------------------ | --------------------------------------------------------- |
| **Word Documents** | Configurable window size (default: N pages around cursor) |
| **Code Files**     | Single file OR multiple dependent files                   |
| **Chat Apps**      | Current conversation window                               |

**For Coding (Special):**

- Support **multiple programming languages** (LLM-powered)
- Analyze **single cursor window** OR **entire code file** OR **multiple related files**
- Reason: Code often depends on imports, classes, functions from other files
- **Configurable:** User can set analysis scope

---

### Feature 8: Custom Prompt Input

**Date:** 2026-01-22

**Feature:**

- Small AI icon appears near cursor (like Grammarly)
- User can **click icon** to add **custom prompt**
- Example: "Make this more formal" or "Explain this code"

**Behavior:**

- Without custom prompt → AI suggests based on detected intent
- With custom prompt → AI follows user's specific instructions

---

### Feature 9: Cross-App Context Tracking 🔥 (Key Differentiator)

**Date:** 2026-01-22

**Scenario:**
User has browser + Word open simultaneously:

- Reading research articles in browser
- Writing report in Word

**Our AI Detects:**

- User is switching between tabs/apps
- Content from browser likely relates to Word document
- AI can suggest content based on what user was just reading

**Implementation:**

- Track active window switches
- Maintain context across apps
- Provide suggestions that understand the **full workflow** (not just current app)
- **Minimal logic** — lightweight, non-intrusive

**Why This Matters:**

- No other tool does this
- Understands user's actual workflow
- Provides much smarter suggestions

---

### Feature 10: Two-Tier Memory Architecture

**Date:** 2026-01-22

```
┌─────────────────────────────────────────────────────────────────┐
│                     SHORT-TERM MEMORY                           │
│  (Local Only, Encrypted, Deleted on Software Close)             │
├─────────────────────────────────────────────────────────────────┤
│  STORES:                                                        │
│  • Detailed current session information                         │
│  • Entire application window analysis (not just text)           │
│  • User's current mood estimation                               │
│  • Recent tab/app switches                                      │
│  • Temporary context for better suggestions                     │
│                                                                 │
│  PRIVACY:                                                       │
│  • ❌ NOT stored in database                                    │
│  • ❌ NOT uploaded anywhere                                     │
│  • ✅ End-to-end encrypted locally                              │
│  • ✅ Deleted when software terminates                          │
└─────────────────────────────────────────────────────────────────┘
                              ↓
                 (Important insights extracted)
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                      LONG-TERM MEMORY                           │
│              (MongoDB Database, Persistent)                     │
├─────────────────────────────────────────────────────────────────┤
│  STORES:                                                        │
│  • User preferences & profile                                   │
│  • Writing style patterns (not actual text)                     │
│  • App-specific behavior patterns                               │
│  • Learned insights for better suggestions                      │
│                                                                 │
│  PRIVACY:                                                       │
│  • ✅ Only anonymized patterns stored                           │
│  • ❌ No actual content stored                                  │
│  • ✅ User can delete anytime                                   │
└─────────────────────────────────────────────────────────────────┘
```

**Database:** MongoDB for all user data (preferences, patterns, profile)

---

### Feature 11: Mood Estimation Engine

**Date:** 2026-01-22

**On Fresh Start / New User (Authorship):**

- Default mood: "Normal"
- Start learning from user behavior

**Mood Signals We Track:**
| Signal | What It Tells Us |
|--------|------------------|
| App usage patterns | Focused work vs browsing |
| Typing speed/style | Rushed, calm, frustrated |
| Time of day | Morning freshness vs evening fatigue |
| App switching frequency | Focused vs distracted |
| What user types | Formal, casual, stressed language |

**How It's Used:**

- Provide mood-appropriate suggestions
- "User seems rushed" → Shorter, quicker suggestions
- "User is in deep focus" → Don't interrupt unnecessarily
- "User is casual" → Allow informal language, emojis

**Storage:**

- Current mood → Short-term memory (deleted on close)
- Mood patterns over time → Long-term memory (insights only)

---

### Feature 12: Full Application Window Analysis

**Date:** 2026-01-22

**Beyond Just Text:**

- Analyze the **entire visible application window**
- Not just the text input field
- Consider all visible context for suggestions

**Examples:**
| App | What We Analyze Beyond Text |
|-----|---------------------------|
| Gmail | Recipient, subject line, thread context |
| VS Code | File name, imports, project structure |
| Word | Document title, headings, images present |
| Slack | Channel name, conversation participants |

---

## � Privacy Model (Grammarly-Inspired)

Based on Grammarly's "Authorship" feature and privacy terms:

### Our Privacy Principles:

1. **"We're transparent about data"**
   - Records what you type, delete, paste
   - Links to websites you copy from
   - Prompts you send to AI tools
   - User is informed upfront

2. **"We'll keep your data safe"**
   - Data stored on device in **encrypted format**
   - Sent over **encrypted connection** if shared
   - User controls when to share

3. **"You're always in control"**
   - User chooses when tracking is on
   - Can stop sharing anytime
   - Can delete all data with one click

---

## �📅 Discussion Log

### 2026-01-22 — Initial Discussion

- Compared our solution with Grammarly's architecture
- Grammarly uses: Browser Extension + Desktop App (separate installs)
- Both send text to cloud servers for processing
- Our differentiator: Local-first option for privacy/speed
- Discussed whether to follow same approach or diverge

### 2026-01-22 — Platform Decision

- Decided: Focus on **Mobile keyboard app + Desktop app**
- NOT building separate browser extension
- Desktop app uses global keyboard hook (covers all apps including browsers)

### 2026-01-22 — Feature Deep Dive

- Analyzed Grammarly screenshots in detail
- Decided: Tooltip suggestions (not inline ghost text)
- Decided: Corrections + auto-suggestions (goes beyond Grammarly)
- Decided: Cross-app context tracking (key differentiator)
- Decided: Two-tier memory (short-term local, long-term DB)
- Decided: Mood estimation for personalized suggestions
- Decided: Full application window analysis (not just text)
- Database: MongoDB for everything

---

## 🎯 Key Differentiators vs Grammarly

| Feature         | Grammarly        | AI Keyboard                     |
| --------------- | ---------------- | ------------------------------- |
| Suggestions     | Corrections only | Corrections + Auto-suggestions  |
| Context         | Current app only | Cross-app tracking              |
| Code Support    | Limited          | Full multi-file, multi-language |
| Mood Awareness  | None             | Active mood estimation          |
| Window Analysis | Text field only  | Full application window         |
| Custom Prompts  | No               | Yes, click icon to add prompt   |
| Memory          | Unclear          | Explicit short-term + long-term |
| Offline         | No               | Yes (local LLM fallback)        |

---

## 🖼️ Grammarly's Overlay System — Technical Analysis

**Date:** 2026-01-22

### Why Grammarly Uses an Overlay Layer

**The Core Problem:**
Grammarly (and we) **cannot directly modify native application UIs**. Word, Notepad, VS Code are independent apps — no external app can inject red underlines into their text rendering.

**Grammarly's Solution: Transparent Overlay Window**

```
┌─────────────────────────────────────────────────────────────────┐
│  GRAMMARLY'S TRANSPARENT OVERLAY (Always on top, click-through) │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │ Red underlines ~~~~~~  Tooltip popups  [G icon]             ││
│  └─────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
                              ↑
                    Positioned exactly above
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│  ACTUAL APPLICATION (Word, Browser, VS Code)                    │
│                                                                 │
│  The quick brown fox jumps over the lazy dog                    │
│                        ↑                                        │
│              Grammarly reads text via UI Automation             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**How it works technically:**

1. Creates a **transparent window** that floats above the target application
2. This overlay is **"click-through"** for most areas (clicks pass to app below)
3. Only the icon and tooltips capture mouse clicks
4. Underlines are drawn on this overlay, positioned to match text in app below
5. Uses **Windows Layered Windows** (WS_EX_LAYERED | WS_EX_TRANSPARENT | WS_EX_TOPMOST)

**Windows API approach:**

```python
# Pseudocode for transparent overlay
window = CreateWindowEx(
    WS_EX_LAYERED | WS_EX_TRANSPARENT | WS_EX_TOPMOST,
    "GrammarlyOverlay",
    ...
)
SetLayeredWindowAttributes(window, transparent_color, alpha, LWA_COLORKEY)
```

**Our Decision:** We will use same overlay approach for native desktop apps.

---

## 🌐 Browser Extension vs Desktop App — Why Both?

**Date:** 2026-01-22

### Observation:

Grammarly uses BOTH browser extension AND desktop app. Why not just desktop app?

### Capability Comparison:

| Capability                     | Desktop App Only             | Browser Extension             |
| ------------------------------ | ---------------------------- | ----------------------------- |
| **Read text content**          | Limited (accessibility APIs) | Full DOM access               |
| **Know exact cursor position** | Approximate                  | Exact pixel position          |
| **Inject visual elements**     | Overlay (may misalign)       | Directly in page DOM          |
| **Read form field context**    | Just text                    | Field name, type, purpose     |
| **Site-specific integration**  | No                           | Yes (knows Gmail vs LinkedIn) |
| **Handle page scroll**         | Overlay must track scroll    | Underlines scroll naturally   |
| **Performance**                | Overlay rendering overhead   | Native page integration       |

### Key Advantages of Browser Extension:

1. **Direct DOM Injection** — Can add underlines as actual HTML elements INSIDE the page

   ```html
   <!-- Browser extension can inject this directly into page -->
   <span
     class="ai-keyboard-underline"
     style="text-decoration: wavy underline red;"
     >teh</span
   >
   ```

2. **Exact Positioning** — Knows exactly where each character is rendered in the DOM

3. **Rich Context** — Can read:
   - Form field names and types
   - Page URL structure
   - Recipient email addresses (in Gmail)
   - Surrounding page content
   - Input field purpose (subject, body, etc.)

4. **No Overlay Lag** — Underlines move with page scroll naturally (they're part of the page)

5. **Site-Specific Features** — Can have special handling for Gmail, LinkedIn, Slack, etc.

### ✅ CORRECTION: Desktop Overlay DOES Work Well!

**User Observation (2026-01-22):**

> "Grammarly's overlay alignments are great and perfectly performed. If I ask any content to insert, it inserts very clearly without any errors and misalignment. All things are very smooth."

**Revised Understanding:**

- Grammarly's desktop app overlay works **excellently** even in browsers
- Text insertion is smooth and accurate
- Alignment issues are NOT a real problem when done correctly

**This means:**

- Browser extension adds **extra capabilities** (rich context, DOM access)
- But desktop overlay alone CAN work well for basic functionality
- The key is **quality implementation**, not architecture choice

### Why We Still Want Browser Extension (Optional Enhancement):

1. **Richer context** — Understanding Gmail vs LinkedIn vs Slack
2. **Form field metadata** — Knowing field purpose, not just content
3. **Better integration** — Page-native UI feels more natural
4. **But NOT required** — Desktop overlay works great on its own

### Our Decision: Use BOTH (Grammarly Approach)

```
┌────────────────────────────────────────────────────────────────┐
│                 OUR ARCHITECTURE DECISION                      │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌─────────────────┐         ┌─────────────────┐              │
│  │   DESKTOP APP   │←───────→│BROWSER EXTENSION│              │
│  │  (System Tray)  │ Native  │  (Companion)    │              │
│  │                 │Messaging│                 │              │
│  │ • Native apps   │   or    │ • DOM reading   │              │
│  │ • Overlay UI    │WebSocket│ • Inline UI     │              │
│  │ • Global hooks  │         │ • Rich context  │              │
│  │ • AI Engine     │         │ • Page-native   │              │
│  └────────┬────────┘         └────────┬────────┘              │
│           │                           │                        │
│           └───────────┬───────────────┘                        │
│                       ↓                                        │
│            ┌─────────────────────┐                             │
│            │  SHARED AI ENGINE   │                             │
│            │  (Lives in Desktop) │                             │
│            │                     │                             │
│            │  • LLM Router       │                             │
│            │  • STM/LTM Manager  │                             │
│            │  • Context Fusion   │                             │
│            └─────────────────────┘                             │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

**Browser Extension Responsibilities:**

- Capture full DOM context
- Show icon/tooltips natively in page
- Handle inline suggestions (if we want them later)
- Send context to desktop app's AI engine

**Desktop App Responsibilities:**

- Handle ALL native apps (Word, VS Code, Slack desktop, etc.)
- Run the AI engine (LLM calls, STM/LTM)
- Overlay UI for native apps
- Coordinate with browser extension

**Communication Between Them:**

- **Native Messaging API** — Chrome/Firefox supported way for extensions to talk to native apps
- OR **localhost WebSocket** — Extension connects to localhost:PORT where desktop app listens

---

## 📍 Icon Positioning Dots — Analysis

**Date:** 2026-01-22

### Observation:

Grammarly shows blue dots indicating valid positions where user can drag the icon. These positions differ between applications.

### Why They Have This:

| Reason                    | Explanation                                             |
| ------------------------- | ------------------------------------------------------- |
| **Avoid UI obstruction**  | Icon shouldn't cover buttons, menus, important elements |
| **Consistent experience** | Users can rely on predictable positions                 |
| **App-specific layouts**  | Gmail compose box ≠ Word document area                  |
| **Edge anchoring**        | Corners and edges are usually safe zones                |

### How They Determine Valid Positions:

1. **Hardcoded for popular apps** — Gmail, Word, Slack have pre-defined safe zones
2. **UI Element Detection** — Detect buttons/menus via accessibility APIs, avoid them
3. **Edge-based defaults** — Corners of text areas are generally safe
4. **User preference memory** — Remember last position per app

### Why Smooth Snapping Animation:

- Better UX — feels polished and intentional
- Prevents icon from being in unusable positions
- Uses animation easing (ease-out timing curve)

### Different Apps = Different Dot Positions:

- Word's text area has different shape than Gmail compose
- Each app has different "safe zones" based on its UI layout
- Grammarly likely maintains per-app configuration

### Our Implementation Plan:

```
ICON POSITIONING SYSTEM
├── Default positions (corners of active text area)
├── App-specific overrides (for popular apps)
├── Dynamic detection (avoid detected UI elements)
├── User preference storage (remember per app)
└── Smooth snapping animation (ease-out, ~200ms)
```

---

## 🔄 Split Screen & Multi-Window Behavior

**Date:** 2026-01-22

### Observation:

When screen is split (e.g., Antigravity + Browser), Grammarly icon:

- Shows in whichever window has active text cursor
- Vanishes from both if no text cursor is active
- Switches instantly when user changes focus

### How This Works:

```
EVENT: User clicks on Browser window (right side)
    ↓
Window focus change detected (WM_SETFOCUS or equivalent)
    ↓
Check: Does browser extension report active text input?
    ↓
YES → Show icon near that text field (via extension DOM injection)
NO → Hide icon
    ↓
EVENT: User clicks on Antigravity (left side)
    ↓
Check: Is there an active text input in this native app?
    ↓
YES → Show overlay icon near text field
NO → Hide icon from both windows
```

### Key Detection Methods:

| Platform    | How to Detect Active Text Input                    |
| ----------- | -------------------------------------------------- |
| **Windows** | `GetFocus()` + UI Automation to check element type |
| **Browser** | `document.activeElement` + check if editable       |
| **Both**    | Monitor focus events, check element type           |

### Our Implementation:

```python
# Pseudocode for focus tracking
def on_focus_change(window_handle):
    app_name = get_process_name(window_handle)

    if is_browser(app_name):
        # Ask browser extension if there's active input
        has_input = query_extension_for_active_input()
    else:
        # Use UI Automation
        focused_element = get_focused_element(window_handle)
        has_input = is_text_input(focused_element)

    if has_input:
        show_icon_near_cursor()
    else:
        hide_icon()
```

---

## 📋 Additional Features Confirmed

**Date:** 2026-01-22

### Clipboard Monitoring

- ✅ Will monitor clipboard for additional context
- Captures what user copies from other apps
- Helps understand research workflow (copy from browser → paste in Word)
- Privacy: Only analyze, don't store clipboard content

### Inline Suggestions Decision

- ❌ We will **NOT** do inline/ghost text suggestions for now
- ✅ We WILL do tooltip-style suggestions (like Grammarly)
- Reason: Inline is technically harder, tooltip works well

---

## ⚡ Smooth Performance Requirements (Critical)

**Date:** 2026-01-22

### Key Observation:

Grammarly's experience is **extremely smooth** — overlay alignment is perfect, text insertion works flawlessly, and everything feels polished. **We must achieve the same quality.**

### Performance Targets:

| Metric                    | Target              | Why                               |
| ------------------------- | ------------------- | --------------------------------- |
| **Overlay positioning**   | < 16ms (60fps)      | Must track cursor smoothly        |
| **Text insertion**        | Instant (< 50ms)    | User shouldn't notice delay       |
| **Suggestion appearance** | < 200ms after pause | Fast enough to feel responsive    |
| **Icon animation**        | 60fps smooth        | Polished feel                     |
| **Focus switch**          | < 100ms             | Icon appears/disappears instantly |

### Text Insertion Mechanism — How It Works

When user accepts a suggestion, we need to insert text into the target application. Here's how:

**Method 1: Clipboard + Paste Simulation (Most Reliable)**

```
1. Save current clipboard contents (to restore later)
2. Copy suggestion text to clipboard
3. Simulate Ctrl+V (paste) keypress
4. Restore original clipboard contents
```

**Method 2: Keyboard Simulation (For character-by-character)**

```
1. Use SendInput (Windows) or equivalent
2. Simulate each character as keypress
3. Slower but works everywhere
```

**Method 3: UI Automation SetValue (Where supported)**

```
1. Get reference to focused text element
2. Use IUIAutomationValuePattern::SetValue
3. Fast but not all apps support it
```

**Our Approach:**

- **Primary:** Clipboard + Paste (fastest, most reliable)
- **Fallback:** Keyboard simulation
- Always restore original clipboard after insertion

### Why Grammarly's Insertion Is Smooth:

1. **Pre-calculated** — Suggestion is ready before user clicks accept
2. **Instant clipboard** — Clipboard operation is < 1ms
3. **Simulated paste** — Same as user pressing Ctrl+V
4. **No visible delay** — Entire operation < 50ms

### Our Implementation Approach:

```python
# Pseudocode for smooth text insertion
import pyperclip
import pyautogui

def insert_text_smoothly(text_to_insert):
    # 1. Save original clipboard
    original_clipboard = pyperclip.paste()

    try:
        # 2. Copy suggestion to clipboard
        pyperclip.copy(text_to_insert)

        # 3. Simulate paste (Ctrl+V)
        pyautogui.hotkey('ctrl', 'v')

        # 4. Small delay to ensure paste completes
        time.sleep(0.05)  # 50ms

    finally:
        # 5. Restore original clipboard
        pyperclip.copy(original_clipboard)
```

### Overlay Alignment — How To Do It Right:

**Tracking the text cursor position:**

1. **UI Automation** — Get caret/cursor position from focused element
2. **GetCaretPos() API** — Windows API for cursor position
3. **Accessibility APIs** — Cross-platform cursor detection

**Keeping overlay aligned:**

```python
# Pseudocode for overlay positioning
def update_overlay_position():
    cursor_pos = get_caret_position()  # From UI Automation
    window_rect = get_active_window_rect()

    # Calculate where to show tooltip
    tooltip_x = cursor_pos.x + 10  # Slight offset to not cover cursor
    tooltip_y = cursor_pos.y + 20  # Below the line

    # Ensure tooltip stays within screen bounds
    tooltip_pos = clamp_to_screen(tooltip_x, tooltip_y)

    # Move overlay window (should be < 16ms for 60fps)
    move_overlay_window(tooltip_pos)
```

**Why it can be smooth:**

- Windows layered windows are hardware-accelerated
- Position updates are just window moves (very fast)
- Only update when cursor actually moves (not every frame)

---

## 📅 Discussion Log (Updated)

### 2026-01-22 — Initial Discussion

- Compared our solution with Grammarly's architecture
- Grammarly uses: Browser Extension + Desktop App (separate installs)
- Both send text to cloud servers for processing
- Our differentiator: Cloud-first, local fallback for privacy/speed
- Discussed whether to follow same approach or diverge

### 2026-01-22 — Platform Decision

- Decided: Focus on **Mobile keyboard app + Desktop app**
- Initially said: NOT building separate browser extension
- Desktop app uses global keyboard hook (covers all apps including browsers)

### 2026-01-22 — Feature Deep Dive

- Analyzed Grammarly screenshots in detail
- Decided: Tooltip suggestions (not inline ghost text)
- Decided: Corrections + auto-suggestions (goes beyond Grammarly)
- Decided: Cross-app context tracking (key differentiator)
- Decided: Two-tier memory (short-term local, long-term DB)
- Decided: Mood estimation for personalized suggestions
- Decided: Full application window analysis (not just text)
- Database: MongoDB for everything

### 2026-01-22 — Tech Stack & Implementation Details

- Confirmed: Python + JavaScript as primary languages
- Confirmed: NO screen capture — use hooks + DOM/APIs instead
- Detailed the multi-layer capture approach
- STM format: Structured JSON that LLM can directly consume
- LTM updates: Periodic pattern extraction, not raw content

### 2026-01-22 — Overlay & Extension Analysis

- Analyzed why Grammarly uses transparent overlay for native apps
- Understood why browser extension is needed (DOM access, exact positioning)
- **REVISED DECISION:** Will build companion browser extension for rich web integration
- Desktop app: Overlay for native apps + AI engine
- Browser extension: DOM capture + native page UI + sends data to desktop
- Clipboard monitoring: CONFIRMED — adds workflow context
- Icon positioning: Will use anchor points like Grammarly
- Split-screen: Icon follows active text cursor

### 2026-01-22 — Smooth Performance Requirements (CRITICAL)

- **CORRECTION:** Grammarly's overlay alignment is EXCELLENT (not problematic as initially thought)
- Text insertion works flawlessly and smoothly
- Overlay can absolutely work well when implemented correctly
- Browser extension is OPTIONAL enhancement, not required
- **KEY REQUIREMENT:** Everything must be smooth like Grammarly
- Defined performance targets:
  - Overlay positioning: < 16ms (60fps)
  - Text insertion: < 50ms (instant feel)
  - Suggestion appearance: < 200ms after pause
  - Icon animation: 60fps smooth
- Text insertion method: Clipboard + Paste simulation (primary)
- Overlay positioning: UI Automation + GetCaretPos API

---

## 🗺️ Final Architecture (Updated)

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         USER'S COMPUTER                                  │
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │                    NATIVE DESKTOP APPS                              ││
│  │                    (Word, VS Code, Notepad, etc.)                   ││
│  │                              ↑                                      ││
│  │                    UI Automation + Overlay                          ││
│  │                              ↑                                      ││
│  └──────────────────────────────┼──────────────────────────────────────┘│
│                                 │                                        │
│  ┌──────────────────────────────┼──────────────────────────────────────┐│
│  │                    DESKTOP APP (System Tray)                        ││
│  │                              │                                      ││
│  │  ┌─────────────┐  ┌─────────┴─────────┐  ┌─────────────────────┐   ││
│  │  │ Keyboard    │  │ Context           │  │ Overlay Renderer    │   ││
│  │  │ Hook        │  │ Processor         │  │ (for native apps)   │   ││
│  │  └─────────────┘  └───────────────────┘  └─────────────────────┘   ││
│  │                              │                                      ││
│  │                              ↓                                      ││
│  │  ┌───────────────────────────────────────────────────────────────┐ ││
│  │  │                    AI ENGINE                                  │ ││
│  │  │                                                               │ ││
│  │  │  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────────┐ │ ││
│  │  │  │ STM       │ │ LTM       │ │ Mood      │ │ LLM Router    │ │ ││
│  │  │  │(Encrypted)│ │(MongoDB)  │ │ Estimator │ │Cloud + Local  │ │ ││
│  │  │  └───────────┘ └───────────┘ └───────────┘ └───────────────┘ │ ││
│  │  │                                                               │ ││
│  │  └───────────────────────────────────────────────────────────────┘ ││
│  │                              ↑                                      ││
│  │                      WebSocket / Native Messaging                   ││
│  │                              ↑                                      ││
│  └──────────────────────────────┼──────────────────────────────────────┘│
│                                 │                                        │
│  ┌──────────────────────────────┼──────────────────────────────────────┐│
│  │                    BROWSER EXTENSION                                ││
│  │                              │                                      ││
│  │  ┌─────────────┐  ┌─────────┴─────────┐  ┌─────────────────────┐   ││
│  │  │ DOM Reader  │  │ Context           │  │ Inline UI           │   ││
│  │  │ (Full page) │  │ Extractor         │  │ (Page-native icons) │   ││
│  │  └─────────────┘  └───────────────────┘  └─────────────────────┘   ││
│  │                                                                     ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │                    WEB BROWSERS                                     ││
│  │                    (Chrome, Firefox, Edge)                          ││
│  │                    → Extension provides rich DOM context             ││
│  │                    → Inline UI elements                              ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                          │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │                    MOBILE (Android) — SEPARATE                      ││
│  │                    Custom IME Keyboard                               ││
│  │                    Connects to same AI Engine (via network)          ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

---

_Last Updated: 2026-01-22 19:34 IST_
