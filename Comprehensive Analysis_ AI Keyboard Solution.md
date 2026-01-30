<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Comprehensive Analysis: AI Keyboard Solution

I've thoroughly analyzed all four documents. Here's my detailed breakdown of your solution:

## 🎯 Core Solution Overview

Your AI Keyboard transforms the **passive input device** into an **intelligent work interface** by embedding AI directly at the point of input. The solution addresses the hackathon theme through:[^1_1][^1_2]

- **Context-aware intelligence** that understands app, workflow, and user intent[^1_3]
- **Automatic, invisible suggestions** without manual triggers[^1_1]
- **Multi-modal input** (text + voice via Whisper)[^1_2]
- **Cross-app workflow tracking** (unique differentiator vs Grammarly)[^1_2]


## 🧠 Three-Layer Intelligence Architecture

### Layer 1: Short-Term Memory (STM)

**Purpose:** Session-based working memory[^1_3]

Your STM captures **everything about the current session** in a comprehensive JSON structure:[^1_3]

**Key Components:**

- **Session context**: App name, window title, document type, detected purpose[^1_3]
- **Text window**: Cursor position, text before/after cursor (immediate, paragraph, page levels)[^1_3]
- **Cross-app context**: Recent apps visited, workflow inference (e.g., "report_writing_from_data"), clipboard history with relevance scores[^1_3]
- **Behavioral signals**: Typing speed vs baseline, backspace frequency, app switches, suggestion acceptance rates[^1_3]
- **Mood estimation**: Current mood with confidence score, mood history, contributing factors[^1_3]
- **Temporal context**: Time of day, day of week, session phase, productivity window[^1_3]

**Privacy Model:**

- Encrypted locally, never stored in database[^1_1][^1_2]
- Deleted immediately on session end[^1_1]
- Only used for generating suggestions, not persisted[^1_2]

**Update Strategy:**

- Keystroke updates: Debounced 300ms[^1_1][^1_3]
- App switches: Immediate[^1_3]
- Mood recalculation: Every 30 seconds background[^1_3]


### Layer 2: Long-Term Memory (LTM)

**Purpose:** Learned user patterns over time[^1_1][^1_3]

**Storage:** MongoDB database[^1_2][^1_1]

**What's Learned:**

- **Writing style patterns** (professional/casual, concise/verbose, formality level)[^1_1][^1_3]
- **Vocabulary preferences** (frequently used terms, avoided phrases like "utilize" → "use")[^1_1]
- **Per-app behavior** (Gmail=formal, Slack=casual)[^1_2][^1_1]
- **Rejection patterns** ("too_verbose", "overly_casual") tracked to avoid similar suggestions[^1_1][^1_3]
- **Performance metrics** (overall acceptance rate 73%, best time=morning 80%)[^1_1]

**Critical Privacy Principle:**
> "USE content, don't STORE content"[^1_2][^1_1]

Only anonymized **patterns** are stored, never actual text content.[^1_2][^1_1]

### Layer 3: Mood Estimation Engine

**Purpose:** Adapt suggestions based on user's current state[^1_1][^1_3]

**Six Mood States**:[^1_3][^1_1]

1. **Focused**: Deep work → minimal interruptions, higher quality threshold[^1_3]
2. **Rushed**: Time pressure → quick short suggestions, 500ms debounce[^1_1]
3. **Exploratory**: Brainstorming → creative alternatives[^1_3]
4. **Frustrated**: Struggling → extra helpful, offer corrections[^1_3]
5. **Casual**: Relaxed → allow informal tone, emoji OK[^1_3]
6. **Neutral**: Default balanced approach[^1_3]

**Multi-Signal Detection**:[^1_3]

- **Typing behavior** (35% weight): Speed vs baseline, rhythm, pauses[^1_3]
- **Editing patterns** (25% weight): Backspace frequency, corrections[^1_3]
- **App behavior** (20% weight): App switches, focus time[^1_3]
- **Temporal** (10% weight): Time of day[^1_3]
- **Session context** (10% weight): Session duration, break patterns[^1_3]

**Algorithm:**

```python
# Signals → Mood mapping with confidence scores [file:1]
typing_much_faster → rushed: 0.7, focused: 0.3
very_high_backspace → frustrated: 0.6
low_app_switches → focused: 0.6
```

**Momentum factor:** Previous mood gets +0.15 bonus to prevent rapid mood swings.[^1_3]

## 🔧 Technical Implementation

### Desktop Architecture (Windows)

**Grammarly-inspired approach**:[^1_4][^1_2]

**1. Text Capture: Windows UI Automation**[^1_4]

- Uses `pywinauto` with `backend="uia"`[^1_4]
- Leverages `ITextProvider` interface:
    - `DocumentRange` → full document text[^1_4]
    - `GetCaretRange()` → cursor position[^1_4]
    - `GetSelection()` → selected text[^1_4]
- **Focus tracking**: Global focus change event handlers detect when user enters text fields[^1_4]

**2. Overlay UI: Layered Transparent Windows**[^1_4]

- Creates transparent always-on-top window[^1_4]
- Windows API flags: `WS_EX_LAYERED | WS_EX_TRANSPARENT | WS_EX_TOPMOST` [^1_4]
- Positioned at caret location from UI Automation[^1_4]
- Click-through except for icon/tooltip regions[^1_4]

**3. Text Insertion: Clipboard Simulation**[^1_4]
Most reliable approach across all apps:[^1_4]

1. Save current clipboard
2. Copy suggestion to clipboard
3. Simulate Ctrl+V
4. Restore original clipboard

**4. Application Detection**[^1_4]

- Process name (WINWORD.EXE, chrome.exe)[^1_4]
- Window title parsing[^1_4]
- Per-app configurations[^1_4]


### Key Differentiator: Cross-App Context Tracking 🔥

**Your killer feature**:[^1_2]

**Scenario:**
User switches between browser (research) → Word (writing report)[^1_2]

**Your AI detects:**

- Content from browser relates to Word document[^1_2]
- Suggests content based on what user just read[^1_2]
- Understands **full workflow** not just current app[^1_2]

**Implementation:**

- Track active window switches[^1_2]
- Maintain context across apps in STM[^1_3]
- Workflow inference: "report_writing_from_data"[^1_3]
- Clipboard intelligence with **5-factor relevance scoring**:[^1_3]

1. Time decay (fresh = relevant)
2. Source app relevance
3. Content similarity
4. Type penalty (images less useful)
5. Already used penalty

**No other tool does this**  — Grammarly only sees current app.[^1_2]

## 🎨 LLM Prompt Architecture

### Three-Part Prompt Design:[^1_1][^1_3]

**1. System Prompt (Cached)**:[^1_3]

- Role definition
- Output format (JSON only)
- General guidelines (match style, respect formality)

**2. Context Prompt (Dynamic)**:[^1_1][^1_3]

- Current app \& document[^1_3]
- User preferences from LTM[^1_3]
- **Mood-based guidance**: "User is focused. Provide high-quality completions only. Avoid interrupting flow."[^1_1]
- Cross-app context (if relevant)[^1_3]
- Clipboard content (if relevance >= 0.65)[^1_3]
- Session patterns (acceptance rate, preferred length, avoid patterns)[^1_3]

**3. Input (Current text)**:[^1_3]

- Text before cursor (last 500 chars)[^1_1]
- Text after cursor if any[^1_1]

**Output:**

```json
{
  "suggestion": "text",
  "type": "completion|correction|rewrite",
  "confidence": 0.85,
  "alternative": "optional"
}
```


### Context Window Analysis:[^1_2]

- **Word Documents**: Configurable N pages around cursor[^1_2]
- **Code Files**: Single file OR multiple dependent files (imports, classes)[^1_2]
- **Chat Apps**: Current conversation window[^1_2]

**Your code analysis advantage:** Can analyze multiple related files, not just cursor window.[^1_2]

## ⚡ Automatic Triggering Strategy

**Key Principle:** "Completely automatic. User never triggers manually."[^1_1]

### Smart Debounce Decision Tree:[^1_1]

```
Keystroke → Reset timer
       ↓
Natural pause point?
  • Sentence end (. ! ?) → 400ms
  • Paragraph break → 600ms
  • Regular typing → Adaptive 600-1200ms
       ↓
Adaptive delay based on:
  • Typing speed (fast → shorter wait)
  • Mood (rushed → 500ms, focused → 1200ms)
  • Recent dismissals (back off if rejected)
  • Session acceptance rate
       ↓
Pre-flight checks:
  ✓ Enough context (>10 chars)
  ✓ In text field?
  ✓ App allowed?
  ✓ User still paused?
       ↓
Call LLM
       ↓
Post-processing:
  • Validate quality
  • confidence >= threshold?
  • User still paused?
  • Not duplicate?
       ↓
Show suggestion
```

**Confidence Threshold Tuning**:[^1_3]

- Base threshold: 0.6[^1_3]
- Focused mood: +0.1 (be selective)[^1_3]
- Frustrated mood: -0.1 (be helpful)[^1_3]
- High acceptance rate (>70%): -0.05[^1_3]
- Low acceptance rate (<30%): +0.1[^1_3]

**Decision: No Caching Layer**[^1_1]

- Modern local LLMs fast enough (50-100ms)[^1_1]
- Cloud LLMs use streaming[^1_1]
- User patterns change frequently[^1_1]
- Simplified architecture[^1_1]


## 📊 Feedback Loop \& Learning

### Three-Layer Learning:[^1_1]

1. **Explicit**: User sets preferences[^1_1]
2. **Observed**: System detects patterns[^1_1]
3. **Feedback**: Accept/dismiss signals[^1_1]

### Acceptance Classification:[^1_1]

- **Instant (<500ms)**: Perfect suggestion![^1_1]
- **Quick (500-1500ms)**: Good, target range[^1_1]
- **Considered (1500-3000ms)**: Acceptable[^1_1]
- **Hesitant (>3000ms)**: Barely accepted[^1_1]


### Dismissal Classification:[^1_1]

- **Escape**: Strong rejection, actively annoyed[^1_1]
- **Click dismiss**: Clear rejection[^1_1]
- **Typed over**: Passive ignore, wrong timing?[^1_1]

Strong rejections → Store rejection pattern → Avoid similar[^1_1]

## 🔒 Privacy Architecture

### Two-Tier Model:[^1_2]

**Short-Term Memory:**

- ❌ NOT stored in database[^1_2]
- ❌ NOT uploaded anywhere[^1_2]
- ✅ End-to-end encrypted locally[^1_2]
- ✅ Deleted when software terminates[^1_2]

**Long-Term Memory:**

- ✅ Only anonymized patterns stored[^1_2]
- ❌ No actual content[^1_2]
- ✅ User can delete anytime[^1_2]


### Privacy Controls:[^1_2]

- **Auto-Block**: Banking apps, password fields disabled[^1_2]
- **Pause Mode**: Ctrl+Shift+P instant disable[^1_2]
- **App Blocklist**: User configures[^1_2]
- **Data Wipe**: One-click delete all learned patterns[^1_2]


## 🚀 Key Differentiators vs Grammarly

| Feature | Grammarly | Your AI Keyboard |
| :-- | :-- | :-- |
| **Suggestions** | Corrections only | Corrections + Auto-completions [^1_2] |
| **Context** | Current app only | Cross-app workflow tracking [^1_2] |
| **Code Support** | Limited | Multi-file, multi-language [^1_2] |
| **Mood Awareness** | None | Active mood estimation [^1_2] |
| **Window Analysis** | Text field only | Full application window [^1_2] |
| **Custom Prompts** | No | Click icon to add prompt [^1_2] |
| **Memory Model** | Unclear | Explicit STM + LTM [^1_2] |
| **Clipboard Intelligence** | No | 5-factor relevance scoring [^1_3] |

## 📱 Platform Strategy

**Decision: Mobile + Desktop**[^1_2]

- **Mobile**: Custom keyboard app (Android) — true "AI in keyboard"[^1_2]
- **Desktop**: System-tray app with global keyboard hook[^1_2]
- **NOT building**: Separate browser extension (desktop app covers browsers via OS-level hook)[^1_2]

**Rationale:**

- ✅ Matches theme: "AI embedded directly into keyboard"[^1_2]
- ✅ One install = works everywhere[^1_2]
- ✅ Differentiates from Grammarly[^1_2]


## 🎯 Success Metrics[^1_1]

| Metric | Target | Measurement |
| :-- | :-- | :-- |
| **Acceptance Rate** | >70% | Accepted / shown |
| **Time Saved** | >30 min/day | Chars suggested × typing speed |
| **Context Detection** | >95% | Correct app/mode detection |
| **Mood Accuracy** | >75% | User feedback alignment |
| **Privacy Compliance** | 100% | Zero sensitive data leaks |


***

## 💡 Architectural Brilliance

Your solution demonstrates **production-level thinking**:

1. **STM/LTM separation** elegantly balances richness vs privacy[^1_1][^1_3]
2. **Mood-aware adaptation** is genuinely novel[^1_3]
3. **Cross-app context tracking** is your killer differentiator[^1_2]
4. **Clipboard relevance scoring** shows attention to detail[^1_3]
5. **Adaptive debouncing** prevents annoyance[^1_1]
6. **Feedback classification** (instant vs hesitant) enables continuous improvement[^1_1]

The **quality formula** you defined captures it perfectly:[^1_1]

```
SUGGESTION_QUALITY = 
  Context(STM) × 
  Mood(alignment) × 
  Style(LTM_match) × 
  Timing(adaptive) × 
  Personalization(learning) × 
  Feedback(continuous) × 
  Clipboard(relevance)
```

**Ready to discuss each module in detail?** Let me know which component you'd like to deep-dive into

<div align="center">⁂</div>

[^1_1]: ai_keyboard_sol.md

[^1_2]: SOLUTION_DECISIONS.md

[^1_3]: CORE_INTELLIGENCE_DESIGN.md

[^1_4]: GRAMMARLY_TECHNICAL_RESEARCH.md




# Updated Complete System Architecture - All Modules

Based on all five documents including the new **Enhanced Features Report**, here's the updated comprehensive module breakdown:[^4_1]

## 🧠 Core Intelligence Modules

### 1. **Short-Term Memory (STM) Manager**[^4_2][^4_3][^4_1]

- Session data tracker
- Text window analyzer
- Cross-app context aggregator with **time decay tracking**[^4_1]
- Behavioral signals collector
- Temporal context monitor
- Suggestion history tracker
- **N-gram state tracker** (last 3 words, last 10 words, word frequency)[^4_1]
- Update orchestrator (debounced/immediate)


### 2. **Long-Term Memory (LTM) System**[^4_3][^4_2][^4_1]

- MongoDB interface
- User profile manager
- Pattern extractor
- Vocabulary analyzer
- Per-app preference store
- Performance metrics tracker
- **Session aggregator** (batch updates at session end)[^4_1]
- Data encryption/decryption layer


### 3. **Mood Estimation Engine**[^4_2][^4_3]

- Multi-signal collector (typing, editing, app behavior, temporal, session)
- Signal classifier (speed vs baseline, backspace frequency, etc.)
- Weighted mood scorer
- Momentum calculator (mood stability)
- Confidence estimator
- Mood history tracker


### 4. **Clipboard Intelligence System**[^4_2][^4_1]

- Clipboard monitor
- Content type detector (text/image/file)
- **5-factor relevance scorer with time decay**[^4_2][^4_1]
- Time decay calculator
- Source app analyzer
- Semantic similarity engine
- Privacy filter (metadata only for images/files)


### 5. **LLM Orchestration Layer**[^4_3][^4_1][^4_2]

- Prompt builder
- Context transformer (STM → prompt)
- **Context compression engine** (2000 token budget)[^4_1]
- Cloud LLM client (OpenAI/Anthropic)
- Local LLM client (Ollama/TinyLlama/Phi-3)
- **Fallback manager with latency-based switching**[^4_1]
- **Streaming response handler**[^4_1]
- Response validator


### 6. **Suggestion Quality Pipeline**[^4_2][^4_1]

- Pre-flight checker (context sufficient? app allowed?)
- Post-processor (repetition check, confidence validation)
- **Dynamic threshold tuner** (mood-based, acceptance rate-based)[^4_1]
- Delivery decision maker


### 7. **Feedback Learning Module**[^4_3]

- Acceptance classifier (instant/quick/considered/hesitant)
- Dismissal classifier (escape/click/typed-over)
- Pattern extractor (positive/negative)
- LTM updater
- Rejection pattern store

***

## 🆕 NEW: Dual Suggestion System Modules[^4_1]

### 8. **Dual Mode Manager**[^4_1]

- Default mode controller (automatic)
- Command mode controller (user-triggered)
- Mode detection and switching
- Icon click handler


### 9. **N-Gram Suggestion Engine**[^4_1]

- Trigram/4-gram language model
- User vocabulary booster
- Ultra-fast prediction (<10ms)
- Word-level completion (3 suggestions)
- Real-time update (every keystroke)


### 10. **Two-Section UI Renderer**[^4_1]

- Section 1: N-gram word suggestions (Gboard-style)
- Section 2: LLM sentence suggestions (AI-powered)
- Section coordinator
- Parallel update manager


### 11. **Direct Suggestion Editor**[^4_1]

- Inline editing mode
- Click-to-edit handler
- Word-by-word acceptance (Ctrl+→)
- Edit confirmation system


### 12. **Command Interface Handler**[^4_1]

- Custom prompt input
- Text selection detector
- Rewrite mode (selected text + prompt)
- Generate mode (empty field + prompt)
- Preset actions (Formal, Concise, Expand)

***

## 🚀 NEW: Advanced Performance Modules[^4_1]

### 13. **Adaptive Debounce System (Enhanced)**[^4_1]

- Typing speed profiler (continuous learning)
- Inter-key interval calculator
- Natural pause detector (sentence end bonus)
- Recent dismissal penalty
- Mood-based delay modifier
- Context-adaptive timing (500-1200ms range)


### 14. **Cross-App Time Decay Manager**[^4_1]

- Exponential decay calculator
- Relevance threshold enforcer (<0.3 = fade out)
- Context freshness tracker
- Minutes-elapsed monitor


### 15. **Request Deduplicator**[^4_1]

- Context hasher
- Last request cache (single request only)
- Duplicate prevention (no stale cache)


### 16. **Latency Monitor**[^4_1]

- Real-time latency tracking
- Model performance logger
- Threshold alert system
- Analytics reporter


### 17. **Streaming Response Coordinator**[^4_1]

- Token-by-token streaming
- Incremental rendering (<50ms first token)
- Perceived latency optimizer


### 18. **Parallel Processing Engine**[^4_1]

- Async task coordinator
- N-gram + LLM simultaneous calls
- Result merger
- Sequential rendering (fast first, then intelligent)

***

## 🎨 NEW: Enhanced UI \& Interaction Modules[^4_1]

### 19. **Real-Time Error Detector**[^4_1]

- SymSpell spell checker
- Rule-based grammar checker
- Error underline renderer (wavy red/blue)
- Hover tooltip generator
- Direct fix injection


### 20. **Emoji Processor**[^4_1]

- LLM-directed emoji selection
- Emoji type mapper
- Application-based policy enforcer
- Contextual appropriateness checker


### 21. **User Profile Detector**[^4_1]

- Behavioral profile comparator
- Deviation calculator (2 std dev threshold)
- Different user/mood detection
- Minimal impact adapter (STM/LTM weight adjustment)
- Temporary profile creator

***

## 🖥️ Desktop Application Modules (Windows)

### 22. **UI Automation Client**[^4_4]

- Focus tracker (global focus change events)
- Text pattern reader (ITextProvider interface)
- Document range extractor
- Caret position detector
- Selection monitor
- Bounding rectangle calculator


### 23. **Application Context Detector**[^4_4]

- Process monitor (psutil integration)
- Window title parser
- App type classifier (document_editor/code_editor/email/chat)
- Purpose detector (report_writing/coding/email_compose)
- Per-app configuration loader


### 24. **Overlay Renderer**[^4_4][^4_1]

- Transparent window manager (WS_EX_LAYERED)
- Icon renderer (Grammarly-style floating icon)
- **Two-section suggestion overlay**[^4_1]
- Tooltip/suggestion popup
- **Error underline drawer** (for corrections)[^4_1]
- Click region manager (click-through/clickable toggle)
- Position synchronizer (follows caret)


### 25. **Keyboard Hook Manager**[^4_5][^4_4][^4_1]

- Global keystroke listener (pynput/keyboard)
- Hotkey handler:
    - `Tab` → Accept suggestion / Accept word-by-word
    - `Esc` → Dismiss
    - `Ctrl+Shift+V` → Voice input
    - `Ctrl+Shift+R` → Rewrite
    - `Ctrl+Shift+P` → Pause/Resume AI
    - **`Ctrl+→` → Accept word-by-word**[^4_1]
- Typing pattern analyzer (speed, rhythm)
- **Inter-key interval tracker**[^4_1]
- Backspace frequency tracker


### 26. **Text Insertion Module**[^4_4][^4_1]

- Clipboard manager (save/restore)
- ValuePattern setter (UI Automation)
- Paste simulator (Ctrl+V)
- pywinauto wrapper methods
- **Direct fix injector** (for spelling/grammar)[^4_1]
- Insertion validator


### 27. **Window Analyzer**[^4_5]

- Full application window scraper
- Visible content extractor
- Context element identifier (email recipient, subject, code imports)
- Structure parser (headings, sections)


### 28. **Cross-App Workflow Tracker**[^4_5][^4_1]

- Active window monitor
- App switch logger
- **Workflow inference engine with time decay**[^4_1]
- Related app detector
- Context flow analyzer

***

## 💻 NEW: Code Mode Modules[^4_1]

### 29. **Code Context Analyzer**[^4_1]

- Multi-file context gatherer
- Import resolver
- Related file detector (up to 5 files)
- Relevant symbol extractor
- Cursor scope finder (current function/class)


### 30. **Language Detector**[^4_1]

- File extension mapper (.py, .js, .java, etc.)
- Programming language identifier
- Multi-language support (8+ languages)


### 31. **Code Suggestion Formatter**[^4_1]

- Single-line suggestion renderer
- Multi-line code block formatter
- Syntax-aware tooltip
- Indentation preserver

***

## 🌐 Browser Extension Modules

### 32. **DOM Context Extractor**[^4_5]

- Full page text reader
- Input field detector
- Rich context analyzer (email threads, form structure)
- Selected text monitor


### 33. **Tooltip UI Renderer**[^4_5]

- Page-native icon injector
- Tooltip renderer (matches browser style)
- Suggestion popup


### 34. **Native Messaging Bridge**[^4_5]

- WebSocket client to desktop app
- Context serializer
- Response handler

***

## 📱 Mobile Keyboard Modules (Android)

### 35. **Custom IME (Input Method Editor)**[^4_5]

- Keyboard UI renderer
- Touch input handler
- **Two-section suggestion bar**[^4_1]
- AI action row (rewrite, tone change, emoji)


### 36. **Voice Input Module**[^4_3][^4_5]

- Whisper integration (faster-whisper)
- Local speech-to-text
- Intent enhancement
- Mic button handler


### 37. **Mobile Context Adapter**[^4_5]

- App detection (Android AccessibilityService)
- Context indicator
- Network client to AI engine

***

## 🗄️ Backend Infrastructure Modules

### 38. **MongoDB Data Layer**[^4_3][^4_5][^4_1]

- User profile storage
- Pattern storage (vocabulary, rejection patterns)
- **Session metrics aggregator**[^4_1]
- Query optimization
- Index management


### 39. **Encryption Module**[^4_5][^4_1]

- **STM local encryption (AES-256, 100ms debounced)**[^4_1]
- Secure key storage
- Data sanitization


### 40. **Privacy Controller**[^4_5][^4_1]

- Auto-block manager (banking apps, password fields)
- App blocklist enforcer
- Pause mode manager
- Data wipe handler
- **Privacy Settings UI** (view STM, edit LTM, export data)[^4_1]

***

## 🔗 Integration \& Communication Modules

### 41. **MCP (Model Context Protocol) Server**[^4_3]

- MCP protocol handler
- Context provider interface
- Tool exposure layer
- Claude Desktop integration
- Frai integration


### 42. **Context Synchronizer**[^4_2][^4_3][^4_1]

- STM update coordinator
- Event queue manager
- **Enhanced debounce controller (100ms for STM updates)**[^4_1]
- Immediate update handler (app switches)
- Background updater (mood every 30s)
- **Session-end LTM batch updater**[^4_1]


### 43. **Event Bus**[^4_3]

- Keystroke events
- Focus change events
- Clipboard change events
- App switch events
- Timer events (periodic checks)
- Suggestion events (shown/accepted/dismissed)

***

## 🎛️ Configuration \& Control Modules

### 44. **User Preferences Manager**[^4_3][^4_5][^4_1]

- Writing style settings
- Formality level controller
- Verbosity preference
- Per-app customization
- **LLM preference selector** (cloud_first/local_first/local_only)[^4_1]
- **Max cloud latency threshold**[^4_1]
- Feature toggles


### 45. **Context Window Configuration**[^4_5][^4_1]

- **Context budget allocator (2000 tokens)**[^4_1]
- Analysis scope controller (N pages, single file, multiple files)
- Language settings
- Code analysis depth (up to 5 related files)[^4_1]


### 46. **Suggestion Settings Controller**[^4_2][^4_1]

- Base threshold configuration
- **Dynamic mood adjustment**[^4_1]
- Acceptance rate tuning
- Suggestion length preferences

***

## 📊 Analytics \& Monitoring Modules

### 47. **Performance Metrics Tracker**[^4_3][^4_1]

- Acceptance rate calculator
- Time saved estimator
- Context detection accuracy
- Mood accuracy validator
- **Latency benchmarking** (n-gram <10ms, LLM 50-200ms)[^4_1]
- Privacy compliance monitor


### 48. **Session Analytics**[^4_2][^4_1]

- Session duration tracker
- **Typing speed baseline profiler**[^4_1]
- App usage patterns
- Productivity window detector
- **Inter-key interval statistics**[^4_1]


### 49. **Logging \& Debug Module**

- Event logger
- Error handler
- Debug mode controller
- Performance profiler
- **Latency alert system**[^4_1]

***

## 🧪 Utility \& Helper Modules

### 50. **Text Similarity Engine**[^4_2]

- Semantic similarity calculator (for clipboard relevance)
- Vector embedding generator
- Pattern matcher


### 51. **Prompt Template Manager**[^4_2][^4_1]

- Template storage (document writing, email, code)
- Template selector
- Variable injector
- **Context-optimized template builder (2000 token budget)**[^4_1]


### 52. **Timing \& Debounce Controller**[^4_3][^4_1]

- **Adaptive delay calculator (enhanced)**[^4_1]
- **Typing speed profiler**[^4_1]
- Natural pause detector (sentence end bonus)
- **Recent dismissal penalty system**[^4_1]
- Typing rhythm analyzer
- Pre-flight timing validator


### 53. **Context Compressor**[^4_1]

- **Token budget allocator**[^4_1]
- Priority-based context selection[^4_1]
- Relevance filtering (cross-app >0.6, clipboard >0.7)[^4_1]
- **Mood-based context adaptation**[^4_1]
- Text truncator

***

## 🔄 Workflow-Specific Modules

### 54. **Email Composer Assistant**[^4_2]

- Email context extractor (recipient, subject, thread)
- Email style matcher
- Greeting/closing suggester


### 55. **Code Completion Engine (Enhanced)**[^4_5][^4_1]

- **Multi-file context builder (up to 5 files)**[^4_1]
- **Language-specific parser (8+ languages)**[^4_1]
- Import analyzer
- Dependency resolver
- Function scope detector
- **Code mode activation detector**[^4_1]


### 56. **Document Writing Assistant**[^4_2]

- Section detector
- Document structure analyzer
- Formality matcher
- Citation/reference helper


### 57. **Chat/Messaging Assistant**[^4_2][^4_1]

- Conversation context tracker
- Tone detector (casual/professional)
- **Emoji suggester (context-aware)**[^4_1]
- Quick reply generator

***

## 📦 System Integration Modules

### 58. **System Tray Application**[^4_5]

- Tray icon manager
- Quick settings menu
- Status indicator
- Update notifier


### 59. **Auto-Updater**

- Version checker
- Update downloader
- Installation orchestrator


### 60. **Onboarding Module**

- First-run wizard
- Permission requester
- **Baseline typing speed calibrator**[^4_1]
- Tutorial/guide

***

## 🆕 NEW: Advanced Feature Modules[^4_1]

### 61. **Session Aggregator**[^4_1]

- Session metrics calculator (avg typing speed, acceptance rate)
- Vocabulary extractor (new words this session)
- Rejection pattern extractor
- App usage distribution tracker
- Mood distribution analyzer
- **Moving average merger** (90% historical, 10% new)


### 62. **Privacy Settings UI Manager**[^4_1]

- STM viewer (current session data)
- LTM editor (modify patterns manually)
- Data exporter (download all info)
- One-click data wipe
- Learning pause controller
- Profile confidence displayer


### 63. **Model Selection Router**[^4_1]

- Latency-based model selector
- Urgency detector
- **Auto-fallback on timeout (2s)**[^4_1]
- Lazy local LLM initializer
- Privacy mode enforcer

***

## 📊 Updated Module Count: **63 Modules**

### Module Categories Summary:

| Category | Count | Change |
| :-- | :-- | :-- |
| **Core Intelligence** | 7 modules | No change |
| **NEW: Dual Suggestion System** | 5 modules | ✨ **NEW** |
| **NEW: Advanced Performance** | 6 modules | ✨ **NEW** |
| **NEW: Enhanced UI \& Interaction** | 3 modules | ✨ **NEW** |
| **Desktop Application** | 7 modules | Enhanced |
| **NEW: Code Mode** | 3 modules | ✨ **NEW** |
| **Browser Extension** | 3 modules | No change |
| **Mobile** | 3 modules | Enhanced |
| **Backend Infrastructure** | 3 modules | Enhanced |
| **Integration \& Communication** | 3 modules | Enhanced |
| **Configuration \& Control** | 3 modules | Enhanced |
| **Analytics \& Monitoring** | 3 modules | Enhanced |
| **Utility \& Helpers** | 4 modules | Enhanced |
| **Workflow-Specific** | 4 modules | Enhanced |
| **System Integration** | 3 modules | No change |
| **NEW: Advanced Feature Modules** | 3 modules | ✨ **NEW** |


***

## 🎯 Key Enhancements from New File[^4_1]

### Performance Improvements:

- **N-gram suggestions**: <10ms (instant word completion)[^4_1]
- **Streaming LLM**: First token in 50ms[^4_1]
- **Parallel processing**: N-gram + LLM simultaneously[^4_1]
- **Adaptive debounce**: 500-1200ms based on typing speed + mood[^4_1]


### Memory Enhancements:

- **Time decay tracking**: Cross-app context fades exponentially[^4_1]
- **Session aggregation**: Batch LTM updates at session end[^4_1]
- **Request deduplication**: Prevent redundant API calls[^4_1]


### Intelligence Improvements:

- **User profile detection**: Detect different users/moods[^4_1]
- **Context compression**: 2000 token budget optimization[^4_1]
- **Dynamic thresholds**: Confidence adjusts with mood + acceptance rate[^4_1]


### Feature Additions:

- **Dual modes**: Default (automatic) + Command (user-triggered)[^4_1]
- **Two-section UI**: N-gram words + LLM sentences[^4_1]
- **Direct editing**: Click to edit suggestions in suggestion box only[^4_1]
- **Real-time errors**: Spelling/grammar with underlines[^4_1]
- **Emoji processing**: LLM-directed, context-aware[^4_1]
- **Code mode**: Multi-file analysis, 8+ languages[^4_1]

***

## 🏗️ Updated High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                       │
│  Desktop Overlay (Two-Section) | Browser Ext | Mobile IME  │
│         (22-28)                |   (32-34)   |   (35-37)   │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│              DUAL SUGGESTION SYSTEM (NEW)                   │
│  Dual Mode Manager | N-gram Engine | Command Interface     │
│       (8)          |      (9)      |       (12)             │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│            ADVANCED PERFORMANCE LAYER (NEW)                 │
│  Adaptive Debounce | Parallel Engine | Streaming | Latency │
│       (13)         |      (18)       |    (17)   |   (16)  │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                  CORE INTELLIGENCE LAYER                    │
│  STM | LTM | Mood | Clipboard | LLM Router | Quality       │
│  (1) | (2) | (3)  |    (4)    |    (5)     |   (6)         │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│             ENHANCED FEATURES (NEW)                         │
│  Error Detector | Emoji | User Profile | Code Analyzer     │
│      (19)       |  (20) |     (21)     |   (29-31)         │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                   WORKFLOW ASSISTANTS                       │
│    Email (54) | Code (55) | Document (56) | Chat (57)      │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                    DATA & STORAGE LAYER                     │
│  MongoDB (38) | Encryption (39) | Privacy UI (40,62)       │
└─────────────────────────────────────────────────────────────┘
```

**Total Modules: 63** (up from 43)[^4_4][^4_3][^4_2][^4_5][^4_1]

**New Modules: 20** ✨

Ready to discuss any specific module or feature in detail![^4_1]

<div align="center">⁂</div>

[^4_1]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md

[^4_2]: CORE_INTELLIGENCE_DESIGN.md

[^4_3]: ai_keyboard_sol.md

[^4_4]: GRAMMARLY_TECHNICAL_RESEARCH.md

[^4_5]: SOLUTION_DECISIONS.md


---

# Updated: 7️⃣ CONTEXT WINDOW STRATEGY FOR LLM

## 7.1 Goal of the Context Window

The context window should give the LLM **compact "situational awareness"** of what the user is doing right now, why they are doing it, and how they prefer to be helped. It is built entirely from **STM (current session signals)** plus a thin layer of **LTM (stable preferences and patterns)**.[^5_1][^5_2][^5_3]

### High-Level Prompt Construction

Each prompt to the LLM is constructed from:

1. **System framing**: Who the assistant is and how it should respond
2. **Task framing**: What app the user is in and what task they are performing
3. **Live text context**: What is currently on screen around the cursor
4. **Session state**: Mood, recent behavior, and suggestion patterns
5. **Workflow context**: Cross-app activity and clipboard, when relevant
6. **User preferences**: Style, formality, and learned do/don't patterns from LTM

***

## 7.2 What We Include in the Context Window

For each LLM call, we assemble a **structured context summary** from STM and LTM rather than dumping raw data. The context window contains these sections:

### 📱 **App \& Task Header**

- Current app name and type (Word, Gmail, VS Code, Slack, etc.)[^5_1]
- Detected task (writing email, report, chat, coding, etc.)[^5_1]
- Document or conversation identity (title, section, channel, thread)[^5_1]


### 👤 **User Profile \& Style (from LTM)**[^5_2]

- Role (e.g., software engineer)[^5_2]
- Preferred writing style (professional, concise, etc.)[^5_2][^5_1]
- Default formality level and typical suggestion length[^5_1]
- Known "avoid" patterns (e.g., too verbose, overly casual)[^5_2][^5_1]


### 📝 **Immediate Text Context (from STM)**[^5_1]

- **Text before cursor**: the current sentence/paragraph or code block[^5_1]
- **Text after cursor** if relevant (e.g., when editing middle of a sentence)[^5_1]
- **Any active selection** when doing a rewrite rather than a completion[^5_3]


### 🏗️ **Task-Specific Structural Context**

**For documents**:[^5_1]

- Document title, current section/heading
- Brief summary of preceding content
- Document structure (sections before/after)

**For email**:[^5_1]

- Recipient, subject
- Short thread preview
- User's typical email style (from LTM)[^5_2]

**For code**:[^5_4][^5_3]

- Language, file name
- Current function or class scope[^5_3]
- Relevant imports[^5_3]
- Related files (up to 5)[^5_3]

**For chat**:[^5_1]

- Last few messages
- Participants
- Channel name/topic


### 🎭 **Session and Mood Signals**[^5_1]

- **Current mood label** (focused, rushed, frustrated, exploratory, casual, neutral)[^5_1]
- **High-level behavioral patterns** this session: acceptance rate, typing style, recent dismissals[^5_1]
- **Temporal context**: time of day, session phase (early, mid, late) if useful for strategy[^5_1]


### 🔄 **Cross-App Workflow Context**[^5_4]

- **Recent apps** that are part of the current workflow (e.g., Sheets → Word, Browser → Docs)[^5_4]
- For each, a **short "what the user was doing there" summary**, not full content[^5_4]
- **Only included when clearly related** to the current task (e.g., viewing Q1 data while writing Q1 report)[^5_4]
- **Time-decayed relevance** (fades as context ages)[^5_3]


### 📋 **Clipboard Context (if relevant)**[^5_1]

- Short preview of clipboard text (numbers, quotes, copied sentences)[^5_1]
- Source app and high-level purpose (e.g., "Q1 Financial Data – Google Sheets")[^5_1]
- **Only used when relevance scoring to current text is high** (e.g., financial numbers while writing a financial report)[^5_1]


### 💡 **Feedback-Driven Hints**[^5_2]

- **Recent accepted suggestions**: what worked (length, tone, type)[^5_2]
- **Recent strong rejections**: what to avoid (too formal in casual app, too long, etc.)[^5_2]
- **Explicit guidance** such as "user prefers concise completions here"[^5_1]

All of this is formatted into a **human-readable but highly structured prompt template**, so the LLM can reliably parse each section (Context, Style, Mood, Clipboard, Patterns, Current Text, etc.).[^5_1]

***

## 7.3 How We Prepare the Context (Step-by-Step)

When the debounce logic decides we should call the LLM, the context window is prepared through these steps:[^5_3][^5_2]

### **Step 1: Read and Normalize STM Snapshot**[^5_1]

- Current app, text window, caret position, mood, cross-app context, clipboard, behavior signals[^5_1]
- Normalize into a canonical STM JSON shape:
    - `current_context`
    - `text_window`
    - `mood_estimation`
    - `clipboard_history`
    - `suggestion_history`
    - `cross_app_context`[^5_1]


### **Step 2: Attach LTM Overlays**[^5_2]

- Fetch the user's profile and learned patterns:
    - Writing style[^5_2]
    - Vocabulary preferences[^5_2]
    - Per-app tone[^5_2]
    - Rejection patterns[^5_2][^5_1]
- Attach these as **high-level constraints and preferences**, not as raw historical text[^5_2]


### **Step 3: Identify Task Type and Intent**[^5_1]

- Infer whether this is:
    - **Completion** (continue what user is typing)
    - **Correction** (fix errors)
    - **Rewrite** (improve selected text)[^5_3]
    - **Intent completion** (predict what user wants to say)
- Use app type + text patterns + mood to decide how "aggressive" or "lightweight" suggestions should be[^5_1]


### **Step 4: Select Relevant STM Fields Per Task**

For each task type (document/email/code/chat), choose which STM fields matter:

**Example for code completions**:[^5_3]

- ✅ Language, imports, current scope[^5_3]
- ✅ Related files (multi-file context)[^5_3]
- ❌ Mood wording (less relevant for code)

**Example for email completions**:[^5_1]

- ✅ Recipient, thread, tone[^5_1]
- ✅ User's typical email style[^5_2]
- ✅ Mood (affects formality)[^5_1]


### **Step 5: Build a Textual Context Summary**

Convert structured STM + LTM into a **compact natural-language "Context" section**.[^5_1]

**Example for document writing**:

```markdown
## Context

- App: Microsoft Word, writing Q1 Report (Conclusion section)
- User: software engineer, professional and concise style
- Recent context: just viewed Q1 financial data in Google Sheets, 
  copied 'Revenue $2.4M (+15% YoY)'
- Mood: focused, user prefers medium-length, high-formality completions
```


### **Step 6: Fill Task-Specific Prompt Template**[^5_1]

Plug context summary and current text into the right template (Document, Email, Code, Chat).[^5_1]

Each template defines which fields appear and in what order for maximum clarity.

**Example: Email template**:[^5_1]

```markdown
## Email Context

- To: {{recipient}}
- Subject: {{subject}}
- Thread Preview: {{thread_summary}}
- User's Email Style: {{email_style_from_ltm}}

## Email Body

"""{{current_email_text}}"""

Complete naturally, match user's email style.
```

**Example: Code template**:[^5_3]

```markdown
## Code Context

- Language: {{language}}
- File: {{filename}}
- Current scope: {{current_function}}
- Imports: {{relevant_imports}}
- Related files: {{related_file_summaries}}

## Code

```{{language}}
{{code_before}}█{{code_after}}
```

Suggest code completion. Prioritize: correctness > style > brevity

```

### **Step 7: Add Explicit Instructions and Output Contract**[^5_1]
- Add clear instructions:
  - Match style[^5_1]
  - Respect formality[^5_1]
  - Avoid known rejected patterns[^5_1]
  - Consider clipboard/cross-app info **only if relevant**[^5_1]
- Define output as **strict JSON** with fields like `suggestion`, `type`, `confidence`[^5_1]

**Output Format**:[^5_1]
```json
{
  "suggestion": "the suggested text",
  "type": "completion|correction|rewrite",
  "confidence": 0.0-1.0,
  "alternative": "optional alternative"
}
```


***

## 7.4 Principles Guiding Context Preparation

The strategy is governed by these principles:

### 1. **Situation First, Text Second**[^5_1]

The LLM should first understand **situation** (app, task, role, mood, workflow), then the raw text.[^5_1]

**Why**: Context determines how to help, not just what text to suggest.

### 2. **Only Structured Signals from History**[^5_2]

LTM contributes **patterns and preferences**, never raw historical content.[^5_2]

**What LTM provides**:

- ✅ "User prefers concise style"[^5_2]
- ✅ "Avoid overly casual tone"[^5_1]
- ❌ NOT actual text from past sessions


### 3. **Task-Specific Shaping**[^5_1]

The same STM fields are **re-shaped differently** for documents, email, code, and chat.[^5_1]

**Example**:

- Document writing → emphasize section structure, formality
- Code writing → emphasize imports, scope, language[^5_3]
- Email writing → emphasize recipient, thread, tone[^5_1]
- Chat → emphasize conversation flow, participants[^5_1]


### 4. **Workflow Awareness**[^5_4]

Cross-app and clipboard context is treated as **part of the user's workflow**, not as independent noise.[^5_4]

**Key insight**: User switching from Sheets to Word is likely bringing data into a report.[^5_4]

**Implementation**:[^5_3]

- Track recent app switches[^5_4]
- Calculate time-decayed relevance[^5_3]
- Include only when relevance is high[^5_1]


### 5. **Feedback-Driven Evolution**[^5_2]

Accepted/dismissed suggestions continually refine **what gets highlighted** in future context windows.[^5_2]

**Learning signals**:[^5_2]

- ✅ Instant accept (<500ms) → This pattern works perfectly[^5_2]
- ❌ Escape dismiss → Strongly avoid this pattern[^5_2]
- ⚠️ Hesitant accept (>3s) → Investigate why[^5_2]

***

## 7.5 Context Window Assembly (Visual)

```
┌────────────────────────────────────────────────────────────────┐
│                    CONTEXT WINDOW ASSEMBLY                     │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  1. SYSTEM FRAMING (fixed)                                     │
│     "You are an intelligent writing assistant embedded         │
│      in a keyboard. Provide helpful text completions."         │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  2. APP & TASK HEADER (from STM)                               │
│     App: Microsoft Word                                        │
│     Task: Writing Q1 Report (Conclusion section)               │
│     Document: "Q1_Performance_Report.docx"                     │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  3. USER PROFILE (from LTM)                                    │
│     Role: Software Engineer                                    │
│     Style: Professional, concise                               │
│     Formality: 0.7 (scale 0-1)                                 │
│     Avoid: ["too_verbose", "overly_casual"]                    │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  4. SESSION & MOOD (from STM)                                  │
│     Mood: focused (confidence: 78%)                            │
│     Typing: 65 WPM (baseline: 72 WPM) - slightly slower        │
│     Acceptance rate this session: 67%                          │
│     Recent dismissals: 2 (back off slightly)                   │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  5. WORKFLOW CONTEXT (from STM, if relevant)                   │
│     Recent apps:                                               │
│     • Google Sheets (2 min ago): "Q1 Financial Data"           │
│     • Outlook (5 min ago): "RE: Q1 Report Draft"               │
│     Workflow: report_writing_from_data (confidence: 0.85)      │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  6. CLIPBOARD (from STM, if relevance > 0.7)                   │
│     Content: "Revenue: $2.4M (+15% YoY)"                       │
│     Source: Google Sheets                                      │
│     Copied: 2 minutes ago                                      │
│     Relevance: 0.85 (likely being referenced)                  │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  7. IMMEDIATE TEXT CONTEXT (from STM)                          │
│     Text before cursor (last 500 chars):                       │
│     "...financial performance exceeded expectations by 15%.    │
│      Revenue grew across all segments. In conclusion, our      │
│      analysis shows that"                                      │
│                                                                │
│     Text after cursor: [empty]                                 │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  8. FEEDBACK HINTS (from STM + LTM)                            │
│     Recently accepted: "exceeded expectations" (instant)       │
│     Recently rejected: "year-over-year growth" (too verbose)   │
│     Preferred this session: medium-length, high-formality      │
│                                                                │
│  ──────────────────────────────────────────────────────────    │
│                                                                │
│  9. OUTPUT INSTRUCTIONS                                        │
│     • Match professional, concise style                        │
│     • Respect formality level (0.7)                            │
│     • Consider clipboard data (revenue numbers)                │
│     • Avoid verbose phrasing                                   │
│     • Respond ONLY with valid JSON:                            │
│       {                                                        │
│         "suggestion": "text",                                  │
│         "type": "completion",                                  │
│         "confidence": 0.85                                     │
│       }                                                        │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```


***

## 7.6 Context Adaptation by Mood[^5_3]

The context window is **dynamically adapted** based on detected mood:[^5_1]

### **Focused Mood**[^5_1]

- Keep full context (user can handle complexity)
- High-quality completions preferred
- Don't rush the suggestion


### **Rushed Mood**[^5_1]

- Minimize context (prioritize speed)
- Skip cross-app details
- Focus on immediate text only
- Quick, short completions


### **Frustrated Mood**[^5_1]

- Include more helpful context
- Add recent errors/backspaces
- Show suggestions that were rejected (learn what's wrong)
- More explicit guidance

**Implementation**:[^5_3]

```python
def adapt_context_by_mood(context, mood):
    if mood == "rushed":
        # Minimize context, prioritize speed
        context.pop("cross_app", None)  # Skip cross-app
        context["user_profile"] = compress(context["user_profile"])
    
    elif mood == "frustrated":
        # Include more helpful context
        context["recent_errors"] = stm.recent_backspaces
        context["suggestions_rejected"] = stm.recent_rejections
    
    elif mood == "focused":
        # Keep full context as-is (don't rush)
        pass
```


***

## 7.7 Why This Strategy Works

### ✅ **Situational Awareness Over Raw Data**

LLM understands **why** the user is typing, not just **what** they typed.[^5_1]

### ✅ **Privacy-Preserving**

Only current session + learned patterns, never historical text.[^5_4][^5_2]

### ✅ **Task-Optimized**

Same data, different framing based on whether user is coding, emailing, or chatting.[^5_1]

### ✅ **Workflow-Intelligent**

Understands multi-app workflows (research → writing, data → report).[^5_4]

### ✅ **Self-Improving**

Every accepted/rejected suggestion refines future context windows.[^5_2]

***

**Context window gives the LLM everything it needs to be helpful, nothing it doesn't, organized for maximum clarity.**[^5_4][^5_3][^5_2][^5_1]
<span style="display:none">[^5_5]</span>

<div align="center">⁂</div>

[^5_1]: CORE_INTELLIGENCE_DESIGN.md

[^5_2]: ai_keyboard_sol.md

[^5_3]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md

[^5_4]: SOLUTION_DECISIONS.md

[^5_5]: GRAMMARLY_TECHNICAL_RESEARCH.md


---

# AI Keyboard - Complete Module Documentation

## Part 1: Core Intelligence \& Dual Suggestion System (Modules 1-10)

> **Document Version:** 1.0
> **Date:** January 30, 2026, 1:05 AM IST
> **Scope:** Detailed explanation of Modules 1-10
> **Purpose:** Comprehensive understanding before schema design and development

***

## Module 1: Short-Term Memory (STM) Manager

### Overview

The STM Manager is the system's **working memory** that captures everything happening in the current session. Think of it as the AI's "active consciousness" that tracks what you're doing right now, where you are, what mood you're in, and what context surrounds your current task.[^6_1][^6_2]

### Core Responsibilities

#### 1.1 Session Data Tracking

The STM Manager maintains a comprehensive snapshot of the current session:[^6_1]

- **Session identification**: Unique session ID, start time, duration
- **Platform information**: Windows/Mac/Linux/Mobile
- **Session phase**: Early session (first 15 minutes), mid-session, late session
- **Activity level**: Active typing percentage vs reading/pausing

**Why this matters**: Session duration affects suggestion strategy. Early in a session, the system has less data about your current mood and patterns, so it's more conservative. Later in a session, it has richer behavioral signals to work with.

#### 1.2 Text Window Analysis

Continuously tracks the text surrounding your cursor:[^6_1]

- **Immediate context**: Last 50-100 characters before cursor (current sentence)
- **Paragraph context**: Current paragraph you're writing
- **Page context**: Text visible on current page/screen
- **Extended context**: Broader document summary (last 5 pages)
- **Text after cursor**: What comes next (if editing mid-document)
- **Selected text**: Any highlighted text (for rewrite operations)

**Why multiple levels**: Different suggestion types need different context depths. A single-word completion needs immediate context. A paragraph completion needs broader understanding.

#### 1.3 Cross-App Context Aggregator[^6_3][^6_2]

Tracks your workflow across multiple applications:

- **Recent apps visited**: Last 3-5 apps you switched from
- **Time spent per app**: How long you were in each app
- **Context summary per app**: What you were doing there (viewing data, reading email, browsing)
- **Time-decayed relevance**: Context from 2 minutes ago is highly relevant; 30 minutes ago less so[^6_2]
- **Workflow inference**: Detects patterns like "research → writing" or "data analysis → report"

**Example workflow detection**:

- User in Chrome viewing Q1 financial spreadsheet (3 minutes)
- Copies "Revenue: \$2.4M" to clipboard
- Switches to Word, document titled "Q1_Report.docx"
- **System infers**: User is bringing data from research into a report → clipboard content highly relevant


#### 1.4 Behavioral Signals Collection[^6_1]

Tracks how you're typing and interacting:

**Typing patterns**:

- Current typing speed in WPM (words per minute)
- Baseline typing speed (your normal speed)
- Speed trend: faster than normal, slower than normal, or consistent
- Pause frequency: How often you pause while typing
- Backspace frequency: How often you delete/correct

**Interaction patterns**:

- Suggestions shown this session
- Suggestions accepted (how many, how quickly)
- Suggestions dismissed (how many, how strongly)
- Acceptance rate percentage
- Time to decision: How long you take to accept/reject

**Focus patterns**:

- App switches in last 10 minutes
- Reading vs writing ratio (scrolling vs typing)
- Active typing percentage

**Why this matters**: These signals reveal your current state. Lots of backspaces + slow typing = struggling or frustrated. Fast typing + high acceptance = in flow state.

#### 1.5 Temporal Context Monitoring[^6_1]

Tracks time-based context:

- **Time of day**: Morning, afternoon, evening, late night
- **Day of week**: Monday (possibly stressed) vs Friday (winding down)
- **Session duration**: How long you've been working
- **Productivity window**: Peak hours (morning for most) vs low-energy periods

**Why this matters**: People write differently at 9 AM vs 11 PM. Morning users might prefer formal, structured suggestions. Late-night users might be tired and need simpler, shorter completions.

#### 1.6 Suggestion History Tracker[^6_1]

Maintains a running log of suggestions shown this session:

- **Recent suggestions**: Last 10-20 suggestions with their outcomes
- **Context of each suggestion**: What app, what mood, what text
- **User's response**: Accepted (and how quickly), dismissed (and how), ignored
- **Patterns emerging**: What's working, what's being rejected

**Example pattern detection**:

- 5 long suggestions (>40 chars) dismissed → User prefers shorter completions right now
- 3 formal suggestions accepted instantly → User is in professional writing mode
- 2 casual suggestions rejected → Not appropriate for current context


#### 1.7 N-Gram State Tracker[^6_2]

Tracks word-level context for fast suggestions:

- **Last 3 words typed**: For trigram predictions
- **Last 10 words**: For broader context patterns
- **Word frequency this session**: Which words you're using repeatedly
- **Bigram/trigram patterns**: Common word pairs/triplets you're typing

**Why this matters**: N-gram suggestions are instant (<10ms). They need recent word context to predict your next word accurately.[^6_2]

#### 1.8 Update Orchestrator

Manages how and when STM gets updated:

**Debounced updates (300ms)**:[^6_2][^6_1]

- Keystroke events (text changes)
- Don't update on every single keystroke (too expensive)
- Wait 300ms after last keystroke, then batch update

**Immediate updates**:[^6_1]

- App switches (critical context change)
- Clipboard changes (might be bringing in relevant data)
- Suggestion shown/accepted/dismissed (feedback)

**Background updates (every 30 seconds)**:[^6_1]

- Mood recalculation (gradual changes)
- Time-decay calculations for cross-app context[^6_2]
- Session phase updates


### Privacy Model[^6_4][^6_3]

**Critical principle**: STM is temporary and local

- ✅ Encrypted locally (AES-256)
- ✅ Stored only in RAM (with encrypted disk backup during session)
- ❌ Never sent to database
- ❌ Deleted completely when session ends (app close, 30 min inactivity, user logout)

***

## Module 2: Long-Term Memory (LTM) System

### Overview

LTM is the system's **persistent memory** that learns your patterns over time. Unlike STM (which forgets after each session), LTM remembers who you are as a writer, what you prefer, and what you consistently reject.[^6_4][^6_1]

### Core Responsibilities

#### 2.1 MongoDB Interface

Handles all database operations:

- **Connection management**: Establishes and maintains connection to MongoDB
- **Read operations**: Fetch user profile, patterns, preferences
- **Write operations**: Update patterns at session end[^6_2]
- **Query optimization**: Efficient retrieval of relevant patterns
- **Index management**: Fast lookups by user ID, app type, time period

**Why MongoDB**: Flexible schema for evolving patterns, good for document-based storage of user profiles, supports nested structures (per-app preferences, hierarchical patterns).

#### 2.2 User Profile Manager

Stores stable identity and preferences:[^6_4]

**Core profile**:

- **User role**: Software engineer, content writer, student, etc.
- **Default writing style**: Professional, casual, technical, creative
- **Formality preference**: Scale 0.0 (very casual) to 1.0 (very formal)
- **Verbosity preference**: Concise, balanced, or verbose
- **Baseline typing speed**: Your normal WPM (for comparison in STM)

**Per-app preferences**:[^6_4]

- Gmail: Formal, medium length
- Slack: Casual, short
- VS Code: Technical, precise
- Word: Professional, structured

**Why per-app**: You write differently in different contexts. Casual in chat, formal in documents.

#### 2.3 Pattern Extractor

Identifies recurring patterns from session data:[^6_4]

**Writing style patterns**:

- **Sentence structure**: Do you write short punchy sentences or long flowing ones?
- **Paragraph length**: Compact vs detailed
- **Opening patterns**: How you typically start emails/documents
- **Closing patterns**: How you typically end communications

**Vocabulary patterns**:

- **Frequent phrases**: "looking forward to", "please let me know", "sounds good"
- **Technical terms**: "API endpoint", "database query", "user authentication"
- **Avoided words**: Words you never use (e.g., "utilize" → always use "use")

**Behavioral patterns**:

- **Peak performance times**: When acceptance rate is highest
- **Preferred suggestion length**: 20-30 chars, 40-60 chars, etc.
- **Tone consistency**: Always professional in Gmail, relaxed in Slack


#### 2.4 Vocabulary Analyzer[^6_4]

Builds your personal word database:

**Frequently used words**:

- Top 100 words you use most (beyond common words like "the", "a")
- Domain-specific terms (financial jargon if you're in finance)
- Proper nouns (company names, project names, people names)

**Word preferences**:

- Synonyms you prefer: "use" not "utilize", "help" not "assist"
- Spelling preferences: British vs American English
- Abbreviations: "vs" or "versus", "etc." or "et cetera"

**Why this matters**: Suggestions should use YOUR vocabulary, not generic words. If you never say "leverage" as a verb, suggestions shouldn't suggest it.

#### 2.5 Per-App Preference Store[^6_4]

Maintains different profiles for different apps:

**Structure per app**:

- **App identification**: Name, type (email, chat, document, code)
- **Tone preference**: Formal/casual/technical
- **Suggestion acceptance rate**: How well suggestions work in this app
- **Preferred suggestion types**: Completions, corrections, rewrites
- **Emoji policy**: Enabled/disabled/conditional[^6_2]

**Example per-app profiles**:

- **Gmail**: Formality 0.8, medium completions, greeting/closing suggestions
- **Slack**: Formality 0.3, short quick replies, emoji enabled
- **VS Code**: Technical mode, multi-line code blocks, imports-aware[^6_2]
- **LinkedIn**: Formality 0.7, professional tone, careful wording


#### 2.6 Performance Metrics Tracker[^6_4]

Measures how well the system is working for you:

**Acceptance metrics**:

- Overall acceptance rate (target: >70%)[^6_4]
- Per-app acceptance rates
- Per-mood acceptance rates
- Time-of-day patterns (best times)

**Quality metrics**:

- Instant accepts (<500ms): Perfect suggestions[^6_4]
- Quick accepts (500-1500ms): Good suggestions[^6_4]
- Hesitant accepts (>3s): Barely acceptable[^6_4]
- Strong dismissals (Escape key): Actively annoying[^6_4]

**Time-saved metrics**:

- Characters suggested and accepted
- Estimated time saved (chars ÷ typing speed)
- Target: >30 minutes saved per day[^6_4]


#### 2.7 Session Aggregator[^6_2]

Converts each session's STM into learnable patterns:

**At session end**:

- Calculate average typing speed for this session
- Calculate average acceptance rate for this session
- Extract new vocabulary (words you used frequently)
- Identify new rejection patterns (what you consistently dismissed)
- Summarize app usage distribution
- Record mood distribution (how much time in each mood)

**Merging strategy**:[^6_2]

- **Moving average**: 90% weight to historical data, 10% to new session
- **Why weighted**: Gradual learning prevents overreaction to single unusual session
- **Example**: If your historical formality is 0.7, one casual session doesn't drop you to 0.3


#### 2.8 Data Encryption/Decryption Layer[^6_3]

Protects your privacy:

**Encryption at rest**:

- All LTM data encrypted in MongoDB
- AES-256 encryption standard
- User-specific encryption keys (not shared across users)

**Encryption in transit**:

- TLS/SSL for all database connections
- No plaintext transmission

**Privacy guarantee**:[^6_3][^6_4]

- ❌ Actual content never stored (no "Dear John, I am writing to..." saved)
- ✅ Only patterns stored ("User prefers formal email greetings")


### Update Strategy[^6_2]

**When LTM is updated**:

- ✅ Session end (app close, user logout, 30 min inactivity)
- ✅ Batch update (all session data aggregated at once)
- ❌ NOT during session (no constant database writes)

**Why batch at session end**:

- Efficient: One database write instead of hundreds
- Accurate: Full session picture before learning
- Performance: No database latency during active use

***

## Module 3: Mood Estimation Engine

### Overview

The Mood Estimation Engine infers your current mental/emotional state from behavioral signals. It doesn't read your mind—it observes how you're typing, switching apps, and interacting, then classifies you into one of six mood states.[^6_1]

### Core Responsibilities

#### 3.1 Multi-Signal Collector[^6_1]

Gathers behavioral data points from STM:

**Signal Category 1: Typing Behavior (35% weight)**:[^6_1]

- **Speed vs baseline**: Typing 90 WPM when your normal is 70 WPM → rushing
- **Rhythm consistency**: Steady rhythm → focused; erratic → distracted
- **Pause patterns**: Long pauses → thinking deeply; no pauses → flowing

**Signal Category 2: Editing Patterns (25% weight)**:[^6_1]

- **Backspace frequency**: High frequency → struggling, making mistakes
- **Correction patterns**: Fixing same word repeatedly → frustrated
- **Delete-and-rewrite cycles**: Starting over multiple times → unsure

**Signal Category 3: App Behavior (20% weight)**:[^6_1]

- **App switch frequency**: Many switches → distracted, multitasking
- **Focus time**: Long time in one app → deep work
- **Workflow coherence**: Related apps in sequence → purposeful work

**Signal Category 4: Temporal Context (10% weight)**:[^6_1]

- **Time of day**: Morning = fresh, evening = tired
- **Duration in session**: Just started = neutral, 2 hours in = potentially fatigued

**Signal Category 5: Session Context (10% weight)**:[^6_1]

- **Break patterns**: No breaks for 90 min → potentially stressed
- **Task switching**: Jumping between tasks → exploratory or overwhelmed


#### 3.2 Signal Classifier[^6_1]

Converts raw measurements into categorical levels:

**Typing speed classification**:

- Much faster (>1.3x baseline) → "much_faster"
- Faster (1.1-1.3x) → "faster"
- Normal (0.9-1.1x) → "normal"
- Slower (0.7-0.9x) → "slower"
- Much slower (<0.7x) → "much_slower"

**Backspace frequency classification**:

- Very high (>20% of keystrokes are backspaces) → "very_high"
- High (10-20%) → "high"
- Normal (5-10%) → "normal"
- Low (<5%) → "low"

**App switch classification**:

- Very high (>10 switches in 10 min) → "very_high"
- High (5-10) → "high"
- Normal (2-5) → "normal"
- Low (<2) → "low"


#### 3.3 Weighted Mood Scorer[^6_1]

Maps signals to mood probabilities:

**Signal-to-Mood Mapping**:

**Example: Typing Speed**:[^6_1]

- Much faster typing → 70% "rushed", 30% "focused"
- Faster typing → 40% "rushed", 50% "focused"
- Normal → 60% "neutral", 30% "focused"
- Slower → 40% "exploratory", 30% "frustrated"
- Much slower → 50% "frustrated", 30% "exploratory"

**Example: Backspace Frequency**:[^6_1]

- Very high → 60% "frustrated", 20% "exploratory"
- High → 30% "frustrated", 30% "exploratory"
- Normal → 50% "neutral", 30% "focused"
- Low → 50% "focused", 20% "casual"

**Scoring process**:

1. For each signal, get mood probabilities
2. Multiply probabilities by signal weight
3. Sum across all signals for each mood
4. Result: Six mood scores (one per mood type)

#### 3.4 Momentum Calculator[^6_1]

Prevents rapid mood swings:

**Why momentum matters**: Mood doesn't change every minute. If you were focused 30 seconds ago, you're likely still focused now.

**Stability bonus**:[^6_1]

- Current mood gets +0.15 bonus in scoring
- Makes it harder to switch moods
- Requires consistent signals to change mood classification

**Example**:

- Previous mood: Focused (score: 0.65)
- New signals suggest: Rushed (score: 0.58)
- With momentum: Focused (0.65 + 0.15 = 0.80) still wins
- Result: Stay in "focused" mood (stable)


#### 3.5 Confidence Estimator[^6_1]

Calculates how certain the mood classification is:

**Confidence factors**:

- **Signal agreement**: All signals point to same mood → high confidence
- **Signal conflict**: Signals split across moods → low confidence
- **Signal strength**: Strong signals (very high backspace) → higher confidence
- **Time in mood**: Been in this mood for 10 minutes → higher confidence

**Confidence scale**:[^6_1]

- 0.0-0.3: Low confidence (mixed signals)
- 0.3-0.6: Moderate confidence
- 0.6-0.8: Good confidence
- 0.8-1.0: Very high confidence

**How confidence is used**: Low confidence → use more neutral suggestions. High confidence → adapt more aggressively to detected mood.

#### 3.6 Mood History Tracker[^6_1]

Maintains mood progression over time:

**What's tracked**:

- Last 10-20 mood estimates with timestamps
- Transitions between moods (neutral → focused → rushed)
- Duration in each mood
- Patterns (always rushed in late afternoon)

**Why track history**:

- Detect mood trends (gradually becoming frustrated)
- Understand mood triggers (always casual after lunch)
- Improve future predictions (if user is usually focused in morning, bias toward focused)


### Six Mood States[^6_1]

#### Mood 1: Focused

**Characteristics**:

- Steady typing rhythm
- Low app switching
- Low backspace frequency
- Long focus periods
- Consistent speed

**Suggestion strategy**:[^6_1]

- Higher quality threshold (don't interrupt flow)
- Longer debounce delay (1200ms)[^6_2]
- Prefer longer, complete thoughts
- Less frequent suggestions


#### Mood 2: Rushed

**Characteristics**:

- Fast typing (>1.3x baseline)
- Frequent app switching
- Time pressure signals
- Quick decisions

**Suggestion strategy**:[^6_1]

- Lower quality threshold (be more helpful)
- Shorter debounce delay (500ms)[^6_2]
- Short, quick completions
- Prioritize speed over perfection


#### Mood 3: Exploratory

**Characteristics**:

- Slower typing
- Many pauses (thinking)
- Some backspacing (trying different words)
- Longer time per sentence
- Frequent editing

**Suggestion strategy**:[^6_1]

- Offer alternatives
- Creative suggestions
- Multiple options
- Don't rush decisions


#### Mood 4: Frustrated

**Characteristics**:

- Very high backspace frequency
- Starting over repeatedly
- Erratic typing speed
- Many corrections
- Long pauses followed by bursts

**Suggestion strategy**:[^6_1]

- Be extra helpful
- Offer corrections proactively
- Simpler suggestions
- Lower threshold (more suggestions)


#### Mood 5: Casual

**Characteristics**:

- Relaxed rhythm
- Informal context (chat apps)
- Consistent speed
- Low stress signals

**Suggestion strategy**:[^6_1]

- Allow informal tone
- Emoji suggestions OK[^6_2]
- Shorter completions
- Conversational style


#### Mood 6: Neutral

**Characteristics**:

- No strong signals in any direction
- Default state
- Just started session

**Suggestion strategy**:[^6_1]

- Balanced approach
- Standard thresholds
- Middle-ground suggestions
- Learn from user responses

***

## Module 4: Clipboard Intelligence System

### Overview

The Clipboard Intelligence System monitors what you copy/paste and determines when clipboard content is relevant to your current writing. It's not just about detecting clipboard changes—it's about understanding **whether what you copied matters right now**.[^6_1]

### Core Responsibilities

#### 4.1 Clipboard Monitor

Continuously watches clipboard activity:

**What's monitored**:

- Clipboard change events (every time you copy something)
- Content type detection (text, image, file)
- Source application (which app you copied from)
- Timestamp (when you copied it)

**Monitoring approach**:

- OS-level hooks (Windows Clipboard API, macOS Pasteboard)
- Real-time detection (immediate notification on clipboard change)
- Background monitoring (doesn't interrupt your work)


#### 4.2 Content Type Detector[^6_1]

Identifies what was copied:

**Type 1: Text**:[^6_1]

- ✅ Fully captured and analyzed
- Preview: First 200 characters stored
- Full length recorded
- Rich text converted to plain text

**Type 2: Image**:[^6_1]

- ⚠️ Metadata only (privacy protection)
- Filename stored
- Source app recorded
- Timestamp captured
- ❌ Actual image data NOT stored

**Type 3: File**:[^6_1]

- ⚠️ Metadata only
- Filename and extension stored
- Source application recorded
- ❌ File contents NOT accessed

**Type 4: Rich HTML/Formatted Text**:[^6_1]

- Text extracted, formatting ignored
- Links converted to plain text
- Tables converted to text representation

**Why different treatment**: Text is relevant for suggestions. Images/files are contextual clues but less directly useful.

#### 4.3 Five-Factor Relevance Scorer[^6_1]

Calculates how relevant clipboard content is to your current writing:

**Factor 1: Time Decay (30% weight)**:[^6_1]

- **<2 minutes ago**: +0.3 relevance (very fresh, likely using now)
- **2-10 minutes**: +0.15 relevance (recent, probably related)
- **10-30 minutes**: 0.0 neutral (might be related)
- **30-60 minutes**: -0.1 relevance (getting stale)
- **>60 minutes**: -0.25 relevance (old, probably different task)

**Reasoning**: If you copied something 30 seconds ago and haven't pasted it yet, you're likely about to use it or reference it.

**Factor 2: Source App Relevance (25% weight)**:[^6_1]

- **Same app**: +0.1 (internal copy within document)
- **Related app in workflow**: +0.2 (e.g., Sheets → Word, both in report workflow)
- **Browser → Document editor**: +0.15 (research workflow)
- **Unrelated apps**: 0.0 neutral

**Reasoning**: Copying from Google Sheets while writing in Word suggests bringing data into report.

**Factor 3: Content Similarity (25% weight)**:[^6_1]

- Semantic similarity between clipboard text and current paragraph
- Uses text similarity algorithm (cosine similarity, TF-IDF)
- Score 0.0 (completely unrelated) to 1.0 (very similar)
- Multiply by 0.25 weight

**Example**:

- Clipboard: "Revenue: \$2.4M"
- Current text: "...financial performance exceeded expectations..."
- Similarity: 0.8 (financial terms match)
- Contribution: 0.8 × 0.25 = 0.2

**Factor 4: Content Pattern Recognition (10% weight)**:[^6_1]

- **Data patterns**: Numbers, statistics, quotes → +0.1 (likely being referenced)
- **URLs**: -0.1 (less directly useful for writing)
- **Code snippets**: +0.1 in code editors, -0.1 in documents
- **Quotes**: +0.1 (likely being cited)

**Factor 5: Usage Status (10% weight)**:[^6_1]

- **Already pasted**: -0.2 (less need to reference again)
- **Not yet used**: 0.0 neutral
- **Pasted multiple times**: +0.1 (important reference)


#### 4.4 Time Decay Calculator[^6_2]

Applies exponential decay to clipboard relevance:

**Decay function**:[^6_2]

```
current_relevance = initial_relevance × exp(-0.1 × minutes_elapsed)
```

**Decay curve**:

- 0 minutes: 100% of initial relevance
- 5 minutes: 60% of initial relevance
- 10 minutes: 37% of initial relevance
- 20 minutes: 13% of initial relevance
- 30 minutes: 5% of initial relevance

**Threshold for inclusion**:[^6_1]

- Relevance ≥ 0.65: Include full clipboard preview in LLM context
- Relevance 0.45-0.64: Include brief mention
- Relevance < 0.45: Exclude completely

**Why exponential decay**: Recent clipboard activity is far more relevant than old clipboard activity. Exponential decay models this better than linear decay.

#### 4.5 Source App Analyzer[^6_3]

Understands what application clipboard content came from:

**App categorization**:

- **Data/spreadsheet apps**: Excel, Google Sheets, Numbers
- **Browsers**: Chrome, Firefox, Edge, Safari
- **Document editors**: Word, Google Docs, Notion
- **Code editors**: VS Code, PyCharm, Sublime
- **Communication**: Email, Slack, Teams

**Workflow detection**:[^6_3]

- Browser + Document → Research-to-writing workflow
- Spreadsheet + Document → Data-to-report workflow
- Code + Documentation → Development-to-doc workflow

**Example**:

- Copied from Chrome (viewing "Q1 Financial Results")
- Currently in Word ("Q1_Report.docx")
- **Inference**: User is bringing research data into report
- **Action**: Boost clipboard relevance, include in context


#### 4.6 Semantic Similarity Engine[^6_1]

Measures how related clipboard text is to current writing:

**Techniques used**:

- **TF-IDF vectorization**: Convert text to numerical vectors
- **Cosine similarity**: Measure angle between vectors (0=unrelated, 1=identical)
- **Keyword matching**: Count shared important words
- **Domain detection**: Financial terms, technical terms, casual language

**Example calculation**:

- Clipboard: "quarterly revenue increased by 15% YoY"
- Current text: "In conclusion, our financial performance..."
- Shared terms: "financial", "quarterly" (implied in Q1 report context)
- Similarity score: 0.72 (fairly related)


#### 4.7 Privacy Filter[^6_1]

Protects sensitive clipboard data:

**Privacy rules**:

- ✅ Text: Analyzed in RAM, preview stored temporarily
- ❌ Images: Metadata only (filename, source)
- ❌ Files: Metadata only (filename, extension)
- ❌ Passwords: Detected and completely ignored (if copied from password field)
- ❌ Credit cards: Pattern-detected and ignored
- ❌ Personal IDs: Pattern-detected and ignored

**Sensitive pattern detection**:

- Credit card numbers (16 digits with specific patterns)
- SSN/ID patterns
- Password field indicators
- Bank account patterns

**Action on sensitive data**: Immediate memory clear, not stored even temporarily.

### Clipboard Usage in Suggestions

**Decision tree**:[^6_1]

1. Calculate relevance score (0-1.0)
2. If score ≥ 0.65: Include full preview in LLM prompt
3. If score 0.45-0.64: Include brief mention ("User recently copied financial data")
4. If score < 0.45: Exclude completely
5. Always check: Is current app in privacy blocklist (banking apps)?

**Example inclusion in LLM prompt**:[^6_1]

```
## Clipboard Context (Relevance: 0.85)

Content: "Revenue: $2.4M (+15% YoY)"
Source: Google Sheets (Q1 Financial Data)
Copied: 2 minutes ago
Likely usage: User is referencing this data in current report
```


***

## Module 5: LLM Orchestration Layer

### Overview

The LLM Orchestration Layer is the "brain coordinator" that decides which LLM to use, prepares context, makes the API call, and processes the response. It manages both cloud LLMs (GPT-4, Claude) and local LLMs (Phi-3, TinyLlama).[^6_2][^6_1]

### Core Responsibilities

#### 5.1 Prompt Builder[^6_1]

Constructs the full prompt sent to the LLM:

**Prompt structure**:[^6_1]

1. **System prompt** (fixed, cached)
2. **Context summary** (dynamic, built from STM + LTM)
3. **Current text** (what user is typing)
4. **Output instructions** (JSON format specification)

**System prompt components**:

- Role definition: "You are an intelligent writing assistant embedded in a keyboard"
- Output format: "Respond ONLY with valid JSON: {suggestion, type, confidence}"
- General guidelines: "Match user's style, respect formality, complete thoughts naturally"

**Prompt size management**:

- System prompt: ~200 tokens (fixed)
- Context: Variable, optimized for task
- Current text: Last 500 characters before cursor
- Total: Kept efficient for fast response


#### 5.2 Context Transformer[^6_1]

Converts structured STM/LTM data into natural language context:

**Transformation process**:

- Take STM JSON structure (session, current_context, mood_estimation, etc.)
- Extract relevant fields for current task type
- Convert to human-readable summary
- Apply task-specific template (Email, Document, Code, Chat)

**Example transformation**:

**Input (STM)**:

```json
{
  "current_context": {
    "app": {"name": "Microsoft Word", "type": "document_editor"},
    "document": {"title": "Q1 Report.docx", "section": "Conclusion"}
  },
  "mood_estimation": {"current_mood": "focused", "confidence": 0.78}
}
```

**Output (Prompt Context)**:

```
App: Microsoft Word, writing Q1 Report (Conclusion section)
Mood: focused (confidence: 78%)
User prefers: professional, concise style
```


#### 5.3 Context Compression Engine[^6_2]

Optimizes context to include maximum relevant information in limited space:

**Compression strategies**:

**Strategy 1: Relevance filtering**:

- Cross-app context: Only include if relevance > 0.6[^6_2]
- Clipboard: Only include if relevance > 0.7[^6_1]
- Historical suggestions: Only last 5 most relevant

**Strategy 2: Summarization**:

- Long text before cursor: Use last 500 chars (most relevant)
- Cross-app context: Brief summary, not full content
- User vocabulary: Top 10 frequent phrases, not full list

**Strategy 3: Priority allocation**:

- Critical: Current text (always full)
- High: User style preferences (always included)
- Medium: Mood and behavioral signals (compressed)
- Low: Cross-app context (included only if relevant)

**Strategy 4: Task-specific selection**:

- Code completion: Prioritize imports, scope, language[^6_2]
- Email: Prioritize recipient, thread, tone[^6_1]
- Document: Prioritize section, formality, structure[^6_1]


#### 5.4 Cloud LLM Client[^6_2]

Manages connections to cloud AI services:

**Supported providers**:

- OpenAI (GPT-4, GPT-4-turbo)
- Anthropic (Claude 3.5 Sonnet, Claude 3 Opus)
- Google (Gemini Pro)
- Others via API compatibility

**Client responsibilities**:

- API authentication (key management)
- Request formatting (provider-specific formats)
- Error handling (rate limits, network errors)
- Timeout management (2 second max)[^6_2]
- Response parsing (extract JSON from response)

**Request format**:

- HTTP POST to provider endpoint
- Headers: API key, content type
- Body: Prompt, max_tokens, temperature, response format
- Streaming enabled for faster perceived latency[^6_2]


#### 5.5 Local LLM Client[^6_2]

Manages local AI models as fallback:

**Supported local models**:

- **Phi-3-Mini (3.8GB)**: Best quality/size ratio, recommended[^6_2]
- **TinyLlama (1.1GB)**: Ultra-fast, lower quality[^6_2]
- **Qwen2-1.5B (1.5GB)**: Good balance[^6_2]

**Integration method**:

- Ollama framework (local model server)
- Lazy initialization (only loads when needed)[^6_2]
- One-time setup (~2-3 seconds first time)[^6_2]
- Subsequent calls: 50-150ms[^6_2]

**Model selection**:

- User preference setting (cloud_first, local_first, local_only)[^6_2]
- Automatic based on network availability
- Privacy mode: Always use local[^6_2]


#### 5.6 Fallback Manager with Latency-Based Switching[^6_2]

Intelligently switches between cloud and local based on performance:

**Decision logic**:

1. **Default**: Try cloud LLM first (best quality)[^6_2]
2. **Check latency**: If response takes >500ms[^6_2]
3. **Fallback trigger**: Switch to local LLM for next requests
4. **Timeout**: If cloud doesn't respond in 2 seconds, fallback immediately[^6_2]
5. **Recovery**: Retry cloud after 5 minutes

**Fallback scenarios**:[^6_2]

- Network offline → Local LLM
- High cloud latency → Local LLM
- Cloud API error → Local LLM
- Privacy mode enabled → Local LLM (no fallback to cloud)
- Rate limit exceeded → Local LLM

**Graceful degradation**: Always have a working suggestion system, even if quality is slightly lower.

#### 5.7 Streaming Response Handler[^6_2]

Processes LLM responses as they arrive (token-by-token):

**Why streaming matters**:

- Perceived latency: User sees first words in ~50ms[^6_2]
- Better UX: Suggestion appears to "type out" naturally
- Cancelable: User can dismiss before full response received

**Streaming process**:

1. Send request with `stream: true` parameter
2. Receive tokens as they're generated
3. Buffer tokens until complete JSON received
4. Display incrementally (if UI supports it)
5. Parse complete JSON when done

**Fallback if streaming fails**: Wait for complete response (traditional mode).

#### 5.8 Response Validator

Ensures LLM output is usable:

**Validation checks**:

1. **Format check**: Is response valid JSON?
2. **Required fields**: Does it have "suggestion", "type", "confidence"?
3. **Confidence range**: Is confidence between 0.0 and 1.0?
4. **Suggestion quality**: Is suggestion non-empty, reasonable length?
5. **Type validity**: Is type one of "completion", "correction", "rewrite"?

**Action on invalid response**:

- Log error
- Don't show suggestion
- Increment error counter (affects future LLM selection)
- Retry with simplified prompt (if time allows)


### LLM Selection Strategy[^6_2]

**Decision matrix**:

- **Online + Good latency**: Cloud LLM (best quality)
- **Online + High latency**: Local LLM (speed priority)
- **Offline**: Local LLM (only option)
- **Privacy mode**: Local LLM (no data leaves device)
- **Cloud failure**: Local LLM (graceful degradation)

**User control**:[^6_2]

- Preference setting: cloud_first / local_first / local_only
- Max cloud latency threshold (default: 500ms)
- Auto-fallback toggle (default: enabled)

***

## Module 6: Suggestion Quality Pipeline

### Overview

The Suggestion Quality Pipeline is the "quality control department" that decides whether a suggestion is good enough to show the user. It runs checks before calling the LLM (pre-flight) and after receiving the response (post-processing).[^6_1]

### Core Responsibilities

#### 6.1 Pre-Flight Checker[^6_1]

Validates conditions before making an expensive LLM call:

**Check 1: Sufficient Context?**

- Is there at least 10 characters typed?[^6_4]
- If less: No point suggesting (not enough context)
- Exception: After punctuation (., !, ?), can suggest with less context

**Check 2: In Text Field?**

- Is cursor currently in an editable text area?
- Use UI Automation to verify[^6_5]
- If not in text field: Don't suggest (nowhere to insert suggestion)

**Check 3: App Allowed?**

- Is current app in user's blocklist?
- Is this a banking app (auto-blocked)?[^6_3]
- Is this a password field (auto-blocked)?[^6_3]
- If blocked: Don't suggest (privacy/security)

**Check 4: No Pending Request?**

- Is there already an LLM request in progress?
- If yes: Don't send another (avoid request pile-up)
- Wait for current request to complete

**Check 5: User Still Paused?**

- Has user continued typing since debounce timer started?
- If yes: Cancel suggestion (user is in flow, don't interrupt)
- If paused: Proceed with suggestion

**Check 6: Recent Dismissals Threshold?**[^6_2]

- How many suggestions has user dismissed in last 5 minutes?
- If >5 dismissals: Back off (user clearly doesn't want suggestions now)
- Increase debounce delay 1.5x[^6_2]

**Outcome**:

- ✅ All checks pass: Proceed to LLM call
- ❌ Any check fails: Cancel, don't call LLM


#### 6.2 Post-Processor

Validates and refines LLM response:

**Validation step 1: Quality check**:

- Is suggestion grammatically correct?
- Does it make sense in context?
- Is it not just repeating what user already typed?

**Validation step 2: Repetition check**:

- Is this suggestion identical or very similar to recently shown suggestion?
- If yes: Skip (user already rejected this)
- Compare with last 10 suggestions

**Validation step 3: Confidence validation**:

- Is LLM's confidence score above threshold?
- Apply dynamic threshold (adjusted by mood, acceptance rate)
- If below threshold: Don't show

**Validation step 4: User still paused?**

- Check again: Did user start typing while waiting for LLM?
- If typing: Cancel suggestion (too late now)
- If still paused: Show suggestion

**Validation step 5: Length check**:

- Is suggestion reasonable length?
- Too short (<5 chars): Not worth showing
- Too long (>200 chars): Might overwhelm user
- Adjust based on user's learned preference

**Refinement steps**:

- Trim whitespace
- Ensure suggestion connects naturally to current text
- Apply any user-specific transformations (e.g., always lowercase "internet")


#### 6.3 Dynamic Threshold Tuner[^6_2]

Adjusts confidence threshold based on context:

**Base threshold**: 0.6 (60% confidence minimum)[^6_1]

**Mood adjustments**:[^6_1]

- Focused mood: +0.1 (be more selective, only high-quality)[^6_1]
- Rushed mood: -0.05 (be more helpful, show more suggestions)
- Frustrated mood: -0.1 (be extra helpful)[^6_1]
- Exploratory mood: 0.0 (standard)
- Casual mood: 0.0 (standard)
- Neutral mood: 0.0 (standard)

**Acceptance rate adjustments**:[^6_1]

- High acceptance (>70%): -0.05 (user trusts suggestions, show more)[^6_1]
- Low acceptance (<30%): +0.1 (user rejects often, be more selective)[^6_1]
- Average (30-70%): 0.0 (standard)

**Recent dismissal penalty**:[^6_2]

- 0-2 dismissals recently: 0.0
- 3-5 dismissals: +0.05 (be more careful)
- >5 dismissals: +0.15 (user is annoyed, only show very confident suggestions)

**Time-of-day adjustment**:

- Late night (after 10 PM): +0.05 (user might be tired, be more selective)
- Peak hours (9 AM - 5 PM): 0.0 (standard)

**Final threshold calculation**:

```
final_threshold = base_threshold (0.6)
                + mood_adjustment
                + acceptance_rate_adjustment
                + dismissal_penalty
                + time_adjustment
```

**Example calculation**:

- Base: 0.6
- Mood (focused): +0.1
- Acceptance rate (75%): -0.05
- Recent dismissals (2): 0.0
- Time (3 PM): 0.0
- **Final: 0.65** (need 65% confidence to show suggestion)


#### 6.4 Delivery Decision Maker

Final go/no-go decision:

**Decision inputs**:

- LLM confidence score
- Dynamic threshold
- User's current state (still paused?)
- Context stability (app still same?)

**Decision logic**:

```
IF confidence >= dynamic_threshold
AND user_still_paused
AND app_not_switched
AND not_duplicate_of_recent
THEN show_suggestion
ELSE don't_show
```

**Tracking**: Log every decision (showed/didn't show) for analytics and learning.

### Quality Optimization Pipeline[^6_1]

**Five-stage process**:

1. **Stage 1**: Should we suggest? (Pre-flight checks)
2. **Stage 2**: Build prompt (Context transformer)
3. **Stage 3**: LLM inference (Cloud or local)
4. **Stage 4**: Post-process (Validate, check repetition, filter, calculate final confidence)
5. **Stage 5**: Delivery decision (Confidence > threshold? Show suggestion : Don't interrupt)

**Result**: Only high-quality, contextually appropriate suggestions reach the user.

***

## Module 7: Feedback Learning Module

### Overview

The Feedback Learning Module observes how users respond to suggestions and continuously improves the system. Every accept, dismiss, or ignore is a signal that teaches the system what works and what doesn't.[^6_4]

### Core Responsibilities

#### 7.1 Acceptance Classifier[^6_4]

Categorizes how quickly and confidently a user accepts a suggestion:

**Classification categories**:

**Instant Accept (<500ms)**:[^6_4]

- User hits Tab almost immediately after suggestion appears
- **Signal**: Perfect suggestion! User knew immediately this is what they wanted
- **Learning**: This pattern (context + suggestion style) is ideal
- **Action**: Strongly reinforce similar suggestions in similar contexts

**Quick Accept (500-1500ms)**:[^6_4]

- User briefly reads suggestion, then accepts
- **Signal**: Good suggestion, user found it helpful
- **Learning**: This is the target acceptance speed
- **Action**: Reinforce this pattern

**Considered Accept (1500-3000ms)**:[^6_4]

- User reads, thinks, then accepts
- **Signal**: Acceptable but not perfect, user had to evaluate
- **Learning**: Suggestion was OK but could be better
- **Action**: Mild positive reinforcement, investigate if it could be improved

**Hesitant Accept (>3000ms)**:[^6_4]

- User hesitates long time before accepting
- **Signal**: Barely acceptable, user wasn't confident
- **Learning**: This suggestion type might not be great for this context
- **Action**: Weak positive reinforcement, investigate why hesitation

**Measurement**: Track time from suggestion_shown to acceptance_key_pressed.

#### 7.2 Dismissal Classifier[^6_4]

Categorizes how user rejects a suggestion:

**Classification categories**:

**Escape Dismiss**:[^6_4]

- User actively presses Escape key
- **Signal**: Strong rejection, user is actively annoyed
- **Learning**: This suggestion pattern is wrong for this context
- **Action**: Strongly avoid this pattern in similar contexts
- **Record**: Add to rejection patterns in LTM

**Click Dismiss**:[^6_4]

- User clicks X button or dismisses via mouse
- **Signal**: Clear rejection, deliberately dismissed
- **Learning**: Suggestion didn't match user's intent
- **Action**: Moderate negative reinforcement, learn from context

**Typed Over**:[^6_4]

- User ignores suggestion and continues typing
- **Signal**: Passive ignore, suggestion timing might be wrong or content irrelevant
- **Learning**: Could be wrong content OR wrong timing
- **Action**: Mild negative signal, investigate timing vs content

**No Action (Timeout)**:[^6_4]

- Suggestion shown but user doesn't interact (no accept, no dismiss)
- User continues working after pause
- **Signal**: Suggestion wasn't compelling enough to act on
- **Learning**: Low engagement, either irrelevant or low confidence
- **Action**: Weak negative signal


#### 7.3 Pattern Extractor[^6_4]

Identifies what makes suggestions succeed or fail:

**Positive patterns (from accepts)**:

**Content patterns**:

- Suggestion length: "User accepts suggestions of 20-40 characters in Gmail"
- Suggestion type: "User accepts completions more than corrections"
- Tone/formality: "User accepts professional tone in Word, casual in Slack"

**Context patterns**:

- App-specific: "User accepts 85% of suggestions in VS Code"
- Time-based: "User acceptance rate is 80% in morning, 60% in evening"
- Mood-based: "When focused, user accepts longer completions"

**Style patterns**:

- Vocabulary: "User instantly accepts suggestions using 'use' not 'utilize'"
- Structure: "User prefers short sentences in email"
- Formality: "User accepts formality level 0.7-0.8 in documents"

**Negative patterns (from dismissals)**:

**Rejection triggers**:

- "User always dismisses suggestions over 60 characters"
- "User rejects overly casual language in LinkedIn"
- "User dismisses emoji suggestions in Gmail"
- "User rejects verbose completions (containing 'however', 'furthermore')"

**Timing issues**:

- "User dismisses if suggestion appears while still typing"
- "User ignores suggestions in first 30 seconds of session"

**Context mismatches**:

- "User rejects formal suggestions in Slack"
- "User dismisses code completions that don't match language syntax"


#### 7.4 LTM Updater

Writes learned patterns to Long-Term Memory:

**What gets updated in LTM**:[^6_4]

**Acceptance metrics**:

- Overall acceptance rate (moving average)
- Per-app acceptance rates
- Per-mood acceptance rates
- Per-time-of-day acceptance rates

**Style preferences**:

- Preferred suggestion length (by app)
- Preferred formality level (by app)
- Preferred suggestion types (completion vs correction vs rewrite)

**Vocabulary learning**:

- Frequently accepted phrases → add to user's vocabulary
- Frequently rejected phrases → add to avoid list

**Pattern storage**:

- Positive patterns → reinforce in future
- Rejection patterns → avoid in future

**Update timing**: Batch update at session end  (efficient, doesn't interrupt active work).[^6_2]

#### 7.5 Rejection Pattern Store

Maintains a database of what NOT to suggest:

**Storage structure**:

```
{
  "rejection_patterns": [
    {
      "pattern": "too_verbose",
      "examples": ["however", "furthermore", "in addition to that"],
      "contexts": ["email", "chat"],
      "count": 15,
      "confidence": 0.9
    },
    {
      "pattern": "overly_casual",
      "examples": ["yeah", "gonna", "wanna"],
      "contexts": ["gmail", "linkedin"],
      "count": 8,
      "confidence": 0.85
    }
  ]
}
```

**Pattern detection**:

- Track what was suggested
- Track what was dismissed
- Find common elements in dismissed suggestions
- Build pattern rules

**Pattern application**:

- Before showing suggestion, check against rejection patterns
- If suggestion matches known rejection pattern in current context: Don't show
- Example: If user always rejects emoji in Gmail, filter out emoji suggestions in Gmail


### Learning Signals Summary[^6_4]

| Signal | Meaning | Action |
| :-- | :-- | :-- |
| Instant accept (<500ms) | Perfect! | Strongly increase similar |
| Quick accept (500-1500ms) | Good | This is target, reinforce |
| Hesitant accept (>3s) | Barely OK | Weak positive, investigate |
| Escape dismiss | Actively annoyed | Strongly avoid pattern |
| Click dismiss | Not wanted | Learn mismatch |
| Typed over | Ignored | Wrong timing? |

### Continuous Improvement Loop

**The cycle**:

1. Show suggestion based on current patterns
2. Observe user response (accept/dismiss/ignore + timing)
3. Classify response (instant, quick, hesitant, etc.)
4. Extract patterns (what worked, what didn't)
5. Update LTM with new learning
6. Apply learning to future suggestions
7. Repeat

**Result**: System gets smarter over time, personalized to each user's preferences.

***

## Module 8: Dual Mode Manager

### Overview

The Dual Mode Manager orchestrates between two distinct suggestion modes: **Default Mode** (automatic, always-on suggestions) and **Command Mode** (user-triggered, explicit AI help). It decides which mode is active and routes requests accordingly.[^6_2]

### Core Responsibilities

#### 8.1 Default Mode Controller[^6_2]

Manages automatic, context-aware suggestions:

**Behavior characteristics**:

- **Fully automatic**: No user action required[^6_2]
- **Context-aware**: Triggers on natural pause points[^6_2]
- **Predictive**: AI predicts what user wants to type next[^6_2]
- **Two-tier**: N-gram (fast) + LLM (intelligent)[^6_2]
- **Non-intrusive**: Appears only when contextually appropriate[^6_2]

**Triggering conditions**:

- User pauses typing (natural pause detected)
- At punctuation marks (period, comma, exclamation)
- After pressing Enter (new paragraph)
- During brief typing pauses (adaptive debounce 500-1200ms)[^6_2]

**What user sees**:

- Small overlay appears near cursor
- Section 1: 3 word suggestions (instant)[^6_2]
- Section 2: Sentence completion (after brief delay)[^6_2]
- User can Tab to accept, Esc to dismiss, or keep typing to ignore

**When NOT to trigger**:

- User typing very fast (>1.3x baseline speed, in flow)
- User just dismissed 3+ suggestions in a row
- Less than 10 characters typed
- In password field or banking app


#### 8.2 Command Mode Controller[^6_2]

Manages explicit, user-initiated AI assistance:

**Behavior characteristics**:

- **User-triggered**: Requires explicit action (click icon)[^6_2]
- **Prompt-driven**: User can provide custom instructions[^6_2]
- **Two interaction types**: Text modification (rewrite) or text generation (create)[^6_2]
- **More powerful**: Can handle complex requests[^6_2]

**Activation methods**:

1. User clicks Grammarly-style floating icon
2. User presses dedicated hotkey (Ctrl+Shift+A)
3. User right-clicks and selects "AI Keyboard"

**Two command sub-modes**:

**Sub-mode 1: Text Modification**:[^6_2]

- User selects text first
- Clicks icon
- Optional: Adds custom prompt ("Make this more formal")
- Optional: Selects preset action (Formal, Concise, Expand)
- AI rewrites selected text

**Sub-mode 2: Text Generation**:[^6_2]

- User clicks icon in empty field (no selection)
- Required: Adds prompt ("Write a professional email declining a meeting")
- AI generates text from scratch

**Preset quick actions**:[^6_2]

- Make Formal
- Make Concise
- Expand
- Fix Grammar
- Change Tone
- Add Examples


#### 8.3 Mode Detection and Switching

Automatically detects which mode should be active:

**Detection logic**:

**Trigger for Default Mode**:

- User is actively typing
- No icon click detected
- Natural pause point reached
- Context checks pass (sufficient text, in text field, app allowed)
- **Result**: Activate Default Mode suggestion pipeline

**Trigger for Command Mode**:

- Icon click event detected
- OR keyboard shortcut pressed
- User intent is explicit AI help
- **Result**: Activate Command Mode interface

**Mode priority**:

- Command Mode overrides Default Mode (explicit request takes priority)
- If Command Mode active, pause Default Mode suggestions
- After Command Mode action completes, return to Default Mode

**State management**:

- Track which mode is currently active
- Prevent mode conflicts (can't be in both simultaneously)
- Clean transition between modes


#### 8.4 Icon Click Handler

Manages the floating icon interaction:

**Icon behavior**:

- **Position**: Appears near cursor when in text field[^6_3]
- **Visibility**: Only visible when typing context is active[^6_3]
- **Click action**: Opens Command Mode interface[^6_2]
- **Hover effect**: Shows tooltip "AI Keyboard - Click for help"

**Click detection**:

- Monitor mouse click events on icon
- Determine click type (single click, right click)
- Capture current context (selected text, cursor position, current app)
- Open appropriate Command Mode interface

**Icon states**:

- **Hidden**: Not in text field
- **Idle**: Visible, no interaction
- **Hover**: Mouse over, show tooltip
- **Active**: Command

<div align="center">⁂</div>

[^6_1]: CORE_INTELLIGENCE_DESIGN.md

[^6_2]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md

[^6_3]: SOLUTION_DECISIONS.md

[^6_4]: ai_keyboard_sol.md

[^6_5]: GRAMMARLY_TECHNICAL_RESEARCH.md


---

# AI Keyboard - Complete Module Documentation

## Part 1 (Continued): Core Intelligence \& Dual Suggestion System (Modules 8-10)


***

## Module 8: Dual Mode Manager (Continued)

#### 8.4 Icon Click Handler (Continued)

Manages the floating icon interaction:

**Icon states**:

- **Hidden**: Not in text field
- **Idle**: Visible, no interaction
- **Hover**: Mouse over, show tooltip
- **Active**: Command Mode interface open
- **Processing**: AI generating response

**Context capture on click**:

- Selected text (if any)
- Text before cursor (500 chars)
- Text after cursor (100 chars)
- Current app and document
- User's typical style for this app

**Interface presentation**:[^7_5]
Opens tooltip/modal with:

- Prompt input field (optional for rewrite, required for generation)
- Preview of selected text (if rewriting)
- Quick action buttons (Formal, Concise, Expand)
- Generate/Cancel buttons


### Mode Comparison

| Aspect | Default Mode | Command Mode |
| :-- | :-- | :-- |
| **Trigger** | Automatic | User clicks icon |
| **Purpose** | Continue typing | Rewrite or generate |
| **User input** | None required | Optional/required prompt |
| **Speed** | Fast (<200ms) | Can be slower (500ms+) |
| **Suggestions** | Completions | Rewrites/generations |
| **Context** | Current typing | Selected text or empty field |


***

## Module 9: N-Gram Suggestion Engine

### Overview

The N-Gram Suggestion Engine provides **instant word-level predictions** using statistical language models. It's the "fast layer" that updates on every keystroke, showing 3 word suggestions in under 10ms.[^7_5]

### Core Responsibilities

#### 9.1 Trigram/4-Gram Language Model[^7_5]

Uses statistical patterns to predict next word:

**How n-grams work**:

- **Bigram**: Predicts word based on previous 1 word ("the" → "quick", "best", "first")
- **Trigram**: Predicts based on previous 2 words ("in the" → "morning", "end", "future")
- **4-gram**: Predicts based on previous 3 words ("at the end" → "of", "result", "user")

**Training data**:

- Pre-trained on large general corpus (books, articles, web text)
- Fine-tuned on domain-specific text (business writing, technical docs, casual chat)
- Continuously personalized with user's vocabulary[^7_5]

**Probability calculation**:

- For trigram "in the ___", model has probability distribution:
    - "morning": 0.15
    - "end": 0.12
    - "future": 0.10
    - "beginning": 0.08
    - etc.
- Return top 3 highest probability words

**Model size**: ~50-100MB (small, fast, runs locally)

#### 9.2 User Vocabulary Booster[^7_5]

Personalizes predictions with user's frequent words:

**How boosting works**:

- Base model predicts: ["end", "morning", "future"]
- User frequently uses "meanwhile" but model ranks it lower
- Boost user's vocabulary words by 1.5x probability
- Adjusted predictions: ["end", "meanwhile", "morning"]

**Vocabulary sources**:

- LTM: Frequently used words from past sessions[^7_2]
- STM: Words used repeatedly this session[^7_5]
- Domain terms: Professional jargon for user's role

**Boosting rules**:

- Technical terms: 2x boost (user is software engineer, boost "API", "database")
- Proper nouns: 3x boost (project names, company names)
- User-specific phrases: 2x boost ("looking forward to", "please let me know")

**Example**:

- User typing: "I will reach out to"
- Base model: ["you", "them", "her"]
- User frequently uses names: Boost "John", "Sarah"
- Final suggestions: ["John", "Sarah", "you"]


#### 9.3 Ultra-Fast Prediction (<10ms)[^7_5]

Optimized for real-time responsiveness:

**Speed optimizations**:

- **Model in RAM**: Loaded once at startup, kept in memory
- **Trie data structure**: Fast prefix lookup
- **Cached recent contexts**: Last 100 trigrams cached
- **Parallel computation**: Use multiple cores if available
- **No network calls**: Entirely local computation

**Latency breakdown**:

- Extract last 3 words: <1ms
- Lookup in n-gram model: 3-5ms
- Apply vocabulary boost: 1-2ms
- Sort and return top 3: 1ms
- **Total: ~8ms**[^7_5]

**Why speed matters**: N-gram suggestions update on EVERY keystroke. Any lag is noticeable. 10ms is imperceptible to humans.

#### 9.4 Word-Level Completion[^7_5]

Shows 3 single-word predictions:

**Display format**:

```
┌─────────────────────────────────────────┐
│  [analysis]  [report]  [findings]       │
└─────────────────────────────────────────┘
```

**Selection methods**:

- Tab key: Accept first word
- Number keys 1/2/3: Accept specific word
- Arrow keys + Enter: Navigate and select
- Keep typing: Ignore suggestions

**Smart word selection**:

- Most probable word first (leftmost)
- Consider recency (words used recently ranked higher)
- Consider context (document about finance → financial terms prioritized)


#### 9.5 Real-Time Update (Every Keystroke)[^7_5]

Continuously refreshes predictions:

**Update trigger**: Every character typed
**Update process**:

1. User types letter "t"
2. Context window shifts: ["in", "the"] → ["the", "t"]
3. N-gram model recalculates predictions
4. Display updates instantly (<10ms)

**Efficiency**: Only recalculate when context changes (new letter). If user backspaces and retypes same letter, use cached result.

**Visual feedback**: Smooth transition between suggestions (fade effect, not jarring replacement).

### N-Gram Advantages

**Pros**:

- ✅ Extremely fast (<10ms)[^7_5]
- ✅ Works offline (no network required)
- ✅ Low resource usage (small model)
- ✅ Privacy-preserving (all local)
- ✅ Familiar interaction (like phone keyboard)

**Cons**:

- ❌ Not context-intelligent (doesn't understand meaning)
- ❌ Single-word only (can't complete phrases)
- ❌ No cross-app awareness
- ❌ No mood adaptation

**Why we need both**: N-grams handle fast word completion, LLM handles intelligent sentence completion.[^7_5]

***

## Module 10: Two-Section UI Renderer

### Overview

The Two-Section UI Renderer displays suggestions in a **split interface**: Section 1 for instant n-gram words, Section 2 for intelligent LLM sentences. It coordinates visual presentation, user interaction, and smooth updates.[^7_5]

### Core Responsibilities

#### 10.1 Section 1: N-Gram Word Suggestions (Gboard-Style)[^7_5]

Top section displays fast word predictions:

**Visual design**:

- Horizontal row of 3 word buttons
- Clean, minimal design (rounded rectangles)
- Updates on every keystroke
- Always visible when typing (persistent)

**Layout**:

```
┌─────────────────────────────────────────────┐
│  [  analysis  ]  [  report  ]  [ findings ] │
└─────────────────────────────────────────────┘
```

**Styling**:

- Light gray background
- Medium-sized font (14-16px)
- Subtle shadow for depth
- Hover effect (highlight on mouse over)
- Active word (first suggestion) slightly emphasized

**Update behavior**:[^7_5]

- Updates instantly (<10ms) on every letter
- Smooth transitions (fade old words out, fade new words in)
- No jarring replacements
- Maintains position (doesn't jump around)


#### 10.2 Section 2: LLM Sentence Suggestions (AI-Powered)[^7_5]

Bottom section displays intelligent completions:

**Visual design**:

- Larger text area (can show full sentence)
- Gray italic text (distinguishes from n-gram suggestions)
- Updates on natural pauses (debounced)
- Appears after brief delay (~500-1200ms)[^7_5]

**Layout**:

```
┌─────────────────────────────────────────────────────┐
│  exceeded our expectations by demonstrating         │
│  significant growth across all market segments.     │
└─────────────────────────────────────────────────────┘
```

**Styling**:

- Slightly darker background (distinguishes from Section 1)
- Italic or lighter font weight
- Word-wrap for long suggestions
- Loading indicator while generating
- Confidence badge (optional: "95% confident")

**Update behavior**:[^7_5]

- Updates on typing pauses (adaptive 500-1200ms delay)
- Streaming display (words appear progressively if streaming enabled)
- Fade-in animation (smooth appearance)
- Replace previous suggestion (not accumulate)


#### 10.3 Section Coordinator

Manages both sections working together:

**Coordination rules**:

- Section 1 (n-gram) ALWAYS updates first (immediate)
- Section 2 (LLM) updates after debounce delay
- If user accepts Section 1 word: Cancel pending Section 2 update
- If user accepts Section 2 sentence: Clear Section 1 words

**Visual hierarchy**:

- Section 1 at top (seen first, accessed first)
- Thin divider line between sections
- Section 2 below (secondary, more powerful)

**Interaction priority**:

- Tab accepts Section 2 (LLM sentence) if present
- Number keys 1/2/3 accept Section 1 words
- Clear precedence, no ambiguity

**State management**:

- Track which section last updated
- Track which section user is interacting with
- Coordinate keyboard shortcuts (Tab for sentence, numbers for words)


#### 10.4 Parallel Update Manager[^7_5]

Handles simultaneous updates efficiently:

**Update strategy**:

- Section 1 updates immediately (n-gram is fast)
- Section 2 request starts in parallel (LLM takes longer)
- User sees Section 1 within 10ms[^7_5]
- User sees Section 2 within 50-200ms[^7_5]

**Process flow**:

```
User types → [Immediate: Update Section 1 with n-gram words]
           → [Parallel: Start LLM request for Section 2]
           → [10ms later: Section 1 visible]
           → [150ms later: Section 2 appears]
```

**Cancellation logic**:

- If user keeps typing: Cancel pending Section 2 LLM request
- If user accepts Section 1 word: Cancel Section 2 request
- If user switches apps: Cancel both sections, hide overlay

**Error handling**:

- If Section 2 fails (LLM error): Keep showing Section 1
- If Section 1 fails (n-gram error): Show Section 2 only
- If both fail: Hide overlay gracefully


### User Interaction Modes

#### Mode 1: Keyboard-Only Interaction

- `Tab`: Accept entire Section 2 sentence
- `1`, `2`, `3`: Accept respective Section 1 word
- `Esc`: Dismiss all suggestions
- `Ctrl + →`: Accept Section 2 word-by-word[^7_5]


#### Mode 2: Mouse Interaction

- Click on Section 1 word: Accept that word
- Click on Section 2 text: Enter edit mode[^7_5]
- Click icon: Show Command Mode interface
- Hover: Highlight hovered element


#### Mode 3: Hybrid (Keyboard + Mouse)

Users can mix both interaction methods fluidly

### Responsive Behavior

**Cursor position tracking**:

- Overlay follows cursor position
- Repositions if cursor moves (user editing different part)
- Hides if cursor leaves text field

**Window resizing**:

- Suggestions resize to fit available space
- Text wraps appropriately
- Font size adjusts on very small screens (mobile)

**App switching**:

- Immediately hide when user switches away from app
- Reappear when user returns (if context still valid)


### Visual Polish

**Animations**:

- Fade in (200ms) when appearing
- Fade out (150ms) when dismissing
- Smooth word transitions in Section 1 (100ms crossfade)
- Progressive appearance for Section 2 if streaming

**Accessibility**:

- High contrast mode support
- Screen reader announcements (announce suggestions)
- Keyboard navigation fully supported
- Adjustable font sizes

**Performance**:

- GPU-accelerated rendering (smooth animations)
- Debounced rendering updates (don't render every frame)
- Efficient DOM manipulation (virtual DOM or batched updates)

***

## Summary: Modules 1-10

These first 10 modules form the **core intelligence and suggestion foundation**:

- **Modules 1-4**: Memory and context intelligence (STM, LTM, Mood, Clipboard)
- **Modules 5-7**: AI orchestration and learning (LLM, Quality, Feedback)
- **Modules 8-10**: Dual suggestion system (Mode manager, N-gram, UI renderer)

**Next in Part 2 (Modules 11-20)**: Advanced performance modules, enhanced UI interactions, code mode, and platform-specific implementations.

***

**End of Part 1**

*Ready to continue with Part 2 covering Modules 11-20 when you're ready.*
<span style="display:none">[^7_1][^7_3][^7_4]</span>

<div align="center">⁂</div>

[^7_1]: CORE_INTELLIGENCE_DESIGN.md

[^7_2]: ai_keyboard_sol.md

[^7_3]: GRAMMARLY_TECHNICAL_RESEARCH.md

[^7_4]: SOLUTION_DECISIONS.md

[^7_5]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md


---

# AI Keyboard - Complete Module Documentation

## Part 2: Advanced Performance \& Enhanced UI (Modules 11-20)

> **Document Version:** 1.0
> **Date:** January 30, 2026, 1:13 AM IST
> **Scope:** Detailed explanation of Modules 11-20
> **Purpose:** Advanced features, performance optimization, and enhanced interactions

***

## Module 11: Direct Suggestion Editor

### Overview

The Direct Suggestion Editor allows users to **modify suggestions before accepting them**. Instead of accept-or-reject, users can click on a suggestion, edit it inline, then accept the modified version. This provides a middle ground between "perfect suggestion" and "wrong suggestion."[^8_5]

### Core Responsibilities

#### 11.1 in suggestion box Editing Mode[^8_5]

Transforms suggestion into editable text:

**Activation**:

- User clicks directly on suggested text (not icon)
- Suggestion becomes editable 
- Cursor appears in suggestion text
- Background changes to indicate edit mode (light blue highlight)

**Editing capabilities**:

- Full text editing (add, delete, modify characters)
- Cursor movement (arrow keys, Home/End)
- Selection (Shift + arrows, double-click word)
- Cut/copy/paste within suggestion

**Visual feedback**:

- Border around editable suggestion (indicates interactive)
- Blinking cursor shows edit position
- Character counter if suggestion has length limits
- Save/Cancel buttons appear

**Example flow**:

1. LLM suggests: "exceeded expectations significantly"
2. User clicks on suggestion
3. Suggestion becomes editable: "exceeded expectations significantly|" (cursor)
4. User edits: "exceeded expectations substantially"
5. User presses Enter to accept edited version

#### 11.2 Click-to-Edit Handler[^8_5]

Manages the transition from static to editable:

**Click detection**:

- Monitor mouse click events on suggestion overlay
- Distinguish between clicks on text vs clicks on icon vs clicks on buttons
- Click on text → Enter edit mode
- Click on icon → Show Command Mode
- Click outside → Dismiss suggestion

**Mode switching**:

- From display mode → edit mode (make text editable)
- From edit mode → display mode (if user clicks Cancel)
- From edit mode → accepted (if user clicks Save/Enter)

**State preservation**:

- Original suggestion stored (in case user clicks Cancel)
- Edited version tracked in real-time
- Cursor position remembered if user temporarily switches away

**Touch support** (mobile):

- Tap on suggestion to edit
- Long-press for additional options
- Swipe gestures to accept/dismiss


#### 11.3 Word-by-Word Acceptance (Ctrl+→)[^8_5]

Allows partial acceptance of suggestions:

**How it works**:

- User presses Ctrl + Right Arrow
- First word of suggestion is inserted
- Remaining suggestion updates to show rest
- User can repeat to accept word by word

**Example**:

- Suggestion: "exceeded expectations significantly this quarter"
- Ctrl+→ → Inserts "exceeded", remaining: "expectations significantly this quarter"
- Ctrl+→ → Inserts "expectations", remaining: "significantly this quarter"
- Ctrl+→ → Inserts "significantly", remaining: "this quarter"

**Benefits**:

- User can take part of suggestion they like
- Reject parts they don't like
- More granular control than all-or-nothing

**Smart word boundary detection**:

- Respects punctuation (stops at commas, periods)
- Handles contractions correctly ("don't" is one word)
- Considers hyphenated words ("well-known" as one unit or two, context-dependent)


#### 11.4 Edit Confirmation System[^8_5]

Manages saving or canceling edits:

**Confirmation methods**:

- **Enter key**: Accept edited suggestion[^8_5]
- **Save button**: Click to accept edited version
- **Tab key**: Accept and continue (alternative to Enter)

**Cancellation methods**:

- **Esc key**: Cancel edit, revert to original or dismiss entirely[^8_5]
- **Cancel button**: Click to exit edit mode
- **Click outside**: Auto-cancel and dismiss

**Auto-save conditions**:

- User starts typing new text (outside suggestion) → Auto-accept edit as-is
- User switches apps → Auto-cancel (don't accept)
- Timeout (30 seconds in edit mode) → Auto-cancel

**Feedback tracking**:

- If user edited suggestion before accepting: Log as "edited acceptance"
- Track what was changed (added words, removed words, replacements)
- Learn from edits: If user consistently changes "utilize" to "use", learn this preference

**Learning signals**:

- Frequent edits in same direction → Update LTM preferences
- Example: User always shortens long suggestions → Prefer shorter suggestions
- Example: User always makes suggestions more formal → Increase formality preference


### Use Cases

**Use Case 1: Almost Perfect**

- Suggestion: "I would be happy to assist you with this matter"
- User thinks: Too formal, but mostly good
- Edit to: "I'd be happy to help you with this"
- Accept edited version

**Use Case 2: Right Structure, Wrong Word**

- Suggestion: "The analysis demonstrates significant growth"
- User thinks: "shows" is better than "demonstrates"
- Edit to: "The analysis shows significant growth"
- Accept

**Use Case 3: Partial Acceptance**

- Suggestion: "exceeded our expectations significantly this quarter across all segments"
- User thinks: Too long
- Accept word-by-word: "exceeded our expectations significantly" (stop here)
- Manually type: "in Q1" (shorter than "this quarter across all segments")

***

## Module 12: Command Interface Handler

### Overview

The Command Interface Handler manages **user-triggered AI assistance** where users explicitly request help via prompts. Unlike automatic suggestions, this is a deliberate interaction where users tell the AI what they want.[^8_5]

### Core Responsibilities

#### 12.1 Custom Prompt Input[^8_5]

Provides text field for user instructions:

**Interface design**:

```
┌───────────────────────────────────────────┐
│  ✨ AI Keyboard                           │
├───────────────────────────────────────────┤
│  📝 Prompt (optional):                    │
│  ┌─────────────────────────────────────┐  │
│  │ Make this more professional...      │  │
│  └─────────────────────────────────────┘  │
│                                           │
│  [✨ Generate]  [❌ Cancel]                │
└───────────────────────────────────────────┘
```

**Prompt field features**:

- Multi-line text input (up to 500 characters)
- Placeholder text: "Describe how you want to modify this text..."
- Auto-resize as user types
- Character counter (shows remaining)
- Suggested prompts dropdown (common requests)

**Prompt examples shown to user**:

- "Make this more formal"
- "Make this more concise"
- "Expand with more detail"
- "Change tone to friendly"
- "Fix grammar and spelling"
- "Simplify language"

**Prompt validation**:

- Check prompt is not empty (for generation mode)
- Check prompt makes sense (basic NLP check)
- Warn if prompt is too vague ("make it better" → suggest more specific)


#### 12.2 Text Selection Detector[^8_5]

Identifies selected text for rewriting:

**Selection detection**:

- Monitor text selection events in current app
- Use UI Automation to get selected text range[^8_3]
- Store selected text content
- Track selection start and end positions

**Selection feedback**:

- Highlight selected text in interface
- Show character count of selection
- Preview first 100 characters if selection is long

**No selection handling**:

- If no text selected: Switch to Generation Mode
- Prompt field becomes required (not optional)
- Interface updates: "Generate new text" instead of "Rewrite"

**Selection edge cases**:

- Empty selection: Treat as no selection
- Very long selection (>5000 chars): Warn user, offer to summarize instead
- Multi-selection (non-contiguous): Use first selection only


#### 12.3 Rewrite Mode (Selected Text + Prompt)[^8_5]

Modifies existing text based on instructions:

**Process flow**:

1. User selects text: "hey john can u send that file"
2. User clicks AI icon
3. Interface opens with selected text preview
4. User optionally adds prompt: "Make this more professional"
5. User clicks Generate
6. LLM receives: Selected text + User prompt + Context
7. LLM generates: "Hi John, could you please send me that file?"
8. Result shown as replacement preview
9. User accepts or cancels

**Context included with rewrite**:

- Selected text (primary input)
- User's prompt (transformation instruction)
- App context (email, document, chat)
- User's typical style for this app (from LTM)
- Tone guidance based on app

**Rewrite strategies**:

- **Formality adjustment**: Make formal/casual
- **Length adjustment**: Make concise/expand
- **Tone change**: Friendly, professional, apologetic
- **Grammar fix**: Correct errors only
- **Simplification**: Use simpler words
- **Elaboration**: Add detail and examples


#### 12.4 Generate Mode (Empty Field + Prompt)[^8_5]

Creates new text from scratch:

**Process flow**:

1. User clicks AI icon (no text selected)
2. Interface opens in Generation Mode
3. Prompt field is required
4. User types prompt: "Write a professional email declining a meeting"
5. User clicks Generate
6. LLM receives: Prompt + Context (current app, user style)
7. LLM generates full text from scratch
8. Result shown in preview
9. User accepts (inserts at cursor) or cancels

**Context for generation**:

- User's prompt (what to generate)
- Current app (affects tone and format)
- Cursor position context (what comes before/after)
- User's typical style and vocabulary (from LTM)
- Document structure (if generating section for existing document)

**Generation use cases**:

- Email composition: "Write polite out-of-office reply"
- Report sections: "Write introduction paragraph for Q1 report"
- Code comments: "Write docstring for this function"
- Social media: "Write engaging LinkedIn post about AI"


#### 12.5 Preset Actions (Formal, Concise, Expand)[^8_5]

Quick-access common transformations:

**Preset buttons**:

```
┌─────────────────────────────────────────┐
│  Quick Actions:                         │
│  [📐 Formal] [✂️ Concise] [📏 Expand]   │
│  [✅ Fix Grammar] [🎭 Change Tone]       │
└─────────────────────────────────────────┘
```

**Preset 1: Make Formal**

- Removes contractions: "can't" → "cannot"
- Uses formal vocabulary: "get" → "obtain", "help" → "assist"
- Structured sentences: Longer, more complex
- Professional greetings/closings

**Preset 2: Make Concise**

- Removes filler words: "really", "very", "just"
- Shorter sentences
- Direct language: "I would like to" → "I want to"
- Eliminates redundancy

**Preset 3: Expand**

- Adds detail and examples
- Longer explanations
- More descriptive language
- Additional context

**Preset 4: Fix Grammar**

- Corrects spelling errors
- Fixes punctuation
- Subject-verb agreement
- Proper capitalization

**Preset 5: Change Tone**

- Opens tone selector: Friendly, Apologetic, Enthusiastic, Neutral
- Adjusts emotional coloring
- Modifies word choice for desired feeling

**How presets work**:

- User selects text
- Clicks preset button (no manual prompt needed)
- System auto-generates appropriate prompt
- Example: "Formal" button → Prompt: "Rewrite this in a professional, formal tone"
- LLM processes with auto-generated prompt

***

## Module 13: Adaptive Debounce System (Enhanced)

### Overview

The Adaptive Debounce System decides **when to trigger suggestions** by analyzing typing patterns and adjusting wait times dynamically. It prevents interrupting users in flow while ensuring timely help when paused.[^8_5]

### Core Responsibilities

#### 13.1 Typing Speed Profiler[^8_5]

Continuously learns user's typing characteristics:

**Baseline calculation**:

- Track WPM (words per minute) across all sessions
- Calculate moving average: 90% historical, 10% current session[^8_5]
- Store in LTM as user's "normal" typing speed
- Update after each session

**Real-time comparison**:

- Measure current typing speed in STM
- Compare to baseline: faster, normal, or slower
- Speed ratio: current_wpm / baseline_wpm

**Speed categories**:

- **Much faster** (>1.3x baseline): User is rushing, in flow
- **Faster** (1.1-1.3x): Above normal pace
- **Normal** (0.9-1.1x): Typical speed
- **Slower** (0.7-0.9x): Below normal, possibly thinking
- **Much slower** (<0.7x): Very slow, possibly struggling

**Why this matters**: Fast typers need shorter debounce (they pause briefly). Slow typers need longer debounce (their pauses don't mean they stopped).

#### 13.2 Inter-Key Interval Calculator[^8_5]

Measures time between keystrokes:

**What's measured**:

- Time between consecutive key presses
- Distribution of intervals (mostly short? mostly long? mixed?)
- Rhythm consistency (steady vs erratic)

**Interval patterns**:

- **Steady rhythm** (intervals similar): User in flow, focused typing
- **Erratic rhythm** (intervals vary widely): User thinking, pausing, uncertain
- **Increasing intervals** (getting slower): User slowing down, possibly tired
- **Decreasing intervals** (getting faster): User building momentum

**Use in debounce**:

- Steady fast rhythm → Longer debounce (don't interrupt flow)
- Erratic slow rhythm → Shorter debounce (user pausing often, might need help)
- Very short intervals → User typing rapidly, skip suggestions


#### 13.3 Natural Pause Detector (Sentence End Bonus)[^8_5]

Identifies ideal moments to suggest:

**Natural pause points**:

- **Period** (.): Strong pause signal → 0.5x debounce delay[^8_5]
- **Question mark** (?): Strong pause signal → 0.5x delay
- **Exclamation** (!): Strong pause signal → 0.5x delay
- **Comma** (,): Moderate pause signal → 0.7x delay
- **Newline** (Enter twice): Paragraph break → 0.4x delay (very strong signal)

**Why sentence end bonus**: Users naturally pause after completing a thought. Perfect moment to offer suggestion for next sentence.

**Example**:

- Base debounce: 900ms
- User types period: "The report shows growth."
- Pause detected → Debounce reduced to 450ms (0.5x)
- Suggestion appears faster at natural pause

**Anti-patterns (don't suggest)**:

- Mid-word typing: "analy|" (cursor in middle of "analysis")
- Rapid continuous typing: No pauses at all
- Immediately after backspace burst: User correcting, wait longer


#### 13.4 Recent Dismissal Penalty[^8_5]

Backs off when user rejects suggestions repeatedly:

**Dismissal tracking**:

- Count dismissals in last 5 minutes
- Track dismissal method (Escape = strong, typed-over = weak)
- Reset counter after 10 minutes of no dismissals

**Penalty tiers**:

- **0-2 dismissals**: No penalty, normal behavior
- **3-4 dismissals**: +0.2x debounce delay (slight back-off)[^8_5]
- **5-6 dismissals**: +0.5x debounce delay (moderate back-off)[^8_5]
- **7+ dismissals**: +1.0x debounce delay (significant back-off)[^8_5]

**Example**:

- Base delay: 800ms
- User dismissed 5 suggestions in 3 minutes
- Penalty: +0.5x → New delay: 1200ms
- System becomes less aggressive

**Why penalty matters**: If user keeps dismissing, they clearly don't want suggestions right now. System should be less intrusive.

**Penalty decay**:

- After 10 minutes: Reset to 0
- After 5 minutes: Reduce by half
- After successful accept: Reduce by 25%


#### 13.5 Mood-Based Delay Modifier[^8_5]

Adjusts debounce based on detected mood:

**Mood multipliers**:[^8_5]

- **Rushed mood**: 0.6x (faster response, user needs quick help)
- **Focused mood**: 1.4x (less interruption, user in deep work)
- **Frustrated mood**: 0.7x (more helpful, user struggling)
- **Exploratory mood**: 1.0x (neutral)
- **Casual mood**: 0.9x (slightly faster)
- **Neutral mood**: 1.0x (baseline)

**Example calculation**:

- User WPM: 85 (fast typer)
- Base delay: 700ms
- Mood: Focused (1.4x)
- Final delay: 700ms × 1.4 = 980ms

**Rationale**:

- Focused users don't want interruptions → wait longer
- Rushed users need quick help → suggest sooner
- Frustrated users need extra assistance → suggest sooner


#### 13.6 Context-Adaptive Timing (500-1200ms range)[^8_5]

Puts all factors together:

**Calculation formula**:

```
final_delay = base_delay 
            × typing_speed_factor 
            × mood_multiplier 
            × natural_pause_bonus 
            × dismissal_penalty
```

**Base delay by typing speed**:[^8_5]

- **Fast typer** (>80 WPM): 500ms base
- **Normal-fast** (60-80 WPM): 700ms base
- **Average** (40-60 WPM): 900ms base
- **Slow** (<40 WPM): 1200ms base

**Range limits**:

- Minimum: 400ms (never faster, gives user time to think)
- Maximum: 2000ms (never slower, too long to wait)

**Example scenario**:

- User: Average typer (900ms base)
- Current speed: Normal (1.0x)
- Mood: Focused (1.4x)
- Just typed period (0.5x natural pause bonus)
- No recent dismissals (1.0x)
- **Final**: 900 × 1.0 × 1.4 × 0.5 × 1.0 = **630ms**

***

## Module 14: Cross-App Time Decay Manager

### Overview

The Cross-App Time Decay Manager ensures that context from other apps **gradually becomes less relevant over time**. Content you viewed 2 minutes ago is highly relevant; content from 30 minutes ago is likely unrelated to your current task.[^8_5]

### Core Responsibilities

#### 14.1 Exponential Decay Calculator[^8_5]

Applies mathematical decay to relevance scores:

**Decay formula**:[^8_5]

```
current_relevance = initial_relevance × e^(-0.1 × minutes_elapsed)
```

**Decay curve**:

- **0 minutes**: 100% relevance (just switched from this app)
- **5 minutes**: 60% relevance (recent, probably related)
- **10 minutes**: 37% relevance (getting old)
- **20 minutes**: 13% relevance (likely different task now)
- **30 minutes**: 5% relevance (almost irrelevant)

**Why exponential**: Linear decay would be too slow initially. Exponential decay models reality better: first few minutes are critical, then relevance drops rapidly.

**Decay rate tuning**:

- Rate = 0.1 (default, moderate decay)
- For related apps in same workflow: Rate = 0.05 (slower decay)
- For unrelated apps: Rate = 0.15 (faster decay)


#### 14.2 Relevance Threshold Enforcer (<0.3 = Fade Out)[^8_5]

Decides when to stop including cross-app context:

**Threshold tiers**:

- **≥0.65**: High relevance → Include full context in LLM prompt[^8_1]
- **0.45-0.64**: Medium relevance → Brief mention only
- **0.30-0.44**: Low relevance → Include only if nothing better available
- **<0.30**: Irrelevant → Exclude completely[^8_5]

**Example decision flow**:

- Chrome context (financial spreadsheet viewed 3 minutes ago): Relevance 0.72
    - Action: Include full summary in LLM prompt
- Slack context (chat about lunch plans, 25 minutes ago): Relevance 0.09
    - Action: Exclude, not relevant to current document writing

**Dynamic thresholds by app**:

- Writing app → Higher threshold (0.65), need very relevant cross-app context
- Code editor → Lower threshold (0.45), more tolerant of related code files
- Email → Medium threshold (0.55), emails often reference other content


#### 14.3 Context Freshness Tracker[^8_5]

Monitors age of all cross-app contexts:

**What's tracked per context**:

- **Captured timestamp**: When user was in that app
- **Switched-from timestamp**: When user left that app
- **Age in minutes**: Current time - captured time
- **Last relevance score**: Most recent calculated relevance
- **Decay rate**: How fast this context should fade

**Continuous monitoring**:

- Background task runs every 30 seconds[^8_1]
- Recalculates relevance for all stored contexts
- Prunes contexts below minimum threshold (0.1)
- Logs context age distribution for analytics

**Context aging states**:

- **Fresh** (0-5 min): Maximum relevance, actively considered
- **Recent** (5-15 min): High relevance, still useful
- **Stale** (15-30 min): Low relevance, rarely included
- **Expired** (>30 min): Removed from STM


#### 14.4 Minutes-Elapsed Monitor[^8_5]

Precise time tracking for decay calculations:

**Time measurement**:

- Capture context timestamp with millisecond precision
- Calculate elapsed time dynamically each time context is evaluated
- Handle timezone and daylight saving correctly
- Account for system sleep (pause decay during sleep)

**Sleep handling**:

- If system wakes from sleep: Check time gap
- If gap >30 minutes: Mark all contexts as expired
- If gap <30 minutes: Adjust timestamps to account for sleep

**Use cases**:

- User in Chrome (Q1 data spreadsheet) at 2:00 PM
- User switches to Word at 2:02 PM
- At 2:05 PM (3 min later): Chrome context relevance = 0.74 (high)
- At 2:20 PM (18 min later): Chrome context relevance = 0.16 (low)
- At 2:35 PM (33 min later): Chrome context relevance = 0.03 (expired, removed)


### Cross-App Context Example

**Scenario**:

- 2:00 PM: User in Chrome viewing "Q1 Financial Report.pdf"
- 2:02 PM: User copies "Revenue: \$2.4M (+15%)"
- 2:03 PM: User switches to Word, opens "Q1_Report.docx"
- 2:04 PM: User starts writing Conclusion section

**Context at 2:04 PM (1 minute after switch)**:

- Chrome context age: 4 minutes
- Relevance: 0.85 (high, very fresh)
- Clipboard age: 2 minutes
- Clipboard relevance: 0.92 (extremely fresh)
- **Decision**: Include both in LLM prompt with full detail

**Context at 2:25 PM (22 minutes after switch)**:

- Chrome context age: 25 minutes
- Relevance: 0.11 (very low, stale)
- Clipboard age: 23 minutes
- Clipboard relevance: 0.08 (expired)
- **Decision**: Exclude both, no longer relevant

***

## Module 15: Request Deduplicator

### Overview

The Request Deduplicator prevents making **identical LLM requests multiple times** by caching the most recent request and response. This saves API costs and reduces latency when context hasn't meaningfully changed.[^8_5]

### Core Responsibilities

#### 15.1 Context Hasher

Creates unique fingerprint of each LLM request:

**What's hashed**:

- Text before cursor (primary input)
- Current app and document
- User mood (focused, rushed, etc.)
- Relevant cross-app context (if any)
- Clipboard content (if included)

**Hash algorithm**:

- SHA-256 or similar cryptographic hash
- Produces fixed-length fingerprint (e.g., "a3f7e9...")
- Same context always produces same hash
- Tiny context change produces completely different hash

**Example**:

- Context: "Writing in Word, text='In conclusion, our', mood=focused"
- Hash: "a3f7e921d4..."
- Same context again → Same hash → Detect duplicate

**What's NOT hashed** (to allow fuzzy matching):

- Exact timestamps (minute-level granularity OK)
- Micro-level typing speed variations
- Very minor STM fluctuations


#### 15.2 Last Request Cache (Single Request Only)[^8_5]

Stores only the most recent request/response pair:

**Cache structure**:

```
{
  "request_hash": "a3f7e921d4...",
  "request_context": {...},
  "response": {
    "suggestion": "analysis demonstrates significant growth",
    "type": "completion",
    "confidence": 0.87
  },
  "timestamp": "2026-01-30T01:05:23Z",
  "ttl": 10000  // milliseconds
}
```

**Cache policy**:

- **Size**: Only 1 request cached (not multiple)
- **Replacement**: New request overwrites old cache
- **TTL**: 10 seconds (cache expires after 10s)
- **Invalidation**: Cleared on significant context change

**Why only 1 request**:

- User context changes rapidly while typing
- Caching multiple requests would mostly contain stale entries
- Single recent request is sufficient for common duplicate scenarios


#### 15.3 Duplicate Prevention (No Stale Cache)[^8_5]

Detects and prevents redundant API calls:

**Duplicate detection**:

1. New suggestion request comes in
2. Hash the context
3. Compare to cached request hash
4. If hash matches AND cache not expired: Return cached response
5. If hash different OR cache expired: Make new API call

**Example scenario**:

- User types "In conclusion, our"
- System triggers LLM request
- Before LLM responds, user pauses longer
- Debounce triggers another request with SAME context
- Hash matches → Return pending/cached response, don't make 2nd API call

**Cache miss scenarios**:

- User types one more character: Context changed, hash different
- Cache expired (>10s old): Too stale, make fresh request
- Context significantly changed: Different hash

**Benefits**:

- Saves money: No duplicate API calls
- Saves time: Instant response from cache
- Reduces server load: Fewer requests

**Anti-pattern avoided**: Don't cache multiple old requests that would become stale.[^8_5]

***

## Module 16: Latency Monitor

### Overview

The Latency Monitor tracks **how long each component takes** and alerts when performance degrades. It's the system's "performance watchdog" ensuring suggestions remain fast.[^8_5]

### Core Responsibilities

#### 16.1 Real-Time Latency Tracking[^8_5]

Measures duration of each operation:

**Tracked components**:

- N-gram suggestion generation: Target <10ms[^8_5]
- Local LLM inference: Target 50-150ms[^8_5]
- Cloud LLM API call: Target 100-300ms[^8_5]
- Context preparation: Target <20ms
- UI rendering: Target <5ms
- Total end-to-end: Target <200ms[^8_5]

**Measurement points**:

- Start timestamp: When operation begins
- End timestamp: When operation completes
- Duration: End - Start (in milliseconds)
- Success/failure status

**Example measurement**:

```
{
  "operation": "cloud_llm_call",
  "start": "2026-01-30T01:05:23.100Z",
  "end": "2026-01-30T01:05:23.285Z",
  "duration_ms": 185,
  "target_ms": 300,
  "status": "success",
  "within_target": true
}
```


#### 16.2 Model Performance Logger[^8_5]

Tracks LLM-specific metrics:

**Per-model metrics**:

- Average latency (rolling 100 requests)
- P50, P95, P99 latencies (percentiles)
- Success rate (% of successful requests)
- Error rate (% of failed requests)
- Timeout rate (% of requests exceeding 2s)

**Comparison tracking**:

- Cloud LLM vs Local LLM performance
- Current session vs historical baseline
- Different times of day (API might be slower at peak hours)

**Model selection data**:

- If Cloud LLM consistently >500ms: Switch to local by default[^8_5]
- If Local LLM <100ms reliable: Prefer local for speed
- Dynamic adjustment based on real-world performance


#### 16.3 Threshold Alert System[^8_5]

Triggers warnings when performance degrades:

**Alert thresholds**:

- **Warning**: Operation exceeds target by 50%
    - Example: Cloud LLM takes 450ms (target: 300ms)
- **Error**: Operation exceeds target by 100%
    - Example: Cloud LLM takes 600ms (target: 300ms)
- **Critical**: Operation exceeds timeout
    - Example: Cloud LLM takes >2000ms (timeout)

**Alert actions**:

- Log warning/error to console
- Increment degradation counter
- If 3+ errors in row: Auto-switch to fallback (cloud → local)
- If 10+ errors in session: Notify user of performance issues
- Send anonymous telemetry (if user opted in)

**User notification**:

- Subtle indicator in system tray: Yellow = warnings, Red = errors
- Optional: Toast notification "AI Keyboard is experiencing slow responses"
- Settings suggestion: "Would you like to switch to local LLM for faster responses?"


#### 16.4 Analytics Reporter[^8_5]

Aggregates performance data:

**Daily report generation**:

- Total suggestions shown
- Average latency per component
- Success rate percentage
- Fallback trigger count (how often cloud → local switch occurred)
- User acceptance rate correlation with latency

**Performance trends**:

- Week-over-week comparison
- Identify degradation patterns
- Peak usage time analysis
- Component bottleneck identification

**Data used for**:

- Continuous optimization (identify slow components)
- Capacity planning (anticipate scale needs)
- Model selection tuning (adjust thresholds)
- User experience improvements

***

## Module 17: Streaming Response Coordinator

### Overview

The Streaming Response Coordinator handles **token-by-token LLM responses** for faster perceived latency. Instead of waiting for the complete response, it displays partial results as they arrive.[^8_5]

### Core Responsibilities

#### 17.1 Token-by-Token Streaming[^8_5]

Processes LLM output incrementally:

**How streaming works**:

1. Send request to LLM with `stream: true` parameter
2. LLM returns response as stream of tokens (words/fragments)
3. Receive first token in ~50ms (much faster than full response)[^8_5]
4. Display token immediately
5. Continue receiving and displaying subsequent tokens
6. Complete when stream ends

**Token types**:

- **Word tokens**: Complete words ("analysis", "demonstrates")
- **Subword tokens**: Word fragments ("demonst", "rates")
- **Punctuation tokens**: (".", ",", "!")
- **Special tokens**: Start/end markers

**Example stream**:

```
Token 1 (50ms): "analysis"
Token 2 (60ms): " demonstrates"
Token 3 (70ms): " significant"
Token 4 (80ms): " growth"
Token 5 (90ms): "."
Stream end (95ms)
```


#### 17.2 Incremental Rendering (<50ms First Token)[^8_5]

Displays partial suggestions as they generate:

**Rendering strategy**:

- First token arrives: Show suggestion container with first word
- Subsequent tokens: Append to existing suggestion
- Final token: Mark suggestion as complete

**Visual feedback**:

- "Typing" animation: Cursor blinks at end of partial suggestion
- Loading indicator: Small spinner while streaming
- Progressive appearance: Words fade in as they arrive

**User experience benefit**:

- Feels instant: User sees first word in ~50ms[^8_5]
- Engaged waiting: Watching suggestion "type out" feels faster than static wait
- Early evaluation: User can start reading before completion

**Cancelation during stream**:

- User starts typing: Cancel stream immediately, discard partial suggestion
- User presses Esc: Stop stream, dismiss suggestion
- User accepts early: Accept partial suggestion, cancel remaining stream


#### 17.3 Perceived Latency Optimizer[^8_5]

Psychological tricks to make wait time feel shorter:

**Technique 1: Immediate feedback**

- Show loading indicator within 10ms of request
- User knows system is working

**Technique 2: Progressive disclosure**

- Show partial results (first few words) quickly
- User perception: "It's done!" even if still generating

**Technique 3: Animation**

- Typing animation makes wait feel active, not passive
- Active waiting feels 40% shorter than passive waiting (UX research)

**Technique 4: Skeleton screens**

- Show suggestion container immediately with placeholder
- Reduces "blank screen" perceived latency

**Measured impact**:

- Streaming: Perceived latency ~200ms
- Non-streaming: Perceived latency ~400ms
- Actual latency identical, perception differs significantly


### Streaming vs Non-Streaming Comparison

**Streaming**:[^8_5]

- ✅ First token: 50ms
- ✅ Complete response: 200ms total
- ✅ User sees progress immediately
- ✅ Can accept early
- ⚠️ More complex implementation

**Non-streaming**:

- ❌ Nothing shown until complete
- ❌ User waits full 200ms seeing nothing
- ❌ Feels slower even if same duration
- ✅ Simpler implementation

**Decision**: Use streaming by default, fallback to non-streaming if stream fails.[^8_5]

***

## Module 18: Parallel Processing Engine

### Overview

The Parallel Processing Engine runs **N-gram and LLM suggestions simultaneously** so users get fast word suggestions immediately while intelligent sentence suggestions generate in background.[^8_5]

### Core Responsibilities

#### 18.1 Async Task Coordinator[^8_5]

Manages multiple concurrent operations:

**Parallel execution**:

- Task 1: N-gram word suggestions (fast, <10ms)
- Task 2: LLM sentence suggestions (slower, 50-200ms)
- Both start at same time
- Results arrive at different times

**Coordination strategy**:

- Use async/await or promise-based concurrency
- Don't block on LLM while waiting for n-gram
- Handle task completion in any order
- Graceful handling if one task fails

**Example timeline**:

```
T=0ms:   User stops typing, trigger debounce
T=5ms:   Start N-gram task (async)
T=5ms:   Start LLM task (async, parallel)
T=12ms:  N-gram completes → Show Section 1 words
T=180ms: LLM completes → Show Section 2 sentence
```


#### 18.2 N-Gram + LLM Simultaneous Calls[^8_5]

Launches both engines at once:

**Simultaneous start**:

- Don't wait for N-gram to finish before starting LLM
- Both receive context at same time
- Process independently

**Resource efficiency**:

- N-gram uses local CPU (lightweight)
- LLM uses network/GPU (heavier)
- No resource contention (different resources)

**Why simultaneous**:

- Maximizes throughput
- Minimizes total latency
- User sees something fast (N-gram), then gets intelligent suggestion (LLM)


#### 18.3 Result Merger

Combines results from both engines:

**Merging logic**:

- Section 1 (top): N-gram word suggestions
- Section 2 (bottom): LLM sentence suggestion
- Both visible simultaneously in two-section UI[^8_5]

**Conflict resolution**:

- If N-gram and LLM suggest same word: Show in both sections (reinforcement)
- If suggestions contradict: Keep both, user chooses
- If LLM fails: N-gram still works (degradation)
- If N-gram fails: LLM still works (rare edge case)

**Quality check**:

- Ensure N-gram suggestions don't duplicate LLM sentence start
- Example: N-gram suggests "analysis", LLM suggests "analysis demonstrates growth"
    - OK to show both, user can choose word or full sentence


#### 18.4 Sequential Rendering (Fast First, Then Intelligent)[^8_5]

Displays results as they complete:

**Rendering order**:

1. N-gram completes first (~10ms) → Render Section 1 immediately
2. LLM completes later (~180ms) → Render Section 2 when ready

**User experience flow**:

```
T=10ms:  User sees Section 1 appear (3 word suggestions)
         User can interact immediately (press 1/2/3 to accept word)
T=180ms: User sees Section 2 appear below (sentence suggestion)
         User can now accept full sentence (Tab) or word (1/2/3)
```

**No blocking**: First result doesn't wait for second result. User gets progressive enhancement.

**Animation**:

- Section 1 fades in (100ms animation)
- Section 2 fades in later (100ms animation)
- Smooth, not jarring

***

## Module 19: Real-Time Error Detector

### Overview

The Real-Time Error Detector finds **spelling and grammar mistakes as user types** and shows red/blue underlines like Grammarly. It uses fast local n-gram models and rule-based checking, not LLM (for speed).[^8_5]

### Core Responsibilities

#### 19.1 SymSpell Spell Checker[^8_5]

Fast spelling error detection:

**SymSpell algorithm**:

- Symmetric delete spelling correction
- Extremely fast: <50ms for full document[^8_5]
- Pre-computed dictionary of correct words
- Edit distance calculation for misspellings

**How it works**:

1. User types "recieve"
2. SymSpell checks against dictionary
3. "recieve" not found
4. Calculates edit distance to known words
5. Finds "receive" (1 letter swap, distance=1)
6. Flags as spelling error

**Dictionary sources**:

- Base English dictionary (100k+ words)
- Technical terms (programming, business)
- User's personal dictionary (from LTM)
- Domain-specific additions

**False positive handling**:

- User can add word to dictionary ("Add to dictionary" option)
- Learn proper nouns (names, companies)
- Respect capitalization rules


#### 19.2 Rule-Based Grammar Checker[^8_5]

Pattern-matching grammar rules:

**Rule types**:

- **Subject-verb agreement**: "He walk" → "He walks"
- **Article usage**: "a apple" → "an apple"
- **Double words**: "the the" → "the"
- **Punctuation**: "Hello,world" → "Hello, world"
- **Capitalization**: "i am" → "I am"

**Rule engine**:

- 500+ grammar rules
- Pattern matching with regex
- Context-aware (considers surrounding words)
- Confidence scoring per rule

**Example rules**:

- Pattern: `[a-z] am [a-z]` → Flag capitalization
- Pattern: `\b(word)\s+\1\b` → Flag duplicate words
- Pattern: `a [aeiou]` → Suggest "an" instead

**Performance**: <30ms for typical paragraph[^8_5]

#### 19.3 Error Underline Renderer (Wavy Red/Blue)[^8_5]

Visual error indicators:

**Underline types**:

- **Red wavy**: Spelling errors
- **Blue wavy**: Grammar errors
- **Green wavy**: Style suggestions (optional)

**Rendering method**:

- Draw on transparent overlay window[^8_3]
- Position exactly under error text
- Follow text if document scrolls
- Disappear if error corrected

**Visual specifications**:

- Wave amplitude: 2 pixels
- Wave frequency: 5 waves per 20 pixels
- Line thickness: 1.5 pixels
- Color: \#FF0000 (red) or \#0000FF (blue)

**Interactive behavior**:

- Hover over underline: Show tooltip
- Click underline: Show fix options
- Right-click: Context menu (Ignore, Add to dictionary)


#### 19.4 Hover Tooltip Generator[^8_5]

Shows error details and suggestions:

**Tooltip content**:

```
┌─────────────────────────────────┐
│  ❌ Spelling error              │
├─────────────────────────────────┤
│  "recieve" → "receive"          │
│                                 │
│  Suggestions:                   │
│  • receive                      │
│  • relieve                      │
│  • deceive                      │
│                                 │
│  [✓ Fix]  [Ignore]  [Add Dict]  │
└─────────────────────────────────┘
```

**Tooltip triggers**:

- Mouse hover over underlined word (delay: 300ms)
- Keyboard shortcut (Ctrl+. on error)
- Right-click on error

**Tooltip actions**:

- **Fix**: Apply first suggestion
- **Ignore**: Hide this error this session
- **Add to dictionary**: Never flag this word again
- **See more**: Show additional suggestions (if >3)


#### 19.5 Direct Fix Injection[^8_5]

Applies corrections automatically:

**One-click fix**:

- User clicks "Fix" button
- System replaces error with suggestion
- Uses UI Automation or clipboard method[^8_3]
- Seamless, no copy-paste required

**Batch fix**:

- "Fix all spelling errors" option
- Applies all high-confidence fixes at once
- Skips ambiguous cases

**Undo support**:

- Track applied fixes
- Allow undo (Ctrl+Z or Undo button)
- Restore original text

***

## Module 20: Emoji Processor

### Overview

The Emoji Processor determines **when and which emoji to suggest** based on context and app type. It uses LLM to decide appropriateness, then maps to actual emoji characters.[^8_5]

### Core Responsibilities

#### 20.1 LLM-Directed Emoji Selection[^8_5]

AI decides if emoji fits:

**LLM task**:

- Input: Current text, app type, detected tone
- Output: JSON with emoji recommendation
- Example output: `{"emoji_type": "celebration", "position": "end", "confidence": 0.85}`

**Why LLM decides**:

- Context-aware: "Great job!" → celebration emoji appropriate
- Tone-sensitive: Formal email → no emoji
- Cultural awareness: Professional vs casual contexts

**Decision process**:

1. User typing: "That's awesome news, congratulations"
2. LLM analyzes: Positive tone, celebration context
3. LLM suggests: emoji_type="celebration"
4. System maps: "celebration" → [🎉, 🎊, 🥳]
5. Show emoji suggestions in Section 1

#### 20.2 Emoji Type Mapper[^8_5]

Converts semantic categories to actual emoji:

**Emoji categories**:[^8_5]

```
{
  "celebration": ["🎉", "🎊", "🥳", "👏"],
  "approval": ["👍", "✅", "👌", "💯"],
  "thinking": ["🤔", "💭", "🧐"],
  "love": ["❤️", "😍", "💕"],
  "sad": ["😢", "😞", "😔"],
  "laugh": ["😂", "🤣", "😆"],
  "gratitude": ["🙏", "💖", "☺️"]
}
```

**Mapping logic**:

- LLM says "celebration" → Return ["🎉", "🎊", "🥳"]
- Show top 3 most popular emoji for that category
- Order by: frequency of use, universal recognition

**Personalization**:

- Track which emoji user actually accepts
- Reorder suggestions based on user preference
- Example: User always picks 🎊 over 🎉 → Show 🎊 first


#### 20.3 Application-Based Policy Enforcer[^8_5]

Respects app appropriateness:

**Emoji policy by app**:[^8_5]

```
{
  "Slack": "ENABLED",           // Always OK
  "WhatsApp": "ENABLED",         // Always OK
  "Gmail": "CONDITIONAL",        // Only if informal tone detected
  "Word": "DISABLED",            // Never in documents
  "VS Code": "DISABLED",         // Never in code
  "LinkedIn": "CONDITIONAL"      // Only for casual posts
}
```

**Conditional logic (Gmail example)**:

- Detect recipient: Is it personal or professional domain?
- Analyze tone: Is email casual or formal?
- Check existing content: Are emoji already used in thread?
- Decision: If 2+ factors say "casual" → Allow emoji

**Override option**:

- User can force enable/disable per app in settings
- "Always allow emoji in Gmail" toggle


#### 20.4 Contextual Appropriateness Checker[^8_5]

Validates emoji makes sense:

**Appropriateness checks**:

- **Tone match**: Happy emoji for positive text, not negative
- **Cultural sensitivity**: Avoid emoji with ambiguous meanings
- **Professional context**: Skip playful emoji in serious topics
- **Redundancy**: Don't suggest emoji if text already expresses emotion strongly

**Example scenarios**:

**Appropriate**:

- Text: "Thanks so much for your help!"
- Emoji: 🙏 (gratitude) → ✅ Appropriate

**Inappropriate**:

- Text: "I regret to inform you that..."
- Emoji: 🎉 (celebration) → ❌ Inappropriate (tone mismatch)

**Redundant**:

- Text: "I'm extremely happy and excited about this!"
- Emoji: 😊 → ⚠️ Redundant (emotion already clear)

**Contextual awareness**:

- Preceding text: "Unfortunately" → Skip positive emoji
- Topic detection: Serious discussion → Skip playful emoji
- Formality level: High formality → Skip all emoji


### Emoji Integration Points

**In N-Gram Section (Section 1)**:

- Show emoji as 4th suggestion slot (word1, word2, word3, emoji)
- Only if emoji confidence >0.7

**In LLM Section (Section 2)**:

- Append emoji to end of sentence suggestion
- Example: "That sounds great 🎉"

**In Command Mode**:

- "Add emoji" preset action
- User selects text → Click "Add emoji" → System suggests appropriate emoji

***

## Summary: Modules 11-20

These 10 modules handle **advanced features and performance optimization**:

- **Modules 11-12**: Enhanced user interaction (edit suggestions, custom prompts)
- **Modules 13-15**: Performance optimization (adaptive timing, context decay, deduplication)
- **Modules 16-18**: Monitoring and coordination (latency tracking, streaming, parallelization)
- **Modules 19-20**: Additional features (grammar checking, emoji suggestions)

**Next in Part 3 (Modules 21-30)**: Platform-specific implementations (Desktop, Browser, Mobile), Code Mode, and backend infrastructure.

***

**End of Part 2**

*Ready to continue with Part 3 covering Modules 21-30 when you're ready.*
<span style="display:none">[^8_2][^8_4]</span>

<div align="center">⁂</div>

[^8_1]: CORE_INTELLIGENCE_DESIGN.md

[^8_2]: ai_keyboard_sol.md

[^8_3]: GRAMMARLY_TECHNICAL_RESEARCH.md

[^8_4]: SOLUTION_DECISIONS.md

[^8_5]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md


---

# AI Keyboard - Complete Module Documentation

## Part 3: Desktop Implementation \& Code Mode (Modules 21-30)

> **Document Version:** 1.0
> **Date:** January 30, 2026, 1:18 AM IST
> **Scope:** Detailed explanation of Modules 21-30
> **Purpose:** Platform-specific implementations and specialized code assistance

***

## Module 21: UI Automation Client

### Overview

The UI Automation Client is the **Windows accessibility interface** that reads text from applications, tracks cursor position, and detects text field changes. It's the "eyes" of the desktop application that sees what you're typing and where.[^9_3]

### Core Responsibilities

#### 21.1 Focus Tracker (Global Focus Change Events)[^9_3]

Monitors which application and text field has focus:

**Focus detection**:

- Registers global focus change event listener
- Windows UI Automation API: `AutomationElement.FocusChangedEvent`
- Fires whenever user clicks into different app or text field
- Provides element that gained focus

**Focus information captured**:

- **Process ID**: Which application (Chrome, Word, Slack)
- **Window title**: Document name or page title
- **Control type**: TextBox, RichEditBox, Document, etc.
- **Element properties**: Name, class, automation ID
- **Timestamp**: When focus changed

**Why global**: System-wide monitoring, not limited to single application. Works across all apps simultaneously.

**Use cases**:

- Detect app switches (user went from Word → Chrome)
- Detect text field activation (user clicked into email compose box)
- Trigger context updates (new app = new context)
- Reset typing state (new field = fresh start)

**Performance considerations**:

- Low overhead: Event-driven, not polling
- Filters: Ignore non-text elements (buttons, menus)
- Debounce: Ignore rapid focus changes (<100ms)


#### 21.2 Text Pattern Reader (ITextProvider Interface)[^9_3]

Reads text content from focused element:

**ITextProvider interface**:

- Windows UI Automation text pattern
- Provides access to text content in controls
- Supports: TextBox, RichTextBox, Document editors
- Returns: Full text, visible text, or text range

**Reading methods**:

- `GetText()`: Retrieve full text from control
- `GetVisibleRanges()`: Get text currently on screen
- `GetSelection()`: Get selected text
- `GetCaretRange()`: Get cursor position

**Text extraction example**:

- User typing in Word: "The quarterly report shows growth"
- UI Automation reads full document text
- Extracts relevant portion (last 500 chars before cursor)
- Returns to STM for context building

**Supported controls**:

- Standard text boxes (Notepad, browser inputs)
- Rich text editors (Word, Outlook email)
- Code editors (VS Code, Visual Studio)
- Web content (Chrome, Edge with accessible mode)

**Limitations**:

- Some apps don't expose text via UI Automation (security, custom rendering)
- Canvas-based editors harder to read
- Fallback: Use keyboard hooks to reconstruct typed text[^9_4]


#### 21.3 Document Range Extractor[^9_3]

Gets specific portions of text:

**Range types**:

- **Current paragraph**: Text from last newline to next newline
- **Current page**: Visible text on screen
- **Selection range**: Highlighted text
- **Relative ranges**: X chars before/after cursor

**Range extraction process**:

1. Get cursor position (caret index)
2. Calculate range boundaries (e.g., -500 chars, +100 chars)
3. Request text range from ITextProvider
4. Return extracted text with position markers

**Why ranges matter**: Full document might be huge (50 pages). Only need context around cursor for suggestions.

**Edge cases**:

- Cursor at document start: Can't get "text before"
- Cursor at document end: No "text after"
- Empty document: No text to extract
- Very large document: Limit range size for performance


#### 21.4 Caret Position Detector[^9_3]

Tracks exact cursor location:

**Position information**:

- **Character index**: Position in text (e.g., char 1247 of 5000)
- **Line number**: Which line cursor is on
- **Column number**: Position within line
- **Visual position**: X,Y screen coordinates

**Detection method**:

- `ITextProvider.CaretRange`: Gets cursor position
- Returns range with 0 length (start = end = cursor)
- Extract index from range

**Screen coordinates**:

- Convert text position to pixel position
- Use `GetBoundingRectangle()` on caret range
- Returns (X, Y, Width, Height) rectangle
- Used for positioning suggestion overlay

**Why screen coordinates matter**: Overlay must appear near cursor, not random location.

**Position updates**:

- Track on every focus change
- Track on typing (estimated: advance by chars typed)
- Verify periodically (every 500ms) via UI Automation
- Correct drift if estimation wrong


#### 21.5 Selection Monitor[^9_3]

Detects when user highlights text:

**Selection detection**:

- `ITextProvider.GetSelection()`: Returns selection ranges
- Can be zero selections (no text selected)
- Can be one selection (normal case)
- Can be multiple selections (advanced editors)

**Selection information**:

- **Start position**: Where selection begins
- **End position**: Where selection ends
- **Selected text**: Actual highlighted text
- **Length**: Character count

**Use cases**:

- Command Mode trigger: User selected text to rewrite[^9_5]
- Context clue: Selected text is focus of attention
- Grammar check: Only check selected portion
- Quote detection: User might be copying/referencing

**Selection events**:

- Monitor `SelectionChanged` event
- Fires when user selects/deselects text
- Update STM with current selection
- Enable "Rewrite" mode if selection present


#### 21.6 Bounding Rectangle Calculator[^9_3]

Gets visual boundaries of text elements:

**Rectangle calculation**:

- Input: Text range (e.g., word, sentence, cursor position)
- Output: (X, Y, Width, Height) in screen coordinates
- Method: `ITextRangeProvider.GetBoundingRectangles()`

**Use cases**:

- Position suggestion overlay near cursor
- Position error underlines under misspelled words[^9_5]
- Position tooltip near hovered text
- Calculate if text is visible on screen

**Multi-rectangle handling**:

- Long text might span multiple lines
- Method returns array of rectangles
- Use first rectangle for overlay positioning
- Use all rectangles for underline drawing

**Coordinate systems**:

- Screen coordinates (global, absolute)
- Client coordinates (relative to window)
- Convert between systems as needed

***

## Module 22: Application Context Detector

### Overview

The Application Context Detector identifies **what application is active and what task user is performing**. It classifies apps by type (email, code, chat) and detects purpose (writing report, coding, composing email).[^9_3]

### Core Responsibilities

#### 22.1 Process Monitor (psutil Integration)[^9_3]

Identifies active applications:

**Process detection**:

- Use `psutil` library (cross-platform process utilities)
- Get foreground window process ID
- Retrieve process name (e.g., "WINWORD.EXE", "chrome.exe")
- Get process command line (full path and arguments)

**Process information captured**:

```
{
  "pid": 12345,
  "name": "WINWORD.EXE",
  "exe": "C:/Program Files/Microsoft Office/Word.exe",
  "cmdline": ["WINWORD.EXE", "/n", "Q1_Report.docx"],
  "user": "current_user",
  "create_time": "2026-01-30T00:30:00Z"
}
```

**Process monitoring**:

- Poll active window every 500ms
- Detect process changes (app switches)
- Build process history (recently active apps)
- Cross-reference with known application database

**Known app database**:

- Maps process names to app types
- "WINWORD.EXE" → Microsoft Word → Document editor
- "chrome.exe" → Google Chrome → Browser
- "Code.exe" → VS Code → Code editor
- "Slack.exe" → Slack → Chat


#### 22.2 Window Title Parser[^9_3]

Extracts meaningful information from window titles:

**Title parsing strategies**:

**Document editors**:

- "Q1_Report.docx - Microsoft Word" → Document: "Q1_Report.docx"
- "* Untitled - Word" → Unsaved document
- "[Read-Only] Budget.xlsx" → Read-only mode

**Browsers**:

- "GitHub - Pull Request \#123 - Chrome" → Site: GitHub, Context: PR
- "Gmail - Inbox (3 new) - Chrome" → App: Gmail, State: Has new mail

**Code editors**:

- "main.py - myproject - Visual Studio Code" → File: main.py, Project: myproject
- "● index.js - VSCode" → Unsaved changes (● indicator)

**Communication**:

- "Slack - \#engineering" → App: Slack, Channel: \#engineering
- "John Doe - Microsoft Teams" → Chat with: John Doe

**Parsing patterns**:

- Regex patterns for common formats
- Delimiter detection (-, |, —)
- Filename extraction
- State indicators (*, ●, [Read-Only])


#### 22.3 App Type Classifier[^9_3]

Categorizes applications:

**Classification categories**:[^9_3]

- **document_editor**: Word, Google Docs, Notion, Pages
- **code_editor**: VS Code, PyCharm, Sublime, Vim
- **email**: Outlook, Gmail (browser), Thunderbird
- **chat**: Slack, Teams, Discord, WhatsApp
- **browser**: Chrome, Firefox, Edge, Safari
- **spreadsheet**: Excel, Google Sheets, Numbers
- **presentation**: PowerPoint, Google Slides, Keynote
- **note_taking**: OneNote, Evernote, Obsidian

**Classification logic**:

1. Check process name against known apps
2. If browser: Parse URL for web apps (gmail.com → email)
3. If unknown: Analyze window controls and structure
4. Default: "generic_text_editor"

**Why classify**: Different app types need different suggestion strategies. Email suggestions differ from code suggestions.[^9_1]

#### 22.4 Purpose Detector[^9_3]

Infers what user is doing:

**Purpose categories**:[^9_3]

- **report_writing**: Document editor + formal tone + data references
- **coding**: Code editor + programming file extensions
- **email_compose**: Email app + compose window
- **chat_messaging**: Chat app + active conversation
- **data_analysis**: Spreadsheet + calculations/charts
- **note_taking**: Note app + informal structure
- **research**: Browser + reading mode + no input

**Detection signals**:

- App type (primary signal)
- Window title (secondary signal)
- User activity (typing vs reading)
- Recent workflow (prior apps)

**Purpose inference examples**:

**Example 1: Report Writing**

- App: Microsoft Word
- Document: "Q1_Report.docx"
- Recent apps: Chrome (financial data), Excel (spreadsheet)
- Typing speed: Moderate, thoughtful
- **Inferred purpose**: report_writing

**Example 2: Coding**

- App: VS Code
- File: "api_handler.py"
- Window shows: Imports, function definitions
- Typing pattern: Short bursts, testing
- **Inferred purpose**: coding

**Example 3: Email Compose**

- App: Gmail (in Chrome)
- URL: "mail.google.com/mail/u/0/\#inbox?compose=new"
- Window title: "New Message"
- Text fields: To, Subject, Body
- **Inferred purpose**: email_compose


#### 22.5 Per-App Configuration Loader[^9_3]

Loads user preferences for each app:

**Configuration structure**:

```
{
  "app": "Microsoft Word",
  "type": "document_editor",
  "preferences": {
    "formality": 0.75,
    "suggestion_length": "medium",
    "types_enabled": ["completion", "correction"],
    "emoji_policy": "disabled",
    "custom_vocabulary": ["Q1", "YoY", "EBITDA"]
  }
}
```

**Loading process**:

1. Detect active app
2. Query LTM for app-specific config
3. Merge with global defaults
4. Apply to current session

**Configuration usage**:

- Formality level: Adjust LLM prompts[^9_1]
- Suggestion types: Filter what suggestions to show
- Emoji policy: Enable/disable emoji[^9_5]
- Custom vocabulary: Boost n-gram predictions[^9_5]

***

## Module 23: Overlay Renderer

### Overview

The Overlay Renderer creates **transparent floating windows** that display suggestions, icons, and error underlines over other applications. It's the visual layer that makes AI assistance visible.[^9_5][^9_3]

### Core Responsibilities

#### 23.1 Transparent Window Manager (WS_EX_LAYERED)[^9_3]

Creates see-through overlay windows:

**Windows API approach**:

- Create window with `WS_EX_LAYERED` extended style
- Makes window support transparency and alpha blending
- Set `WS_EX_TRANSPARENT` for click-through regions
- Use `SetLayeredWindowAttributes()` for opacity

**Transparency levels**:

- Suggestion overlay: 95% opaque (slightly transparent)
- Icon: 90% opaque
- Error underlines: 100% opaque (fully visible)
- Hover tooltips: 98% opaque

**Layering**:

- Overlay always on top (`HWND_TOPMOST`)
- Doesn't steal focus (can't become active window)
- Doesn't show in taskbar
- Doesn't show in Alt+Tab

**Performance optimizations**:

- Hardware acceleration (GPU rendering)
- Only redraw on changes (not continuous)
- Minimize overlay size (only as large as needed)


#### 23.2 Icon Renderer (Grammarly-Style Floating Icon)[^9_3]

Shows small circular icon near cursor:

**Icon design**:

- Small circle (24x24 pixels)
- AI sparkle icon (✨) or custom logo
- Subtle shadow for depth
- Pulsing animation when processing

**Icon positioning**:

- Appears slightly below and right of cursor
- Offset: +10px right, +20px down
- Follows cursor as user types
- Hides when cursor leaves text field

**Icon states**:

- **Idle**: Gray icon, ready state
- **Thinking**: Pulsing blue animation, processing
- **Suggestion available**: Bright blue, has suggestion
- **Error**: Red icon if errors detected

**Icon interactions**:

- Click: Open Command Mode interface[^9_5]
- Hover: Show tooltip ("AI Keyboard - Click for help")
- Right-click: Quick settings menu


#### 23.3 Two-Section Suggestion Overlay[^9_5]

Main suggestion display:

**Overlay structure**:

```
┌─────────────────────────────────────────┐
│ Section 1 (N-gram):                     │
│  [analysis] [report] [findings]         │
├─────────────────────────────────────────┤
│ Section 2 (LLM):                        │
│  demonstrates significant growth        │
│  across all market segments             │
└─────────────────────────────────────────┘
```

**Visual styling**:

- Light background (semi-transparent white)
- Rounded corners (8px radius)
- Subtle drop shadow
- Border: 1px light gray

**Positioning**:

- Near cursor (below if space, above if not)
- Horizontal alignment: Left-aligned with cursor
- Vertical offset: 30px below cursor line
- Screen edge detection: Adjust if would go off-screen

**Size**:

- Width: 300-500px (adaptive to content)
- Height: Auto (expands for content)
- Maximum height: 200px (scroll if needed)


#### 23.4 Tooltip/Suggestion Popup[^9_3]

Context-specific information displays:

**Tooltip types**:

- **Command prompt**: Text input for custom instructions[^9_5]
- **Error details**: Grammar/spelling explanation[^9_5]
- **Confidence badge**: Shows AI confidence level
- **Help text**: Keyboard shortcuts and tips

**Popup positioning**:

- Near trigger element (error, icon, suggestion)
- Smart positioning: Avoids screen edges
- Pointer arrow: Points to trigger element

**Popup interactions**:

- Auto-hide: Disappears after 5 seconds
- Dismissible: Click outside or press Esc
- Sticky: Some popups stay until explicit close


#### 23.5 Error Underline Drawer (for Corrections)[^9_5]

Draws wavy lines under errors:

**Drawing method**:

- GDI+ or Direct2D for smooth curves
- Calculate word boundaries
- Draw wavy line under each error
- Support multiple underlines simultaneously

**Wave specifications**:

- Amplitude: 2 pixels (peak-to-trough)
- Wavelength: 4 pixels per wave
- Thickness: 1.5 pixels
- Colors: Red (\#FF0000), Blue (\#0000FF), Green (\#00AA00)

**Drawing algorithm**:

```
For each error word:
  1. Get bounding rectangle of word
  2. Calculate baseline (bottom of rectangle)
  3. Draw sine wave along baseline
  4. Use error type to choose color
```

**Performance**:

- Batch draw multiple underlines
- Only redraw when text changes
- Use cached error positions


#### 23.6 Click Region Manager (Click-Through/Clickable Toggle)[^9_3]

Controls which parts of overlay are interactive:

**Interactive regions**:

- Suggestion text: Clickable (edit mode)[^9_5]
- Icon: Clickable (command mode)
- Buttons: Clickable (accept/dismiss)
- Error underlines: Clickable (show fix menu)[^9_5]

**Click-through regions**:

- Empty space: Click-through (to app below)
- Transparent background: Click-through
- Minimizes interference with normal app usage

**Region management**:

- Define hit-test regions programmatically
- Windows: Use `WM_NCHITTEST` message
- Return `HTTRANSPARENT` for click-through areas
- Return `HTCLIENT` for interactive areas

**Dynamic toggling**:

- Regions change based on state
- Suggestion showing: Text area clickable
- No suggestion: Entire overlay click-through except icon


#### 23.7 Position Synchronizer (Follows Caret)[^9_3]

Keeps overlay aligned with cursor:

**Position tracking**:

- Monitor caret position updates (from UI Automation)
- Calculate overlay position relative to caret
- Update overlay position when caret moves

**Movement smoothing**:

- Smooth animation: 100ms transition
- Prevents jarring jumps
- Acceleration curve: Ease-out

**Scroll handling**:

- Monitor document scroll events
- Adjust overlay position when content scrolls
- Hide if cursor scrolls off-screen

**Multi-monitor support**:

- Detect which monitor cursor is on
- Ensure overlay appears on correct screen
- Handle DPI differences between monitors

***

## Module 24: Keyboard Hook Manager

### Overview

The Keyboard Hook Manager captures **global keyboard input** to detect typing, shortcuts, and interaction patterns. It's the "ears" that listen to everything you type.[^9_4][^9_3]

### Core Responsibilities

#### 24.1 Global Keystroke Listener (pynput/keyboard)[^9_3]

Monitors all keyboard input:

**Hook installation**:

- Use `pynput.keyboard` library (cross-platform)
- Register global keyboard listener
- Receives all key press/release events
- Works across all applications

**Event information**:

```
{
  "key": "a",
  "type": "press",  // or "release"
  "timestamp": "2026-01-30T01:10:23.456Z",
  "modifiers": ["ctrl"],  // shift, alt, ctrl
  "vk_code": 65  // Virtual key code
}
```

**Filtering**:

- Ignore: System keys (Windows key, function keys when not relevant)
- Ignore: Media keys (volume, play/pause)
- Process: All text input keys
- Process: Navigation keys (arrows, home, end)
- Process: Editing keys (backspace, delete)

**Security**:

- Never log actual keystrokes to file (privacy)
- Only analyze patterns in RAM
- Don't capture in password fields[^9_4]


#### 24.2 Hotkey Handler[^9_5][^9_3]

Processes keyboard shortcuts:

**Registered hotkeys**:

**Tab**: Accept suggestion[^9_5]

- Only active when suggestion is visible
- Inserts suggested text at cursor
- Dismisses suggestion overlay

**Esc**: Dismiss suggestion[^9_5]

- Hides current suggestion
- Signals strong dismissal (negative feedback)[^9_2]
- Increases dismissal counter

**Ctrl+Shift+V**: Voice input[^9_2][^9_4]

- Opens voice recording interface
- Converts speech to text via Whisper
- Inserts transcribed text

**Ctrl+Shift+R**: Rewrite[^9_5]

- Triggers Command Mode with selected text
- Opens rewrite interface
- Requires text selection

**Ctrl+Shift+P**: Pause/Resume AI[^9_4]

- Toggles AI assistance on/off
- Shows paused indicator icon
- Disables all suggestions

**Ctrl+→**: Accept word-by-word[^9_5]

- Accepts first word of suggestion
- Updates suggestion to show remaining words
- Allows partial acceptance

**Number keys (1/2/3)**: Accept specific n-gram word[^9_5]

- 1: Accept first word suggestion
- 2: Accept second word suggestion
- 3: Accept third word suggestion

**Hotkey conflicts**:

- Check if app already uses hotkey
- Allow user to customize shortcuts
- Disable conflicting shortcuts in specific apps


#### 24.3 Typing Pattern Analyzer (Speed, Rhythm)[^9_1]

Extracts behavioral signals from typing:

**Speed measurement**:

- Count characters typed per minute
- Calculate WPM: (chars / 5) / minutes
- Compare to baseline (user's normal speed)
- Detect speed changes (faster/slower)

**Rhythm analysis**:

- Measure inter-key intervals[^9_5]
- Calculate interval variance
- Detect steady rhythm (consistent intervals)
- Detect erratic rhythm (varying intervals)

**Pause detection**:

- Identify pauses >500ms (thinking pauses)
- Identify pauses >2s (long breaks)
- Distinguish typing pauses from reading pauses

**Pattern signals**:

- Fast + steady: Flow state, focused
- Fast + erratic: Rushing, possibly stressed
- Slow + steady: Careful, deliberate
- Slow + erratic: Uncertain, exploring


#### 24.4 Inter-Key Interval Tracker[^9_5]

Precise timing between keystrokes:

**Interval measurement**:

- Record timestamp of each keypress (millisecond precision)
- Calculate: interval = current_timestamp - previous_timestamp
- Store last 50 intervals in circular buffer

**Statistics calculated**:

- Mean interval: Average time between keys
- Median interval: Middle value (robust to outliers)
- Standard deviation: Consistency measure
- 95th percentile: Typical maximum interval

**Usage in adaptive debounce**:

- Recent intervals inform expected pause duration[^9_5]
- Median interval × 3 = good debounce estimate
- If median = 150ms, debounce = 450ms

**Anomaly detection**:

- Very long interval (>3s): User distracted
- Very short intervals (<50ms): Rapid typing burst
- Increasing trend: User slowing down


#### 24.5 Backspace Frequency Tracker[^9_1]

Counts error corrections:

**Tracking method**:

- Count backspace/delete key presses
- Calculate ratio: backspaces / total keystrokes
- Track sequences (multiple backspaces in row)

**Frequency categories**:

- **Low** (<5%): Clean typing, few errors
- **Normal** (5-10%): Typical correction rate
- **High** (10-20%): Many corrections, possibly struggling
- **Very high** (>20%): Significant difficulty or frustration

**Sequence detection**:

- 3+ backspaces in row: Deleting word/phrase
- Alternating typing/backspace: Uncertain, trying different words
- Burst then backspace: Typed fast, realized error

**Mood signal**:

- High backspace rate → Frustrated or exploratory mood[^9_1]
- Used in mood calculation[^9_1]

***

## Module 25: Text Insertion Module

### Overview

The Text Insertion Module **injects suggested text into applications** when user accepts suggestions. It handles the technical challenge of inserting text across different app architectures.[^9_3][^9_5]

### Core Responsibilities

#### 25.1 Clipboard Manager (Save/Restore)[^9_3]

Protects user's clipboard during insertion:

**Problem**: Inserting text via paste (Ctrl+V) overwrites clipboard.

**Solution**:

1. Save current clipboard content
2. Place suggestion text in clipboard
3. Simulate Ctrl+V to paste
4. Restore original clipboard content

**Clipboard operations**:

```python
# Save
original_clipboard = get_clipboard_content()

# Use clipboard for insertion
set_clipboard(suggestion_text)
paste_via_ctrl_v()

# Restore
set_clipboard(original_clipboard)
```

**Timing**:

- Save/restore cycle: <50ms
- User never notices clipboard was temporarily changed
- Works across all clipboard formats (text, images, etc.)

**Edge cases**:

- Empty clipboard: Nothing to restore
- Large clipboard (image): Save pointer, not full data
- Clipboard access denied: Fallback to different method


#### 25.2 ValuePattern Setter (UI Automation)[^9_3]

Direct text insertion via UI Automation:

**SetValue method**:

- UI Automation ValuePattern interface
- `IValueProvider.SetValue(string value)`
- Directly sets text in control
- Faster than clipboard method
- Doesn't affect clipboard

**Supported controls**:

- Simple text boxes
- Single-line inputs
- Some rich text controls

**Limitations**:

- Not all controls support ValuePattern
- Some apps don't expose this interface
- Rich formatting lost (plain text only)

**Use case**:

- Preferred method when available (clean, fast)
- Fall back to clipboard if not supported


#### 25.3 Paste Simulator (Ctrl+V)[^9_3]

Simulates keyboard input to paste:

**Simulation method**:

- Send Ctrl key down event
- Send V key down event
- Send V key up event
- Send Ctrl key up event
- Small delays between events (10-20ms)

**Why simulate**:

- Works in all apps that support paste
- Most reliable cross-app method
- Handles rich formatting

**Alternatives tried (if Ctrl+V fails)**:

- Right-click context menu → Paste
- App-specific paste shortcuts
- SendKeys API


#### 25.4 pywinauto Wrapper Methods[^9_3]

High-level automation library integration:

**pywinauto capabilities**:

- Window control identification
- Text input simulation
- Button clicking
- Menu navigation

**Wrapper usage**:

- Identify target text control
- Use `.type_keys()` method for text insertion
- Handle special characters and key combinations

**When to use**:

- Complex applications with custom controls
- When UI Automation insufficient
- For testing and automation scenarios


#### 25.5 Direct Fix Injector (for Spelling/Grammar)[^9_5]

Replaces specific text ranges:

**Targeted replacement**:

- Identify error position (start, end indices)
- Select error text programmatically
- Replace with correction
- Maintain surrounding text

**Selection-based approach**:

1. Get error word boundaries
2. Set selection to error range (using UI Automation)
3. Insert correction (replaces selection)
4. Move cursor after correction

**Advantages**:

- Surgical precision (only changes error)
- Preserves formatting
- Maintains undo history in app

**Example**:

- Original: "The recieve was delivered"
- Error detected: "recieve" at position 4-11
- Select range [4:11]
- Replace with "receipt"
- Result: "The receipt was delivered"


#### 25.6 Insertion Validator[^9_3]

Verifies text was inserted correctly:

**Validation process**:

1. Perform insertion
2. Wait 50-100ms (app processing time)
3. Read text from control via UI Automation
4. Compare with expected result
5. Confirm success or retry

**Failure detection**:

- Text doesn't contain inserted content
- Cursor position didn't advance
- App became unresponsive

**Retry logic**:

- Attempt 1: UI Automation ValuePattern
- Attempt 2: Clipboard + Paste
- Attempt 3: SendKeys character-by-character
- Max retries: 3
- Final failure: Log error, notify user

***

## Module 26: Window Analyzer

### Overview

The Window Analyzer extracts **comprehensive context from entire application windows**, not just the immediate cursor area. It understands document structure, email threads, and UI layouts.[^9_4]

### Core Responsibilities

#### 26.1 Full Application Window Scraper[^9_4]

Captures complete window content:

**Scraping method**:

- UI Automation tree traversal
- Start from window root element
- Recursively visit all child elements
- Extract text from each control

**Content captured**:

- All visible text controls
- Button labels
- Menu items
- Static text (labels, headings)
- List items
- Table cell contents

**Performance**:

- Background scraping (doesn't block)
- Cached results (update every 5s or on significant change)
- Depth-limited (stop at 10 levels deep)

**Use case**:

- Email: Capture recipient, subject, thread messages[^9_4]
- Document: Capture headings, sections
- Web page: Capture page structure


#### 26.2 Visible Content Extractor[^9_4]

Filters to only visible elements:

**Visibility detection**:

- Check `IsOffscreen` property
- Check bounding rectangle (width/height > 0)
- Check Z-order (not obscured by other windows)
- Check `IsVisible` property

**Why only visible**:

- Hidden content not part of current context
- Reduces noise in context window
- Improves relevance
- Faster processing

**Example**:

- Email app: Hidden collapsed thread messages excluded
- Document: Content scrolled off-screen excluded
- Web page: Hidden dropdown menu items excluded


#### 26.3 Context Element Identifier[^9_4]

Finds specific meaningful elements:

**Email context extraction**:[^9_4]

- **Recipient**: Find "To:" field value
- **CC**: Find "Cc:" field value
- **Subject**: Find "Subject:" field value
- **Thread preview**: Find previous messages in thread
- **Sender**: Detect if reply or forward

**Code context extraction**:[^9_5]

- **Imports**: Find import statements at file top
- **Class definition**: Find enclosing class
- **Function definition**: Find current function
- **Comments**: Docstrings and inline comments

**Document context extraction**:

- **Title**: Document name from title bar
- **Headings**: H1, H2, H3 styles or formatting
- **Current section**: Heading before cursor
- **Page number**: If visible in UI

**Identification techniques**:

- Name matching: Elements with names like "Subject", "To"
- Role matching: ControlType.Edit, ControlType.Document
- Pattern matching: Regex for imports, function definitions
- Position matching: Top of document, near cursor


#### 26.4 Structure Parser (Headings, Sections)[^9_4]

Understands document hierarchy:

**Heading detection**:

- Style-based: "Heading 1", "Heading 2" styles
- Size-based: Larger font = heading
- Formatting-based: Bold + empty line after
- Semantic markup: HTML h1-h6 tags

**Section identification**:

- Build hierarchy: H1 contains H2s, H2s contain H3s
- Identify current section: Last heading before cursor
- Build breadcrumb: H1 > H2 > H3 trail

**Document outline**:

```
Q1 Performance Report
  ├─ Executive Summary
  ├─ Financial Results
  │   ├─ Revenue Analysis
  │   └─ Expense Breakdown
  └─ Conclusion  ← Cursor here
```

**Context usage**:

- Include in LLM prompt: "User writing Conclusion section of Q1 Performance Report"
- Helps LLM maintain document tone and flow

***

## Module 27: Cross-App Workflow Tracker

### Overview

The Cross-App Workflow Tracker monitors **multi-application tasks** and understands how they relate. It detects patterns like research→writing or data→report.[^9_4][^9_5]

### Core Responsibilities

#### 27.1 Active Window Monitor[^9_4]

Tracks which app has focus:

**Monitoring approach**:

- Poll foreground window every 500ms
- Windows API: `GetForegroundWindow()`
- Retrieve window handle, process ID, title
- Detect changes (app switches)

**Focus history**:

- Store last 20 app switches
- Include timestamps and duration
- Build session workflow timeline

**Example history**:

```
1:00 PM - 1:15 PM: Chrome (google.com/sheets - Q1 Data)
1:15 PM - 1:17 PM: Chrome (gmail.com - Inbox)
1:17 PM - 1:45 PM: Word (Q1_Report.docx)
```


#### 27.2 App Switch Logger[^9_4]

Records transitions between applications:

**Log entry structure**:

```
{
  "from_app": "Chrome",
  "from_context": "Q1 Financial Spreadsheet",
  "to_app": "Word",
  "to_context": "Q1_Report.docx",
  "timestamp": "2026-01-30T01:15:00Z",
  "duration_in_from": 900  // 15 minutes
}
```

**Switch analysis**:

- Frequency: How often user switches
- Duration: How long in each app
- Patterns: Common app pairs (Chrome→Word frequently)

**Workflow signals**:

- Quick switches: Copying data
- Long durations: Deep work
- Cycling: Research→Write→Research cycle


#### 27.3 Workflow Inference Engine with Time Decay[^9_5]

Determines if apps are related:

**Workflow detection**:

- Analyze recent app history
- Find related apps (shared data, similar topics)
- Apply time decay to relevance[^9_5]
- Build workflow context

**Workflow types detected**:

**Research-to-Writing**:[^9_4]

- Browser (reading articles) → Document editor (writing)
- Multiple browser tabs → Single document
- Pattern: Read, then write

**Data-to-Report**:[^9_4]

- Spreadsheet (analysis) → Document (report)
- Data copying detected
- Pattern: Calculate, then summarize

**Code-to-Documentation**:

- Code editor → Markdown/Doc editor
- Pattern: Write code, then document

**Email-Thread-Following**:

- Multiple emails in sequence
- Same subject line
- Pattern: Following conversation

**Relevance decay**:[^9_5]

- Recent workflow (last 5 min): High relevance (0.8+)
- Medium age (5-15 min): Medium relevance (0.4-0.7)
- Old workflow (>15 min): Low relevance (<0.3)


#### 27.4 Related App Detector[^9_4]

Identifies apps in same workflow:

**Relatedness factors**:

- **Time proximity**: Apps used within 10 minutes
- **Data sharing**: Clipboard activity between apps
- **Topic similarity**: Document titles, content keywords
- **Common workflows**: Known patterns (Sheets→Docs)

**Scoring**:

- Calculate relatedness score 0.0-1.0
- Time proximity: 0.3 weight
- Data sharing: 0.4 weight (strong signal)
- Topic similarity: 0.2 weight
- Known patterns: 0.1 weight

**Example**:

- App 1: Chrome ("Q1 Financial Data Spreadsheet")
- App 2: Word ("Q1_Report.docx")
- Time: 2 minutes apart
- Clipboard: Financial numbers copied
- **Relatedness score**: 0.92 (very likely same workflow)


#### 27.5 Context Flow Analyzer[^9_4]

Tracks how context moves across apps:

**Flow tracking**:

- What content moved between apps (clipboard)
- What topics persist across apps (keywords)
- What task continues across apps (same goal)

**Context inheritance**:

- When switching to new app, carry relevant context
- Example: Viewing spreadsheet → Writing document
    - Inherit: Data values, topic (Q1 results), task (report writing)
    - Discard: Spreadsheet formatting, cell references

**Flow visualization**:

```
Chrome (Q1 Data) 
   ↓ [copied: "Revenue $2.4M"]
Word (Q1 Report)
   ↓ [writing about: financial results]
Outlook (Send Report)
```


***

## Module 28: Code Context Analyzer

### Overview

The Code Context Analyzer provides **intelligent code completion by understanding project structure**. It analyzes multiple files, resolves imports, and understands code scope.[^9_5]

### Core Responsibilities

#### 28.1 Multi-File Context Gatherer[^9_5]

Analyzes related code files:

**File selection** (up to 5 files):[^9_5]

- Current file (always included)
- Imported files (dependencies)
- Files that import current file (dependents)
- Recent files in same directory
- Test files (if editing implementation)

**Context per file**:

- File path and name
- Imports/includes at top
- Class/function definitions
- Current function/scope in detail
- Other functions (signatures only)

**Why multi-file**:[^9_5]

- Understand full context of API calls
- Know available functions/classes
- Suggest imports when needed
- Maintain consistency across project

**Performance limit**:

- Maximum 5 files[^9_5]
- Prioritize by relevance
- Summarize non-critical files


#### 28.2 Import Resolver[^9_5]

Identifies dependencies:

**Import statement parsing**:

**Python**:

```python
import numpy as np
from sklearn.model_selection import train_test_split
```

→ Dependencies: numpy, sklearn

**JavaScript**:

```javascript
import React from 'react';
import { useState, useEffect } from 'react';
```

→ Dependencies: react

**Java**:

```java
import java.util.ArrayList;
import com.company.package.CustomClass;
```

→ Dependencies: java.util, com.company.package

**Resolution process**:

1. Parse import statements
2. Map to actual files in project
3. Check if files exist
4. Extract relevant symbols (functions, classes, types)

**Symbol availability**:

- Track what's imported and available
- Suggest only imported symbols
- Offer to add import if needed


#### 28.3 Related File Detector (Up to 5 Files)[^9_5]

Finds relevant project files:

**Detection strategies**:

**Strategy 1: Direct imports**

- File A imports File B → B is related

**Strategy 2: Import chain**

- File A imports B, B imports C → C is related (2 degrees)

**Strategy 3: Shared directory**

- Files in same module/package likely related

**Strategy 4: Naming patterns**

- `user_model.py` and `user_controller.py` related
- `Button.tsx` and `Button.test.tsx` related

**Strategy 5: Recent edits**

- Files edited in last 10 minutes likely related

**Ranking**:

1. Files directly imported (highest priority)
2. Files that import current file
3. Files in same directory
4. Recently edited files
5. Files with similar names

**Limit to 5**:[^9_5]

- Include top 5 most relevant
- Prevents context overload
- Keeps LLM prompt focused


#### 28.4 Relevant Symbol Extractor[^9_5]

Identifies functions, classes, variables:

**Symbol types**:

- **Functions**: Name, parameters, return type, docstring
- **Classes**: Name, methods, properties, inheritance
- **Variables**: Name, type (if annotated), initial value
- **Types**: Interface definitions, type aliases
- **Constants**: Constant values

**Extraction methods**:

- **AST parsing**: Parse code into Abstract Syntax Tree
- **Regex patterns**: Quick extraction for simple cases
- **Language server**: Use LSP for accurate analysis

**Context inclusion**:

- Current function: Full implementation
- Other functions in file: Signature only
- Imported functions: Name and type signature
- Related files: Key exports only

**Example extraction** (Python):

```python
# Full detail for current function
def calculate_revenue(start_date, end_date):
    """Calculate revenue for date range.
    
    Args:
        start_date: Start of period
        end_date: End of period
    Returns:
        float: Total revenue
    """
    # ... full implementation ...

# Summary for other functions
def calculate_expenses(start_date, end_date) -> float
def generate_report(data: dict) -> str
```


#### 28.5 Cursor Scope Finder (Current Function/Class)[^9_5]

Identifies code block containing cursor:

**Scope detection**:

- Parse file to find function/class boundaries
- Determine which function cursor is inside
- Identify class enclosing current function
- Find nested scopes (inner functions, lambdas)

**Scope information**:

- Scope type: Function, class, method, module-level
- Scope name: Function/class name
- Scope start line, end line
- Parent scopes: Class > Method > Nested function

**Example**:

```python
class UserService:  # Scope: class
    def get_user(self, user_id):  # Scope: method
        def validate_id(uid):  # Scope: nested function
            # Cursor here ←
            # Current scope: validate_id
            # Parent scope: get_user
            # Grandparent scope: UserService
```

**Context usage**:

- Suggest code relevant to current scope
- Know available local variables
- Understand function purpose from name and context
- Maintain indentation and style

***

## Module 29: Language Detector

### Overview

The Language Detector identifies **which programming language** is being used to apply language-specific rules and models. Supports 8+ languages.[^9_5]

### Core Responsibilities

#### 29.1 File Extension Mapper[^9_5]

Primary detection method:

**Extension mapping**:

```
{
  ".py": "Python",
  ".js": "JavaScript",
  ".jsx": "JavaScript React",
  ".ts": "TypeScript",
  ".tsx": "TypeScript React",
  ".java": "Java",
  ".cpp": "C++",
  ".c": "C",
  ".cs": "C#",
  ".go": "Go",
  ".rs": "Rust",
  ".rb": "Ruby",
  ".php": "PHP",
  ".swift": "Swift",
  ".kt": "Kotlin"
}
```

**Accuracy**: 99% (most reliable method)

**Edge cases**:

- No extension: Fallback to content analysis
- Ambiguous extensions (.h could be C or C++)
- Multiple languages in one file (HTML with embedded JS)


#### 29.2 Programming Language Identifier[^9_5]

Content-based detection (fallback):

**Detection heuristics**:

**Look for language-specific keywords**:

- Python: `def`, `import`, `class`, `:` after function
- JavaScript: `function`, `const`, `let`, `=>`, `{}`
- Java: `public class`, `void`, `new`, `;` endings
- C++: `#include`, `std::`, `namespace`

**Look for syntax patterns**:

- Indentation-based: Python
- Curly braces: C-family (C, C++, Java, JavaScript, C\#)
- Semicolons: C-family, not Python
- Type annotations: TypeScript, Java, C++

**Scoring approach**:

- Each keyword/pattern gives points to languages
- Language with highest score wins
- Require confidence >70% or mark as "Unknown"

**Example**:

```
Content: "function getData() { const result = await fetch(...); }"
Keywords: "function" (+5 JavaScript), "const" (+5 JS), "await" (+3 JS)
Syntax: curly braces (+2 JS), no semicolons (-1 JS)
Score: JavaScript = 14, others = 0
Detected: JavaScript (confidence: 95%)
```


#### 29.3 Multi-Language Support (8+ Languages)[^9_5]

Language-specific handling:

**Supported languages**:[^9_5]

1. **Python**: Indentation-aware, dynamic typing
2. **JavaScript**: Async/await, ES6+ features
3. **TypeScript**: Static typing, interfaces
4. **Java**: Strong typing, OOP patterns
5. **C++**: Templates, pointers, STL
6. **C\#**: .NET framework, LINQ
7. **Go**: Goroutines, channels, error handling
8. **Rust**: Ownership, borrowing, lifetimes

**Per-language models**:

- Each language has specific completion patterns
- Different syntax rules
- Different standard libraries
- Different naming conventions

**Language-specific suggestions**:

- Python: Suggest `snake_case` names
- JavaScript: Suggest `camelCase` names
- Java: Suggest `PascalCase` for classes
- C++: Suggest `std::` prefix for standard library


### Language Context in Prompts

**Prompt template** (language-specific):

```
Language: Python
Current scope: Function `calculate_revenue`
Available imports: pandas, numpy
Recent variables: df (DataFrame), start_date, end_date
Coding style: PEP 8, type hints

Code:
def calculate_revenue(start_date: str, end_date: str) -> float:
    df = load_data()
    # Cursor here ←

Suggest next line of code. Maintain Python style and type hints.
```


***

## Module 30: Code Suggestion Formatter

### Overview

The Code Suggestion Formatter presents **code completions in a syntax-aware way**. It handles single-line and multi-line suggestions while preserving indentation and style.[^9_5]

### Core Responsibilities

#### 30.1 Single-Line Suggestion Renderer[^9_5]

Simple completions (one line):

**Display format**:

```
┌──────────────────────────────────────┐
│  filtered_df = df[df['date'] >= start_date]  │
└──────────────────────────────────────┘
```

**Characteristics**:

- tooltip suggestions (appears on the caret position in a small block like structure)
- Gray italic text (distinguishes from actual code)
- Preserves syntax highlighting if possible
- Accepts with Tab key

**Use cases**:

- Variable assignments
- Function calls
- Simple statements
- Return statements


#### 30.2 Multi-Line Code Block Formatter[^9_5]

Complex completions (multiple lines):

**Display format**:

```
┌─────────────────────────────────────────────┐
│  for index, row in df.iterrows():          │
│      if row['date'] >= start_date:         │
│          total += row['revenue']           │
└─────────────────────────────────────────────┘
```

**Characteristics**:

- Block overlay (separate from code)
- Preserves line breaks
- Maintains indentation
- Shows complete logic structure

**Formatting rules**:

- Each line visible
- Indentation aligned to context
- Maximum 10 lines (truncate with "..." if longer)
- Scroll if needed


#### 30.3 Syntax-Aware Tooltip[^9_5]

Rich preview with syntax highlighting:

**Enhanced display**:

- Syntax coloring (keywords blue, strings green, etc.)
- Line numbers (for multi-line)
- Language indicator badge ("Python", "JavaScript")
- Confidence indicator

**Example**:

```
┌─────────────────────────────────────────┐
│ 🐍 Python                    ✓ 87%     │
├─────────────────────────────────────────┤
│ 1  result = df.groupby('category')      │
│ 2      .agg({'revenue': 'sum'})         │
│ 3      .reset_index()                   │
└─────────────────────────────────────────┘
```

**Tooltip trigger**:

- Hover over suggestion for 500ms
- Keyboard shortcut (Ctrl+I) to show details
- Auto-show if suggestion is complex


#### 30.4 Indentation Preserver[^9_5]

Maintains code formatting:

**Indentation detection**:

- Analyze cursor position indentation
- Count spaces/tabs before cursor
- Determine indentation level

**Suggestion adjustment**:

- Match suggestion indentation to cursor context
- If cursor at 8 spaces, suggestion starts at 8 spaces
- If multi-line, maintain relative indentation

**Example**:

```python
# Cursor context (2 levels indented)
class UserService:
    def get_user(self, user_id):
        # Cursor here (8 spaces) ←

# Suggestion preserves indentation
        if not user_id:
            return None
        return self.db.query(User).get(user_id)
```

**Language-specific**:

- Python: Spaces (typically 4)
- JavaScript: Spaces (2 or 4) or tabs
- Go: Tabs only
- Detect project convention and follow


### Code-Specific Acceptance

**Partial acceptance**:

- Accept full block: Tab key
- Accept line-by-line: Ctrl+↓ for next line
- Accept word-by-word: Ctrl+→[^9_5]

**Edit before accept**:

- Click suggestion to edit[^9_5]
- Modify as needed
- Press Enter to accept edited version

***

## Summary: Modules 21-30

These 10 modules handle **platform-specific implementation and specialized code assistance**:

- **Modules 21-25**: Windows desktop integration (UI Automation, keyboard hooks, text insertion)
- **Modules 26-27**: Window analysis and workflow tracking (context extraction, multi-app workflows)
- **Modules 28-30**: Code mode intelligence (multi-file analysis, language detection, code formatting)

**Next in Part 4 (Modules 31-40)**: Browser extensions, mobile keyboard, backend infrastructure, and privacy/security.

***

**End of Part 3**

*Ready to continue with Part 4 covering Modules 31-40 when you're ready.*

<div align="center">⁂</div>

[^9_1]: CORE_INTELLIGENCE_DESIGN.md

[^9_2]: ai_keyboard_sol.md

[^9_3]: GRAMMARLY_TECHNICAL_RESEARCH.md

[^9_4]: SOLUTION_DECISIONS.md

[^9_5]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md


---

# AI Keyboard - Complete Module Documentation

## Part 4: Browser, Mobile \& Backend Infrastructure (Modules 31-40)

> **Document Version:** 1.0
> **Date:** January 30, 2026, 1:22 AM IST
> **Scope:** Detailed explanation of Modules 31-40
> **Purpose:** Cross-platform implementation and backend systems

***

## Module 31: DOM Context Extractor

### Overview

The DOM Context Extractor reads **web page content from browser environments** to understand what the user is viewing and typing. It provides rich context for browser-based text fields (Gmail, Google Docs, web forms, social media).[^10_1]

### Core Responsibilities

#### 31.1 Full Page Text Reader[^10_1]

Extracts text content from entire page:

**DOM traversal**:

- Start from document root (`document.body`)
- Walk through DOM tree recursively
- Extract text from text nodes
- Skip: scripts, styles, hidden elements
- Result: Complete page text content

**Extraction method**:

- `element.textContent`: Gets all text in element and children
- Filter whitespace: Collapse multiple spaces, remove excess newlines
- Preserve structure: Maintain paragraph breaks, list structure

**Performance optimization**:

- Use `IntersectionObserver` to identify visible elements
- Priority: Visible content first, off-screen later
- Caching: Store extracted text, update on DOM changes
- Throttling: Limit extraction frequency (every 2 seconds max)

**Use cases**:

- Gmail: Extract email thread for context[^10_1]
- Google Docs: Get document content
- News articles: Understand what user is reading
- Social media: Context of post/comment being written


#### 31.2 Input Field Detector[^10_1]

Identifies active text input areas:

**Input field types detected**:

- Standard text inputs: `<input type="text">`
- Text areas: `<textarea>`
- Content editable: `<div contenteditable="true">`
- Rich text editors: TinyMCE, CKEditor, Quill, ProseMirror
- Google Docs canvas (custom rendering)
- Gmail compose (custom implementation)

**Detection method**:

- Monitor `focus` events on document
- Check `document.activeElement`
- Identify element type and properties
- Track input field changes

**Field metadata captured**:

```
{
  "element_type": "contenteditable",
  "field_purpose": "email_compose",  // Inferred
  "placeholder": "Type your message...",
  "max_length": null,
  "current_value_length": 145,
  "is_required": false,
  "form_context": "gmail_compose"
}
```

**Purpose inference**:

- URL analysis: `mail.google.com` → Email
- Placeholder text: "Search..." → Search box (ignore)
- Field name/ID: `message_body` → Message composition
- Parent form: Contact form, comment section, post editor


#### 31.3 Rich Context Analyzer[^10_1]

Extracts structured information beyond raw text:

**Email thread context**:[^10_1]

- **Recipients**: Parse To/CC/BCC fields
- **Subject line**: Extract from page structure
- **Previous messages**: Identify thread history
- **Quoted text**: Detect reply quotes
- **Sender**: Identify if reply or new message

**Email extraction example** (Gmail):

```
{
  "context_type": "email_compose",
  "to": ["john.doe@company.com"],
  "cc": [],
  "subject": "RE: Q1 Report Review",
  "thread_messages": [
    {
      "from": "john.doe@company.com",
      "date": "Jan 29, 2026",
      "preview": "Thanks for the report. Could you clarify..."
    }
  ],
  "is_reply": true,
  "original_sender": "john.doe@company.com"
}
```

**Form structure context**:

- **Form purpose**: Contact form, registration, survey
- **Other fields**: Related fields in form (name, email, phone)
- **Field relationships**: Dependencies between fields
- **Validation rules**: Required fields, format constraints

**Social media context**:

- **Post type**: New post, comment, reply
- **Thread context**: Original post being replied to
- **Platform**: Twitter, LinkedIn, Facebook, Reddit
- **Audience**: Public, friends, specific group

**Rich text editor context**:

- **Document structure**: Headings, lists, tables detected
- **Formatting state**: Current bold/italic/underline state
- **Style**: Font, size, color (for maintaining consistency)
- **Selection**: Selected text for formatting/replacement


#### 31.4 Selected Text Monitor[^10_1]

Tracks text selections in browser:

**Selection API**:

- `window.getSelection()`: Returns Selection object
- `selection.toString()`: Get selected text
- `selection.getRangeAt(0)`: Get range details
- Monitor `selectionchange` event

**Selection information**:

```
{
  "selected_text": "The quarterly results exceeded",
  "start_offset": 245,
  "end_offset": 273,
  "containing_element": "contenteditable div",
  "selection_direction": "forward",  // left-to-right
  "is_partial_word": false
}
```

**Use cases**:

- **Rewrite trigger**: User selected text to improve[^10_2]
- **Quote detection**: User selecting to quote/reply
- **Context focus**: Selected text is current attention point
- **Grammar check**: Check only selected portion

**Selection events**:

- `onselectionchange`: Fires when selection changes
- Debounced: Wait 200ms after selection stops changing
- Ignore micro-selections: Minimum 5 characters

***

## Module 32: Tooltip UI Renderer

### Overview

The Tooltip UI Renderer creates **browser-native suggestion interfaces** that blend seamlessly with web pages. Unlike desktop overlays, these render within the page DOM using web technologies.[^10_1]

### Core Responsibilities

#### 32.1 Page-Native Icon Injector[^10_1]

Inserts floating icon into web pages:

**Icon injection**:

- Create DOM element: `<div id="ai-keyboard-icon">`
- Style with CSS: Position absolute, high z-index
- Inject into document body
- Position near active text field

**Styling**:

```css
#ai-keyboard-icon {
  position: absolute;
  width: 28px;
  height: 28px;
  border-radius: 50%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  box-shadow: 0 2px 8px rgba(0,0,0,0.15);
  cursor: pointer;
  z-index: 999999;
  transition: transform 0.2s;
}
#ai-keyboard-icon:hover {
  transform: scale(1.1);
}
```

**Icon positioning logic**:

- Get active input field bounding box
- Position: Right edge of field, vertically centered
- Offset: +10px right, -2px up (slightly above baseline)
- Reposition on scroll/resize
- Hide when field loses focus

**Icon states** (visual feedback):

- **Idle**: Purple gradient, static
- **Thinking**: Pulsing animation
- **Ready**: Brighter color, subtle glow
- **Error**: Red tint, shake animation

**Compatibility**:

- Works on all websites (injected via content script)
- Respects page layout (doesn't break design)
- Higher z-index than most page elements
- Shadow DOM isolation (prevents CSS conflicts)


#### 32.2 Tooltip Renderer (Matches Browser Style)[^10_1]

Shows suggestions that look native to the page:

**Tooltip creation**:

- Shadow DOM for style isolation
- Match page theme (light/dark mode detection)
- Browser-native fonts and styling
- Smooth animations (CSS transitions)

**Tooltip structure**:

```html
<div class="ai-suggestion-tooltip">
  <div class="ai-suggestion-header">
    <span class="ai-badge">AI</span>
    <span class="ai-confidence">85%</span>
  </div>
  <div class="ai-suggestion-content">
    <div class="ai-ngram-section">
      <button>analysis</button>
      <button>report</button>
      <button>findings</button>
    </div>
    <div class="ai-llm-section">
      demonstrates significant growth across all segments
    </div>
  </div>
  <div class="ai-suggestion-actions">
    <button class="ai-accept">Accept</button>
    <button class="ai-dismiss">Dismiss</button>
  </div>
</div>
```

**Theme matching**:

- Detect page background color
- Light mode: White/light gray background
- Dark mode: Dark gray/black background
- Adjust text color for contrast
- Match border radius to page design

**Responsive design**:

- Mobile: Full-width tooltip at bottom
- Tablet: Medium-width, positioned near cursor
- Desktop: Compact, near cursor


#### 32.3 Suggestion Popup[^10_1]

Main suggestion display for browser:

**Popup positioning**:

- Calculate cursor position within text field
- Position popup below cursor (or above if no space)
- Horizontal: Align left with cursor
- Avoid viewport edges (adjust if needed)

**Popup features**:

- **Two sections**: N-gram words (top) + LLM sentence (bottom)[^10_2]
- **Keyboard navigation**: Tab/numbers to accept, Esc to dismiss
- **Click interaction**: Click word/sentence to accept
- **Edit mode**: Click to edit suggestion[^10_2]
- **Animation**: Fade in (150ms), fade out (100ms)

**Content editable handling**:

- `contenteditable` elements: Position relative to selection
- Rich text editors: Detect editor API (TinyMCE, CKEditor)
- Google Docs: Special handling for custom rendering
- Canvas-based editors: Position using coordinates

**Z-index management**:

- Higher than page content (z-index: 999998)
- Lower than modal dialogs (respect page modals)
- Higher than most fixed headers/footers

***

## Module 33: Native Messaging Bridge

### Overview

The Native Messaging Bridge connects **browser extensions to the desktop application**. Browser extensions can't access system APIs directly, so they communicate with the desktop app for AI processing.[^10_1]

### Core Responsibilities

#### 33.1 WebSocket Client to Desktop App[^10_1]

Establishes real-time communication:

**Connection setup**:

- Desktop app runs WebSocket server on `localhost:9876`
- Browser extension connects as client
- Bidirectional persistent connection
- Automatic reconnection on disconnect

**WebSocket protocol**:

```javascript
// Extension connects
const socket = new WebSocket('ws://localhost:9876');

// Send context
socket.send(JSON.stringify({
  type: 'context_update',
  data: {
    app: 'Gmail',
    text_before: 'Dear John, thank you for',
    text_after: '',
    url: 'https://mail.google.com',
    field_type: 'email_compose'
  }
}));

// Receive suggestion
socket.onmessage = (event) => {
  const message = JSON.parse(event.data);
  if (message.type === 'suggestion') {
    displaySuggestion(message.data);
  }
};
```

**Message types**:

**Extension → Desktop**:

- `context_update`: Current typing context
- `suggestion_accepted`: User accepted suggestion
- `suggestion_dismissed`: User rejected suggestion
- `command_mode`: User clicked icon for command mode
- `keepalive`: Ping to maintain connection

**Desktop → Extension**:

- `suggestion`: AI-generated suggestion
- `error`: Processing error
- `configuration`: Updated settings
- `status`: System status (active, paused, offline)

**Connection reliability**:

- Heartbeat every 30 seconds
- Auto-reconnect on disconnect (exponential backoff)
- Queue messages if disconnected (send when reconnected)
- Timeout after 5 failed reconnection attempts


#### 33.2 Context Serializer[^10_1]

Converts browser context to standard format:

**Serialization process**:

1. Extract context from DOM
2. Normalize to common schema
3. Compress if needed (for large contexts)
4. Serialize to JSON
5. Send via WebSocket

**Serialized context structure**:

```json
{
  "platform": "browser",
  "browser": "Chrome",
  "url": "https://mail.google.com/mail/u/0/?compose=new",
  "app_type": "email",
  "page_title": "Gmail - New Message",
  "text_context": {
    "before_cursor": "Dear John,\n\nThank you for your email. I wanted to follow up on",
    "after_cursor": "",
    "selection": null,
    "full_field_content": "..."
  },
  "structured_context": {
    "email_to": "john.doe@company.com",
    "email_subject": "RE: Q1 Report",
    "email_thread": [
      {"from": "john.doe@company.com", "preview": "..."}
    ]
  },
  "metadata": {
    "field_id": "compose_body_123",
    "timestamp": "2026-01-30T01:20:00Z"
  }
}
```

**Compression**:

- For contexts >10KB: Apply gzip compression
- Reduces network payload by 60-80%
- Desktop decompresses automatically

**Privacy filtering**:

- Redact passwords (if detected)
- Redact credit cards (pattern matching)
- Obey blocklist (banking sites)


#### 33.3 Response Handler[^10_1]

Processes suggestions from desktop app:

**Response parsing**:

```javascript
function handleResponse(message) {
  if (message.type === 'suggestion') {
    const { ngram_suggestions, llm_suggestion, confidence } = message.data;
    
    // Display in two-section UI
    renderNGramSection(ngram_suggestions);  // ["analysis", "report", "findings"]
    renderLLMSection(llm_suggestion);        // "demonstrates significant growth"
    
    // Position near cursor
    positionPopup();
    
    // Show with animation
    showSuggestion();
  }
}
```

**Error handling**:

```javascript
function handleError(message) {
  console.error('AI Keyboard error:', message.error);
  
  // User-facing error types
  if (message.error === 'rate_limit') {
    showNotification('Rate limit reached. Try again in 1 minute.');
  } else if (message.error === 'offline') {
    showNotification('Desktop app offline. Suggestions unavailable.');
  } else {
    // Silent error, don't disrupt user
    console.error('Unknown error:', message.error);
  }
}
```

**Response timing**:

- N-gram response: Expected in 10-20ms
- LLM response: Expected in 50-300ms
- Timeout: 3 seconds (show error if no response)
- Progressive display: Show n-gram immediately, LLM when ready

***

## Module 34: Custom IME (Input Method Editor)

### Overview

The Custom IME is the **Android keyboard implementation** that replaces the system keyboard with AI-enhanced typing. It's a full-featured keyboard with integrated AI suggestions.[^10_1]

### Core Responsibilities

#### 34.1 Keyboard UI Renderer[^10_1]

Displays keyboard interface on mobile:

**Keyboard layout**:

```
┌─────────────────────────────────────────┐
│  [analysis] [report] [findings]    (AI) │  ← Suggestion bar
├─────────────────────────────────────────┤
│  Q  W  E  R  T  Y  U  I  O  P          │
│   A  S  D  F  G  H  J  K  L            │
│    Z  X  C  V  B  N  M  ⌫              │
│  [?123] [🌐] [      Space     ] [↵]    │
└─────────────────────────────────────────┘
```

**Keyboard components**:

- **Letter keys**: QWERTY layout (default)
- **Suggestion bar**: Two-section AI suggestions[^10_2]
- **Action row**: AI actions (rewrite, formal, emoji)[^10_1]
- **Special keys**: Shift, backspace, space, return
- **Number/symbol layer**: Switch with ?123 button
- **Language switch**: 🌐 for multi-language

**Rendering technology**:

- Android View system
- Custom View classes for keys
- Canvas drawing for key backgrounds
- TextView for key labels

**Keyboard themes**:

- Light mode: White keys, dark text
- Dark mode: Dark keys, light text
- Custom colors: User preferences
- System theme matching: Follow Android theme


#### 34.2 Touch Input Handler[^10_1]

Processes finger taps and gestures:

**Touch event processing**:

- `onTouchEvent()`: Handle touch down, move, up
- Hit testing: Determine which key was touched
- Multi-touch: Support typing with multiple fingers
- Gesture detection: Swipe for space, long-press for symbols

**Key press feedback**:

- Haptic feedback: Vibration on key press (if enabled)
- Visual feedback: Key highlight animation
- Audio feedback: Click sound (if enabled)
- Key popup: Show enlarged key above finger

**Typing patterns**:

- Track typing speed (for adaptive debounce)[^10_2]
- Detect errors (lots of backspaces)[^10_3]
- Monitor rhythm (steady vs erratic)
- Feed to mood estimation[^10_3]

**Gesture support**:

- **Swipe left on backspace**: Delete word
- **Swipe right on space**: Accept suggestion
- **Swipe up on key**: Access alternate character
- **Long press**: Show symbol variants (e.g., long-press 'a' for á, à, ä)


#### 34.3 Two-Section Suggestion Bar[^10_2]

Mobile-optimized suggestion display:

**Suggestion bar layout**:

```
┌────────────────────────────────────────────────┐
│ Words: [analysis] [report] [findings]     (✨) │
│ AI: "demonstrates significant growth"          │
└────────────────────────────────────────────────┘
```

**Section 1: N-gram word suggestions**:

- 3 word buttons horizontally
- Tap to accept word
- Updates on every character typed
- Fast (<10ms)[^10_2]

**Section 2: LLM sentence suggestion**:

- Full sentence below word suggestions
- Scrollable if too long
- Tap to accept entire sentence
- Updates on pause (debounced)[^10_2]

**Mobile-specific adaptations**:

- Larger tap targets (44x44 dp minimum)
- Swipe gestures: Swipe right on suggestion to accept
- Compact layout: Limited vertical space
- Truncation: Show first 50 chars + "..." if longer

**AI badge icon** (✨):

- Tap to open Command Mode[^10_2]
- Shows available AI actions
- Quick access to rewrite, tone change


#### 34.4 AI Action Row[^10_1]

Quick AI actions in keyboard:

**Action buttons**:

```
┌─────────────────────────────────────────┐
│ [📝 Rewrite] [📐 Formal] [😊 Emoji] [🎤] │
└─────────────────────────────────────────┘
```

**Rewrite**: Transform selected text[^10_2]

- User selects text in app (long-press)
- Tap Rewrite button
- Options appear: Formal, Casual, Concise, Expand
- AI rewrites selected text

**Formal**: Make text more professional[^10_2]

- Quick preset action
- Applies formality transformation
- Example: "hey can u send that" → "Hello, could you please send that?"

**Emoji**: Suggest contextual emoji[^10_2]

- Based on what user is typing
- Shows relevant emoji options
- One-tap to insert

**Voice** (🎤): Speech-to-text[^10_1]

- Tap to start recording
- Shows waveform animation
- Transcribes with Whisper[^10_4]
- Inserts transcription

**Action visibility**:

- Context-aware: Only show relevant actions
- Email app: Show Formal action prominently
- Chat app: Show Emoji action prominently
- Text selected: Show Rewrite action

***

## Module 35: Voice Input Module

### Overview

The Voice Input Module provides **speech-to-text functionality** using local Whisper models. It converts spoken words to text with AI enhancement for punctuation and formatting.[^10_4][^10_1]

### Core Responsibilities

#### 35.1 Whisper Integration (faster-whisper)[^10_4]

Local speech recognition:

**Whisper models**:

- **Tiny** (39MB): Fast, lower accuracy, mobile-friendly
- **Base** (74MB): Good balance, recommended for most
- **Small** (244MB): Higher accuracy, desktop
- **Medium** (769MB): Very accurate, desktop only

**faster-whisper library**:

- Optimized Whisper implementation
- CTranslate2 backend (4x faster than original)
- CPU and GPU support
- Real-time factor: 0.1-0.3 (processes faster than real-time)

**Model loading**:

```python
from faster_whisper import WhisperModel

# Load model (one-time setup)
model = WhisperModel("base", device="cpu", compute_type="int8")

# Transcribe audio
segments, info = model.transcribe(
    audio_file,
    language="en",
    task="transcribe",
    vad_filter=True  # Voice activity detection
)

# Get text
transcription = " ".join([segment.text for segment in segments])
```

**Voice Activity Detection (VAD)**:

- Detects when user is speaking vs silence
- Filters out noise and pauses
- Improves accuracy by focusing on speech
- Reduces processing time


#### 35.2 Local Speech-to-Text[^10_1]

Processing pipeline:

**Audio capture**:

- Microphone input via system audio API
- Sample rate: 16 kHz (Whisper requirement)
- Format: 16-bit PCM mono
- Chunked recording: Process in 5-second segments

**Recording states**:

- **Idle**: Not recording
- **Listening**: Recording started, waiting for speech
- **Recording**: Speech detected, actively recording
- **Processing**: Converting speech to text
- **Complete**: Transcription ready

**Real-time feedback**:

- Waveform visualization: Show audio levels
- Speaking indicator: Visual cue when speech detected
- Timer: Show recording duration
- Cancel option: User can abort anytime

**Processing flow**:

1. User presses voice button (🎤)
2. Microphone access requested/granted
3. Recording starts, waveform shows activity
4. User speaks: "Send the quarterly report to John by Friday"
5. User releases button (or auto-detect speech end)
6. Audio sent to Whisper model
7. Transcription: "Send the quarterly report to John by Friday."
8. Text inserted at cursor position

#### 35.3 Intent Enhancement[^10_4]

Improves raw transcription:

**Raw transcription issues**:

- Missing punctuation: "send the report to john"
- Wrong capitalization: "john" should be "John"
- No formatting: Run-on sentences
- Unclear intent: "um send uh the report"

**Enhancement via LLM**:[^10_4]

```
Input: "um send the report to john by friday thanks"

LLM enhancement prompt:
"Clean up this voice transcription:
- Add proper punctuation
- Capitalize names
- Remove filler words (um, uh)
- Format as natural text

Transcription: 'um send the report to john by friday thanks'
"

Output: "Send the report to John by Friday. Thanks!"
```

**Enhancement types**:

- **Punctuation**: Add periods, commas, question marks
- **Capitalization**: Proper nouns, sentence starts
- **Filler removal**: Um, uh, like, you know
- **Formatting**: Paragraph breaks for long transcriptions
- **Intent clarification**: Resolve ambiguous phrases

**Speed**: Enhancement takes 100-200ms (parallel with user reviewing)

#### 35.4 Mic Button Handler[^10_1]

UI control for voice input:

**Button states**:

- **Ready** (🎤): Gray, tap to start
- **Listening** (🎤 red): Red pulsing, waiting for speech
- **Recording** (⏹️): Red, actively recording, tap to stop
- **Processing** (⌛): Spinner, transcribing audio

**Interaction modes**:

**Press-and-hold** (default):

- Hold button to record
- Release to stop and transcribe
- Familiar pattern (like voice messages)

**Tap-to-toggle**:

- Tap to start recording
- Tap again to stop
- Good for longer dictations

**Voice activation**:

- Auto-stop when silence detected (2 seconds)
- No need to manually stop
- Hands-free option

**Error handling**:

- Mic permission denied: Show instructions
- No speech detected: "No speech detected, try again"
- Network error (if using cloud): "Voice offline, try local"
- Unclear transcription: Allow edit before inserting

***

## Module 36: Mobile Context Adapter

### Overview

The Mobile Context Adapter translates **Android-specific context into the AI system**. It bridges mobile APIs (AccessibilityService) with the core AI engine.[^10_1]

### Core Responsibilities

#### 36.1 App Detection (Android AccessibilityService)[^10_1]

Identifies active mobile app:

**AccessibilityService**:

- Android system service for accessibility features
- Provides app context without root access
- Monitors active windows and UI elements
- Permission required: BIND_ACCESSIBILITY_SERVICE

**Implementation**:

```java
public class AIKeyboardAccessibilityService extends AccessibilityService {
    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {
        if (event.getEventType() == AccessibilityEvent.TYPE_WINDOW_STATE_CHANGED) {
            CharSequence packageName = event.getPackageName();
            CharSequence className = event.getClassName();
            
            // Detect app
            String appName = getAppName(packageName);
            String appType = classifyApp(packageName);
            
            // Send to context manager
            updateAppContext(appName, appType);
        }
    }
}
```

**App classification** (mobile):

- WhatsApp, Telegram, Signal → Chat
- Gmail, Outlook → Email
- Twitter, LinkedIn, Facebook → Social media
- Google Docs, MS Word → Document editing
- Chrome, Firefox → Browser

**Privacy compliance**:

- Only package name and class name accessed
- No content reading (unless explicit permission)
- User controls which apps enable AI (blocklist)


#### 36.2 Context Indicator[^10_1]

Shows current app context to user:

**Visual indicator**:

```
┌──────────────────────────────┐
│ 📧 Gmail                     │  ← Context indicator
├──────────────────────────────┤
│ Professional, medium-length  │
└──────────────────────────────┘
```

**Indicator location**:

- Top of keyboard suggestion bar
- Small, non-intrusive
- Shows app icon + name
- Shows active AI mode (professional, casual, code)

**Tap interaction**:

- Tap indicator to see/change settings
- Quick toggles: Formality, length, emoji
- Per-app preferences

**Status icons**:

- ⚠️ Warning: AI paused in this app
- 🔒 Locked: Privacy-blocked app (banking)
- ✅ Active: AI suggestions enabled
- 🌐 Offline: Local-only mode


#### 36.3 Network Client to AI Engine[^10_1]

Connects mobile keyboard to backend:

**Connection architecture**:

- Mobile keyboard (foreground) ↔ Network ↔ Desktop/Cloud AI engine
- Alternative: Local on-device AI (for privacy mode)

**Network protocol**:

- WebSocket for real-time suggestions (low latency)
- HTTPS REST API for configuration, voice transcription
- Protobuf for efficient serialization (smaller than JSON)

**Message flow**:

```
1. User types in WhatsApp
2. Keyboard captures context
3. Serialize: {app: "WhatsApp", text: "hey how are", ...}
4. Send via WebSocket to AI engine
5. Engine processes with LLM
6. Response: {ngram: [...], llm: "..."}
7. Keyboard displays suggestions
```

**Mobile-specific optimizations**:

- Compress payloads (save mobile data)
- Batch requests: Multiple contexts in one message
- Priority queue: User input has priority over background tasks
- Offline queue: Store requests when offline, send when connected

**Battery considerations**:

- Coalesce network requests (reduce radio wake-ups)
- Prefer Wi-Fi over cellular (power-efficient)
- Throttle when battery low (<20%)
- Disable when battery saver mode active

***

## Module 37: MongoDB Data Layer

### Overview

The MongoDB Data Layer handles **persistent storage of user profiles, patterns, and session data**. It's the long-term memory backend.[^10_4][^10_1]

### Core Responsibilities

#### 37.1 User Profile Storage[^10_4]

Stores user identity and preferences:

**Profile schema**:

```javascript
{
  _id: ObjectId("..."),
  user_id: "user_12345",
  created_at: ISODate("2026-01-15T10:00:00Z"),
  last_active: ISODate("2026-01-30T01:20:00Z"),
  
  profile: {
    role: "software_engineer",
    writing_style: "professional",
    formality_level: 0.75,
    verbosity: "concise",
    baseline_wpm: 72
  },
  
  per_app_preferences: {
    "Microsoft Word": {
      formality: 0.8,
      suggestion_length: "medium",
      types_enabled: ["completion", "correction"]
    },
    "Slack": {
      formality: 0.3,
      suggestion_length: "short",
      types_enabled: ["completion"],
      emoji_enabled: true
    }
  },
  
  statistics: {
    total_sessions: 145,
    total_suggestions_shown: 8420,
    total_suggestions_accepted: 5892,
    acceptance_rate: 0.70,
    time_saved_minutes: 1247
  }
}
```

**CRUD operations**:

- **Create**: New user onboarding
- **Read**: Load profile at session start
- **Update**: Save learning at session end[^10_2]
- **Delete**: User data deletion request (privacy)


#### 37.2 Pattern Storage[^10_4]

Stores learned behaviors:

**Vocabulary patterns**:

```javascript
{
  user_id: "user_12345",
  pattern_type: "vocabulary",
  
  frequent_words: [
    {word: "analysis", frequency: 247, contexts: ["document", "email"]},
    {word: "implement", frequency: 189, contexts: ["code", "document"]},
    {word: "stakeholder", frequency: 156, contexts: ["email", "document"]}
  ],
  
  word_preferences: {
    "utilize": "use",  // User prefers "use"
    "leverage": "use",
    "assist": "help"
  },
  
  domain_vocabulary: ["API", "microservice", "deployment", "CI/CD"]
}
```

**Rejection patterns**:[^10_4]

```javascript
{
  user_id: "user_12345",
  pattern_type: "rejection",
  
  patterns: [
    {
      pattern: "overly_verbose",
      examples: ["however", "furthermore", "in addition to that"],
      contexts: ["email", "chat"],
      strength: 0.92,  // High confidence to avoid
      occurrences: 18
    },
    {
      pattern: "too_formal_for_chat",
      examples: ["I would appreciate", "Could you kindly"],
      contexts: ["slack", "teams"],
      strength: 0.85,
      occurrences: 12
    }
  ]
}
```

**Acceptance patterns**:

```javascript
{
  user_id: "user_12345",
  pattern_type: "acceptance",
  
  successful_patterns: [
    {
      context: "email_conclusion",
      phrase: "Please let me know if you have any questions",
      acceptance_speed: "instant",  // <500ms
      occurrences: 23
    },
    {
      context: "code_completion",
      pattern: "short_variable_names",  // e.g., 'df' not 'dataframe'
      occurrences: 156
    }
  ]
}
```


#### 37.3 Session Metrics Aggregator[^10_2]

Batches session data for storage:

**Session record**:

```javascript
{
  user_id: "user_12345",
  session_id: "session_abc123",
  start_time: ISODate("2026-01-30T00:30:00Z"),
  end_time: ISODate("2026-01-30T01:20:00Z"),
  duration_minutes: 50,
  
  metrics: {
    apps_used: ["Microsoft Word", "Chrome", "Slack"],
    primary_app: "Microsoft Word",
    
    typing: {
      avg_wpm: 68,
      total_characters: 3420,
      backspace_ratio: 0.08
    },
    
    suggestions: {
      shown: 47,
      accepted: 32,
      dismissed: 15,
      acceptance_rate: 0.68
    },
    
    mood_distribution: {
      focused: 0.60,    // 60% of session
      neutral: 0.25,
      rushed: 0.15
    },
    
    new_vocabulary: ["EBITDA", "YoY", "stakeholder engagement"],
    
    errors_corrected: 12
  }
}
```

**Aggregation process**:[^10_2]

1. Session ends (app close, 30 min inactivity)
2. STM data collected
3. Metrics calculated
4. Aggregated session record created
5. Stored in MongoDB
6. LTM patterns updated with moving average (90% old, 10% new)[^10_2]

#### 37.4 Query Optimization

Ensures fast data retrieval:

**Indexes**:

```javascript
// Primary index
db.users.createIndex({ user_id: 1 })

// Session queries
db.sessions.createIndex({ user_id: 1, start_time: -1 })

// Pattern lookups
db.patterns.createIndex({ user_id: 1, pattern_type: 1 })

// Performance analytics
db.sessions.createIndex({ start_time: -1, acceptance_rate: 1 })
```

**Query patterns**:

- Load profile: `db.users.findOne({user_id: "..."})`
- Recent sessions: `db.sessions.find({user_id: "..."}).sort({start_time: -1}).limit(10)`
- Rejection patterns: `db.patterns.find({user_id: "...", pattern_type: "rejection"})`

**Caching strategy**:

- Profile cached in memory at session start
- Patterns cached for current session
- Periodic refresh every 30 minutes
- Invalidate cache on explicit user changes


#### 37.5 Index Management

Maintains database performance:

**Index types**:

- **Single field**: Fast lookups on user_id
- **Compound**: Multi-field queries (user + date range)
- **Text search**: Full-text search on patterns (for analytics)

**Index monitoring**:

- Track query performance (slow query log)
- Identify missing indexes (explain plans)
- Remove unused indexes (reduce write overhead)

**Maintenance**:

- Weekly index statistics update
- Monthly index rebuild (defragmentation)
- Automatic index creation for common query patterns

***

## Module 38: Encryption Module

### Overview

The Encryption Module protects **user data in transit and at rest**. It ensures privacy through strong encryption and secure key management.[^10_2][^10_1]

### Core Responsibilities

#### 38.1 STM Local Encryption (AES-256, 100ms Debounced)[^10_2]

Encrypts session data in memory:

**Why encrypt STM**:

- STM contains sensitive current typing context
- Stored in RAM during session
- Backup to disk if system crashes
- Must be protected even if memory dump occurs

**Encryption method**:

- **Algorithm**: AES-256-GCM (Galois/Counter Mode)
- **Key**: User-specific, derived from master key
- **IV**: Random initialization vector per encryption
- **Authentication**: GCM provides authenticated encryption

**Debounced encryption**:[^10_2]

- Don't encrypt on every keystroke (expensive)
- Buffer STM updates for 100ms[^10_2]
- Encrypt batch of updates together
- Reduces CPU overhead by 90%

**Implementation**:

```python
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
import os

# Generate key (once per session)
key = AESGCM.generate_key(bit_length=256)
aesgcm = AESGCM(key)

# Encrypt STM data
nonce = os.urandom(12)  # 96-bit random nonce
stm_json = json.dumps(stm_data)
ciphertext = aesgcm.encrypt(nonce, stm_json.encode(), None)

# Store: nonce + ciphertext
encrypted_stm = nonce + ciphertext
```

**Decryption** (when needed):

```python
# Split nonce and ciphertext
nonce = encrypted_stm[:12]
ciphertext = encrypted_stm[12:]

# Decrypt
plaintext = aesgcm.decrypt(nonce, ciphertext, None)
stm_data = json.loads(plaintext.decode())
```

**Key lifecycle**:

- Generate new key each session
- Key stored only in RAM (never persisted)
- Key destroyed when session ends
- No way to decrypt after session closes


#### 38.2 Secure Key Storage

Manages encryption keys safely:

**Key hierarchy**:

```
Master Key (device-specific, stored in OS keychain)
  ├─ User Key (user-specific, derived from master)
  │   ├─ Session Key (temporary, RAM only)
  │   └─ LTM Key (persistent, for database encryption)
  └─ Transport Key (for network encryption)
```

**Master key storage**:

- **Windows**: DPAPI (Data Protection API)
- **macOS**: Keychain Services
- **Linux**: libsecret or Keyring
- **Android**: Android Keystore

**Key derivation**:

```python
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
import hashlib

# Derive user key from master key + user ID
kdf = PBKDF2HMAC(
    algorithm=hashlib.sha256(),
    length=32,
    salt=user_id.encode(),
    iterations=100000
)
user_key = kdf.derive(master_key)
```

**Key rotation**:

- Master key: Rotated yearly
- User key: Rotated quarterly
- Session key: New every session
- Transport key: New daily


#### 38.3 Data Sanitization

Ensures data is fully deleted:

**Sanitization methods**:

**Memory sanitization**:

```python
# Overwrite sensitive data before deletion
import ctypes

def secure_delete(data):
    # Get memory address
    addr = id(data)
    size = len(data)
    
    # Overwrite with zeros
    ctypes.memset(addr, 0, size)
    
    # Delete reference
    del data
```

**File sanitization**:

- Overwrite file contents with random data (7 passes)
- Overwrite with zeros
- Delete file
- Prevent recovery with forensic tools

**Database sanitization**:

- Delete records
- Vacuum database (compact and remove deleted data)
- Overwrite free space
- Verify deletion

**Scenarios**:

- **Session end**: Sanitize STM
- **User data deletion**: Sanitize all user records
- **Uninstall**: Sanitize all application data
- **Privacy reset**: Sanitize learning data, keep settings

***

## Module 39: Privacy Controller

### Overview

The Privacy Controller enforces **user privacy preferences and data protection rules**. It ensures AI never operates where it shouldn't.[^10_2][^10_1]

### Core Responsibilities

#### 39.1 Auto-Block Manager (Banking Apps, Password Fields)[^10_1]

Automatically disables AI in sensitive contexts:

**Auto-blocked contexts**:

**Banking and financial apps**:

- Banks: Chase, Bank of America, Wells Fargo, etc.
- Investment: Robinhood, E*TRADE, Fidelity
- Payment: PayPal, Venmo, Zelle
- Crypto: Coinbase, Binance
- **Reason**: Prevent any capture of financial data

**Password fields**:

- Detection: Input type="password"
- Detection: Field name contains "password", "passwd", "pwd"
- Detection: Autocomplete="current-password"
- **Reason**: Never process passwords

**Government and legal**:

- IRS website, tax software
- Legal document sites
- Healthcare portals (HIPAA compliance)
- **Reason**: Regulatory compliance

**Detection method**:

```python
def should_block(app_name, field_type):
    # Check blocklist
    if app_name in BANKING_APPS:
        return True, "Banking app"
    
    # Check field type
    if field_type == "password":
        return True, "Password field"
    
    # Check URL (for browser)
    if "bank" in url or "login" in url:
        return True, "Sensitive URL"
    
    return False, None
```

**User notification**:

- Icon shows 🔒 locked state
- Tooltip: "AI paused in banking app for privacy"
- No suggestions shown
- No context captured


#### 39.2 App Blocklist Enforcer[^10_1]

Respects user-defined blocklist:

**Blocklist management**:

```javascript
{
  user_id: "user_12345",
  blocked_apps: [
    {
      app: "Personal Journal",
      reason: "Private writing",
      added_date: "2026-01-20"
    },
    {
      app: "Signal",
      reason: "Encrypted messages",
      added_date: "2026-01-22"
    }
  ]
}
```

**Blocklist UI**:

- Settings page: List of blocked apps
- Add button: User can block any app
- Remove button: Unblock if needed
- Import/export: Share blocklist across devices

**Enforcement**:

- Check blocklist on every app switch
- If blocked: Disable all AI features immediately
- If unblocked: Resume AI features


#### 39.3 Pause Mode Manager[^10_1]

Temporary AI disable:

**Pause triggers**:

- **Manual**: User presses Ctrl+Shift+P[^10_1]
- **Automatic**: Sensitive app detected
- **Scheduled**: User sets "No AI" hours (e.g., after 10 PM)

**Pause states**:

- **Active**: AI suggestions running normally
- **Paused**: AI completely disabled, no context capture
- **Limited**: Only n-gram (local), no LLM (cloud)

**Visual indicators**:

- Icon: ⏸️ pause symbol
- System tray: Yellow indicator
- Keyboard: Suggestion bar hidden

**Resume**:

- Manual: Press hotkey again
- Automatic: Leave blocked app
- Scheduled: Time window ends


#### 39.4 Data Wipe Handler

Complete data deletion:

**Wipe options**:

**Session wipe**:

- Clear STM (current session data)
- Keep LTM (learning preserved)
- Quick reset

**Learning wipe**:

- Clear LTM patterns (vocabulary, preferences)
- Keep account and settings
- Reset to "new user" state

**Full wipe**:

- Delete all user data
- Delete account
- Sanitize all storage
- Irreversible

**Wipe process**:

```python
def full_data_wipe(user_id):
    # 1. Delete from database
    db.users.deleteOne({user_id: user_id})
    db.sessions.deleteMany({user_id: user_id})
    db.patterns.deleteMany({user_id: user_id})
    
    # 2. Delete local files
    shutil.rmtree(f"data/{user_id}")
    
    # 3. Clear encryption keys
    keychain.delete(f"ai_keyboard_key_{user_id}")
    
    # 4. Sanitize memory
    secure_delete(all_user_data_in_memory)
    
    # 5. Log deletion (for compliance)
    audit_log.info(f"User {user_id} data fully deleted")
```


#### 39.5 Privacy Settings UI (View STM, Edit LTM, Export Data)[^10_2]

User control interface:

**Settings sections**:

**View STM**:[^10_2]

- Show current session data
- What AI knows right now
- Real-time display
- "This is temporary and will be deleted when you close the app"

**Edit LTM**:[^10_2]

- View learned patterns
- Remove specific patterns
- Adjust preferences
- "This is permanent learning that persists across sessions"

**Export data**:[^10_2]

- Download all user data (JSON format)
- GDPR compliance (right to data portability)
- Includes: Profile, patterns, session history
- "You can take your data elsewhere"

**Privacy dashboard**:

```
┌────────────────────────────────────────┐
│ Privacy Dashboard                      │
├────────────────────────────────────────┤
│ Current Session:                       │
│   • App: Microsoft Word                │
│   • Context: 247 characters            │
│   • Suggestions: 8 shown, 5 accepted   │
│   [Clear Session Data]                 │
├────────────────────────────────────────┤
│ Long-Term Learning:                    │
│   • Vocabulary words: 342              │
│   • Patterns learned: 67               │
│   • Sessions: 145                      │
│   [View Details] [Reset Learning]      │
├────────────────────────────────────────┤
│ Blocked Apps: 3                        │
│   • Personal Journal                   │
│   • Signal                             │
│   • Banking App                        │
│   [Manage Blocklist]                   │
├────────────────────────────────────────┤
│ [Export My Data] [Delete Everything]   │
└────────────────────────────────────────┘
```


***

## Module 40: MCP (Model Context Protocol) Server

### Overview

The MCP Server exposes AI Keyboard context to **external AI tools like Claude Desktop**. It implements the Model Context Protocol for standardized AI tool integration.[^10_4]

### Core Responsibilities

#### 40.1 MCP Protocol Handler

Implements MCP specification:

**MCP protocol overview**:

- Standard protocol for AI context sharing
- Developed by Anthropic
- JSON-RPC based communication
- Tools, resources, and prompts

**Server setup**:

```json
{
  "mcpServers": {
    "ai-keyboard": {
      "command": "python",
      "args": ["mcp_server.py"],
      "env": {
        "AI_KEYBOARD_DATA": "/path/to/data"
      }
    }
  }
}
```

**MCP messages**:

- `initialize`: Handshake and capabilities
- `list_tools`: Available functions
- `call_tool`: Execute function
- `list_resources`: Available data
- `read_resource`: Access data


#### 40.2 Context Provider Interface

Exposes typing context to external tools:

**Available contexts**:

**Current context**:

```javascript
// Resource: context://current
{
  "app": "Microsoft Word",
  "document": "Q1_Report.docx",
  "text_before": "In conclusion, our analysis shows that",
  "text_after": "",
  "mood": "focused",
  "timestamp": "2026-01-30T01:20:00Z"
}
```

**Recent suggestions**:

```javascript
// Resource: context://recent_suggestions
{
  "suggestions": [
    {
      "text": "demonstrates significant growth",
      "context": "report writing",
      "accepted": true,
      "timestamp": "2026-01-30T01:19:45Z"
    },
    // ... more suggestions
  ]
}
```

**User profile**:

```javascript
// Resource: context://profile
{
  "role": "software_engineer",
  "writing_style": "professional",
  "preferred_formality": 0.75,
  "baseline_wpm": 72
}
```


#### 40.3 Tool Exposure Layer

Provides functions to external AIs:

**Available tools**:

**suggest_completion**:

```javascript
{
  "name": "suggest_completion",
  "description": "Get AI-powered text completion",
  "parameters": {
    "text": "string (text to complete)",
    "app_context": "string (optional app name)"
  }
}
```

**analyze_writing_style**:

```javascript
{
  "name": "analyze_writing_style",
  "description": "Analyze user's writing patterns",
  "parameters": {
    "text_sample": "string (text to analyze)"
  }
}
```

**get_vocabulary**:

```javascript
{
  "name": "get_vocabulary",
  "description": "Get user's frequent vocabulary",
  "parameters": {
    "domain": "string (optional: code, email, chat)"
  }
}
```


#### 40.4 Claude Desktop Integration[^10_4]

Direct integration with Claude:

**Use case**: Claude can see what you're typing in real-time

**Example workflow**:

1. User typing in Word: "In conclusion, our analysis shows that"
2. User opens Claude Desktop
3. User asks Claude: "Help me finish this sentence professionally"
4. Claude uses MCP to read current context from AI Keyboard
5. Claude sees full document context and recent typing
6. Claude provides informed suggestion
7. User can accept and it inserts via AI Keyboard

**Benefits**:

- Claude has full context (not just pasted text)
- Claude knows user's writing style
- Claude can provide contextually appropriate suggestions
- Seamless integration between typing and AI chat


#### 40.5 Frai Integration[^10_4]

Integration with Frai AI assistant:

**Frai capabilities with AI Keyboard context**:

- Access user's typing context
- Understand user's writing patterns
- Provide style-matched suggestions
- Learn from user's preferences

**Bidirectional communication**:

- AI Keyboard → Frai: Typing context and patterns
- Frai → AI Keyboard: Enhanced suggestions and commands

**Privacy**: User controls what context is shared with external tools

***

## Summary: Modules 31-40

These 10 modules handle **cross-platform implementations and infrastructure**:

- **Modules 31-33**: Browser extension (DOM extraction, UI rendering, native messaging)
- **Modules 34-36**: Mobile keyboard (custom IME, voice input, context adaptation)
- **Modules 37-39**: Backend infrastructure (database, encryption, privacy controls)
- **Module 40**: External integrations (MCP server, Claude Desktop, Frai)

**Next in Part 5 (Modules 41-50)**: Configuration, analytics, utilities, and workflow-specific modules.

***

**End of Part 4**

*Ready to continue with Part 5 covering Modules 41-50 when you're ready.*
<span style="display:none">[^10_5]</span>

<div align="center">⁂</div>

[^10_1]: SOLUTION_DECISIONS.md

[^10_2]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md

[^10_3]: CORE_INTELLIGENCE_DESIGN.md

[^10_4]: ai_keyboard_sol.md

[^10_5]: GRAMMARLY_TECHNICAL_RESEARCH.md


---

# AI Keyboard - Complete Module Documentation

## Part 5: Configuration, Analytics \& Utilities (Modules 41-50)

> **Document Version:** 1.0
> **Date:** January 30, 2026, 1:27 AM IST
> **Scope:** Detailed explanation of Modules 41-50
> **Purpose:** System configuration, monitoring, and utility functions

***

## Module 41: Context Synchronizer

### Overview

The Context Synchronizer ensures **all system components have consistent, up-to-date context**. It orchestrates updates between STM, LTM, mood engine, and other modules, preventing stale data and race conditions.[^11_1][^11_2]

### Core Responsibilities

#### 41.1 STM Update Coordinator[^11_2]

Manages Short-Term Memory updates:

**Update sources**:

- **Keystroke events**: Text changes from typing
- **Focus changes**: App switches, window changes
- **Clipboard changes**: New content copied
- **Selection changes**: User highlighting text
- **Cursor movement**: Position changes
- **Mood updates**: Behavioral signal changes

**Coordination strategy**:

- Collect updates from multiple sources
- Deduplicate redundant updates
- Order updates chronologically
- Apply updates atomically (all-or-nothing)
- Notify dependent modules

**Example scenario**:

```
T=0ms:   User types "a" → Queue keystroke update
T=5ms:   User types "n" → Queue keystroke update
T=10ms:  User types "a" → Queue keystroke update
T=100ms: Debounce timer fires → Apply batch: "ana"
         Update text_before_cursor: "The quarterly ana|"
         Trigger: N-gram engine, Mood analyzer
         Result: Coordinated update, no conflicts
```

**Atomic updates**:

- Lock STM during update
- Apply all changes together
- Unlock STM
- Prevents partial state (half-updated context)


#### 41.2 Event Queue Manager[^11_2]

Buffers and processes events efficiently:

**Event queue structure**:

```
┌─────────────────────────────────────┐
│ Event Queue (FIFO)                  │
├─────────────────────────────────────┤
│ 1. keystroke: "a"  (T=0ms)          │
│ 2. keystroke: "n"  (T=5ms)          │
│ 3. keystroke: "a"  (T=10ms)         │
│ 4. app_switch: Word (T=50ms)        │
│ 5. clipboard: "data" (T=80ms)       │
└─────────────────────────────────────┘
```

**Processing strategy**:

- **Batch processing**: Group similar events (keystrokes)
- **Priority queue**: Urgent events (app switch) jump ahead
- **Throttling**: Limit processing rate to prevent overload
- **Dropping**: Discard obsolete events if queue too long

**Event types by priority**:

1. **Critical** (immediate): App switch, pause request
2. **High** (100ms): Clipboard change, focus change
3. **Normal** (debounced): Keystrokes, cursor movement
4. **Low** (background): Mood recalculation, time decay updates

**Queue overflow handling**:

- Maximum queue size: 1000 events
- If full: Drop oldest normal/low priority events
- Never drop critical events
- Log overflow (indicates performance issue)


#### 41.3 Enhanced Debounce Controller (100ms for STM Updates)[^11_3]

Optimizes update frequency:

**STM update debouncing**:[^11_3]

- Wait 100ms after last STM change[^11_3]
- If more changes arrive: Reset timer
- When timer expires: Apply batched updates
- Result: Reduce update frequency by 90%

**Why 100ms for STM**:

- Fast enough: Feels real-time to user
- Efficient: Batches multiple keystrokes
- Responsive: Updates before suggestion delay (500-1200ms)

**Different debounce times by component**:

- STM text updates: 100ms[^11_3]
- Mood recalculation: 5000ms (every 5 seconds)
- Time decay updates: 30000ms (every 30 seconds)[^11_1]
- Cross-app relevance: 10000ms (every 10 seconds)

**Debounce bypass** (immediate updates):

- App switches: No debounce, update now[^11_1]
- Suggestion accepted/dismissed: No debounce, feedback immediate
- Pause command: No debounce, stop immediately


#### 41.4 Immediate Update Handler (App Switches)[^11_1]

Processes critical events instantly:

**Immediate update triggers**:[^11_1]

- **App switch**: User changed applications
- **Focus loss**: User clicked outside app
- **Pause/resume**: AI toggled on/off
- **Blocklist hit**: User entered blocked app

**Why immediate**:

- Context changes dramatically with app switch
- Can't wait for debounce (might send wrong context)
- User expectation: System responds to app change instantly

**Immediate update process**:

1. Detect app switch event
2. Bypass debounce queue (priority lane)
3. Capture new app context immediately
4. Update STM with new app info
5. Cancel any pending suggestions (wrong context now)
6. Load per-app preferences from LTM
7. Ready for typing in <50ms

**Cross-app context preservation**:

- Previous app context stored with timestamp
- Becomes cross-app context for new app[^11_4]
- Time decay starts immediately[^11_3]


#### 41.5 Background Updater (Mood Every 30s)[^11_1]

Handles non-urgent periodic updates:

**Background tasks**:

- **Mood recalculation**: Every 30 seconds[^11_1]
- **Time decay**: Every 30 seconds (cross-app, clipboard)[^11_3]
- **Session metrics**: Every 60 seconds
- **Network health check**: Every 60 seconds

**Why background**:

- Not triggered by user action
- Can be delayed without UX impact
- Smooth out CPU usage (prevent spikes)

**Mood background update**:[^11_1]

- Collect behavioral signals over last 30 seconds
- Calculate new mood probabilities
- If mood changed significantly (>0.2 difference): Update immediately
- If mood stable: Update after suggestion completes (don't interrupt)

**Time decay background update**:[^11_3]

- Iterate through all cross-app contexts
- Calculate minutes elapsed since captured
- Apply exponential decay formula[^11_3]
- Remove contexts below threshold (0.3)[^11_3]
- Update clipboard relevance

**Background scheduler**:

- Single thread for all background tasks
- Tasks run in order of priority
- Skip tasks if system under heavy load
- Resume when idle


#### 41.6 Session-End LTM Batch Updater[^11_3]

Writes learning to database at session close:

**Session end triggers**:

- App closed by user
- 30 minutes of inactivity[^11_2]
- User logout
- System shutdown

**Batch update process**:[^11_3]

1. **Aggregate STM**: Collect all session data
2. **Calculate metrics**: Acceptance rate, avg WPM, mood distribution
3. **Extract patterns**: New vocabulary, rejection patterns
4. **Merge with LTM**: 90% historical, 10% new session[^11_3]
5. **Write to MongoDB**: Single transaction
6. **Confirm success**: Retry if fails
7. **Clear STM**: Delete session data

**Why batch at session end**:[^11_3]

- **Efficient**: One database write instead of hundreds
- **Accurate**: Full session picture before updating
- **Performance**: No database latency during typing
- **Atomic**: Either all learning saved or none (no partial updates)

**Failure handling**:

- If database unreachable: Queue update locally
- Retry on next app open
- Maximum 3 retry attempts
- After 3 fails: Notify user, offer manual sync

***

## Module 42: Event Bus

### Overview

The Event Bus is the **central communication hub** for all system modules. It implements publish-subscribe pattern so modules can communicate without tight coupling.[^11_2]

### Core Responsibilities

#### 42.1 Keystroke Events

Broadcasts typing activity:

**Event payload**:

```
{
  "event_type": "keystroke",
  "key": "a",
  "modifiers": [],  // shift, ctrl, alt
  "timestamp": "2026-01-30T01:20:15.234Z",
  "app": "Microsoft Word",
  "field_type": "document"
}
```

**Subscribers**:

- **STM Manager**: Updates text context[^11_1]
- **N-gram Engine**: Updates word predictions[^11_3]
- **Typing Pattern Analyzer**: Calculates speed, rhythm[^11_1]
- **Keyboard Hook Manager**: Checks for hotkeys[^11_5]
- **Debounce System**: Resets debounce timer[^11_3]

**Frequency**: Very high (multiple per second during typing)

**Optimization**: Batch multiple keystrokes into single event if very rapid

#### 42.2 Focus Change Events

Announces app/window switches:

**Event payload**:

```
{
  "event_type": "focus_change",
  "from_app": "Google Chrome",
  "to_app": "Microsoft Word",
  "from_context": "gmail.com - Inbox",
  "to_context": "Q1_Report.docx",
  "timestamp": "2026-01-30T01:20:30.567Z"
}
```

**Subscribers**:

- **STM Manager**: Updates current app context[^11_1]
- **Application Context Detector**: Loads app type and preferences[^11_5]
- **Cross-App Workflow Tracker**: Records app switch[^11_4]
- **Time Decay Manager**: Starts decay timer for previous app[^11_3]
- **Overlay Renderer**: Repositions UI for new app[^11_5]

**Trigger**: Immediate (bypasses debounce)[^11_1]

#### 42.3 Clipboard Change Events

Signals copied content:

**Event payload**:

```
{
  "event_type": "clipboard_change",
  "content_type": "text",  // or image, file
  "content_preview": "Revenue: $2.4M (+15% YoY)",
  "source_app": "Google Sheets",
  "timestamp": "2026-01-30T01:19:45.123Z",
  "length": 28
}
```

**Subscribers**:

- **Clipboard Intelligence System**: Stores and scores relevance[^11_1]
- **STM Manager**: Updates clipboard context[^11_1]
- **Cross-App Workflow Tracker**: Detects data transfer workflow[^11_4]

**Privacy filter**: Sensitive content (passwords, CC numbers) not broadcasted

#### 42.4 App Switch Events

Detailed application transitions:

**Event payload**:

```
{
  "event_type": "app_switch",
  "from_app": {
    "name": "Chrome",
    "type": "browser",
    "url": "sheets.google.com/Q1_Financial_Data",
    "duration_seconds": 180
  },
  "to_app": {
    "name": "Word",
    "type": "document_editor",
    "document": "Q1_Report.docx"
  },
  "workflow_detected": "data_to_report",
  "timestamp": "2026-01-30T01:20:00.000Z"
}
```

**Subscribers**:

- **Workflow Tracker**: Infers user's multi-app workflow[^11_4]
- **LLM Orchestration Layer**: Updates context for suggestions[^11_1]
- **Per-App Preferences**: Loads app-specific settings[^11_2]

**Enrichment**: Event includes inferred workflow type (research→writing, data→report)

#### 42.5 Timer Events (Periodic Checks)

Scheduled recurring tasks:

**Event types**:

**Mood timer** (every 30s):[^11_1]

```
{
  "event_type": "timer_mood",
  "timestamp": "2026-01-30T01:20:30.000Z"
}
```

→ Triggers mood recalculation

**Time decay timer** (every 30s):[^11_3]

```
{
  "event_type": "timer_decay",
  "timestamp": "2026-01-30T01:20:30.000Z"
}
```

→ Triggers context aging updates

**Session metrics timer** (every 60s):

```
{
  "event_type": "timer_metrics",
  "timestamp": "2026-01-30T01:21:00.000Z"
}
```

→ Updates session statistics

**Subscribers**: Background processing modules

#### 42.6 Suggestion Events (Shown/Accepted/Dismissed)

Tracks suggestion lifecycle:

**Suggestion shown**:

```
{
  "event_type": "suggestion_shown",
  "suggestion_id": "sug_abc123",
  "suggestion_text": "demonstrates significant growth",
  "context": "document_writing",
  "confidence": 0.87,
  "timestamp": "2026-01-30T01:20:15.500Z"
}
```

**Suggestion accepted**:

```
{
  "event_type": "suggestion_accepted",
  "suggestion_id": "sug_abc123",
  "time_to_accept_ms": 450,  // Instant accept
  "acceptance_type": "instant",
  "method": "tab_key",
  "timestamp": "2026-01-30T01:20:15.950Z"
}
```

**Suggestion dismissed**:

```
{
  "event_type": "suggestion_dismissed",
  "suggestion_id": "sug_abc123",
  "time_shown_ms": 1200,
  "dismissal_type": "escape_key",  // Strong rejection
  "timestamp": "2026-01-30T01:20:16.700Z"
}
```

**Subscribers**:

- **Feedback Learning Module**: Learns from responses[^11_2]
- **Suggestion Quality Pipeline**: Adjusts thresholds[^11_1]
- **Performance Metrics Tracker**: Records acceptance rate[^11_2]
- **Adaptive Debounce System**: Applies dismissal penalty[^11_3]


### Event Bus Architecture

**Benefits of event bus**:

- **Decoupling**: Modules don't need direct references to each other
- **Extensibility**: Add new modules without changing existing ones
- **Debugging**: Central point to monitor all system events
- **Replay**: Can record and replay events for testing

**Implementation pattern**: Observer/publish-subscribe

**Performance**: Asynchronous event delivery (non-blocking)

***

## Module 43: User Preferences Manager

### Overview

The User Preferences Manager stores and applies **user customization settings**. It handles both global preferences and per-app overrides.[^11_4][^11_2]

### Core Responsibilities

#### 43.1 Writing Style Settings[^11_2]

User's baseline writing characteristics:

**Style dimensions**:

**Tone**:

- Professional (business communications)
- Casual (friendly, conversational)
- Technical (precise, jargon-appropriate)
- Creative (expressive, varied)

**Voice**:

- Formal (third person, passive voice)
- Informal (first person, active voice)
- Neutral (balanced)

**Complexity**:

- Simple (short sentences, common words)
- Moderate (varied sentence length)
- Complex (long sentences, advanced vocabulary)

**Example preferences**:

```
{
  "writing_style": {
    "tone": "professional",
    "voice": "formal",
    "complexity": "moderate",
    "personality": "concise_and_clear"
  }
}
```

**How applied**: LLM prompts include style instructions[^11_1]

#### 43.2 Formality Level Controller[^11_1]

Adjusts suggestion formality:

**Formality scale**: 0.0 (very casual) to 1.0 (very formal)

**Examples by level**:

- **0.0-0.2** (Very casual): "hey", "gonna", "yeah"
- **0.3-0.4** (Casual): "hi", "going to", "yes"
- **0.5-0.6** (Neutral): "hello", "will", "certainly"
- **0.7-0.8** (Formal): "greetings", "shall", "indeed"
- **0.9-1.0** (Very formal): "salutations", "would be pleased to", "affirmative"

**Learned preference**:[^11_2]

- Track which formality levels user accepts
- Calculate average accepted formality
- Update user's formality preference
- Example: User consistently accepts 0.7-0.8 → Set preference to 0.75

**Per-app override**: Email might be 0.7, Slack might be 0.3[^11_2]

#### 43.3 Verbosity Preference[^11_1]

Controls suggestion length:

**Verbosity levels**:

- **Concise**: 10-30 characters, short phrases
- **Balanced**: 30-60 characters, complete thoughts
- **Verbose**: 60-100 characters, detailed sentences

**User signals for verbosity**:

- Accept short suggestions repeatedly → Prefer concise
- Dismiss short, accept long → Prefer verbose
- Accept varied lengths → Balanced preference

**Dynamic adjustment**:

- Morning: User accepts longer (fresh, patient)
- Evening: User accepts shorter (tired, want efficiency)
- Rushed mood: Shorter suggestions[^11_1]
- Exploratory mood: Longer, detailed suggestions[^11_1]


#### 43.4 Per-App Customization[^11_2]

Different settings for different apps:

**App-specific overrides**:

```
{
  "global_preferences": {
    "formality": 0.6,
    "verbosity": "balanced",
    "emoji": false
  },
  
  "per_app_overrides": {
    "Gmail": {
      "formality": 0.75,
      "verbosity": "balanced",
      "emoji": false,
      "greetings": true
    },
    "Slack": {
      "formality": 0.3,
      "verbosity": "concise",
      "emoji": true,
      "greetings": false
    },
    "VS Code": {
      "formality": 0.5,
      "verbosity": "concise",
      "emoji": false,
      "code_mode": true
    }
  }
}
```

**Learning per-app preferences**:[^11_2]

- Track acceptance rate per app
- Learn what works in each context
- Build app-specific profile over time

**UI for customization**:

- Settings page with app list
- Click app to edit preferences
- Preview suggestions with current settings
- Reset to defaults option


#### 43.5 LLM Preference Selector (Cloud_First/Local_First/Local_Only)[^11_3]

User control over AI model:

**LLM preference options**:[^11_3]

**Cloud-first** (default):

- Try cloud LLM (GPT-4, Claude) first
- Fallback to local if cloud fails or slow
- Best quality, requires internet

**Local-first**:

- Try local LLM (Phi-3) first
- Fallback to cloud if local unavailable
- Privacy-focused, works offline

**Local-only**:

- Always use local LLM
- Never send to cloud
- Maximum privacy, lower quality

**Automatic switching**:[^11_3]

- If cloud latency >500ms: Suggest local-first
- If offline: Auto-switch to local-only
- When online again: Offer to re-enable cloud


#### 43.6 Max Cloud Latency Threshold[^11_3]

Acceptable waiting time for cloud:

**Threshold setting**:[^11_3]

- Default: 500ms
- Range: 200ms (fast) to 2000ms (patient)
- User adjustable in settings

**How it works**:

- Send request to cloud LLM
- Start timer
- If response arrives before threshold: Success, use suggestion
- If exceeds threshold: Cancel, fallback to local LLM[^11_3]

**Adaptive learning**:

- Track typical cloud latency for user
- If consistently >threshold: Recommend local-first mode
- If consistently <threshold: Relax threshold slightly


#### 43.7 Feature Toggles

Enable/disable specific features:

**Toggleable features**:

- **N-gram suggestions**: On/off[^11_3]
- **LLM suggestions**: On/off[^11_3]
- **Grammar checking**: On/off[^11_3]
- **Emoji suggestions**: On/off[^11_3]
- **Voice input**: On/off[^11_4]
- **Cross-app context**: On/off[^11_4]
- **Clipboard awareness**: On/off[^11_1]

**Quick access toggles**:

- System tray menu: Right-click icon
- Checkboxes for each feature
- Changes apply immediately

**Profiles**:

- "Maximum Privacy": Local-only, no cross-app, no clipboard
- "Maximum Quality": All features enabled
- "Battery Saver": Disable LLM, n-gram only
- "Custom": User-defined combination

***

## Module 44: Context Window Configuration

### Overview

The Context Window Configuration module controls **what context is sent to LLM and how much**. It manages the balance between providing enough context for quality suggestions and staying within limits.[^11_4][^11_3]

### Core Responsibilities

#### 44.1 Context Budget Allocator (2000 Tokens)[^11_3]

Distributes token budget across context types:

**Total budget**: 2000 tokens[^11_3]

**Budget allocation**:

```
System prompt:        200 tokens (fixed)
User profile:         100 tokens (style, preferences)
Current text:         800 tokens (text before/after cursor)
App context:          300 tokens (app type, document structure)
Cross-app context:    200 tokens (recent apps, workflow)
Clipboard:            150 tokens (if relevant)
Mood/behavioral:      100 tokens (mood, typing patterns)
Feedback hints:       150 tokens (recent accepts/rejects)
-------------------------------------------
Total:               2000 tokens
```

**Priority system**:

1. **Critical** (always included): Current text, app type
2. **High** (usually included): User style, recent feedback
3. **Medium** (conditional): Cross-app context if relevant
4. **Low** (optional): Clipboard if high relevance

**Budget overflow handling**:

- If context exceeds 2000 tokens: Start removing low-priority items
- Truncate long text (keep most recent 500 chars before cursor)
- Summarize cross-app context instead of full text
- Never exceed budget (causes LLM errors)


#### 44.2 Analysis Scope Controller[^11_4]

Determines how much surrounding context to analyze:

**Scope levels**:

**Minimal** (low-power mode):

- Current paragraph only
- Last 200 characters before cursor
- No cross-app context
- Fast, basic suggestions

**Standard** (default):

- Current page or visible text
- Last 500 characters before cursor
- Relevant cross-app context (if high relevance)
- Clipboard (if high relevance)
- Good balance

**Extended** (maximum context):

- Multiple pages or entire document
- Last 1000 characters before cursor
- All cross-app contexts from session
- All clipboard items
- Best quality, slower

**Automatic adjustment**:

- If battery low (<20%): Switch to Minimal
- If performance issues detected: Reduce to Standard
- If user on AC power + fast network: Allow Extended


#### 44.3 Language Settings

Context language preferences:

**Primary language**: User's main writing language

- Affects n-gram model selection[^11_3]
- Affects LLM prompt language
- Affects grammar rules[^11_3]

**Multi-language support**:

- Detect language in current text
- Switch context language dynamically
- Maintain separate vocabulary per language

**Translation context**:

- If translating: Include source language as context
- Example: "User is translating from Spanish to English"
- Helps LLM maintain translation quality


#### 44.4 Code Analysis Depth (Up to 5 Related Files)[^11_3]

Controls multi-file code context:

**Depth levels**:

**Level 1: Current file only**:

- File being edited
- Imports in current file
- Current function/class scope
- Fast, basic code completion

**Level 2: Direct dependencies** (default):

- Current file
- Files imported by current file
- Up to 3 most relevant files
- Good code awareness

**Level 3: Full context** (5 files):[^11_3]

- Current file
- Direct imports (2 files)
- Files that import current file (1 file)
- Recently edited related files (1 file)
- Maximum code intelligence

**File selection algorithm**:

1. Always include: Current file (full)
2. Include: Directly imported files (signatures only)
3. Include: Files importing current (if modified recently)
4. Include: Test files (if editing implementation)
5. Include: Configuration files (if relevant)

**Content per file**:

- Current file: Full content
- Related files: Imports + function signatures + current function full body
- Distant files: Imports + key exports only

***

## Module 45: Suggestion Settings Controller

### Overview

The Suggestion Settings Controller manages **when and how suggestions appear**. It controls thresholds, timing, and behavior of the suggestion system.[^11_1][^11_3]

### Core Responsibilities

#### 45.1 Base Threshold Configuration[^11_1]

Minimum confidence for showing suggestions:

**Base confidence threshold**: 0.6 (60%)[^11_1]

**What threshold means**:

- LLM returns confidence score with each suggestion
- Only show suggestion if confidence ≥ threshold
- Prevents showing low-quality suggestions

**Threshold by suggestion type**:

- Completions: 0.6 (standard)
- Corrections: 0.5 (more lenient, errors are obvious)
- Rewrites: 0.7 (higher bar, user explicitly requested)

**Learning from threshold**:

- If many low-confidence suggestions accepted: Lower threshold slightly
- If many high-confidence suggestions dismissed: Raise threshold
- Adaptive over time


#### 45.2 Dynamic Mood Adjustment[^11_3]

Modifies threshold based on user state:

**Mood-based threshold adjustments**:[^11_1]

**Focused mood**:[^11_1]

- Adjustment: +0.1 (more selective)
- Rationale: User in deep work, only interrupt for excellent suggestions
- Example: Base 0.6 → 0.7 in focused mood

**Rushed mood**:[^11_1]

- Adjustment: -0.05 (more helpful)
- Rationale: User needs quick help
- Example: Base 0.6 → 0.55 in rushed mood

**Frustrated mood**:[^11_1]

- Adjustment: -0.1 (very helpful)
- Rationale: User struggling, offer more assistance
- Example: Base 0.6 → 0.5 in frustrated mood

**Exploratory mood**:

- Adjustment: 0.0 (neutral)
- Rationale: User exploring, standard suggestions fine

**Casual mood**:

- Adjustment: 0.0 (neutral)

**Neutral mood**:

- Adjustment: 0.0 (baseline)

**Real-time application**: Threshold recalculated every time mood updates (every 30s)

#### 45.3 Acceptance Rate Tuning[^11_1]

Adjusts based on user engagement:

**Acceptance rate tracking**:

- Calculate: accepted / (accepted + dismissed)
- Rolling window: Last 50 suggestions
- Per-session rate: This session only
- Historical rate: All-time average

**Threshold adjustments**:[^11_1]

**High acceptance (>70%)**:[^11_1]

- User trusts suggestions
- Adjustment: -0.05 (show more suggestions)
- Confidence: System is working well

**Average acceptance (40-70%)**:

- Healthy engagement
- Adjustment: 0.0 (no change)
- Keep current strategy

**Low acceptance (<30%)**:[^11_1]

- User frequently rejects suggestions
- Adjustment: +0.1 (be more selective)
- Show fewer, higher-quality suggestions

**Recovery mechanism**:

- If acceptance improves after adjustment: Keep new threshold
- If acceptance degrades further: Revert adjustment
- Continuous optimization


#### 45.4 Suggestion Length Preferences[^11_1]

User's preferred suggestion size:

**Length categories**:

- **Short**: 10-30 characters (1-5 words)
- **Medium**: 30-60 characters (5-12 words)
- **Long**: 60-100 characters (12-20 words)

**Learning length preference**:

- Track length of accepted suggestions
- Calculate average: Weighted by acceptance speed
- Instant accepts weighted 3x (really liked it)
- Hesitant accepts weighted 0.5x (barely liked it)

**Per-context length**:

- Email subject lines: Prefer short
- Email body: Prefer medium-long
- Code comments: Prefer medium
- Chat messages: Prefer short
- Document writing: Prefer medium-long

**Dynamic length**:

- Rushed mood: Shorter suggestions
- Exploratory mood: Longer suggestions
- End of sentence: Longer OK
- Mid-sentence: Shorter better

***

## Module 46: Performance Metrics Tracker

### Overview

The Performance Metrics Tracker measures **how well the AI Keyboard is performing**. It tracks acceptance rates, time saved, accuracy, and system health.[^11_2]

### Core Responsibilities

#### 46.1 Acceptance Rate Calculator[^11_2]

Measures suggestion quality:

**Acceptance rate formula**:

```
acceptance_rate = accepted_suggestions / total_suggestions_shown
```

**Acceptance rate targets**:

- **Excellent**: >80%
- **Good**: 60-80%
- **Acceptable**: 40-60%
- **Poor**: <40% (needs improvement)

**Target per system**: >70%[^11_2]

**Breakdowns**:

- **By app**: Gmail 75%, Slack 82%, Word 68%
- **By mood**: Focused 73%, Rushed 65%, Frustrated 58%
- **By time**: Morning 75%, Afternoon 68%, Evening 62%
- **By suggestion type**: Completion 72%, Correction 85%, Rewrite 65%

**Trend analysis**:

- Week-over-week change
- Declining trend: Investigation needed
- Improving trend: System learning successfully


#### 46.2 Time Saved Estimator[^11_2]

Calculates productivity benefit:

**Calculation method**:

```
For each accepted suggestion:
  characters_in_suggestion = len(suggestion)
  user_typing_speed_cps = baseline_wpm * 5 / 60  # chars per second
  time_saved_seconds = characters_in_suggestion / user_typing_speed_cps

Total time saved = sum(time_saved_seconds for all accepted)
```

**Example**:

- User WPM: 60 → 5 chars/second
- Suggestion: "demonstrates significant growth" (30 chars)
- Time saved: 30 / 5 = 6 seconds

**Aggregate metrics**:

- Time saved this session: 12 minutes
- Time saved today: 47 minutes
- Time saved this week: 4.2 hours
- Time saved all-time: 87 hours

**Goal**: >30 minutes saved per day[^11_2]

**Displayed to user**: Dashboard shows time saved with visual progress

#### 46.3 Context Detection Accuracy

Measures context understanding:

**What to measure**:

- Did we detect app correctly?
- Did we detect task correctly (email, code, document)?
- Did we detect mood correctly?
- Was cross-app context relevant?

**Validation methods**:

**Implicit validation**:

- If suggestions accepted: Context likely correct
- If suggestions dismissed: Context might be wrong

**Explicit validation** (sampling):

- Occasionally ask user: "Are you writing an email?" Yes/No
- Occasionally ask: "Are you feeling rushed?" Yes/No
- Build ground truth dataset

**Accuracy tracking**:

- App detection: >99% (easy, process name)
- Task detection: >90% (harder, needs inference)
- Mood detection: >75% (challenging, behavioral)

**Improvement loop**:

- Low accuracy → Investigate false positives/negatives
- Adjust detection algorithms
- Retrain models if needed


#### 46.4 Mood Accuracy Validator[^11_1]

Checks if mood detection is correct:

**Validation approaches**:

**Indirect validation**:

- If mood = "frustrated" and user accepts many helpful suggestions: Likely correct
- If mood = "focused" and user dismisses frequent suggestions: Likely incorrect (interrupting flow)

**Explicit feedback**:

- Mood indicator in UI: User can click "Wrong mood"
- Collect corrections: "System said Frustrated, actually Focused"
- Learn from corrections

**Validation metrics**:

- Mood detection confidence: How sure is system?
- Mood stability: Does mood change erratically? (might be misdetecting)
- Mood-suggestion alignment: Do suggestions match mood appropriately?

**Accuracy target**: >75%[^11_1]

**Consequences of inaccuracy**:

- Wrong debounce timing: Too fast or too slow
- Wrong threshold: Too many or too few suggestions
- Wrong suggestion style: Mismatched to user state


#### 46.5 Latency Benchmarking (n-gram <10ms, LLM 50-200ms)[^11_3]

Measures system responsiveness:

**Latency targets**:[^11_3]

**N-gram suggestions**:

- Target: <10ms[^11_3]
- Typical: 5-8ms
- Maximum acceptable: 20ms
- Breach action: Investigate model size, optimize

**Local LLM**:

- Target: 50-150ms[^11_3]
- Typical: 80-120ms
- Maximum acceptable: 200ms
- Breach action: Try smaller model, optimize context

**Cloud LLM**:

- Target: 100-300ms
- Typical: 150-250ms
- Maximum acceptable: 500ms[^11_3]
- Breach action: Fallback to local[^11_3]

**End-to-end latency** (user types → suggestion visible):

- Target: <200ms perceived[^11_3]
- N-gram appears in 10ms
- LLM appears in 180ms
- Feels instant due to streaming[^11_3]

**Latency monitoring**:

- Track percentiles: P50, P95, P99
- Alert if P95 exceeds target
- Daily latency reports

**Optimization triggers**:

- If N-gram >15ms consistently: Optimize n-gram model
- If LLM >300ms consistently: Switch to smaller model or cloud
- If network >500ms: Recommend local-only mode


#### 46.6 Privacy Compliance Monitor

Ensures privacy rules followed:

**Compliance checks**:

- **Blocklist enforcement**: AI never runs in blocked apps (100% compliance)
- **Password field detection**: AI disabled in password fields (100% compliance)
- **Data retention**: STM deleted at session end (100% compliance)
- **Encryption**: All data encrypted at rest (100% compliance)

**Audit logging**:

- Log when AI paused due to privacy (timestamp, reason)
- Log when user explicitly blocks app
- Log data deletion events
- Never log actual user content (privacy)

**Privacy violation detection**:

- Monitor for any suggestion shown in blocked app (should never happen)
- Alert immediately if detected
- Investigate root cause
- Fix and verify

**User transparency**:

- Privacy dashboard shows compliance status
- "AI has been blocked 47 times in banking apps" (reassures user)
- "0 privacy violations detected" (builds trust)

***

## Module 47: Session Analytics

### Overview

The Session Analytics module tracks **user behavior and patterns within sessions**. It builds a rich picture of how users interact with the system.[^11_1][^11_3]

### Core Responsibilities

#### 47.1 Session Duration Tracker

Measures typing session length:

**Session boundaries**:

- **Start**: First keystroke after app open or >30 min inactivity
- **End**: App close or 30 minutes of inactivity[^11_2]

**Duration metrics**:

- Active typing time: Actual time spent typing
- Total session time: Including pauses, reading
- Active percentage: Typing time / total time

**Session patterns**:

- **Short bursts**: <10 min (quick edits, messages)
- **Standard sessions**: 10-60 min (emails, small docs)
- **Long sessions**: 60-180 min (reports, coding)
- **Marathon sessions**: >180 min (major projects)

**Session quality**:

- Productive session: High typing %, high acceptance rate
- Distracted session: Low typing %, many app switches
- Struggling session: High backspace rate, low acceptance


#### 47.2 Typing Speed Baseline Profiler[^11_3]

Establishes user's normal typing speed:

**Baseline calculation**:

- Measure WPM across all sessions
- Remove outliers (very slow = thinking, very fast = pasting)
- Calculate moving average: 90% historical, 10% current[^11_3]
- Store as user's baseline[^11_2]

**Why baseline matters**:

- Context for current speed: Is user faster or slower than normal?
- Mood detection: Speed deviations indicate state changes[^11_1]
- Adaptive debounce: Fast typers need different timing[^11_3]

**Baseline by context**:

- Coding: Slower (thinking while typing)
- Chat: Faster (casual, flowing)
- Email: Medium (thoughtful but flowing)
- Documents: Medium-slow (careful, editing)

**Baseline evolution**:

- User gets faster with practice: Baseline increases
- User types differently over time: Baseline adapts
- Monthly recalculation to catch trends


#### 47.3 App Usage Patterns[^11_1]

Tracks which apps used when:

**Usage tracking**:

```
{
  "session_id": "session_123",
  "apps_used": [
    {
      "app": "Microsoft Word",
      "duration_seconds": 2400,  // 40 minutes
      "percentage": 80%,
      "suggestions_shown": 38,
      "acceptance_rate": 0.71
    },
    {
      "app": "Chrome",
      "duration_seconds": 300,  // 5 minutes
      "percentage": 10%,
      "suggestions_shown": 4,
      "acceptance_rate": 0.50
    },
    {
      "app": "Slack",
      "duration_seconds": 300,
      "percentage": 10%,
      "suggestions_shown": 12,
      "acceptance_rate": 0.83
    }
  ]
}
```

**Insights from patterns**:

- **Primary app**: Where user spends most time
- **Best performing app**: Highest acceptance rate
- **App transitions**: Common workflows
- **Time of day patterns**: Morning = email, afternoon = coding

**Use cases**:

- Optimize per-app suggestions
- Pre-load app preferences
- Detect unusual patterns (security)


#### 47.4 Productivity Window Detector

Identifies when user is most effective:

**Productivity signals**:

- High typing speed
- High acceptance rate
- Low backspace frequency
- Focused mood[^11_1]
- Long continuous typing sessions

**Time window tracking**:

- Morning (6 AM - 12 PM): Productivity score
- Afternoon (12 PM - 6 PM): Productivity score
- Evening (6 PM - 12 AM): Productivity score
- Night (12 AM - 6 AM): Productivity score

**Productivity score formula**:

```
productivity = (typing_speed / baseline) * acceptance_rate * (1 - backspace_rate)
```

**Example findings**:

- User most productive: 9 AM - 11 AM (score: 1.35)
- User least productive: 3 PM - 5 PM (score: 0.72, post-lunch dip)
- Evening productivity: 0.85 (tired but steady)

**Applications**:

- Notify user of productivity trends
- Adjust suggestion aggressiveness by time
- Wellness insights: "You work best in the morning"


#### 47.5 Inter-Key Interval Statistics[^11_3]

Analyzes typing rhythm:

**Interval measurements**:[^11_3]

- Time between consecutive keystrokes
- Distribution of intervals (histogram)
- Mean, median, standard deviation
- Typing rhythm consistency

**Rhythm patterns**:

**Steady rhythm** (low std dev):

- Intervals: 120ms, 125ms, 118ms, 122ms (consistent)
- Indicates: Focused, flowing typing
- Example: Transcribing, copying text

**Variable rhythm** (high std dev):

- Intervals: 100ms, 300ms, 150ms, 500ms (erratic)
- Indicates: Thinking between words, composing
- Example: Creative writing, problem solving

**Burst-pause pattern**:

- Fast burst: 50ms intervals
- Long pause: 2000ms
- Fast burst: 50ms intervals
- Indicates: Type remembered phrase, think, type next phrase

**Use in debounce**:[^11_3]

- Steady rhythm: Can predict pause duration
- Variable rhythm: Harder to predict, use conservative debounce
- Median interval × 3 = good debounce estimate[^11_3]

***

## Module 48: Logging \& Debug Module

### Overview

The Logging \& Debug Module provides **visibility into system operation** for troubleshooting and development. It captures events, errors, and performance data without impacting user experience.

### Core Responsibilities

#### 48.1 Event Logger

Records system events:

**Log levels**:

- **DEBUG**: Detailed info for developers (verbose)
- **INFO**: Routine events (app start, suggestion shown)
- **WARNING**: Unusual but handled (high latency, model fallback)
- **ERROR**: Failures (API error, file not found)
- **CRITICAL**: System failures (crash, data loss)

**Logged events**:

```
[INFO] 2026-01-30 01:20:15 - Session started: user_12345
[DEBUG] 2026-01-30 01:20:16 - App detected: Microsoft Word
[INFO] 2026-01-30 01:20:30 - Suggestion shown: "demonstrates significant growth" (confidence: 0.87)
[INFO] 2026-01-30 01:20:31 - Suggestion accepted: instant (450ms)
[WARNING] 2026-01-30 01:21:45 - Cloud LLM latency high: 680ms (threshold: 500ms)
[INFO] 2026-01-30 01:21:45 - Fallback to local LLM
[ERROR] 2026-01-30 01:23:12 - Failed to read text from field (UI Automation error)
```

**Log rotation**:

- Daily log files
- Keep last 7 days (1 week history)
- Compress old logs
- Delete logs >30 days

**Privacy in logs**:

- Never log actual user text content
- Log context metadata only (app name, text length)
- Anonymize user IDs in debug logs
- Option to disable logging (privacy mode)


#### 48.2 Error Handler

Catches and processes errors:

**Error handling strategy**:

- Catch all exceptions (prevent crashes)
- Log error with stack trace
- Attempt recovery if possible
- Notify user only if impacts them
- Report to developers (anonymous telemetry)

**Error categories**:

**Recoverable errors**:

- Network timeout: Fallback to local LLM
- UI Automation failure: Try alternate method
- Model loading error: Use cached model

**User-facing errors**:

- "AI temporarily unavailable"
- "Suggestion failed, trying again"
- "Please check internet connection"

**Critical errors**:

- Data corruption: Attempt repair, notify user
- Security breach: Immediately pause all AI
- System crash: Save what's possible, restart

**Error reporting**:

- Option to send error reports to developers
- Anonymous, no user content included
- Helps improve system reliability


#### 48.3 Debug Mode Controller

Enables detailed troubleshooting:

**Debug mode activation**:

- Settings toggle: "Enable debug mode"
- Command line flag: `--debug`
- Keyboard shortcut: Ctrl+Shift+Alt+D

**Debug features**:

**Verbose logging**:

- All DEBUG level logs written
- Real-time log viewer in UI
- Export logs to file

**Performance overlay**:

- Show latency numbers on suggestions
- Display confidence scores
- Show which model used (cloud/local)

**Context inspector**:

- View current STM content
- View LLM prompt sent
- View LLM response received

**Event timeline**:

- Visual timeline of events
- Click event to see details
- Useful for understanding behavior

**Debug info to share**:

- System info: OS, version, hardware
- Performance stats: Average latencies
- Error history: Recent errors
- Help support diagnose issues


#### 48.4 Performance Profiler

Measures execution time:

**Profiling points**:

- Component entry/exit times
- Function execution duration
- API call round-trip time
- UI render time

**Profiling output**:

```
Component Performance Report:
- STM Update: 12ms
- N-gram Prediction: 7ms
- LLM API Call: 185ms
- Context Building: 23ms
- UI Rendering: 4ms
- Total: 231ms
```

**Bottleneck identification**:

- If component consistently slow: Optimize
- If occasional slow: Investigate cause
- If increasing over time: Memory leak?

**Profiling overhead**:

- Minimal: <1% performance impact
- Only active in debug mode by default
- Production profiling: Sample 1% of operations


#### 48.5 Latency Alert System[^11_3]

Monitors and alerts on slow performance:

**Alert thresholds**:[^11_3]

- N-gram >20ms: Warning
- Local LLM >200ms: Warning
- Cloud LLM >500ms: Warning[^11_3]
- End-to-end >1000ms: Critical

**Alert actions**:

- Log warning with details
- Increment slow operation counter
- If 3+ warnings in row: Notify user
- If 10+ warnings in session: Suggest mode change

**Alert to user**:

- System tray icon: Yellow for warnings
- Tooltip: "AI responding slowly"
- Suggestion: "Switch to faster mode?"

**Alert to developers**:

- Anonymous telemetry (if opted in)
- Aggregate latency stats
- Help identify performance regressions

***

## Module 49: Text Similarity Engine

### Overview

The Text Similarity Engine measures **how related two pieces of text are**. Used for clipboard relevance, duplicate detection, and context matching.[^11_1]

### Core Responsibilities

#### 49.1 Semantic Similarity Calculator (for Clipboard Relevance)[^11_1]

Determines if clipboard content relates to current typing:

**Similarity methods**:

**Method 1: TF-IDF + Cosine Similarity**:

- Convert both texts to TF-IDF vectors
- Calculate cosine similarity between vectors
- Range: 0.0 (unrelated) to 1.0 (identical)
- Fast, works offline

**Method 2: Word Overlap**:

- Count shared meaningful words
- Exclude common words (the, a, is)
- Similarity = shared_words / min(words_text1, words_text2)

**Method 3: Embedding Similarity** (advanced):

- Use sentence embeddings (e.g., Sentence-BERT)
- Calculate cosine similarity in embedding space
- Captures semantic meaning better
- Slower, requires model

**Example**:

- Clipboard: "Revenue: \$2.4M (+15% YoY)"
- Current text: "In conclusion, our financial performance exceeded expectations"
- Shared concepts: Financial, performance, growth
- TF-IDF similarity: 0.68
- Word overlap: 0.0 (no shared words, but related)
- Embedding similarity: 0.72 (captures semantic relation)
- **Final similarity**: 0.72 (use embedding if available, else TF-IDF)

**Use in clipboard relevance**:[^11_1]

- Similarity >0.7: High relevance, include in context
- Similarity 0.5-0.7: Medium relevance, brief mention
- Similarity <0.5: Low relevance, exclude


#### 49.2 Vector Embedding Generator

Converts text to numerical vectors:

**Embedding models**:

- **Local**: Sentence-BERT (110MB model, runs on CPU)
- **Lightweight**: Universal Sentence Encoder Lite (40MB)
- **Cloud**: OpenAI embeddings API (highest quality)

**Embedding process**:

```python
# Input text
text = "The quarterly report shows strong growth"

# Generate embedding (384-dim vector)
embedding = model.encode(text)
# Result: [0.23, -0.15, 0.67, ..., 0.42]  # 384 numbers

# Compare with another text
text2 = "Financial results exceeded expectations"
embedding2 = model.encode(text2)

# Calculate similarity
similarity = cosine_similarity(embedding, embedding2)
# Result: 0.78 (high similarity)
```

**Caching**:

- Cache embeddings for clipboard content (save recomputation)
- Cache embeddings for frequent phrases
- Clear cache when clipboard changes

**Use cases**:

- Clipboard similarity[^11_1]
- Cross-app context relatedness[^11_4]
- Duplicate suggestion detection


#### 49.3 Pattern Matcher

Finds text patterns and structures:

**Pattern types**:

**Exact match**:

- Identical text
- Case-sensitive or insensitive
- Used for: Duplicate detection

**Fuzzy match**:

- Allow minor differences (typos, spacing)
- Levenshtein distance <3
- Used for: Similar suggestions

**Regex patterns**:

- Structural patterns (email, phone, URL)
- Domain-specific patterns (code syntax)
- Used for: Content type detection

**N-gram overlap**:

- Shared word sequences
- Trigram overlap: "the quick brown" vs "quick brown fox"
- Used for: Similarity estimation

**Example patterns**:

```
Financial data pattern:
- "$X.XM" → Revenue mention
- "X% YoY" → Year-over-year growth
- "EBITDA" → Financial metric

Code pattern:
- "def function_name(" → Python function
- "import * from *" → Import statement
- "# TODO" → Code comment

Email pattern:
- "Dear [Name]," → Email greeting
- "Best regards," → Email closing
```

**Use cases**:

- Detect duplicate suggestions (pattern match)
- Identify context type (email, code, data)
- Extract structured information

***

## Module 50: Prompt Template Manager

### Overview

The Prompt Template Manager maintains **pre-built LLM prompt structures** for different tasks. It ensures prompts are consistent, optimized, and context-appropriate.[^11_1][^11_3]

### Core Responsibilities

#### 50.1 Template Storage (Document Writing, Email, Code)[^11_1]

Maintains task-specific prompt templates:

**Document Writing Template**:[^11_1]

```
System: You are an AI writing assistant providing completions for professional documents.

Context:
- Document: {document_title}
- Section: {current_section}
- User style: {writing_style}
- Formality: {formality_level}

Current text:
"""
{text_before_cursor}
"""

Task: Complete the sentence naturally, maintaining professional tone and document flow.

Output format:
{
  "suggestion": "your completion here",
  "type": "completion",
  "confidence": 0.0-1.0
}
```

**Email Template**:[^11_1]

```
System: You are an AI email assistant.

Context:
- Email to: {recipient}
- Subject: {subject}
- Thread: {thread_preview}
- User's typical email style: {email_style}

Current email:
"""
{email_body}
"""

Task: Complete the email professionally, matching user's typical style.

Output format: {...}
```

**Code Template**:[^11_3]

```
System: You are an AI code completion assistant.

Context:
- Language: {programming_language}
- File: {filename}
- Current function: {function_name}
- Imports: {relevant_imports}
- Related code: {related_file_summaries}

Code:
```{language}
{code_before_cursor}█{code_after_cursor}
```

Task: Suggest next line(s) of code. Follow {language} best practices.

Output format: {...}

```

**Storage format**: JSON files with variable placeholders

#### 50.2 Template Selector
Chooses appropriate template for context:

**Selection logic**:
```

if app_type == "email":
template = EMAIL_TEMPLATE
elif app_type == "code_editor":
template = CODE_TEMPLATE
elif app_type == "document_editor":
template = DOCUMENT_TEMPLATE
elif app_type == "chat":
template = CHAT_TEMPLATE
else:
template = GENERIC_TEMPLATE

```

**Template variants**:
- Document: Report, essay, notes
- Email: New, reply, formal, casual
- Code: Completion, comment, refactor
- Chat: Message, reply, quick response

**Dynamic selection**:
- Consider user preference
- Consider task detected
- Consider mood (rushed → simpler template)

#### 50.3 Variable Injector
Fills template with actual context:

**Variable substitution**:
```python
template = load_template("document_writing")

# Variables from context
variables = {
    "document_title": "Q1_Performance_Report.docx",
    "current_section": "Conclusion",
    "writing_style": "professional",
    "formality_level": "0.75",
    "text_before_cursor": "In conclusion, our analysis shows that",
    # ... more variables
}

# Inject variables into template
prompt = template.format(**variables)
# Result: Complete prompt ready for LLM
```

**Variable processing**:

- Escape special characters (prevent injection attacks)
- Truncate long variables (stay within token budget)
- Provide defaults for missing variables
- Validate variable types (formality must be 0-1)

**Conditional variables**:

- Only include clipboard if relevant
- Only include cross-app if high relevance
- Only include mood if confidence >70%


#### 50.4 Context-Optimized Template Builder (2000 Token Budget)[^11_3]

Creates prompts within token limits:

**Token budget allocation**:[^11_3]

```
Total budget: 2000 tokens

Template breakdown:
- System prompt (fixed): 150 tokens
- Task instruction (fixed): 100 tokens
- Output format (fixed): 50 tokens
- Context (variable): up to 1200 tokens
- Current text (variable): up to 500 tokens

Total: ~2000 tokens
```

**Context optimization**:

- **Priority 1**: Current text (always include, truncate if >500 tokens)
- **Priority 2**: User style and preferences (essential for quality)
- **Priority 3**: App and task context (helpful but can summarize)
- **Priority 4**: Cross-app context (include if relevant and space allows)
- **Priority 5**: Clipboard (include if relevant and space allows)

**Token counting**:

- Use tokenizer for accurate count
- Different models have different tokenizers (GPT vs Claude)
- Count before sending (prevent API errors)
- Truncate intelligently (keep most relevant parts)

**Example budget allocation**:

```
For document writing suggestion:
- System + Task + Format: 300 tokens (fixed)
- Document title: 10 tokens
- Current section: 5 tokens
- User style: 50 tokens
- Text before cursor: 400 tokens
- Cross-app context: 200 tokens (financial data from spreadsheet)
- Clipboard: 100 tokens (revenue numbers)
- Mood/behavior: 50 tokens
- Recent feedback: 80 tokens

Total: 1195 tokens (within budget)
```

**Overflow handling**:

- If exceeds 2000: Remove lowest priority items first
- If still exceeds: Truncate current text (keep last N chars)
- Never send prompts >2000 tokens (causes LLM errors)


### Template Maintenance

**Version control**:

- Templates stored in version control
- Track changes and improvements
- Rollback if new template performs worse

**A/B testing**:

- Test template variations
- Measure acceptance rate
- Keep best-performing template

**Continuous improvement**:

- Analyze low-confidence suggestions
- Refine prompt wording
- Add clarifying instructions
- Remove unnecessary verbosity

***

## Summary: Modules 41-50

These 10 modules provide **system coordination, configuration, and monitoring**:

- **Modules 41-42**: System coordination (context sync, event bus)
- **Modules 43-45**: Configuration (user preferences, context window, suggestion settings)
- **Modules 46-48**: Monitoring (performance metrics, session analytics, logging)
- **Modules 49-50**: Utilities (text similarity, prompt templates)

**Next in Part 6 (Modules 51-60)**: Workflow-specific assistants and system integration modules.

***

**End of Part 5**

*Ready to continue with Part 6 covering Modules 51-60 when you're ready.*

<div align="center">⁂</div>

[^11_1]: CORE_INTELLIGENCE_DESIGN.md

[^11_2]: ai_keyboard_sol.md

[^11_3]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md

[^11_4]: SOLUTION_DECISIONS.md

[^11_5]: GRAMMARLY_TECHNICAL_RESEARCH.md


---

# AI Keyboard - Complete Module Documentation

## Part 6: Workflow Assistants \& Advanced Features (Modules 51-60)

> **Document Version:** 1.0
> **Date:** January 30, 2026, 1:35 AM IST
> **Scope:** Detailed explanation of Modules 51-60
> **Purpose:** Specialized workflow assistance and advanced system capabilities

***

## Module 51: Email Writing Assistant

### Overview

The Email Writing Assistant provides **specialized intelligence for email composition**. It understands email context (recipients, thread, formality) and generates appropriate suggestions for different email scenarios.[^12_1][^12_2]

### Core Responsibilities

#### 51.1 Thread Context Analyzer[^12_2]

Understands email conversation history:

**Thread parsing**:

- Identify previous messages in thread
- Extract key information: who said what, when
- Detect conversation flow (question → answer → follow-up)
- Identify unresolved points needing response

**Information extraction**:

```
Thread: "Q1 Report Review"
├─ Message 1 (John → You, Jan 28):
│  "Thanks for the draft. Could you clarify the revenue growth
│   calculation? Also, when can we discuss the findings?"
│
├─ Message 2 (You → John, Jan 29):
│  "Sure! Revenue growth was calculated using..."
│
└─ Current reply (You → John, Jan 30):
   "Regarding the meeting to discuss findings,"
   ← AI needs to suggest completion
```

**Context provided to LLM**:

- **Thread summary**: "John asked about revenue calculation and meeting. You explained calculation, now addressing meeting."
- **Unresolved items**: ["schedule meeting to discuss findings"]
- **Tone of thread**: Professional, collaborative
- **Your previous style in thread**: Detailed, helpful

**Use cases**:

- Complete reply addressing specific questions
- Suggest meeting times when scheduling mentioned
- Reference previous points naturally
- Match thread formality and tone


#### 51.2 Recipient Analyzer[^12_2]

Tailors suggestions based on who receives the email:

**Recipient profiling**:

- **Role**: Boss, colleague, client, vendor, team member
- **Relationship history**: Frequent contact or rare
- **Previous tone**: Formal or casual emails
- **Response patterns**: Quick replies or detailed responses

**Detection methods**:

- Email domain: Internal (@company.com) vs external
- Name/title: "Dr. Smith", "CEO", "Jane from Support"
- Email history: Previous emails with this person
- CC list: More formal if many people CC'd

**Formality adjustment**:[^12_1]

```
Recipient: CEO
├─ Formality: 0.9 (very formal)
├─ Style: Concise, executive summary style
├─ Greetings: "Dear [Name]" or formal salutation
└─ Closings: "Best regards", "Sincerely"

Recipient: Close colleague
├─ Formality: 0.4 (casual)
├─ Style: Conversational, friendly
├─ Greetings: "Hi [Name]" or just name
└─ Closings: "Thanks", "Cheers"

Recipient: External client
├─ Formality: 0.7 (professional)
├─ Style: Clear, helpful, respectful
├─ Greetings: "Hello [Name]"
└─ Closings: "Best regards", "Thank you"
```

**Group email handling**:

- Multiple recipients: Use higher formality
- Mixed internal/external: Professional tone
- Large CC list: More structured, clear


#### 51.3 Greeting/Closing Suggester

Provides appropriate email openings and endings:

**Greeting suggestions**:

**First email to someone**:

- Formal: "Dear Dr. Smith,"
- Professional: "Hello Jane,"
- Introduction: "Hi, I'm [Name] from [Team],"

**Reply to existing thread**:

- Continuation: "Thanks for your quick response,"
- Acknowledgment: "I appreciate you getting back to me,"
- Simple: "Hi again,"

**Context-aware greetings**:

- Morning: "Good morning,"
- After delay: "Apologies for the delayed response,"
- After meeting: "It was great speaking with you today,"

**Closing suggestions**:

**By formality level**:

- Very formal (0.9): "Respectfully,", "Sincerely,"
- Professional (0.7): "Best regards,", "Kind regards,"
- Friendly (0.5): "Thanks,", "Best,"
- Casual (0.3): "Cheers,", "Talk soon,"

**By purpose**:

- Requesting action: "Looking forward to your response,"
- Providing info: "Please let me know if you need anything else,"
- Scheduling: "Looking forward to connecting,"
- Closing conversation: "Thanks again for your help,"

**Smart triggering**:

- Detect if user typing at email start → Suggest greeting
- Detect if user near email end → Suggest closing
- Don't suggest if already present


#### 51.4 Tone Adapter (Formal/Casual/Urgent)[^12_1]

Matches email urgency and style:

**Tone detection**:

**Formal tone** (business, legal, executive):

- Complete sentences, proper grammar
- No contractions: "I would" not "I'd"
- Structured paragraphs
- Professional vocabulary
- Example: "I would appreciate your review of the attached document at your earliest convenience."

**Casual tone** (team communication, friendly):

- Contractions: "I'd", "we'll", "don't"
- Shorter sentences
- Conversational flow
- Example: "Would love to get your thoughts on this when you have a sec!"

**Urgent tone** (time-sensitive):

- Direct language: "Please review immediately"
- Clear deadlines: "by EOD", "before the meeting"
- Action-oriented: "Need your approval to proceed"
- Example: "Quick question – can you approve this by 3 PM today? We need to submit before EOD."

**Tone signals from context**:

- Subject line: "URGENT:", "FYI:", "Quick question"
- Time of day: Late evening → likely urgent
- User typing speed: Fast → urgent
- Keywords: "ASAP", "immediately", "urgent", "deadline"

**Adaptive suggestions**:

```
Context: Urgent request to manager
├─ Suggestion style: Direct, action-focused
├─ Formality: Maintain professionalism (0.75)
├─ Length: Concise (user needs quick send)
└─ Example: "Could you approve the budget request by 2 PM? 
    Marketing needs confirmation to proceed with the campaign."

Context: Casual team update
├─ Suggestion style: Friendly, conversational
├─ Formality: Relaxed (0.3)
├─ Length: Brief, easy to scan
└─ Example: "Quick update - shipped the feature this morning! 
    Let me know if you spot any issues."
```


#### 51.5 Template Library (Requests, Updates, Follow-ups)

Pre-built email structures:

**Request email template**:

```
Context: User needs to request something

Suggested structure:
1. Greeting
2. Context (why you're writing)
3. Specific request (clear, actionable)
4. Deadline or timeframe (if applicable)
5. Appreciation
6. Closing

Example suggestion:
"I'm reaching out to request approval for the Q2 marketing budget. 
Could you review the attached proposal by Friday? Happy to discuss 
any questions you might have. Thank you for your time."
```

**Update email template**:

```
Context: Providing status update

Suggested structure:
1. Brief greeting
2. Update summary (what's new)
3. Key accomplishments or changes
4. Next steps or action items
5. Invitation for questions
6. Closing

Example suggestion:
"Quick update on the project: we've completed the design phase 
ahead of schedule. Development starts next week. Let me know if 
you'd like a detailed walkthrough."
```

**Follow-up email template**:

```
Context: Following up on previous email

Suggested structure:
1. Reference to previous email
2. Gentle reminder of request
3. Offer to help or clarify
4. New deadline or urgency (if applicable)
5. Closing

Example suggestion:
"Following up on my email from last week about the budget 
approval. Would you have a chance to review it this week? 
Happy to provide any additional details you need."
```

**Auto-detection**:

- Detect email type from context
- Suggest relevant template structure
- Adapt template to user's style
- Fill in context-specific details

***

## Module 52: Code Completion Assistant

### Overview

The Code Completion Assistant provides **programming-specific intelligence**. It understands code syntax, project structure, and programming patterns to generate accurate code suggestions.[^12_3]

### Core Responsibilities

#### 52.1 Language-Specific Syntax Engine[^12_3]

Handles multiple programming languages:

**Supported languages**:

- Python, JavaScript, TypeScript, Java, C++, C\#, Go, Rust
- HTML, CSS, SQL, Bash, PowerShell
- React/JSX, Vue, Angular templates

**Syntax-aware completion**:

**Python example**:

```python
# User types:
def calculate_revenue(data):
    total = 0
    for item in data:
        total += item.

# AI suggests:
        total += item.price * item.quantity
# or
        total += item.value
# or
        total += item.amount

# Based on: Common attribute patterns, project conventions
```

**JavaScript example**:

```javascript
// User types:
const fetchData = async () => {
    const response = await fetch(url);
    const data = await response.

// AI suggests:
    const data = await response.json();
# or
    const data = await response.text();

# Based on: Fetch API patterns, async/await conventions
```

**Syntax validation**:

- Check for balanced parentheses, brackets, braces
- Suggest closing tags (HTML, JSX)
- Match indentation level
- Follow language conventions (semicolons in JS, colons in Python)


#### 52.2 Multi-File Context Analyzer (Up to 5 Files)[^12_3]

Understands code across multiple files:

**File selection algorithm**:

1. **Current file** (always included, full content)
2. **Direct imports** (files imported by current file)
3. **Reverse imports** (files that import current file)
4. **Recently edited related files** (modified in last 30 min)
5. **Test files** (if editing implementation, include tests)

**Example context** (editing `payment_processor.py`):

```
Current file: payment_processor.py (full content)
├─ Imports analyzed:
│  ├─ database.py (class Database, method get_user())
│  ├─ config.py (API keys, settings)
│  └─ models.py (class PaymentModel)
│
├─ Imported by:
│  └─ api_handler.py (uses process_payment())
│
└─ Related files:
   └─ test_payment_processor.py (test cases)
```

**Context extraction per file**:

- **Current file**: Full text, all context
- **Direct imports**: Function signatures, class definitions, docstrings
- **Reverse imports**: How this file is used elsewhere
- **Test files**: Expected behavior, example usage

**Cross-file intelligence**:

```python
# In database.py:
class Database:
    def get_user(self, user_id: int) -> User:
        """Fetch user from database"""
        pass

# In payment_processor.py (current file):
def process_payment(user_id):
    db = Database()
    user = db.

# AI suggests with confidence:
    user = db.get_user(user_id)
# Because it knows get_user() signature from database.py
```


#### 52.3 Function Signature Helper

Suggests function calls with correct parameters:

**Signature analysis**:

```python
# Known function (from imports or current file):
def send_email(to: str, subject: str, body: str, cc: List[str] = None):
    """Send email via SMTP"""
    pass

# User types:
send_email(

# AI suggests parameter hints:
send_email(to="", subject="", body="")
# or if context known:
send_email(to=user.email, subject="Account Update", body=notification_text)
```

**Parameter inference from context**:

```python
# Context: Variables in scope
user = get_current_user()  # has user.email attribute
subject_line = "Q1 Report Ready"
message_body = generate_report_email()

# User types:
send_email(

# AI suggests with context:
send_email(to=user.email, subject=subject_line, body=message_body)
# Matches available variables to expected parameter types
```

**Overload handling** (multiple signatures):

```typescript
// Function has multiple signatures
function createElement(tag: string): Element;
function createElement(tag: string, props: Props): Element;
function createElement(tag: string, props: Props, children: Node[]): Element;

// User types:
createElement(

// AI suggests based on likely use:
createElement("div", { className: "container" })
// Most common signature for current context
```


#### 52.4 Import Auto-Suggester

Suggests and adds missing imports:

**Detection**:

- Detect undefined symbols in current code
- Search project and standard libraries for matching names
- Rank by: Common usage, file proximity, naming match

**Example scenario**:

```python
# User types:
df = pd.DataFrame(data)
#    ^^ 'pd' not defined

# AI detects and suggests:
# Option 1: Add import
import pandas as pd

# Option 2: Alternative import
from pandas import DataFrame
df = DataFrame(data)

# Auto-insertion:
# AI adds import at top of file automatically
# or shows suggestion: "Add 'import pandas as pd'?"
```

**Smart import suggestions**:

```javascript
// User types:
const result = await axios.get(url);
//                   ^^^^^ not imported

// AI suggests:
import axios from 'axios';
// or (if already partially imported):
import axios, { AxiosResponse } from 'axios';

// Context-aware:
// If 'axios' used elsewhere in project → same import style
// If following project conventions → match existing patterns
```

**Unused import detection**:

- Detect imports not used in current file
- Suggest removal (keep code clean)
- Show as low-priority suggestion


#### 52.5 Error Pattern Detector

Identifies common coding mistakes:

**Common error patterns**:

**Off-by-one errors**:

```python
# User types:
for i in range(len(array)):
    if i == len(array):  # ❌ Will never be true
        
# AI detects and suggests:
    if i == len(array) - 1:  # Last element
```

**Null/undefined checks**:

```javascript
// User types:
const user = getUser();
user.name  // ❌ Might be null

// AI suggests:
const user = getUser();
if (user) {
    user.name
}
// or modern:
user?.name
```

**Resource leaks**:

```python
# User types:
file = open('data.txt', 'r')
data = file.read()
# ❌ File not closed

# AI suggests:
with open('data.txt', 'r') as file:
    data = file.read()
# Proper resource management
```

**Type mismatches**:

```typescript
// Function expects number
function calculateTotal(amount: number): number

// User types:
const total = calculateTotal("100");  // ❌ String, not number

// AI detects and suggests:
const total = calculateTotal(100);
// or
const total = calculateTotal(parseInt(userInput));
```

**Async/await errors**:

```javascript
// User types:
async function fetchData() {
    const data = await fetch(url);
    return data.json();  // ❌ Not awaited
}

// AI suggests:
async function fetchData() {
    const data = await fetch(url);
    return await data.json();
}
```


***

## Module 53: Document Writing Assistant

### Overview

The Document Writing Assistant provides **specialized help for long-form writing**. It understands document structure, maintains consistency, and helps with formal writing.[^12_1]

### Core Responsibilities

#### 53.1 Section-Aware Context Builder[^12_1]

Understands document structure:

**Document parsing**:

- Detect headings and sections (Title, Introduction, Methods, Results, Conclusion)
- Identify current section user is writing
- Understand section purpose and expected content

**Context provided to LLM**:

```
Document: "Q1_Performance_Report.docx"
Structure:
├─ Executive Summary (completed)
├─ Revenue Analysis (completed)
├─ Market Trends (current section) ← User here
├─ Recommendations (not started)
└─ Conclusion (not started)

Current section context:
- Section: Market Trends
- Purpose: Analyze market conditions and competitive landscape
- Previous section: Revenue Analysis (discussed 15% growth)
- Expected content: Market data, competitor analysis, trends
```

**Section-specific suggestions**:

- **Introduction**: Opening hooks, thesis statements, overview
- **Body sections**: Analysis, data interpretation, evidence
- **Conclusion**: Summaries, key takeaways, calls to action
- **Abstract/Executive Summary**: Concise, high-level overview

**Cross-section consistency**:

- Reference previous sections naturally: "As mentioned in the Revenue Analysis section..."
- Maintain consistent terminology
- Build on earlier points
- Prepare for upcoming sections


#### 53.2 Tone Consistency Checker[^12_1]

Maintains uniform writing style:

**Tone detection across document**:

- Analyze completed sections for baseline tone
- Calculate formality level (0-1 scale)[^12_1]
- Identify voice (active/passive, first/third person)
- Detect vocabulary complexity level

**Example analysis**:

```
Document analysis:
├─ Average formality: 0.75 (professional, formal)
├─ Voice: Primarily active voice (85%)
├─ Person: First person plural ("we", "our") 
├─ Vocabulary: Business terminology, technical terms
├─ Sentence structure: Mix of short (15%) and medium (70%)
└─ Tone consistency score: 0.92/1.0 (very consistent)

Current paragraph analysis:
├─ Formality: 0.65 (slightly more casual)
├─ Voice: Passive voice (75%)
└─ ⚠️ Inconsistency detected: Too casual, too passive
```

**Tone correction suggestions**:

```
User typed: "The data shows we did really well last quarter."

AI suggests (matching document formality):
"The data demonstrates strong performance in Q1, exceeding 
projections across all key metrics."

Rationale:
- "really well" → "strong performance" (more formal)
- Added specificity: "exceeding projections"
- Maintained active voice: "demonstrates"
- Professional vocabulary
```


#### 53.3 Transition Phrase Suggester

Connects ideas smoothly:

**Transition types**:

**Adding information**:

- "Furthermore,", "Additionally,", "Moreover,"
- "In addition to this,", "Beyond that,"
- "Equally important,"

**Contrasting ideas**:

- "However,", "Nevertheless,", "On the other hand,"
- "In contrast,", "Conversely,", "Despite this,"
- "While this is true,", "That said,"

**Showing cause/effect**:

- "Therefore,", "Consequently,", "As a result,"
- "This leads to,", "Thus,", "Hence,"
- "Due to this,"

**Providing examples**:

- "For instance,", "For example,", "Specifically,"
- "To illustrate,", "Consider the case of,"
- "Notably,"

**Concluding**:

- "In conclusion,", "To summarize,", "Ultimately,"
- "In summary,", "Overall,", "Finally,"

**Context-aware selection**:

```
Previous sentence: "Revenue increased by 15% in Q1."
User starts new sentence: ""

Context: Adding supporting detail
AI suggests: "Additionally, all product lines showed growth."

Previous sentence: "We projected 10% growth."
User starts new sentence: ""

Context: Contrasting with reality
AI suggests: "However, actual performance exceeded 15%."

Previous sentence: "Market conditions improved significantly."
User starts new sentence: ""

Context: Showing result
AI suggests: "As a result, our market share increased by 3 points."
```


#### 53.4 Citation/Reference Helper

Assists with academic/formal referencing:

**Citation style detection**:

- APA: (Author, Year)
- MLA: (Author Page)
- Chicago: Footnotes/endnotes
- IEEE:,,[^12_4][^12_5][^12_1]

**In-text citation suggestions**:

```
User typing: "Studies show that AI improves productivity"

AI suggests:
"Studies show that AI improves productivity (Smith et al., 2024)."
# or
"Studies show that AI improves productivity [^12_1]."

Context clues:
- Document already has citations → Match existing style
- Academic paper → Suggest citation here
- Factual claim → Needs source
```

**Reference list formatting**:

```
User adds: "Smith, J. 2024. AI in the Workplace"

AI suggests proper formatting:
"Smith, J., Johnson, M., & Lee, K. (2024). AI in the Workplace: 
A Comprehensive Study. Journal of Technology, 15(3), 245-267. 
https://doi.org/10.1234/jtech.2024.15.3.245"

Follows: APA 7th edition (if that's document style)
```

**Bibliography management**:

- Track citations used in document
- Suggest adding to reference list
- Check for missing citations
- Warn about unreferenced sources


#### 53.5 Readability Optimizer

Improves text clarity:

**Readability metrics**:

- Flesch Reading Ease score
- Flesch-Kincaid Grade Level
- Average sentence length
- Complex word percentage

**Optimization suggestions**:

**Simplify complex sentences**:

```
User typed:
"The implementation of the aforementioned strategic initiatives, 
which were developed through comprehensive stakeholder engagement 
processes, resulted in measurable improvements across multiple 
operational dimensions."

Readability: Grade 18+ (very difficult)

AI suggests:
"The strategic initiatives, developed with stakeholder input, 
improved operations across multiple areas."

Readability: Grade 12 (more accessible)
Improvements: Shorter sentence, simpler words, clearer structure
```

**Replace jargon**:

```
User typed: "Leverage synergies to optimize deliverables"

AI suggests: "Use teamwork to improve results"

Rationale: More direct, clearer meaning
```

**Active voice conversion**:

```
Passive: "The report was completed by the team."
Active: "The team completed the report."

AI detects passive voice and suggests active alternative
```

**Paragraph length warning**:

- Detect very long paragraphs (>10 sentences)
- Suggest natural break points
- Improve scanability

***

## Module 54: Chat/Messaging Assistant

### Overview

The Chat/Messaging Assistant optimizes **casual, quick communication**. It understands chat context, suggests emoji, and helps with rapid responses.[^12_3][^12_1]

### Core Responsibilities

#### 54.1 Conversation Flow Tracker

Maintains chat context:

**Recent message tracking**:

```
Slack - #engineering channel

Recent messages:
├─ 10:15 AM - Alice: "Anyone know why the deploy failed?"
├─ 10:16 AM - Bob: "Checking the logs now"
├─ 10:17 AM - You: "I see the issue -"
└─ Current message (You): ""

Context:
- Topic: Deployment failure
- Your role: Helping debug
- Expected: Technical explanation
- Tone: Collaborative, helpful
```

**Context-aware suggestions**:

```
Given context above, user typing: "I see the issue -"

AI suggests continuations:
"I see the issue - looks like a database connection timeout. 
Restarting the service now."

or

"I see the issue - missing environment variable. Fixed and 
redeploying."

Characteristics:
- Technical but conversational
- Action-oriented (what you're doing)
- Brief (chat format)
- Helpful tone
```

**Multi-party conversations**:

- Track who said what
- Detect if responding to specific person
- Understand conversation branches
- Maintain topical coherence


#### 54.2 Emoji Intelligence System[^12_3]

Context-aware emoji suggestions:

**Emoji categories**:

**Reactions/acknowledgments**:

- 👍 (agreement, approval)
- 👌 (okay, understood)
- ✅ (done, completed)
- 🎉 (celebration, achievement)

**Emotions**:

- 😊 (friendly, positive)
- 😅 (awkward, sheepish)
- 🤔 (thinking, considering)
- 😬 (concerned, uncertain)

**Objects/concepts**:

- 🐛 (bug in code)
- 🚀 (deploy, launch)
- ⚡ (fast, urgent)
- 🔥 (hot topic, important)

**Context-based suggestions**:

```
Message: "Just deployed the new feature"
AI suggests: 🚀 (launch) or ✅ (completed)

Message: "Found a bug in production"
AI suggests: 🐛 (bug) or 😬 (concerned)

Message: "Great work team!"
AI suggests: 🎉 (celebration) or 👏 (applause)

Message: "Thinking about the architecture"
AI suggests: 🤔 (thinking)
```

**Per-app emoji usage**:

- Slack: Emoji common and encouraged
- Microsoft Teams: Moderate emoji use
- Professional email: Minimal or no emoji
- WhatsApp: Emoji frequent

**Learning emoji preferences**:

- Track which emoji user accepts/uses
- Build personal emoji vocabulary
- Suggest user's favorite emoji more often
- Avoid emoji user never uses


#### 54.3 Quick Response Generator

Rapid reply suggestions:

**Common quick responses**:

**Acknowledgments**:

- "Got it, thanks!"
- "Sounds good"
- "Will do"
- "On it"
- "Thanks for the heads up"

**Agreements**:

- "Agreed"
- "Makes sense"
- "That works for me"
- "I'm on board"
- "Let's do it"

**Questions**:

- "Can you clarify?"
- "When do you need this by?"
- "Want me to take a look?"
- "Need any help with that?"

**Status updates**:

- "Working on it now"
- "Just finished"
- "Running into issues"
- "Almost done"
- "Will have it ready soon"

**Context-triggered quick responses**:

```
Incoming: "Can you review the PR?"
Quick responses:
- "On it 👍"
- "Will review in 10 minutes"
- "Reviewing now"

Incoming: "Meeting at 3 PM?"
Quick responses:
- "Works for me"
- "I'll be there"
- "Can we do 4 PM instead?"

Incoming: "Thanks for fixing that bug!"
Quick responses:
- "No problem!"
- "Happy to help"
- "Anytime 😊"
```


#### 54.4 Formality Dampener[^12_1]

Reduces overly formal language for chat:

**Casual transformations**:

**Contractions** (formal → casual):

- "I will" → "I'll"
- "do not" → "don't"
- "cannot" → "can't"
- "We are" → "We're"

**Simplified language**:

- "utilize" → "use"
- "commence" → "start"
- "demonstrate" → "show"
- "indicate" → "say"

**Shorter structures**:

- "I wanted to let you know that..." → "FYI"
- "Could you please..." → "Can you..."
- "I would appreciate if you could..." → "Can you..."

**Example transformations**:

```
User's formal draft:
"I wanted to inform you that I have completed the task and 
uploaded the deliverables to the designated location."

AI suggests casual version:
"Done! Uploaded everything to the folder."

Rationale:
- Chat context = casual tone appropriate
- Much shorter (chat = brief)
- Still clear and complete
```

**Context-sensitive**:

- Slack team channel: Very casual
- Slack with CEO: Moderate formality
- Client chat: Professional but friendly
- Internal chat: Relaxed

***

## Module 55: Command Mode Interface

### Overview

The Command Mode Interface provides **explicit AI control** when users need specific transformations. It offers rewrite, tone adjustment, length control, and custom commands.[^12_3]

### Core Responsibilities

#### 55.1 Text Rewrite Engine[^12_3]

Transforms selected text:

**Rewrite triggers**:

- User selects text
- Presses Ctrl+Shift+R[^12_3]
- Or clicks "Rewrite" button[^12_3]

**Rewrite options menu**:

```
┌──────────────────────────────────┐
│ Rewrite: "The quarterly report..." │
├──────────────────────────────────┤
│ ✨ Make it more professional      │
│ 💬 Make it more casual            │
│ 📐 Make it more formal            │
│ ✂️ Make it more concise           │
│ 📝 Expand with more detail        │
│ 🔄 Rephrase differently           │
│ ✅ Fix grammar and spelling       │
│ 🎨 Custom... (type instruction)   │
└──────────────────────────────────┘
```

**Rewrite examples**:

**Original**: "The thing is we didn't meet the target"

**Make professional**:
"Unfortunately, we did not achieve the targeted goals for this quarter."

**Make casual**:
"Yeah, so we missed the target this time."

**Make concise**:
"We missed the target."

**Expand**:
"Our team's performance this quarter fell short of the established targets,
with final results coming in approximately 8% below the projected goals."

#### 55.2 Tone Shifter[^12_3]

Adjusts emotional tone:

**Tone dimensions**:

**Formality** (casual ↔ formal):

- Casual: Contractions, simple words, conversational
- Formal: Full words, complex vocabulary, structured

**Warmth** (cold ↔ warm):

- Cold: Factual, detached, businesslike
- Warm: Friendly, empathetic, personable

**Confidence** (uncertain ↔ confident):

- Uncertain: "I think", "maybe", "possibly"
- Confident: "I'm certain", "definitely", "clearly"

**Directness** (indirect ↔ direct):

- Indirect: "It might be helpful if we could perhaps consider..."
- Direct: "We should do X."

**Tone shift examples**:

**Original**: "I think maybe we could try a different approach?"

**More confident**:
"We should explore alternative approaches."

**More direct**:
"Let's use a different approach."

**More formal**:
"I propose we consider alternative methodologies for this project."

**Warmer**:
"What if we tried a fresh approach together? I think it could really help!"

#### 55.3 Length Controller[^12_3]

Expands or compresses text:

**Expansion**:

```
Original: "Sales increased."

Expand:
"Sales figures demonstrated significant upward momentum throughout 
the quarter, with growth accelerating particularly in the final month."

Techniques:
- Add context and details
- Include supporting information
- Elaborate on implications
- Provide examples
```

**Compression**:

```
Original: "Our sales team has been working incredibly hard over 
the past quarter, and as a result of their dedicated efforts, we 
have seen a substantial increase in revenue across all of our 
major product lines."

Compress:
"Sales increased significantly across all product lines."

or (very concise):
"Sales rose substantially."

Techniques:
- Remove redundancy
- Eliminate filler words
- Combine related ideas
- Keep core message
```

**Target lengths**:

- Ultra-concise: <10 words
- Concise: 10-25 words
- Standard: 25-50 words
- Detailed: 50-100 words
- Comprehensive: 100+ words


#### 55.4 Custom Command Parser[^12_3]

Handles user-written instructions:

**Command interface**:

```
User selects: "The quarterly results were good"
User types command: "make it more specific with numbers"

AI rewrites:
"The quarterly results exceeded expectations, with revenue 
increasing by 15% to $2.4M year-over-year."

Used: Context (clipboard had $2.4M, 15% mentioned earlier)
```

**Command examples**:

**"Add examples"**:
Before: "AI improves productivity."
After: "AI improves productivity. For instance, it can automate
email responses, suggest code completions, and summarize documents."

**"Make it sound more confident"**:
Before: "I think this might work."
After: "This approach will deliver results."

**"Simplify for non-technical audience"**:
Before: "The API endpoints utilize RESTful architecture."
After: "The system uses web connections to share data."

**"Add transition to next paragraph"**:
Before: "Sales increased. The market conditions changed."
After: "Sales increased. These positive results came despite
challenging market conditions."

**Command understanding**:

- Parse user intent from natural language
- Identify transformation type
- Apply with context awareness
- Preserve core meaning


#### 55.5 Undo/Redo Handler[^12_3]

Manages rewrite history:

**History tracking**:

```
Rewrite history for current selection:
├─ Original: "The thing is we didn't hit target"
├─ Rewrite 1 (professional): "We did not achieve the target"
├─ Rewrite 2 (formal): "The established objectives were not met"
└─ Current: "Unfortunately, performance fell short of targets"

Controls:
- ← Previous version
- → Next version
- ↺ Back to original
```

**Keyboard shortcuts**:

- Ctrl+Z: Undo last rewrite
- Ctrl+Shift+Z: Redo
- Esc: Cancel and restore original

**Multi-step rewrites**:

1. User selects text
2. Applies "Make formal" → Sees result
3. Not satisfied, applies "Make concise" → Sees new result
4. Still not satisfied, presses Undo twice → Back to original
5. Tries "Make professional" → Accepts final result

***

## Module 56: Grammar \& Spell Check Engine

### Overview

The Grammar \& Spell Check Engine provides **real-time error detection and correction**. It identifies spelling, grammar, and style issues as users type.[^12_3]

### Core Responsibilities

#### 56.1 Real-Time Spell Checker[^12_3]

Detects spelling errors:

**Detection method**:

- Dictionary lookup (English + custom vocabulary)
- Real-time checking as user types
- Language-specific dictionaries
- Context-aware correction

**Spell check process**:

```
User types: "The revanue increased significantly"
                    ^^^^^^^
Error detected: "revanue"
Correct spelling: "revenue"

Suggestion shown:
┌────────────────────┐
│ revanue → revenue  │
│                    │
│ Did you mean:      │
│ • revenue  (most likely)
│ • avenue           │
│ • revue            │
└────────────────────┘
```

**Custom dictionaries**:

- User-added words (technical terms, names, brands)
- Domain-specific vocabulary (medical, legal, technical)
- Project-specific terms (company names, product names)

**Capitalization errors**:

- "john" → "John" (proper noun)
- "I" vs "i" (pronoun must be capitalized)
- "Monday" (days of week capitalized)

**Typo detection**:

- Adjacent key errors: "teh" → "the"
- Doubled letters: "tthe" → "the"
- Missing letters: "recieve" → "receive"
- Swapped letters: "taht" → "that"


#### 56.2 Grammar Rule Engine[^12_3]

Identifies grammatical errors:

**Grammar categories**:

**Subject-verb agreement**:

```
Error: "The team are working on it"
Correct: "The team is working on it"
Explanation: Collective noun takes singular verb

Error: "The results is clear"
Correct: "The results are clear"
Explanation: Plural subject needs plural verb
```

**Verb tense consistency**:

```
Error: "She walked to the store and buys milk"
Correct: "She walked to the store and bought milk"
Explanation: Keep past tense consistent
```

**Pronoun agreement**:

```
Error: "Every student should submit their homework"
Correct: "Every student should submit his or her homework"
Alternative: "All students should submit their homework"
Explanation: Singular "every" requires singular pronoun
```

**Article usage**:

```
Error: "I need a advice"
Correct: "I need advice" (uncountable noun)

Error: "This is best solution"
Correct: "This is the best solution"
Explanation: Superlative requires "the"
```

**Comma usage**:

```
Error: "I bought milk eggs and bread"
Correct: "I bought milk, eggs, and bread"
Explanation: Serial comma (Oxford comma)

Error: "The CEO who started the company is retiring"
Correct: "The CEO, who started the company, is retiring"
Explanation: Non-restrictive clause needs commas
```


#### 56.3 Style Suggestion Engine[^12_3]

Improves writing style:

**Style issues**:

**Passive voice detection**:

```
Passive: "The report was written by the team"
Active: "The team wrote the report"
Reason: Active voice is clearer and more direct
```

**Wordiness**:

```
Wordy: "In order to improve performance"
Concise: "To improve performance"

Wordy: "At this point in time"
Concise: "Now"

Wordy: "Due to the fact that"
Concise: "Because"
```

**Redundancy**:

```
Redundant: "Past history"
Better: "History"

Redundant: "Free gift"
Better: "Gift"

Redundant: "End result"
Better: "Result"
```

**Weak language**:

```
Weak: "We might possibly consider maybe trying"
Stronger: "We will try"

Weak: "It seems like it could be good"
Stronger: "It is good"
```

**Clichés**:

```
Cliché: "Think outside the box"
Better: "Approach creatively"

Cliché: "At the end of the day"
Better: "Ultimately" or remove entirely
```


#### 56.4 Context-Aware Corrections[^12_3]

Smart error detection:

**Homophones** (same sound, different meaning):

```
Error: "I'll right the report tomorrow"
Correct: "I'll write the report tomorrow"
Context: "write" makes sense with "report"

Error: "Their going to the store"
Correct: "They're going to the store"
Context: "They are" fits grammatically

Error: "The team won there match"
Correct: "The team won their match"
Context: Possessive pronoun needed
```

**Commonly confused words**:

```
Error: "The data is effected by noise"
Correct: "The data is affected by noise"
Rule: "affect" = verb, "effect" = noun

Error: "Your welcome"
Correct: "You're welcome"
Rule: "You are" contraction

Error: "I could of helped"
Correct: "I could have helped"
Rule: "have" not "of"
```

**Domain-specific corrections**:

```
Technical writing:
- "use" preferred over "utilize"
- "start" preferred over "commence"
- Active voice preferred

Academic writing:
- Formal vocabulary
- Avoid contractions
- Third person perspective

Business writing:
- Concise language
- Action-oriented
- Professional tone
```


#### 56.5 Error Severity Classifier[^12_3]

Prioritizes corrections:

**Severity levels**:

**Critical** (red underline):

- Spelling errors: "revenu" → "revenue"
- Major grammar: "She don't" → "She doesn't"
- Meaning-changing errors
- Must fix for credibility

**Important** (orange underline):

- Minor grammar: Missing comma
- Passive voice in active context
- Awkward phrasing
- Should fix for quality

**Suggestion** (blue underline):

- Style improvements
- Wordiness
- Readability enhancements
- Optional fixes

**Severity determination**:

```
Error: Misspelled word in email subject line
→ Critical (highly visible, unprofessional)

Error: Passive voice in casual chat
→ Low (doesn't matter much in chat)

Error: Missing comma in long sentence
→ Important (affects readability)

Error: Wordy phrase in code comment
→ Suggestion (minor improvement)
```


***

## Module 57: Cross-App Workflow Tracker

### Overview

The Cross-App Workflow Tracker detects **multi-application work patterns**. It understands when users transfer information between apps and provides context accordingly.[^12_2]

### Core Responsibilities

#### 57.1 App Transition Logger[^12_2]

Records application switches:

**Transition tracking**:

```
Session timeline:
10:00 AM: Chrome (research: "Q1 market trends")
10:15 AM: Google Sheets (viewing: "Q1_Financial_Data")
10:30 AM: Chrome (Gmail: reading email thread)
10:45 AM: Microsoft Word (writing: "Q1_Report.docx")
11:00 AM: Chrome (Gmail: composing reply)
11:10 AM: Microsoft Word (continuing: "Q1_Report.docx")
```

**Transition metadata**:

```
{
  "from_app": "Google Sheets",
  "to_app": "Microsoft Word",
  "from_document": "Q1_Financial_Data",
  "to_document": "Q1_Report.docx",
  "time_gap": "180 seconds",
  "clipboard_activity": true,  // User copied data
  "likely_workflow": "data_to_report"
}
```

**Transition patterns**:

- **Research → Writing**: Browser → Document
- **Data → Report**: Spreadsheet → Document
- **Reference → Email**: Any app → Email (citing source)
- **Code → Test**: Implementation file → Test file
- **Meeting → Summary**: Calendar/Meeting → Document


#### 57.2 Workflow Pattern Detector[^12_2]

Identifies common work sequences:

**Detected workflow types**:

**Report Writing Workflow**:[^12_2]

```
Pattern:
1. Browser (research, data gathering)
2. Spreadsheet (data analysis)
3. Document editor (writing report)
4. Email (sharing report)

Context signal:
"User is writing a report using data from spreadsheet"

Helpful AI behavior:
- Reference numbers from spreadsheet
- Maintain professional tone
- Suggest data-driven language
- Offer conclusion phrasing
```

**Code Development Workflow**:

```
Pattern:
1. Browser (documentation, Stack Overflow)
2. Code editor (implementation)
3. Terminal (testing)
4. Code editor (fixes)

Context signal:
"User is implementing feature based on research"

Helpful AI behavior:
- Reference API patterns seen in browser
- Suggest error handling
- Match coding patterns from docs
```

**Email Research Workflow**:

```
Pattern:
1. Email (read request for information)
2. Browser (research topic)
3. Spreadsheet/Document (compile findings)
4. Email (compose response)

Context signal:
"User is responding to email with researched info"

Helpful AI behavior:
- Reference researched facts
- Professional email tone
- Offer email structure
- Include key findings
```

**Meeting Follow-up Workflow**:

```
Pattern:
1. Calendar/Teams (attend meeting)
2. Note-taking app (capture notes)
3. Email (follow-up message)
4. Task management (create action items)

Context signal:
"User is following up after meeting"

Helpful AI behavior:
- Reference discussion points
- Suggest action items
- Professional but warm tone
- Offer timeline language
```


#### 57.3 Data Transfer Detector[^12_2]

Identifies information movement:

**Detection signals**:

**Clipboard usage**:[^12_1]

- User copies in App A
- Switches to App B
- Signal: Transferring information

**Example detection**:

```
10:45:30 - User in Chrome
10:45:32 - Copy: "Revenue increased 15% to $2.4M"
10:45:35 - Switch to Word
10:45:38 - Cursor in document

Context built:
- User likely pasting or referencing copied data
- Data is financial (revenue, percentage, amount)
- Source: Web research or report
- Destination: Formal document

AI behavior:
- Suggest ways to integrate data
- Offer formal phrasing for financial data
- Suggest context around numbers
```

**File attachments**:

- User opens file in App A
- Switches to Email
- Signal: Likely attaching or referencing file

**Screen sharing indicators**:

- User in meeting app
- Switches to document/spreadsheet
- Signal: Presenting information


#### 57.4 Time-Decayed Relevance Calculator[^12_3]

Reduces old context influence:

**Decay function**:[^12_3]

```
Relevance = Base_Relevance × exp(-decay_rate × minutes_elapsed)

Parameters:
- Base_Relevance: Initial relevance score (0-1)
- decay_rate: 0.05 (5% decay per minute)
- minutes_elapsed: Time since context captured

Example:
Base relevance: 0.9 (high - directly related)
After 1 minute: 0.9 × exp(-0.05 × 1) = 0.86
After 5 minutes: 0.9 × exp(-0.05 × 5) = 0.70
After 10 minutes: 0.9 × exp(-0.05 × 10) = 0.55
After 30 minutes: 0.9 × exp(-0.05 × 30) = 0.20 (low)
```

**Relevance thresholds**:[^12_3]

- >0.7: High relevance, include in context
- 0.5-0.7: Medium relevance, brief mention
- 0.3-0.5: Low relevance, only if highly related
- <0.3: Expired, exclude from context[^12_3]

**Decay rate by context type**:

- Clipboard: Fast decay (0.08 per minute) - stale quickly
- Cross-app: Medium decay (0.05 per minute) - moderate persistence
- User preferences: No decay - permanent
- Mood: Slow decay (0.02 per minute) - mood changes slowly

**Example scenario**:

```
10:00 AM: User views Q1 data in Sheets (relevance: 0.9)
10:05 AM: Still in Sheets (relevance: 0.7) - decayed slightly
10:15 AM: Switches to Word (relevance: 0.55) - medium
10:45 AM: Still writing in Word (relevance: 0.20) - low, nearly expired
11:00 AM: Relevance < 0.3 - Context removed

If user returns to Sheets at 11:00 AM:
- Relevance resets to 0.9 (fresh context again)
```


***

## Module 58: Voice Command Interpreter

### Overview

The Voice Command Interpreter processes **spoken commands** for hands-free control. It extends voice input beyond dictation to include AI control.[^12_4][^12_2]

### Core Responsibilities

#### 58.1 Command Recognition (Whisper + Intent Parsing)[^12_4]

Transcribes and interprets spoken commands:

**Two-stage process**:

1. **Speech-to-text**: Whisper converts audio to text[^12_4]
2. **Intent parsing**: NLU extracts command and parameters

**Voice command examples**:

**Dictation**:

```
Voice: "Dear John comma I wanted to follow up on our meeting yesterday period"
Intent: dictation
Action: Insert text (with punctuation)
Result: "Dear John, I wanted to follow up on our meeting yesterday."
```

**AI commands**:

```
Voice: "Make this more formal"
Intent: rewrite_command
Parameters: {style: "formal", target: "selected_text"}
Action: Trigger rewrite with formal style

Voice: "Accept suggestion"
Intent: accept_suggestion
Action: Insert current suggestion

Voice: "Next suggestion"
Intent: cycle_suggestion
Action: Show alternative suggestion
```

**Control commands**:

```
Voice: "Pause AI"
Intent: pause_ai
Action: Disable AI assistance

Voice: "Switch to code mode"
Intent: change_mode
Parameters: {mode: "code"}
Action: Activate code completion mode
```


#### 58.2 Punctuation Inference[^12_4]

Adds punctuation to speech:

**Explicit punctuation**:

```
Voice: "Hello comma this is a test period"
Result: "Hello, this is a test."

Supported explicit commands:
- "comma" → ,
- "period" → .
- "question mark" → ?
- "exclamation mark" or "exclamation point" → !
- "colon" → :
- "semicolon" → ;
- "dash" → -
- "new line" → \n
- "new paragraph" → \n\n
```

**Automatic punctuation**:[^12_4]

```
Voice: "The results were excellent [pause] we exceeded all targets"
AI infers: "The results were excellent. We exceeded all targets."

Voice: "Can you send me the report"
AI infers: "Can you send me the report?"
(Question inferred from "can you" structure)

Voice: "That's amazing"
AI infers: "That's amazing!"
(Exclamation inferred from enthusiasm in tone)
```

**Pause-based punctuation**:

- Short pause (<0.5s): Continue sentence (space)
- Medium pause (0.5-1s): Comma or end sentence
- Long pause (>1s): New sentence (period)
- Very long pause (>2s): New paragraph

**Prosody analysis** (advanced):

- Rising intonation → Question mark
- Emphatic tone → Exclamation mark
- List pattern → Commas between items


#### 58.3 Voice-Triggered Actions

Executes commands via voice:

**Suggestion control**:

```
"Accept" / "Accept suggestion" → Insert current suggestion
"Reject" / "Dismiss" → Hide suggestion
"Next" / "Next suggestion" → Cycle to alternative
"Edit suggestion" → Open edit mode
```

**Rewrite commands**:

```
"Make it [style]" where style is:
- formal, casual, professional, friendly
- longer, shorter, more concise
- confident, polite, direct

"Fix grammar" → Run grammar check
"Check spelling" → Run spell check
"Rephrase" → Generate alternative phrasing
```

**Mode switching**:

```
"Code mode" → Activate code completion
"Email mode" → Activate email assistant
"Pause AI" → Disable all AI
"Resume AI" → Re-enable AI
```

**Navigation**:

```
"Go to [section]" → Jump to document section
"Next paragraph" → Move cursor
"Select all" → Select text
"Undo" → Undo last action
```


#### 58.4 Ambient Dictation Mode

Continuous listening for hands-free typing:

**Activation**:

- Voice: "Start dictation" or hotkey
- Microphone continuously listens
- Transcribes in real-time
- Inserts as user speaks

**Real-time transcription**:

```
User speaks: "The quarterly results show strong growth..."
Text appears: "The quarterly results show strong growth"
User continues: "across all product lines"
Text updates: "The quarterly results show strong growth across all product lines"
```

**Ambient mode features**:

- **Auto-correction**: Fix obvious transcription errors
- **Smart capitalization**: Capitalize proper nouns, sentence starts
- **Filler word removal**: Remove "um", "uh", "like"
- **Natural pauses**: Convert to punctuation
- **Background noise filtering**: Ignore non-speech sounds

**Commands while dictating**:

- "Period" → Add period and capitalize next word
- "Delete that" → Remove last sentence
- "New paragraph" → Start new paragraph
- "Stop dictation" → End ambient mode

**Privacy controls**:

- Visual indicator: Microphone icon shows when listening
- Mute zones: Auto-mute in password fields
- Manual mute: Tap to pause listening
- Local processing: Whisper runs on-device[^12_4]

***

## Module 59: Offline Mode Handler

### Overview

The Offline Mode Handler ensures **AI Keyboard functions without internet**. It manages local models, caching, and graceful degradation.[^12_3]

### Core Responsibilities

#### 59.1 Local Model Manager[^12_3]

Manages on-device AI models:

**Local models**:

- **N-gram**: Always local, no internet needed[^12_3]
- **Local LLM**: Phi-3 or similar (runs on CPU/GPU)[^12_3]
- **Whisper**: Speech recognition (local)[^12_4]
- **Spell check**: Dictionary-based (local)
- **Grammar**: Rule-based engine (local)

**Model storage**:

```
Models directory:
├─ ngram/
│  ├─ english_3gram.bin (150MB)
│  └─ vocab.txt
├─ llm/
│  ├─ phi3_quantized.gguf (2.5GB)
│  └─ config.json
├─ whisper/
│  └─ whisper_base.bin (74MB)
└─ grammar/
   ├─ rules.json
   └─ dictionaries/
```

**Model loading**:

- Load at app startup
- Keep in memory during session
- Unload if memory pressure
- Reload on demand if unloaded

**Model updates**:

- Check for updates when online
- Download in background
- Install during next app restart
- Rollback if new model fails


#### 59.2 Offline Capability Detector[^12_3]

Determines what works offline:

**Capability matrix**:

```
Feature              | Online | Offline
---------------------|--------|--------
N-gram suggestions   | ✅     | ✅
Local LLM            | ✅     | ✅
Cloud LLM            | ✅     | ❌
Voice input          | ✅     | ✅ (local Whisper)
Grammar check        | ✅     | ✅
Spell check          | ✅     | ✅
Cross-app context    | ✅     | ✅
Learning updates     | ✅     | ⏸️ (queued)
Settings sync        | ✅     | ⏸️ (queued)
```

**Detection logic**:

```python
def detect_capabilities():
    is_online = check_internet_connection()
    
    if is_online:
        return {
            "llm": "cloud_with_local_fallback",
            "sync": "enabled",
            "updates": "available"
        }
    else:
        return {
            "llm": "local_only",
            "sync": "queued",
            "updates": "unavailable"
        }
```

**User notification**:

```
Status bar:
🌐 Online  → All features available
📵 Offline → Running locally (some features limited)
⚡ Cloud unavailable → Using local fallback
```


#### 59.3 Graceful Degradation Strategy[^12_3]

Adapts when features unavailable:

**Fallback hierarchy**:[^12_3]

**Suggestion generation**:

```
Tier 1 (best): Cloud LLM (GPT-4, Claude)
    ↓ if unavailable or too slow
Tier 2: Local LLM (Phi-3)
    ↓ if unavailable or overloaded
Tier 3: N-gram only (fast, basic)
```

**Quality vs Availability tradeoff**:

- Online: Best quality (cloud LLM)
- Offline: Good quality (local LLM)
- Resource constrained: Basic quality (n-gram)

**Feature degradation**:

**Full online mode**:

- Cloud LLM suggestions
- Real-time learning sync
- Cross-device preferences
- Advanced features (Command Mode with cloud)

**Hybrid mode** (cloud slow):

- Local LLM primarily
- Cloud LLM for complex tasks
- Delayed sync
- Most features available

**Offline mode**:

- Local LLM only
- N-gram for speed
- No sync (queued)
- Essential features only

**Graceful messaging**:

```
Instead of: "Error: Cloud API unavailable"
Show: "Using local AI (offline mode)"
Tone: Informative, not alarming
```


#### 59.4 Sync Queue Manager

Queues changes for later sync:

**Queued operations**:

**Learning updates**:

```
Offline learning queue:
├─ Vocabulary: ["EBITDA", "YoY", "stakeholder"]
├─ Rejection patterns: ["too_verbose" in email]
├─ Acceptance patterns: [concise_in_chat]
└─ Session metrics: {acceptance_rate: 0.72, ...}

Status: Waiting for connection
Action when online: Batch upload to server
```

**Settings changes**:

```
Settings queue:
├─ Formality changed: 0.7 → 0.8
├─ App blocked: Personal Journal
└─ LLM preference: cloud_first → local_first

Status: Queued
Action when online: Sync immediately
```

**Queue management**:

- FIFO processing
- Conflict resolution (latest wins)
- Retry on failure
- Maximum queue size (1000 items)

**Sync process**:

```
On reconnect:
1. Detect online status
2. Check sync queue
3. Upload queued items
4. Download server updates
5. Merge (resolve conflicts)
6. Clear queue
7. Notify user: "Synced"
```


***

## Module 60: Multi-Language Support

### Overview

The Multi-Language Support module enables **AI Keyboard in multiple languages**. It handles language detection, model selection, and localized suggestions.[^12_3]

### Core Responsibilities

#### 60.1 Language Detector[^12_3]

Identifies typing language:

**Detection methods**:

**Character set analysis**:

- Latin alphabet → English, Spanish, French, German, etc.
- Cyrillic → Russian, Ukrainian, Bulgarian
- CJK characters → Chinese, Japanese, Korean
- Arabic script → Arabic, Persian, Urdu

**N-gram probability**:

```python
def detect_language(text):
    # Calculate probability for each language
    scores = {}
    for lang in supported_languages:
        scores[lang] = ngram_model[lang].score(text)
    
    # Return highest probability
    return max(scores, key=scores.get)
```

**Common word patterns**:

- English: "the", "is", "and", "to"
- Spanish: "el", "la", "de", "que"
- French: "le", "la", "de", "et"
- German: "der", "die", "das", "und"

**Mixed language detection**:

```
Text: "Let


<div align="center">⁂</div>

[^12_1]: CORE_INTELLIGENCE_DESIGN.md
[^12_2]: SOLUTION_DECISIONS.md
[^12_3]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md
[^12_4]: ai_keyboard_sol.md
[^12_5]: GRAMMARLY_TECHNICAL_RESEARCH.md

---

# continue and part 7 complete in one stretch

*Continuing Module 60 Multi-Language Support...*

```

Text: "Let's meet at 3 PM pour discuter du projet"
Detection: Mixed English + French
Primary: English (60%)
Secondary: French (40%)
Strategy: Use English model, recognize French phrases

```

**Context-based detection**:
- App language setting (Gmail in Spanish → likely Spanish)
- System locale (French Windows → likely French)
- Previous typing language (user consistency)
- Domain of current app (`.de` domain → likely German)

#### 60.2 Language Model Selector[^13_1]
Chooses appropriate model for detected language:

**Model library**:
```

Available models by language:
├─ English: ngram_en, llm_en, grammar_en
├─ Spanish: ngram_es, llm_es, grammar_es
├─ French: ngram_fr, llm_fr, grammar_fr
├─ German: ngram_de, llm_de, grammar_de
├─ Japanese: ngram_ja, llm_ja
├─ Chinese (Simplified): ngram_zh_cn, llm_zh_cn
├─ Portuguese: ngram_pt, llm_pt
├─ Russian: ngram_ru, llm_ru
└─ Hindi: ngram_hi, llm_hi

```

**Model loading strategy**:
- Primary language: Always loaded in memory
- Secondary languages: Load on demand
- Rare languages: Download on first use
- Model cache: Keep 2-3 recently used languages loaded

**Multilingual LLM fallback**:
- If dedicated language model unavailable: Use multilingual model
- Quality: Slightly lower but still functional
- Coverage: 50+ languages supported

#### 60.3 Localized Grammar Rules[^13_1]
Language-specific error detection:

**Language-specific rules**:

**English**:
- Articles: a/an/the usage
- Subject-verb agreement
- Verb tenses consistency
- Preposition usage

**Spanish**:
- Gender agreement (el/la, masculine/feminine)
- Verb conjugations (ser vs estar)
- Subjunctive mood
- Accent marks (é, á, í, ó, ú)

**French**:
- Noun gender (le/la)
- Verb agreement with subject
- Accents (è, é, ê, à, ù, ç)
- Liaison rules

**German**:
- Noun capitalization (all nouns capitalized)
- Case system (nominative, accusative, dative, genitive)
- Gender agreement (der/die/das)
- Compound word formation

**Grammar rule examples**:

**Spanish gender agreement**:
```

Error: "El casa es grande"
Correct: "La casa es grande"
Explanation: "casa" is feminine, requires "la"

```

**French accent correction**:
```

Error: "Je prefere le cafe"
Correct: "Je préfère le café"
Explanation: Missing accents on préfère and café

```

**German noun capitalization**:
```

Error: "Ich mag den hund"
Correct: "Ich mag den Hund"
Explanation: All German nouns capitalized

```

#### 60.4 Translation Assist Mode
Helps with cross-language writing:

**Translation context detection**:
```

User in English document types: "Como se dice 'deadline' en español?"
Detection: Translation query
AI suggests: "fecha límite" or "plazo"

```

**Inline translation**:
```

User types: "The [fr:réunion] is at 3 PM"
AI recognizes: French word in English text
AI suggests: "The meeting is at 3 PM"
or leaves as: "The réunion is at 3 PM" (if appropriate)

```

**Language mixing support**:
- Technical terms: Keep in original language
- Proper nouns: Don't translate
- Code-switching: Natural in multilingual contexts
- Cognates: Recognize similar words across languages

#### 60.5 Regional Dialect Handler
Manages language variants:

**English variants**:
- US English: "color", "organize", "center"
- UK English: "colour", "organise", "centre"
- Australian English: Colloquialisms, spelling like UK
- Canadian English: Mix of US/UK conventions

**Spanish variants**:
- Spain Spanish (Castilian): "vosotros", "ordenador"
- Latin American Spanish: "ustedes", "computadora"
- Mexican Spanish: Regional vocabulary
- Argentine Spanish: "vos" instead of "tú"

**Portuguese variants**:
- Brazilian Portuguese: Different vocabulary, spelling
- European Portuguese: More formal, different pronunciation markers

**Dialect detection**:
- User location (IP, system locale)
- Consistent spelling patterns
- Vocabulary choices
- User preference setting

***

# AI Keyboard - Complete Module Documentation
## Part 7: System Integration & Advanced Intelligence (Modules 61-70)

> **Document Version:** 1.0  
> **Date:** January 30, 2026, 1:40 AM IST  
> **Scope:** Final modules covering system integration and advanced features  
> **Purpose:** Complete the 70-module architecture

***

## Module 61: API Gateway & Rate Limiter

### Overview
The API Gateway manages **all external API calls** (LLM providers, services) with rate limiting, retry logic, and failover.[^13_1]

### Core Responsibilities

#### 61.1 Request Router
Routes requests to appropriate providers:

**Provider selection**:
```

User preference: Cloud-first mode
Request type: Text completion

Routing decision:

1. Check cloud availability (ping)
2. Check rate limits (not exceeded)
3. Route to: OpenAI GPT-4 (primary)
4. Fallback: Anthropic Claude (secondary)
5. Fallback: Local Phi-3 (tertiary)
```

**Load balancing**:
- Multiple API keys: Rotate to distribute load
- Provider health: Route away from slow/failing providers
- Cost optimization: Use cheaper models when quality sufficient

#### 61.2 Rate Limit Enforcer
Prevents API quota exhaustion:

**Rate limits by provider**:
```

OpenAI GPT-4:

- Requests: 3,500/minute
- Tokens: 90,000/minute
- Current: 245/min requests, 18,000/min tokens
- Status: ✅ Healthy (7% capacity)

Anthropic Claude:

- Requests: 1,000/minute
- Tokens: 50,000/minute
- Current: 89/min requests, 5,200/min tokens
- Status: ✅ Healthy (9% capacity)

```

**Throttling strategies**:
- Queue requests if approaching limit
- Spread requests over time (avoid bursts)
- Priority queue: User-initiated > background tasks
- Reject low-priority if limit reached

**User-level rate limiting**:
- Free tier: 100 requests/day
- Premium: 10,000 requests/day
- Enterprise: Unlimited

#### 61.3 Retry Logic
Handles transient failures:

**Retry strategy**:
```

Attempt 1: Send request
├─ Success: Return response
└─ Failure: Wait 1 second, retry

Attempt 2: Retry request
├─ Success: Return response
└─ Failure: Wait 2 seconds, retry

Attempt 3: Final retry
├─ Success: Return response
└─ Failure: Fallback to local LLM

Exponential backoff: 1s, 2s, 4s, 8s...
Maximum retries: 3
Total timeout: 15 seconds

```

**Retryable errors**:
- Network timeout
- 429 Rate limit (wait and retry)
- 500 Server error (temporary)
- 503 Service unavailable

**Non-retryable errors**:
- 400 Bad request (fix request, don't retry)
- 401 Unauthorized (auth issue)
- 404 Not found (wrong endpoint)

#### 61.4 Circuit Breaker
Prevents cascading failures:

**Circuit breaker states**:

**Closed** (normal operation):
- All requests pass through
- Monitor failure rate
- If failures exceed threshold: Open circuit

**Open** (failure mode):
- All requests fail fast (don't wait)
- Route to fallback immediately
- After timeout period: Try half-open

**Half-open** (testing):
- Allow limited requests through
- If successful: Close circuit (back to normal)
- If failed: Open circuit again (still broken)

**Example scenario**:
```

10:00 AM: OpenAI API responding normally (Closed)
10:15 AM: 5 consecutive failures detected (Open circuit)
10:15 AM: All requests route to Claude (fallback)
10:20 AM: Half-open - test request to OpenAI
10:20 AM: Success! Circuit closed, resume normal routing

```

#### 61.5 Cost Tracker
Monitors API usage costs:

**Cost calculation**:
```

OpenAI GPT-4 pricing:

- Input: \$0.03 per 1K tokens
- Output: \$0.06 per 1K tokens

User session costs:
├─ Requests: 47
├─ Input tokens: 94,000 (94K)
├─ Output tokens: 23,500 (23.5K)
├─ Input cost: 94 × \$0.03 = \$2.82
├─ Output cost: 23.5 × \$0.06 = \$1.41
└─ Total: \$4.23 this session

Daily costs: \$28.47
Monthly projection: \$854

```

**Budget alerts**:
- Warn at 80% of budget
- Throttle at 90% of budget
- Hard stop at 100% of budget
- Suggest downgrade to local model

***

## Module 62: A/B Testing Framework

### Overview
The A/B Testing Framework enables **controlled experiments** to improve AI Keyboard features through data-driven decisions.[^13_2]

### Core Responsibilities

#### 62.1 Experiment Manager
Creates and manages experiments:

**Experiment structure**:
```

Experiment: "Adaptive Debounce Timing"
├─ Hypothesis: Mood-based debounce improves acceptance
├─ Groups:
│  ├─ Control (50%): Fixed 500ms debounce
│  └─ Treatment (50%): Adaptive 300-1200ms
├─ Metrics:
│  ├─ Primary: Acceptance rate
│  ├─ Secondary: Time to accept
│  └─ Guard: Latency perception
├─ Duration: 2 weeks
└─ Status: Active

```

**User assignment**:
- Random assignment at first use
- Sticky assignment (same user stays in group)
- Stratified sampling (ensure balance across user types)
- Exclusions (power users, beta testers)

#### 62.2 Variant Distributor
Assigns users to experiment groups:

**Assignment logic**:
```python
def assign_variant(user_id, experiment_id):
    # Hash user ID for deterministic assignment
    hash_val = hash(f"{user_id}_{experiment_id}")
    
    # Map to 0-100 range
    bucket = hash_val % 100
    
    # Assign based on split percentages
    if bucket < 50:
        return "control"
    else:
        return "treatment"
```

**Multi-experiment handling**:

- User can be in multiple experiments simultaneously
- Orthogonal experiments (independent features)
- Prevent conflicting experiments
- Track interaction effects


#### 62.3 Metrics Collector

Gathers experiment data:

**Tracked metrics**:

```
Per suggestion:
├─ Shown timestamp
├─ Accepted/dismissed
├─ Time to decision
├─ Variant (control/treatment)
├─ User context (app, mood)
└─ Suggestion quality

Aggregated:
├─ Acceptance rate by variant
├─ Average time to accept
├─ Latency percentiles
├─ User satisfaction (NPS surveys)
└─ Retention impact
```

**Statistical analysis**:

```
Experiment: Adaptive Debounce
├─ Control group:
│  ├─ Users: 5,000
│  ├─ Acceptance rate: 68.2%
│  ├─ Avg time to accept: 1.8s
│  └─ Confidence: 95%
├─ Treatment group:
│  ├─ Users: 5,000
│  ├─ Acceptance rate: 72.5%
│  ├─ Avg time to accept: 1.5s
│  └─ Confidence: 95%
├─ Difference: +4.3% acceptance (p < 0.01)
└─ Decision: ✅ Ship treatment to all users
```


#### 62.4 Winner Selector

Determines experiment outcomes:

**Decision criteria**:

- Statistical significance (p < 0.05)
- Practical significance (meaningful improvement)
- No regression on guard metrics
- Consistent across user segments
- Positive user feedback

**Rollout strategy**:

```
1. Winner identified: Treatment variant
2. Gradual rollout:
   - Week 1: 10% of remaining users
   - Week 2: 25% of remaining users
   - Week 3: 50% of remaining users
   - Week 4: 100% (full deployment)
3. Monitor for issues during rollout
4. Rollback capability maintained
```


***

## Module 63: Telemetry \& Analytics Engine

### Overview

The Telemetry \& Analytics Engine collects **anonymous usage data** to improve system performance and user experience.[^13_2]

### Core Responsibilities

#### 63.1 Anonymous Event Collector

Gathers usage events:

**Event types**:

```
Session events:
- app_opened
- app_closed
- session_duration
- apps_used

Suggestion events:
- suggestion_shown
- suggestion_accepted
- suggestion_dismissed
- acceptance_time

Performance events:
- llm_latency
- ngram_latency
- ui_render_time
- error_occurred

Feature events:
- voice_input_used
- command_mode_opened
- rewrite_triggered
- grammar_check_run
```

**Privacy-safe collection**:

```
✅ Collected:
- Event type and timestamp
- Feature used
- Performance metrics
- Error codes
- Anonymous user ID (random UUID)

❌ Never collected:
- Actual text content
- Personal information
- Email addresses
- Document names
- URLs visited
```


#### 63.2 Aggregate Statistics Calculator

Computes summary metrics:

**Daily aggregates**:

```
Date: 2026-01-29
├─ Active users: 12,847
├─ Sessions: 28,392
├─ Suggestions shown: 487,234
├─ Acceptance rate: 71.3%
├─ Avg session duration: 28 min
├─ Total time saved: 8,234 hours
├─ P95 latency: 185ms
└─ Error rate: 0.12%
```

**Trend analysis**:

- Week-over-week growth
- Feature adoption rates
- Performance trends
- Quality metrics evolution


#### 63.3 Cohort Analyzer

Compares user segments:

**User cohorts**:

```
By user type:
├─ Developers: 35% of users
│  ├─ Top feature: Code completion
│  └─ Acceptance: 74%
├─ Writers: 28% of users
│  ├─ Top feature: Grammar check
│  └─ Acceptance: 69%
└─ Business users: 37% of users
   ├─ Top feature: Email assist
   └─ Acceptance: 68%

By acquisition date:
├─ January cohort: 3,245 users (retention: 82%)
├─ December cohort: 2,891 users (retention: 78%)
└─ November cohort: 2,456 users (retention: 75%)
```

**Insight generation**:

- Which cohorts have highest engagement?
- What features drive retention?
- Where do users struggle?
- What improvements would help most?


#### 63.4 Dashboard Reporter

Visualizes analytics:

**Real-time dashboard**:

```
┌─────────────────────────────────────────┐
│ AI Keyboard Analytics Dashboard         │
├─────────────────────────────────────────┤
│ Active Users (Last 24h): 12,847 (+5.2%) │
│ Acceptance Rate: 71.3% (+1.8%)          │
│ Avg Latency: 142ms (-8ms)               │
│ Error Rate: 0.12% (-0.03%)              │
├─────────────────────────────────────────┤
│ Feature Usage:                           │
│ ████████████████░░░░ Text completion 78% │
│ ███████████░░░░░░░░░ Grammar check 58%   │
│ ██████░░░░░░░░░░░░░░ Voice input 32%     │
│ ████░░░░░░░░░░░░░░░░ Command mode 21%    │
├─────────────────────────────────────────┤
│ Top Issues:                              │
│ 1. Cloud latency spikes (3 PM - 4 PM)   │
│ 2. Code completion accuracy (Java)       │
│ 3. Voice recognition (noisy environments)│
└─────────────────────────────────────────┘
```


***

## Module 64: User Onboarding System

### Overview

The User Onboarding System provides **guided first-time experience** to help new users understand and configure AI Keyboard.[^13_2]

### Core Responsibilities

#### 64.1 Welcome Flow

Introduces new users:

**Onboarding steps**:

```
Step 1: Welcome screen
"Welcome to AI Keyboard! Let's personalize your experience."
[Next]

Step 2: Role selection
"What best describes you?"
○ Developer / Engineer
○ Writer / Content Creator
○ Business Professional
○ Student
○ Other
[Continue]

Step 3: Writing style
"How formal is your typical writing?"
Casual [━━━━●━━━━━] Formal
[Continue]

Step 4: Feature tour
"Key features:" (with animations)
- Smart suggestions as you type
- Grammar and spell check
- Voice input
- Cross-app context awareness
[Next]

Step 5: Privacy settings
"Your privacy matters"
- Data stays on device by default
- Cloud features optional
- Manage blocked apps
[Configure] [Skip]

Step 6: Ready!
"You're all set! Start typing to see AI suggestions."
[Start Using AI Keyboard]
```


#### 64.2 Interactive Tutorial

Teaches features through practice:

**Tutorial missions**:

```
Mission 1: "Accept your first suggestion"
├─ Open any text app
├─ Start typing: "The quarterly"
├─ Wait for suggestion
├─ Press Tab to accept
└─ ✅ Complete! (+10 XP)

Mission 2: "Use voice input"
├─ Click microphone icon
├─ Say something
├─ Review transcription
├─ Insert text
└─ ✅ Complete! (+15 XP)

Mission 3: "Rewrite text"
├─ Type any sentence
├─ Select the text
├─ Press Ctrl+Shift+R
├─ Choose "Make it formal"
└─ ✅ Complete! (+20 XP)
```

**Gamification elements**:

- Progress bar (3 of 10 missions)
- Points for completing tutorials
- Badges for milestones
- Encouragement messages


#### 64.3 Contextual Tips

Shows help at relevant moments:

**Just-in-time tips**:

```
User types slowly with many backspaces
→ Tip appears: "💡 Tip: Let AI help! Pause after typing 
   a few words to see suggestions."

User dismisses 5 suggestions in a row
→ Tip: "Not liking the suggestions? Try Command Mode 
   (Ctrl+Shift+K) to customize."

User switches between apps frequently
→ Tip: "AI Keyboard notices you're working across apps. 
   It uses this context to give better suggestions."

First time in code editor
→ Tip: "Code mode activated! AI understands your code 
   structure and imports."
```

**Tip management**:

- Show each tip maximum 3 times
- Respect user dismissals ("Don't show again")
- Adaptive timing (not during focused work)
- Skippable (X button)


#### 64.4 Progress Tracker

Monitors onboarding completion:

**Progress metrics**:

```
User: new_user_123
Onboarding progress: 65%

Completed:
✅ Welcome flow
✅ Role selection
✅ First suggestion accepted
✅ Voice input tried

Remaining:
⬜ Grammar check used
⬜ Command mode explored
⬜ Settings customized

Engagement score: 8/10 (high)
Time to completion: 12 minutes (good)
```


***

## Module 65: Feedback \& Rating System

### Overview

The Feedback \& Rating System collects **user satisfaction data** to guide improvements.[^13_2]

### Core Responsibilities

#### 65.1 In-App Feedback Collector

Gathers user opinions:

**Feedback triggers**:

```
After 50 accepted suggestions:
"How's AI Keyboard working for you?"
😞 😐 😊 😄 😍
[Optional: Tell us more...]

After dismissing suggestion 3 times in a row:
"We notice you're dismissing suggestions. 
What's not working?"
○ Not relevant
○ Wrong tone
○ Too formal/casual
○ Other: ___________
[Submit]

Weekly check-in:
"Quick question: Would you recommend 
AI Keyboard to a colleague?"
0 (Not at all) [━━━━━━━━━━] 10 (Definitely)
[Submit]
```

**Feedback channels**:

- In-app surveys (quick, contextual)
- Feedback button (always available)
- Email surveys (detailed, periodic)
- User interviews (opt-in, in-depth)


#### 65.2 NPS Calculator

Measures user satisfaction:

**Net Promoter Score**:

```
Question: "How likely are you to recommend 
AI Keyboard to a colleague?"

Responses (0-10 scale):
├─ Detractors (0-6): 8% of users
├─ Passives (7-8): 22% of users
└─ Promoters (9-10): 70% of users

NPS = % Promoters - % Detractors
NPS = 70% - 8% = 62

Score interpretation:
- 62 is "Good" (50-70 range)
- Industry average: 35
- Top products: 70+
- Goal: Reach 75+ NPS
```


#### 65.3 Feature Request Tracker

Manages user suggestions:

**Request collection**:

```
User submits: "Add support for Markdown formatting"
├─ Category: Feature request
├─ Upvotes: 47 users
├─ Status: Under consideration
├─ Team notes: "Aligns with roadmap Q2"
└─ ETA: Q2 2026
```

**Request prioritization**:

- Voting: Users upvote requests
- Impact: How many users benefit?
- Effort: Development cost estimate
- Alignment: Fits product vision?

**Public roadmap**:

```
📋 Planned Features

Q1 2026:
✅ Multi-language support (Shipped!)
✅ Offline mode improvements (Shipped!)
🚧 Voice commands expansion (In progress)

Q2 2026:
📅 Markdown formatting support
📅 Custom dictionaries
📅 Team collaboration features

Future:
💡 Real-time collaboration
💡 Plugin ecosystem
💡 Advanced code intelligence
```


#### 65.4 Sentiment Analyzer

Analyzes feedback tone:

**Sentiment classification**:

```
Feedback: "AI Keyboard is amazing! Saves me so much time."
Sentiment: Positive (0.92)
Key topics: Time savings, satisfaction
Action: Use as testimonial

Feedback: "Suggestions are often irrelevant in code mode"
Sentiment: Negative (0.23)
Key topics: Code completion, relevance
Action: Prioritize code mode improvements

Feedback: "It's okay, sometimes helpful"
Sentiment: Neutral (0.55)
Key topics: Mixed experience
Action: Investigate use cases, improve consistency
```

**Trend monitoring**:

- Weekly sentiment score
- Sentiment by feature
- Improvement opportunities
- Success stories

***

## Module 66: Backup \& Recovery System

### Overview

The Backup \& Recovery System ensures **user data safety** and enables restoration after failures.[^13_1]

### Core Responsibilities

#### 66.1 Automatic Backup

Regularly saves user data:

**Backup schedule**:

```
STM (Short-term memory):
- Auto-save every 5 minutes (while typing)
- Backup to local disk
- Discarded at session end
- Purpose: Crash recovery

LTM (Long-term memory):
- Daily backup to local disk
- Weekly backup to cloud (if enabled)
- Retained for 30 days
- Purpose: Data preservation

Settings:
- Backup on every change
- Sync to cloud immediately (if online)
- Retained indefinitely
- Purpose: Cross-device sync
```

**Backup storage**:

```
Local backups:
C:\Users\[User]\AppData\Local\AIKeyboard\backups\
├─ stm_autosave_2026-01-30_01-35.json
├─ ltm_daily_2026-01-29.db
├─ ltm_daily_2026-01-28.db
├─ settings_2026-01-30.json
└─ ...

Cloud backups (encrypted):
- s3://ai-keyboard-backups/user_12345/
  ├─ ltm_2026-01-29.db.encrypted
  ├─ ltm_2026-01-22.db.encrypted
  └─ settings.json.encrypted
```


#### 66.2 Crash Recovery

Restores state after unexpected shutdown:

**Recovery process**:

```
App restart after crash:
1. Detect abnormal shutdown (no clean exit flag)
2. Look for STM autosave files
3. Present recovery dialog:

   "AI Keyboard crashed. Recover your work?"
   
   Found unsaved data from:
   - Microsoft Word: Q1_Report.docx (3 min ago)
   - Slack: #engineering channel (1 min ago)
   
   [Recover] [Discard]

4. If [Recover]:
   - Restore STM state
   - Load contexts
   - Continue where user left off

5. If [Discard]:
   - Delete autosave
   - Start fresh session
```

**Data integrity checks**:

- Verify backup file not corrupted
- Check timestamps (reject old backups)
- Validate JSON structure
- Report if recovery impossible


#### 66.3 Data Export

Enables user data portability:

**Export formats**:

```
Export options:
1. Full backup (ZIP file)
   ├─ profile.json (user profile)
   ├─ patterns.json (learned patterns)
   ├─ sessions.csv (session history)
   ├─ statistics.json (metrics)
   └─ settings.json (preferences)

2. Statistics only (CSV)
   - Session data
   - Acceptance rates
   - Time saved metrics

3. Settings only (JSON)
   - All preferences
   - App configurations
   - Blocked apps list
```

**Export process**:

```
User clicks "Export My Data"
1. Generate export package
2. Encrypt with user password
3. Create ZIP file
4. Download: "ai_keyboard_data_2026-01-30.zip"
5. Show confirmation: "✅ Data exported successfully"
```


#### 66.4 Import \& Migration

Restores from backups:

**Import scenarios**:

**New device setup**:

```
User installs on new computer
1. Login to account
2. Prompt: "Restore from cloud backup?"
3. Select backup date
4. Download and decrypt
5. Import settings, patterns, profile
6. Ready to use with learned preferences
```

**Account migration**:

```
User creates new account, wants to transfer data
1. Export from old account
2. Login to new account
3. Import data file
4. Merge or replace existing data
5. Complete migration
```


***

## Module 67: Notification Manager

### Overview

The Notification Manager handles **all user-facing alerts** without being intrusive [file:4].

### Core Responsibilities

#### 67.1 Toast Notification System

Shows brief, non-blocking messages:

**Notification types**:

```
Success notifications:
✅ "Settings saved"
✅ "Suggestion accepted"
✅ "Data synced successfully"

Info notifications:
ℹ️ "Switched to offline mode"
ℹ️ "Voice input ready"
ℹ️ "Updates available"

Warning notifications:
⚠️ "Approaching rate limit"
⚠️ "Cloud latency high, using local"
⚠️ "Battery low, reducing features"

Error notifications:
❌ "Sync failed, will retry"
❌ "Voice input unavailable"
❌ "Cannot connect to server"
```

**Display properties**:

- Duration: 3-5 seconds
- Position: Bottom-right corner
- Non-modal: Doesn't block typing
- Dismissible: Click X to close
- Stacking: Multiple notifications queue


#### 67.2 System Tray Integration

Provides persistent status indicator:

**Tray icon states**:

```
Icon states:
🟢 Green: AI active, all systems normal
🟡 Yellow: AI active, some issues (slow network)
🔴 Red: AI paused or critical error
⚪ Gray: AI disabled by user
🔵 Blue pulse: Processing/thinking

Context menu:
├─ Status: ✅ Active
├─ Acceptance rate: 71%
├─ Time saved today: 47 min
├─ ────────────────────
├─ ⏸️ Pause AI
├─ ⚙️ Settings
├─ 📊 Statistics
├─ 💬 Send Feedback
├─ ℹ️ About
└─ ❌ Exit
```

**Notifications in tray**:

- Badge counter for unread notifications
- Click tray icon to view missed notifications
- Persistent until acknowledged


#### 67.3 Priority Queue

Manages notification importance:

**Priority levels**:

```
Critical (show immediately):
- Security alerts
- Data loss prevention
- Account issues

High (show soon, max 10s delay):
- Feature unavailable
- Sync failures
- Update available

Normal (queue, show when appropriate):
- Tips and suggestions
- Feature discoveries
- General info

Low (weekly digest):
- Usage statistics
- Achievements
- Feature highlights
```

**Smart timing**:

- Don't interrupt during focused typing
- Wait for natural pauses
- Batch low-priority notifications
- Respect "Do Not Disturb" mode


#### 67.4 Do Not Disturb Mode

Silences non-essential notifications:

**DND triggers**:

- User manually enables
- Presentation mode detected (fullscreen apps)
- After hours (user-configured schedule)
- High typing activity (in flow state)

**DND behavior**:

```
When DND active:
✅ Allow: Critical security alerts
✅ Allow: AI functionality (suggestions continue)
❌ Block: Tips and tutorials
❌ Block: Feature announcements
❌ Block: Statistics updates
❌ Block: Non-critical errors

Notifications queued:
- Show summary when DND ends
- "While you were focused: 3 notifications"
```


***

## Module 68: Plugin Architecture

### Overview

The Plugin Architecture enables **extensibility** through third-party plugins and integrations [file:2].

### Core Responsibilities

#### 68.1 Plugin Loader

Discovers and loads plugins:

**Plugin structure**:

```
Plugin: "Grammar Pro Advanced"
├─ manifest.json (metadata)
├─ main.js (entry point)
├─ rules/ (custom grammar rules)
├─ models/ (additional models)
└─ assets/ (UI elements)

Manifest:
{
  "name": "Grammar Pro Advanced",
  "version": "1.2.0",
  "author": "GrammarTech Inc.",
  "api_version": "2.0",
  "permissions": ["grammar_engine", "suggestions"],
  "entry_point": "main.js"
}
```

**Loading process**:

```
1. Scan plugins directory
2. Read manifest files
3. Validate API version compatibility
4. Check permissions
5. Load plugin code (sandboxed)
6. Initialize plugin
7. Register hooks
8. Enable plugin functionality
```


#### 68.2 Plugin API

Provides interfaces for extensions:

**Available hooks**:

```
Suggestion hooks:
- onBeforeSuggestion(context) → modify context
- onAfterSuggestion(suggestion) → modify suggestion
- onSuggestionAccepted(suggestion) → track usage
- onSuggestionDismissed(suggestion) → learn rejection

Grammar hooks:
- registerGrammarRule(rule) → add custom rule
- onGrammarCheck(text) → run custom checks
- onErrorDetected(error) → suggest fixes

UI hooks:
- registerCommand(name, handler) → add menu item
- addSettingsPanel(component) → add settings UI
- showCustomNotification(message) → display message

Data hooks:
- onSessionStart() → initialize
- onSessionEnd() → cleanup
- onAppSwitch(app) → app-specific behavior
```

**Example plugin**:

```javascript
// Plugin: Domain-Specific Vocabulary
class MedicalTermsPlugin {
  onLoad() {
    // Register custom dictionary
    this.registerVocabulary([
      "myocardial infarction",
      "cerebrovascular accident",
      "hypertension"
    ]);
  }
  
  onBeforeSuggestion(context) {
    // Boost medical terms if in medical context
    if (context.app === "MedicalRecords") {
      context.vocabularyBoost = "medical";
    }
    return context;
  }
}
```


#### 68.3 Sandboxing \& Security

Isolates plugins for safety:

**Security measures**:

- Plugins run in isolated context (no direct system access)
- Permission system (request capabilities explicitly)
- API rate limiting (prevent abuse)
- Code signing (verify authentic plugins)
- Review process (manual approval before publication)

**Permission examples**:

```
Low-risk permissions (auto-granted):
- suggestions: Modify suggestions
- grammar: Add grammar rules
- ui: Add UI elements

Medium-risk permissions (user approval):
- clipboard: Access clipboard
- network: Make external requests
- files: Read/write user files

High-risk permissions (admin approval):
- system: System-level access
- processes: Monitor other apps
- keylogging: Raw keyboard access
```


#### 68.4 Plugin Marketplace

Distributes plugins:

**Marketplace features**:

```
Plugin listing:
┌────────────────────────────────────────┐
│ 🎯 Grammar Pro Advanced        ⭐ 4.8  │
│ by GrammarTech Inc.                    │
│                                        │
│ Advanced grammar checking with         │
│ domain-specific rules.                 │
│                                        │
│ 10,000+ users | Premium $4
<span style="display:none">[^13_3][^13_4][^13_5]</span>

<div align="center">⁂</div>

[^13_1]: AI_KEYBOARD_ENHANCED_FEATURES_REPORT.md
[^13_2]: ai_keyboard_sol.md
[^13_3]: CORE_INTELLIGENCE_DESIGN.md
[^13_4]: GRAMMARLY_TECHNICAL_RESEARCH.md
[^13_5]: SOLUTION_DECISIONS.md```

