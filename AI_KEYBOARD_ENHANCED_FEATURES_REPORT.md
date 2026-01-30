
# AI KEYBOARD — Enhanced Features & Architecture Report
## Comprehensive Design Document with LLM Strategy & Latency Analysis

> **Version:** 3.0 — Extended Features Integration  
> **Date:** January 29, 2026  
> **Focus:** Advanced suggestion systems, memory architecture, and performance optimization

---

## EXECUTIVE SUMMARY

This report extends the existing AI Keyboard architecture with advanced features designed to provide:
- **Dual suggestion modes** (default + command-based)
- **Multi-tier suggestion system** (n-gram + LLM-powered)
- **Intelligent memory management** (STM/LTM with privacy controls)
- **Context-aware performance** (adaptive debounce, smart triggering)
- **Production-ready latency targets** (50-200ms for suggestions)

---

## 1️⃣ DUAL SUGGESTION SYSTEM ARCHITECTURE

### System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                   USER INPUT DETECTION                          │
└────────────────────────────┬────────────────────────────────────┘
                             │
                    ┌────────┴────────┐
                    │                 │
          ┌─────────▼─────────┐  ┌───▼──────────────┐
          │  DEFAULT MODE     │  │  COMMAND MODE    │
          │  (Automatic)      │  │  (User-Triggered)│
          └─────────┬─────────┘  └───┬──────────────┘
                    │                │
                    └────────┬───────┘
                             │
                    ┌────────▼────────┐
                    │  SUGGESTION     │
                    │  RENDERER       │
                    └─────────────────┘
```

### 1.1 Default Suggestion System

**Behavior:**
- Fully automatic, context-aware suggestions
- Triggers on natural pause points (punctuation, pauses)
- AI predicts what user wants to type next
- Two-tier rendering: n-gram (fast) + LLM (intelligent)

**Triggering Logic:**
```python
def should_show_default_suggestion(stm):
    # Natural pause detected (punctuation, typing pause)
    if not is_natural_pause(stm.last_keystroke):
        return False

    # Enough context available
    if len(stm.text_before_cursor) < 10:
        return False

    # User not in rapid typing mode
    if stm.typing_speed > stm.baseline_speed * 1.3:
        return False  # Too fast, don't interrupt

    # Adaptive confidence threshold
    confidence_threshold = calculate_dynamic_threshold(stm)

    return True
```

### 1.2 Command System

**Behavior:**
- User explicitly requests AI help via Grammarly icon click
- Two modes:
  - **Text modification:** User selects text + provides prompt
  - **Text generation:** User provides prompt in empty field

**Interface:**
```
User clicks [G] icon → Tooltip opens
┌───────────────────────────────────────────┐
│  ✨ AI Keyboard                           │
├───────────────────────────────────────────┤
│  📝 Prompt (optional):                    │
│  ┌─────────────────────────────────────┐  │
│  │ Make this more professional...      │  │
│  └─────────────────────────────────────┘  │
│                                           │
│  Selected Text:                           │
│  "hey john can u send that file"          │
│                                           │
│  [✨ Generate]  [❌ Cancel]                │
└───────────────────────────────────────────┘
```

**Use Cases:**
1. **Rewrite:** Select text → Click icon → Optionally add prompt → Generate
2. **Generate:** Click icon in empty field → Add prompt → Generate
3. **Quick actions:** Click icon → Select preset (Formal, Concise, Expand)

---

## 2️⃣ TWO-SECTION EDITOR ARCHITECTURE

### Visual Layout

```
┌─────────────────────────────────────────────────────────────────┐
│              SUGGESTION OVERLAY (Tooltip/Popup)                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  SECTION 1: N-GRAM WORD SUGGESTIONS (Gboard-style)     │   │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐                 │   │
│  │  │ analysis│  │ report  │  │ findings│  ← 3 words      │   │
│  │  └─────────┘  └─────────┘  └─────────┘                 │   │
│  │  Updates on EVERY letter change (ultra-fast)            │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ──────────────────────────────────────────────────────────    │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  SECTION 2: LLM SENTENCE SUGGESTIONS (AI-powered)      │   │
│  │                                                         │   │
│  │  "exceeded our expectations by demonstrating           │   │
│  │  significant growth across all market segments."       │   │
│  │                                                         │   │
│  │  Updates on natural pauses (debounced)                 │   │
│  │  ✓ Contextually intelligent                            │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  [Tab] Accept  [Esc] Dismiss  [↑↓] Navigate                    │
└─────────────────────────────────────────────────────────────────┘
```

### 2.1 Section 1: N-Gram Word Suggestions

**Technology:** Trigram/4-gram language model (local, fast)

**Characteristics:**
- **Latency:** <10ms (instant)
- **Updates:** Every letter typed
- **Display:** 3 words maximum
- **Purpose:** Fast, familiar typing assistance (like Gboard)

**Training Data:**
- Pre-trained on large corpus
- Personalized with user's vocabulary from LTM
- Context-aware: considers last 2-3 words

**Implementation:**
```python
class NGramSuggester:
    def __init__(self):
        self.model = load_trigram_model()  # Pre-trained
        self.user_vocab = load_from_ltm()  # Personalized

    def get_suggestions(self, last_words: list[str]) -> list[str]:
        # Ultra-fast prediction
        base_suggestions = self.model.predict(last_words[-3:])

        # Boost user's frequent words
        boosted = apply_user_vocabulary_boost(
            base_suggestions, 
            self.user_vocab
        )

        return boosted[:3]  # Top 3
```

### 2.2 Section 2: LLM Sentence Suggestions

**Technology:** Cloud LLM (GPT-4/Claude) with local fallback

**Characteristics:**
- **Latency:** 50-200ms (optimized)
- **Updates:** Debounced (adaptive 500-1200ms)
- **Display:** Full sentence/phrase
- **Purpose:** Intelligent, context-aware completion

**Context Integration:**
```python
def generate_llm_suggestion(stm, ltm):
    context = build_context(
        text_before=stm.text_before_cursor[-500:],
        text_after=stm.text_after_cursor[:100],
        app_context=stm.current_app,
        mood=stm.mood,
        cross_app=stm.cross_app_context,
        clipboard=stm.clipboard_if_relevant,
        user_style=ltm.writing_style
    )

    return llm.complete(context, max_tokens=50)
```

---

## 3️⃣ DIRECT SUGGESTION EDITING

### User Interaction Modes

**Mode 1: Click on Suggestion**
- User clicks directly on suggested text
- Text becomes editable inline
- User can modify before accepting
- Keyboard shortcuts active:
  - `Enter` → Accept edited suggestion
  - `Esc` → Cancel
  - `Tab` → Accept word-by-word

**Mode 2: Click on Icon**
- Shows Accept/Reject buttons explicitly
- Keyboard shortcuts enabled:
  - `Tab` / `Ctrl+Enter` → Accept
  - `Esc` / `Ctrl+Backspace` → Reject

**Mode 3: Keyboard-Only**
- `Tab` → Accept entire suggestion
- `Ctrl+→` → Accept word-by-word
- `Esc` → Dismiss

### Implementation:
```python
class EditableSuggestion:
    def on_suggestion_click(self, suggestion_text):
        # Make suggestion editable
        self.mode = "EDITING"
        self.editable_text = suggestion_text
        self.show_inline_editor()

    def on_icon_click(self):
        # Show explicit buttons
        self.mode = "CONFIRM"
        self.show_accept_reject_buttons()

    def on_keyboard_shortcut(self, key):
        if key == "Tab":
            self.accept_suggestion()
        elif key == "Esc":
            self.dismiss_suggestion()
        elif key == "Ctrl+Right":
            self.accept_word_by_word()
```

---

## 4️⃣ SMART TRIGGER SYSTEM

### Adaptive Debounce Strategy

**Principle:** Typing speed varies by user and context

**Implementation:**
```python
class AdaptiveDebouncer:
    def calculate_delay(self, stm) -> int:
        # Base delay from typing speed profile
        user_wpm = stm.typing_speed_wpm

        if user_wpm > 80:       # Fast typer
            base_delay = 500
        elif user_wpm > 60:     # Normal-fast
            base_delay = 700
        elif user_wpm > 40:     # Average
            base_delay = 900
        else:                   # Slow/careful
            base_delay = 1200

        # Mood modifier
        mood_modifier = {
            "rushed": 0.6,      # Faster response
            "focused": 1.4,     # Less interruption
            "frustrated": 0.7,  # More helpful
            "neutral": 1.0
        }

        delay = base_delay * mood_modifier.get(stm.mood, 1.0)

        # Recent rejection penalty (back off if user is dismissing)
        if stm.recent_dismissals > 3:
            delay *= 1.5  # Wait longer

        # Natural pause bonus (reduce delay)
        if is_sentence_end(stm.last_character):
            delay *= 0.5  # Quick response after period

        return int(delay)
```

### Typing Speed Profiling

**Continuous Learning:**
```python
class TypingProfiler:
    def update_baseline(self, session_data):
        # Calculate WPM for session
        chars_typed = len(session_data.keystrokes)
        time_seconds = session_data.duration
        wpm = (chars_typed / 5) / (time_seconds / 60)

        # Update moving average
        self.baseline_wpm = (
            self.baseline_wpm * 0.8 +  # Historical weight
            wpm * 0.2                   # Current session weight
        )

        # Detect typing rhythm
        self.inter_key_intervals = calculate_intervals(
            session_data.timestamps
        )
```

---

## 5️⃣ TWO-TIER MEMORY ARCHITECTURE (ENHANCED)

### 5.1 Short-Term Memory (STM)

**Storage:** In-memory (RAM), encrypted at rest

**Update Frequency:** Continuous (every keystroke, debounced 100ms)

**Lifespan:** Current session only (deleted on close)

**Enhanced Data Structure:**
```python
STM = {
    # Core context (existing)
    "current_context": {...},
    "cross_app_context": {...},

    # NEW: Time-decay tracking
    "cross_app_time_decay": {
        "chrome_context": {
            "content": "Financial data Q1",
            "captured_at": "2026-01-29T14:30:00Z",
            "relevance": 0.85,  # Starts high
            "decay_rate": 0.1,  # Per minute
            "current_relevance": calculate_decayed_relevance()
        }
    },

    # NEW: Clipboard with time tracking
    "clipboard_history": [
        {
            "content": "Revenue $2.4M",
            "copied_at": "2026-01-29T14:32:00Z",
            "source_app": "Chrome",
            "relevance": 0.9,
            "minutes_ago": 2,
            "decayed_relevance": 0.85  # Fades over time
        }
    ],

    # NEW: N-gram state
    "ngram_context": {
        "last_3_words": ["in", "conclusion", "our"],
        "last_10_words": [...],
        "word_frequency_this_session": {...}
    }
}
```

**Cross-App Time Decay Algorithm:**
```python
def calculate_cross_app_relevance(context_entry):
    minutes_elapsed = (now() - context_entry.captured_at).minutes

    base_relevance = context_entry.initial_relevance

    # Exponential decay
    decay_factor = math.exp(-0.1 * minutes_elapsed)

    current_relevance = base_relevance * decay_factor

    # Threshold: Below 0.3, consider irrelevant
    if current_relevance < 0.3:
        return 0.0  # Fade out completely

    return current_relevance
```

### 5.2 Long-Term Memory (LTM)

**Storage:** MongoDB (persistent, encrypted)

**Update Frequency:** End of session (batch update)

**Lifespan:** Persistent until user deletes

**Session Aggregation:**
```python
def aggregate_session_to_ltm(stm_session):
    # Calculate average metrics
    avg_typing_speed = mean(stm_session.typing_speeds)
    avg_acceptance_rate = mean(stm_session.acceptance_rates)

    # Extract patterns
    frequent_phrases = extract_frequent_ngrams(stm_session)
    rejected_patterns = extract_rejection_patterns(stm_session)

    # Update LTM
    ltm.update({
        "typing_baseline_wpm": (
            ltm.typing_baseline_wpm * 0.9 +  # Historical
            avg_typing_speed * 0.1            # New session
        ),
        "acceptance_rate": moving_average(
            ltm.acceptance_rate,
            avg_acceptance_rate
        ),
        "vocabulary": merge_vocabulary(
            ltm.vocabulary,
            frequent_phrases
        ),
        "rejection_patterns": update_rejection_patterns(
            ltm.rejection_patterns,
            rejected_patterns
        )
    })
```

### 5.3 Privacy UI

**Settings Interface:**
```
┌───────────────────────────────────────────────────────────┐
│  🔒 AI Keyboard Privacy Settings                          │
├───────────────────────────────────────────────────────────┤
│                                                           │
│  📊 SHORT-TERM MEMORY (Current Session)                   │
│  ├─ Status: Active (will be deleted on close)            │
│  ├─ Contains: Text context, app switches, clipboard      │
│  ├─ Size: ~2.4 MB (encrypted locally)                    │
│  └─ [🗑️ Clear Now]                                        │
│                                                           │
│  🧠 LONG-TERM MEMORY (Persistent Learning)                │
│  ├─ Status: Storing patterns only (no actual text)       │
│  ├─ Stored: Writing style, preferences, vocabulary       │
│  ├─ Size: 145 KB                                          │
│  ├─ [👀 View Data]  [✏️ Edit]  [🗑️ Delete All]          │
│  └─ ☑ Allow learning from my writing patterns            │
│                                                           │
│  🔐 USER PROFILE DETECTION                                │
│  ├─ Current profile confidence: 94%                       │
│  ├─ ☑ Detect different users/moods                        │
│  └─ [📋 View Profile Properties]                          │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

**User Controls:**
1. **View STM:** See what's currently being tracked
2. **Edit LTM:** Modify learned patterns manually
3. **Delete All:** One-click complete data wipe
4. **Export Data:** Download all stored information
5. **Pause Learning:** Temporarily stop LTM updates

---

## 6️⃣ LOCAL LLM FALLBACK STRATEGY

### Decision Matrix

| Scenario | LLM Choice | Rationale |
|----------|------------|-----------|
| **Online + Good latency** | Cloud LLM (GPT-4/Claude) | Best quality |
| **Online + High latency** | Local LLM | Speed priority |
| **Offline** | Local LLM | Only option |
| **Privacy mode enabled** | Local LLM | No data leaves device |
| **Cloud LLM failure** | Local LLM | Graceful degradation |

### Implementation Strategy

**Recommendation: YES, use local LLM as fallback**

**Rationale:**
- Provides graceful degradation (not failure)
- Offline functionality is valuable
- Privacy-conscious users appreciate it
- Overhead is manageable (one-time setup)

**Overhead Management:**
```python
class LLMRouter:
    def __init__(self):
        self.cloud_llm = GPT4Client()
        self.local_llm = None  # Lazy load

    def get_suggestion(self, context):
        # Try cloud first (if online and not privacy mode)
        if self.is_online() and not self.privacy_mode:
            try:
                response = self.cloud_llm.complete(
                    context, 
                    timeout=2.0  # Fast timeout
                )
                if response.latency_ms < 500:
                    return response
            except (Timeout, NetworkError):
                pass  # Fall through to local

        # Fallback to local
        if self.local_llm is None:
            self.local_llm = self.initialize_local()  # Lazy init

        return self.local_llm.complete(context)

    def initialize_local(self):
        # One-time setup (~ 2-3 seconds)
        return OllamaClient(model="phi-3-mini")  # 3.8GB
```

**Local Model Selection:**
- **Phi-3-Mini (3.8GB):** Best quality/size ratio
- **TinyLlama (1.1GB):** Ultra-fast, lower quality
- **Qwen2-1.5B (1.5GB):** Good balance

**User Control:**
```python
# User setting
preferences = {
    "llm_preference": "cloud_first" | "local_first" | "local_only",
    "auto_fallback": True,  # Automatic switching
    "max_cloud_latency_ms": 500  # Switch to local if exceeded
}
```

---

7️⃣ CONTEXT WINDOW STRATEGY FOR LLM
7.1 Goal of the Context Window
The context window should give the LLM a compact “situational awareness” of what the user is doing right now, why they are doing it, and how they prefer to be helped. It is built entirely from STM (current session signals) plus a thin layer of LTM (stable preferences and patterns).

At a high level, each prompt to the LLM is constructed from:

System framing: Who the assistant is and how it should respond.

Task framing: What app the user is in and what task they are performing.

Live text context: What is currently on screen around the cursor.

Session state: Mood, recent behavior, and suggestion patterns.

Workflow context: Cross-app activity and clipboard, when relevant.

User preferences: Style, formality, and learned do/don’t patterns from LTM.

7.2 What We Include in the Context Window
For each LLM call, we assemble a structured context summary from STM and LTM rather than dumping raw data. Conceptually, the context window contains these sections:

App & task header

Current app name and type (Word, Gmail, VS Code, Slack, etc.).

Detected task (writing email, report, chat, coding, etc.).

Document or conversation identity (title, section, channel, thread).

User profile & style (from LTM)

Role (e.g., software engineer).

Preferred writing style (professional, concise, etc.).

Default formality level and typical suggestion length.

Known “avoid” patterns (e.g., too verbose, overly casual).
​

Immediate text context (from STM)

Text before cursor: the current sentence/paragraph or code block.

Text after cursor if relevant (e.g., when editing middle of a sentence).

Any active selection when doing a rewrite rather than a completion.

Task-specific structural context

For documents: document title, current section/heading, brief summary of preceding content.

For email: recipient, subject, short thread preview, user’s typical email style.

For code: language, file name, current function or class, relevant imports.

For chat: last few messages, participants, channel name/topic.

Session and mood signals

Current mood label (focused, rushed, frustrated, exploratory, casual, neutral).

High-level behavioral patterns this session: acceptance rate, typing style, recent dismissals.

Temporal context: time of day, session phase (early, mid, late) if useful for strategy.

Cross‑app workflow context

Recent apps that are part of the current workflow (e.g., Sheets → Word, Browser → Docs).

For each, a short “what the user was doing there” summary, not full content.

Only included when clearly related to the current task (e.g., viewing Q1 data while writing Q1 report).

Clipboard context (if relevant)

Short preview of clipboard text (numbers, quotes, copied sentences).

Source app and high-level purpose (e.g., “Q1 Financial Data – Google Sheets”).

Only used when relevance scoring to current text is high (e.g., financial numbers while writing a financial report).

Feedback‑driven hints

Recent accepted suggestions: what worked (length, tone, type).

Recent strong rejections: what to avoid (too formal in casual app, too long, etc.).

Explicit guidance such as “user prefers concise completions here”.

All of this is formatted into a human-readable but highly structured prompt template, so the LLM can reliably parse each section (Context, Style, Mood, Clipboard, Patterns, Current Text, etc.).

7.3 How We Prepare the Context (Step-by-Step)
When the debounce logic decides we should call the LLM, the context window is prepared through these conceptual steps:

Read and normalize STM snapshot

Current app, text window, caret position, mood, cross‑app context, clipboard, behavior signals.

Normalize into a canonical STM JSON shape (currentcontext, textwindow, moodestimation, clipboardhistory, suggestionhistory, etc.).
​

Attach LTM overlays

Fetch the user’s profile and learned patterns (style, vocabulary, per‑app tone, rejection patterns).

Attach these as high-level constraints and preferences, not as raw historical text.
​

Identify task type and intent

Infer whether this is completion, correction, rewrite, or intent completion.

Use app type + text patterns + mood to decide how “aggressive” or “lightweight” suggestions should be.

Select relevant STM fields per task

For each task type (document/email/code/chat), choose which STM fields matter.

Example: code completions ignore mood wording but care deeply about language, imports, and current scope; email completions care about recipient, thread, and tone.

Build a textual context summary

Convert structured STM + LTM into a compact natural-language “Context” section.

Example for document writing:

“App: Microsoft Word, writing Q1 Report Conclusion section.”

“User: software engineer, professional and concise style.”

“Recent context: just viewed Q1 financial data in Google Sheets, copied ‘Revenue 2.4M 15% YoY’.”

“Mood: focused, user prefers medium-length, high-formality completions.”.

Fill task-specific prompt template

Plug context summary and current text into the right template (Document, Email, Code, Chat).

Each template defines which fields appear and in what order for maximum clarity.

Example: Email template always includes To, Subject, Thread Preview, User Email Style before the body.

Add explicit instructions and output contract

Add clear instructions: match style, respect formality, avoid known rejected patterns, consider clipboard/cross‑app info only if relevant.

Define output as strict JSON with fields like suggestion, type, confidence, etc..

At the end of this process, the LLM sees a single, well-structured prompt that encodes: “here is where we are, who the user is, what they’re doing, what just happened across apps, how they like to be helped, and the exact text to complete or rewrite”.

7.4 Principles Guiding Context Preparation
Without thinking about token limits, the strategy is governed by these principles:

Situation first, text second: The LLM should first understand situation (app, task, role, mood, workflow), then the raw text.

Only structured signals from history: LTM contributes patterns and preferences, never raw historical content.

Task-specific shaping: The same STM fields are re-shaped differently for documents, email, code, and chat.

Workflow awareness: Cross‑app and clipboard context is treated as part of the user’s workflow, not as independent noise.

Feedback-driven evolution: Accepted/dismissed suggestions continually refine what gets highlighted in future context windows.

This gives you a context-window strategy that is entirely about what information we surface and how we organize it, independent of any token budgeting logic.

---

## 8️⃣ EMOJI SUGGESTIONS

### Integration Strategy

**Section 1 (N-gram):** Emoji as word alternatives
**Section 2 (LLM):** Emoji integrated into sentence

### Emoji Selection

**LLM Role:** Determine WHEN and WHICH emoji (not generate directly)

**Implementation:**
```python
def get_emoji_suggestions(context, app_type):
    # Check if emojis are appropriate
    if not allows_emojis(app_type):
        return []  # No emojis in formal documents

    # LLM determines emoji fit
    llm_response = llm.suggest({
        "text": context.text_before_cursor,
        "task": "suggest_emoji_type",
        "format": "json"
    })

    # Response: {"emoji_type": "celebration", "position": "end"}

    # Map to actual emoji
    emoji_map = {
        "celebration": ["🎉", "🎊", "🥳"],
        "approval": ["👍", "✅", "👏"],
        "thinking": ["🤔", "💭", "🧐"]
    }

    return emoji_map.get(llm_response["emoji_type"], [])
```

### Application-Based Rules

```python
EMOJI_POLICY = {
    "Slack": "ENABLED",
    "WhatsApp": "ENABLED",
    "Gmail": "CONDITIONAL",  # Only if informal tone detected
    "Word": "DISABLED",
    "VS Code": "DISABLED",
    "LinkedIn": "CONDITIONAL"
}
```

---

## 9️⃣ N-GRAM REWRITE FACILITY

### Use Case: Grammarly-Style Error Underlines

**Real-time Error Detection:**
- Spelling mistakes
- Grammar errors
- Style issues

**Technology:**
- Local n-gram model + rule-based grammar checker
- Fast (< 50ms per text scan)
- No LLM needed

### Implementation

```python
class RealtimeErrorDetector:
    def __init__(self):
        self.spell_checker = SymSpell()  # Fast spell check
        self.grammar_rules = load_grammar_rules()

    def scan_text(self, text):
        errors = []

        # Spelling errors
        words = text.split()
        for i, word in enumerate(words):
            if not self.spell_checker.is_correct(word):
                suggestions = self.spell_checker.suggest(word)
                errors.append({
                    "type": "spelling",
                    "word": word,
                    "position": i,
                    "suggestions": suggestions[:3]
                })

        # Grammar errors (rule-based)
        grammar_issues = self.grammar_rules.check(text)
        errors.extend(grammar_issues)

        return errors

    def render_underlines(self, errors):
        # Draw red underlines in overlay
        for error in errors:
            draw_wavy_underline(
                position=error["position"],
                color="red" if error["type"] == "spelling" else "blue"
            )
```

### Hover Tooltip

```
User hovers over underlined word:
┌─────────────────────────────────────┐
│  ❌ Spelling error                  │
├─────────────────────────────────────┤
│  "recieve" → "receive"              │
│                                     │
│  Suggestions:                       │
│  • receive                          │
│  • deceive                          │
│  • perceive                         │
│                                     │
│  [✓ Fix]  [Ignore]  [Add to dict]  │
└─────────────────────────────────────┘
```

**Direct Injection:**
- User clicks suggestion → Text replaced automatically
- No clipboard involved
- Uses UI Automation SetValue() or clipboard paste

---

## 🔟 DIFFERENT USER DETECTION

### Problem

Same device, multiple users (or same user in very different moods)

### Detection Strategy

**Behavioral Profile Comparison:**
```python
class UserProfileDetector:
    def detect_profile_shift(self, stm, ltm):
        # Extract current session profile
        current_profile = {
            "typing_speed_wpm": stm.current_typing_speed,
            "formality_level": infer_formality(stm.text_window),
            "vocabulary_overlap": calculate_vocab_overlap(stm, ltm),
            "app_usage_pattern": stm.app_usage_sequence,
            "time_of_day_pattern": stm.time_of_day
        }

        # Compare with LTM expected ranges
        deviations = {}
        for metric, value in current_profile.items():
            expected_range = ltm.expected_ranges[metric]
            deviation = calculate_deviation(value, expected_range)
            deviations[metric] = deviation

        # Count significant deviations
        significant_count = sum(
            1 for d in deviations.values() if d > 2.0  # 2 std deviations
        )

        # If multiple metrics deviate significantly
        if significant_count >= 3:
            return "DIFFERENT_USER_OR_MODE"
        else:
            return "SAME_USER"

    def adapt_suggestions(self, detection_result, stm):
        if detection_result == "DIFFERENT_USER_OR_MODE":
            # Create temporary profile from STM only
            # Don't use LTM heavily
            suggestion_weight = {
                "stm": 0.9,  # Trust current session
                "ltm": 0.1   # Minimal historical influence
            }
        else:
            # Normal operation
            suggestion_weight = {
                "stm": 0.6,
                "ltm": 0.4
            }

        return suggestion_weight
```

### Minimal Impact Principle

**Design Goal:** Detection should enhance, not disrupt

**Implementation:**
- Passive detection (no user intervention needed)
- Gradual adaptation (not sudden changes)
- Small weight adjustment (not complete profile switch)
- Privacy-preserving (no user identification)

---

## 1️⃣1️⃣ CODE SUGGESTIONS MODE

### Separate Mode Activation

**Triggers:**
- File extension (.py, .js, .java, etc.)
- App detection (VS Code, PyCharm, Sublime)
- User manual toggle

### Multi-File Analysis

**Challenge:** Code depends on multiple files

**Solution:**
```python
class CodeContextAnalyzer:
    def gather_code_context(self, current_file):
        context = {
            "current_file": {
                "content": read_file(current_file),
                "language": detect_language(current_file),
                "imports": extract_imports(current_file),
                "cursor_scope": find_current_function_or_class()
            },
            "related_files": []
        }

        # Analyze imports to find related files
        for import_statement in context["current_file"]["imports"]:
            related_file = resolve_import(import_statement)
            if related_file and file_exists(related_file):
                context["related_files"].append({
                    "path": related_file,
                    "relevant_symbols": extract_relevant_symbols(related_file)
                })

        # Limit to prevent context explosion
        context["related_files"] = context["related_files"][:5]

        return context
```

### Language Detection

```python
LANGUAGE_MAP = {
    ".py": "Python",
    ".js": "JavaScript",
    ".ts": "TypeScript",
    ".java": "Java",
    ".cpp": "C++",
    ".go": "Go",
    ".rs": "Rust",
    ".rb": "Ruby"
}

def detect_language(file_path):
    ext = os.path.splitext(file_path)[1]
    return LANGUAGE_MAP.get(ext, "Unknown")
```

### Code Suggestion Format

**Single-line suggestions:**
```python
# User types: def calculate_
# Suggestion: total_price(items, tax_rate):
```

**Multi-line suggestions:**
```python
# User types: # Sort users by age
# Suggestion:
# sorted_users = sorted(users, key=lambda x: x.age)
```

**Tooltip rendering:**
```
┌────────────────────────────────────────┐
│  💡 Code suggestion                    │
├────────────────────────────────────────┤
│  def calculate_total(items, tax):      │
│      subtotal = sum(items)             │
│      return subtotal * (1 + tax)       │
│                                        │
│  [Tab] Accept  [Esc] Dismiss           │
└────────────────────────────────────────┘
```

---

## 1️⃣2️⃣ STM/LTM UPDATE STRATEGY

### STM: Frequent Local Updates

**Update Frequency:** Continuous (debounced 100ms)

```python
class STMUpdater:
    def __init__(self):
        self.update_queue = Queue()
        self.debounce_timer = None

    def on_event(self, event):
        # Queue the update
        self.update_queue.put(event)

        # Reset debounce timer
        if self.debounce_timer:
            self.debounce_timer.cancel()

        # Schedule update
        self.debounce_timer = Timer(0.1, self.flush_updates)
        self.debounce_timer.start()

    def flush_updates(self):
        # Apply all queued updates
        while not self.update_queue.empty():
            event = self.update_queue.get()
            self.apply_update(event)

        # Encrypt and save to local storage
        encrypt_and_save(self.stm)
```

**Events that trigger STM update:**
- Keystroke (text change)
- App switch
- Clipboard change
- Suggestion shown/accepted/dismissed
- Mood recalculation (every 30s)

### LTM: Session-End Batch Update

**Update Timing:** When session ends (app close, inactivity timeout)

```python
class LTMUpdater:
    def on_session_end(self, stm_session):
        # Aggregate session metrics
        aggregated = self.aggregate_session(stm_session)

        # Merge with existing LTM
        self.merge_into_ltm(aggregated)

        # Save to MongoDB
        self.persist_to_database()

    def aggregate_session(self, stm_session):
        return {
            "avg_typing_speed": mean(stm_session.typing_speeds),
            "avg_acceptance_rate": mean(stm_session.acceptance_rates),
            "new_vocabulary": extract_new_words(stm_session),
            "new_rejection_patterns": extract_rejections(stm_session),
            "app_usage": stm_session.app_time_distribution,
            "mood_distribution": stm_session.mood_history
        }

    def merge_into_ltm(self, aggregated):
        # Moving average approach
        for metric in aggregated:
            if metric in self.ltm:
                self.ltm[metric] = (
                    self.ltm[metric] * 0.9 +  # Historical weight
                    aggregated[metric] * 0.1   # New session weight
                )
            else:
                self.ltm[metric] = aggregated[metric]
```

**Session End Triggers:**
- Application close
- Inactivity for 30 minutes
- User logout
- System shutdown

---

## 1️⃣3️⃣ NO CACHING DECISION

### Rationale

**Why no caching:**
1. Context changes rapidly (every keystroke)
2. Cached suggestions would be stale
3. Adds complexity without benefit
4. Modern LLMs are fast enough (50-200ms)
5. Local n-gram is already near-instant

**Alternative: Smart Request Deduplication**

Instead of caching, avoid duplicate requests:

```python
class RequestDeduplicator:
    def __init__(self):
        self.last_request_hash = None
        self.last_response = None

    def get_suggestion(self, context):
        # Hash the context
        context_hash = hash_context(context)

        # If identical to last request
        if context_hash == self.last_request_hash:
            return self.last_response  # Reuse

        # Otherwise, make new request
        response = llm.complete(context)

        # Store for potential reuse
        self.last_request_hash = context_hash
        self.last_response = response

        return response
```

**Benefit:** Prevents redundant API calls without stale cache

---

## 1️⃣4️⃣ LLM CALLING STRATEGY & LATENCY

### Target Latencies

```
┌─────────────────────────────────────────────────────────────┐
│  LATENCY TARGETS BY COMPONENT                               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  N-gram Suggestions:              < 10ms (instant)          │
│  ├─ Model inference: 5ms                                    │
│  └─ Rendering: 3ms                                          │
│                                                             │
│  Local LLM (Phi-3-mini):          50-150ms                  │
│  ├─ Context preparation: 10ms                               │
│  ├─ Model inference: 100ms (quantized)                      │
│  └─ Post-processing: 10ms                                   │
│                                                             │
│  Cloud LLM (GPT-4-turbo):         100-300ms                 │
│  ├─ Context preparation: 10ms                               │
│  ├─ Network round-trip: 50ms                                │
│  ├─ API processing: 150ms (streaming)                       │
│  └─ Post-processing: 10ms                                   │
│                                                             │
│  Emoji Processing:                20-50ms                   │
│  Error Detection (n-gram):        30-80ms                   │
│  Code Suggestions:                150-400ms (multi-file)    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Optimization Strategies

**1. Streaming Responses**
```python
def stream_suggestion(context):
    for token in llm.stream(context):
        yield token  # Show incrementally
        # User sees first words in ~50ms
```

**2. Parallel Processing**
```python
async def get_dual_suggestions(context):
    # Start both simultaneously
    ngram_task = asyncio.create_task(get_ngram_suggestions(context))
    llm_task = asyncio.create_task(get_llm_suggestion(context))

    # N-gram completes first (~10ms)
    ngram_result = await ngram_task
    show_ngram_suggestions(ngram_result)

    # LLM completes later (~150ms)
    llm_result = await llm_task
    show_llm_suggestion(llm_result)
```

**3. Context Compression**
- Limit context to 2000 tokens
- Prioritize recent text over distant text
- Skip low-relevance cross-app context

**4. Model Selection by Urgency**
```python
def select_model(mood, urgency):
    if mood == "rushed" or urgency == "high":
        return "local_llm"  # Faster, lower latency
    else:
        return "cloud_llm"  # Better quality
```

### Latency Monitoring

```python
class LatencyMonitor:
    def track_suggestion_latency(self, start_time, end_time, model):
        latency_ms = (end_time - start_time) * 1000

        # Log to analytics
        log_metric({
            "metric": "suggestion_latency",
            "model": model,
            "latency_ms": latency_ms,
            "timestamp": now()
        })

        # Alert if threshold exceeded
        if latency_ms > LATENCY_THRESHOLD[model]:
            alert(f"High latency detected: {latency_ms}ms for {model}")
```

---

## 1️⃣5️⃣ COMPLETE SYSTEM FLOW

### End-to-End Suggestion Pipeline

```
User Types Character
        ↓
┌───────────────────────────────────────────────────────────────┐
│ 1. KEYSTROKE CAPTURE                                          │
│    • Global keyboard hook captures keystroke                  │
│    • Update STM with new character                            │
│    • Reset debounce timer                                     │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 2. IMMEDIATE N-GRAM UPDATE (< 10ms)                           │
│    • Calculate 3-word suggestions                             │
│    • Update Section 1 of suggestion overlay                   │
│    • User sees instant word suggestions                       │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 3. ADAPTIVE DEBOUNCE WAIT                                     │
│    • Calculate delay based on typing speed + mood             │
│    • Wait for typing pause (500-1200ms)                       │
│    • Cancel if user keeps typing                              │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 4. PRE-FLIGHT CHECKS                                          │
│    • Sufficient context? (> 10 chars)                         │
│    • In text field?                                           │
│    • User still paused?                                       │
│    • Recent dismissals < threshold?                           │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
         ┌───────────┴────────────┐
         │                        │
┌────────▼────────┐      ┌────────▼────────┐
│ 5a. COMMAND     │      │ 5b. DEFAULT     │
│     MODE        │      │     MODE        │
│ • User clicked  │      │ • Automatic     │
│   icon          │      │   trigger       │
│ • Custom prompt │      │ • Context-aware │
└────────┬────────┘      └────────┬────────┘
         │                        │
         └───────────┬────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 6. CONTEXT PREPARATION                                        │
│    • Build optimized context (2000 tokens max)                │
│    • Include: text before, user profile, mood, cross-app      │
│    • Calculate cross-app relevance with time decay            │
│    • Filter clipboard by relevance threshold                  │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 7. LLM SELECTION & CALL                                       │
│    • Choose: Cloud LLM (default) or Local LLM (fallback)      │
│    • Send context to selected LLM                             │
│    • Stream response for low perceived latency                │
│    • Timeout after 2s, fallback to local if needed            │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 8. POST-PROCESSING                                            │
│    • Validate suggestion quality                              │
│    • Check confidence >= dynamic threshold                    │
│    • Verify user still paused (no new keystrokes)             │
│    • Not duplicate of recent suggestion?                      │
│    • Emoji processing (if applicable)                         │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 9. RENDER SUGGESTION                                          │
│    • Update Section 2 of overlay (LLM suggestion)             │
│    • Position tooltip at cursor location                      │
│    • Enable keyboard shortcuts (Tab, Esc, etc.)               │
│    • Make suggestion editable on click                        │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 10. USER INTERACTION                                          │
│     • User accepts (Tab) → Insert text, log acceptance        │
│     • User dismisses (Esc) → Hide, log dismissal              │
│     • User edits → Allow inline editing                       │
│     • User ignores → Auto-hide after 10s                      │
└────────────────────┬──────────────────────────────────────────┘
                     ↓
┌───────────────────────────────────────────────────────────────┐
│ 11. LEARNING UPDATE                                           │
│     • Update STM immediately (encrypted local)                │
│     • Track: acceptance rate, time to decision, patterns      │
│     • Adjust future suggestion thresholds                     │
│     • Queue for LTM update at session end                     │
└───────────────────────────────────────────────────────────────┘
```

---

## 1️⃣6️⃣ FEATURE SUMMARY TABLE

| Feature | Technology | Latency | Update Frequency | Storage |
|---------|-----------|---------|------------------|---------|
| **N-gram Word Suggestions** | Trigram model | < 10ms | Every keystroke | RAM only |
| **LLM Sentence Suggestions** | GPT-4/Phi-3 | 50-200ms | Debounced (500-1200ms) | None (generated fresh) |
| **Spelling/Grammar Check** | SymSpell + rules | 30-80ms | Every pause | Rules in disk |
| **Emoji Suggestions** | LLM-directed | 20-50ms | Conditional | Emoji map in RAM |
| **Code Suggestions** | LLM + AST | 150-400ms | On pause | None |
| **Cross-App Tracking** | UI Automation | Immediate | On app switch | STM only |
| **Clipboard Intelligence** | Text analysis | Immediate | On clipboard change | STM only |
| **Mood Estimation** | Multi-signal ML | Background | Every 30s | STM + LTM patterns |
| **User Profile Detection** | Statistical | Background | Continuous | LTM ranges |
| **STM Updates** | Encrypted JSON | < 5ms | Every 100ms (debounced) | Encrypted local file |
| **LTM Updates** | MongoDB | 50-100ms | Session end | Persistent database |

---

## 1️⃣7️⃣ PERFORMANCE BENCHMARKS

### Target Performance (Production-Ready)

```
BEST CASE (User perceives as instant):
├─ N-gram suggestions: 8ms
├─ Local LLM: 80ms
└─ Cloud LLM: 120ms

ACCEPTABLE CASE (User barely notices):
├─ N-gram suggestions: 15ms
├─ Local LLM: 150ms
└─ Cloud LLM: 250ms

DEGRADED CASE (User notices slight delay):
├─ N-gram suggestions: 25ms
├─ Local LLM: 300ms
└─ Cloud LLM: 500ms (switch to local fallback)
```

### Optimization Priorities

1. **Minimize context preparation** (< 10ms)
2. **Use streaming for LLM** (partial results ASAP)
3. **Parallel n-gram + LLM calls** (don't wait for both)
4. **Aggressive timeout on cloud** (2s max, then fallback)
5. **Quantized local models** (INT8 for 2x speedup)

---

## CONCLUSION

This enhanced architecture provides:

✅ **Dual suggestion modes** for flexibility  
✅ **Multi-tier performance** (instant n-gram + intelligent LLM)  
✅ **Privacy-first memory** (STM local, LTM user-controlled)  
✅ **Adaptive behavior** (typing speed, mood, context-aware)  
✅ **Graceful degradation** (local fallback, no failures)  
✅ **Production-ready latency** (< 200ms target)  
✅ **Comprehensive learning** (session-based LTM updates)

**Next Steps:**
1. Implement n-gram model integration
2. Build two-section UI renderer
3. Develop adaptive debounce system
4. Test latency optimizations
5. Create privacy settings UI
6. Deploy local LLM with lazy loading
7. Integrate emoji processing pipeline
8. Build code mode with multi-file analysis

---

**Generated:** January 29, 2026  
**Document Version:** 3.0  
**Total Features:** 15 major enhancements  
