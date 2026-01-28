# AI Keyboard — Core Intelligence Systems Design

> This document details the strategic design of the three core systems that power intelligent suggestions.
> **Objective:** Maximize suggestion quality for real-world production use.
> Date: 2026-01-22

---

## 🎯 Design Philosophy

Before diving into technical details, here's our guiding philosophy:

**The best suggestion is one that:**

1. **Anticipates** what the user wants to say before they finish thinking it
2. **Adapts** to the user's current context, mood, and style
3. **Learns** from patterns without being invasive
4. **Feels invisible** — like the keyboard itself is intelligent

---

# 1️⃣ SHORT-TERM MEMORY (STM) — Data Structure Design

## Purpose

STM captures **everything relevant about the current session** to provide maximally contextualized suggestions. It's the "working memory" that the LLM uses to understand the user's immediate context.

## Design Principles

1. **Comprehensive but structured** — Capture all relevant data, organized for LLM consumption
2. **Temporal awareness** — Know what happened recently vs. long ago in this session
3. **Multi-dimensional context** — App, content, behavior, mood, cross-app workflow
4. **Privacy-first** — Encrypted locally, deleted on session end

---

## STM Data Structure (Complete JSON Schema)

```json
{
  "session": {
    "id": "uuid-v4",
    "started_at": "2026-01-22T14:30:00Z",
    "duration_minutes": 45,
    "platform": "windows"
  },

  "user_profile_snapshot": {
    "preferences": {
      "writing_style": "professional",
      "verbosity": "concise",
      "formality_default": 0.7
    },
    "role": "software_engineer",
    "common_contexts": ["email", "code", "documentation"]
  },

  "current_context": {
    "app": {
      "name": "Microsoft Word",
      "process": "WINWORD.EXE",
      "window_title": "Q1 Report.docx - Word",
      "app_type": "document_editor",
      "detected_purpose": "report_writing"
    },

    "document": {
      "title": "Q1 Report.docx",
      "type": "report",
      "estimated_length": "medium",
      "structure": {
        "current_section": "Conclusion",
        "sections_before": ["Introduction", "Analysis", "Findings"],
        "sections_after": ["Recommendations", "Appendix"]
      }
    },

    "text_window": {
      "cursor_position": {
        "paragraph": 42,
        "character": 156
      },
      "text_before_cursor": {
        "immediate": "In conclusion, our analysis shows that",
        "paragraph": "In conclusion, our analysis shows that",
        "page": "...financial performance exceeded expectations by 15%. Revenue grew across all segments. In conclusion, our analysis shows that",
        "extended": "[Last 5 pages of content - summarized]"
      },
      "text_after_cursor": {
        "immediate": "",
        "paragraph": "",
        "page": "[Next page content if exists]"
      },
      "selected_text": null
    }
  },

  "cross_app_context": {
    "recent_apps": [
      {
        "app": "Chrome",
        "title": "Q1 Financial Data - Google Sheets",
        "time_spent_seconds": 180,
        "switched_from_at": "2026-01-22T15:10:00Z",
        "context_summary": "Viewing financial spreadsheet with Q1 data"
      },
      {
        "app": "Outlook",
        "title": "RE: Q1 Report Draft - Email",
        "time_spent_seconds": 60,
        "switched_from_at": "2026-01-22T15:05:00Z",
        "context_summary": "Reading email about report deadline"
      }
    ],
    "workflow_inference": {
      "detected_workflow": "report_writing_from_data",
      "confidence": 0.85,
      "related_apps": ["Excel/Sheets", "Word", "Email"]
    },
    "clipboard_history": [
      {
        "content_preview": "Revenue: $2.4M (+15% YoY)",
        "source_app": "Chrome",
        "copied_at": "2026-01-22T15:08:00Z",
        "type": "text"
      }
    ]
  },

  "behavioral_signals": {
    "typing_patterns": {
      "current_speed_wpm": 65,
      "baseline_speed_wpm": 72,
      "speed_trend": "slightly_slower",
      "pause_frequency": "normal",
      "backspace_frequency": "low"
    },
    "interaction_patterns": {
      "suggestions_shown": 12,
      "suggestions_accepted": 8,
      "suggestions_dismissed": 4,
      "acceptance_rate_session": 0.67,
      "last_suggestion_outcome": "accepted",
      "time_to_decision_avg_ms": 1200
    },
    "focus_patterns": {
      "app_switches_last_10min": 3,
      "reading_vs_writing_ratio": 0.3,
      "active_typing_percentage": 0.6
    }
  },

  "mood_estimation": {
    "current_mood": "focused",
    "confidence": 0.78,
    "mood_history": [
      { "time": "14:30", "mood": "neutral", "confidence": 0.5 },
      { "time": "14:45", "mood": "focused", "confidence": 0.65 },
      { "time": "15:00", "mood": "focused", "confidence": 0.78 }
    ],
    "contributing_factors": {
      "time_of_day": "afternoon",
      "typing_speed_vs_baseline": 0.9,
      "task_engagement": "high",
      "multitasking_level": "low"
    }
  },

  "temporal_context": {
    "time_of_day": "15:15",
    "day_of_week": "Wednesday",
    "session_phase": "mid_session",
    "productivity_window": "peak_afternoon"
  },

  "suggestion_history": {
    "recent_suggestions": [
      {
        "suggestion": "exceeded expectations",
        "context": "performance",
        "outcome": "accepted",
        "time": "15:12"
      },
      {
        "suggestion": "year-over-year growth",
        "context": "financial",
        "outcome": "dismissed",
        "time": "15:10"
      }
    ],
    "patterns_this_session": {
      "preferred_suggestion_length": "medium",
      "preferred_formality": "high",
      "rejected_patterns": ["overly casual", "too verbose"]
    }
  }
}
```

---

## STM Update Strategy

| Event                         | What Updates                           | Latency           |
| ----------------------------- | -------------------------------------- | ----------------- |
| Keystroke                     | `text_window`, `typing_patterns`       | Debounced (300ms) |
| App switch                    | `current_context`, `cross_app_context` | Immediate         |
| Suggestion shown              | `suggestion_history`                   | Immediate         |
| Suggestion accepted/dismissed | `behavioral_signals`, `patterns`       | Immediate         |
| Every 30 seconds              | `mood_estimation`, `temporal_context`  | Background        |
| Clipboard change              | `clipboard_history`                    | Immediate         |

---

## STM → LLM Prompt Transformation

The full STM is too large for every LLM call. We transform it into a **context summary**:

```
CONTEXT SUMMARY (for LLM):
- App: Microsoft Word, writing Q1 Report (Conclusion section)
- User: Software engineer, prefers professional/concise style
- Current text: "In conclusion, our analysis shows that"
- Recent context: Just viewed Q1 financial data in Sheets, copied "Revenue: $2.4M"
- Mood: Focused, typing steadily
- Session: 45 min in, 67% suggestion acceptance rate
- Patterns: Prefers medium-length, high formality, rejected verbose options
```

---

# 2️⃣ MOOD ESTIMATION ALGORITHM

## Purpose

Mood affects how users want to be helped. Understanding mood = better suggestions.

## Mood Categories

| Mood            | Description                      | Suggestion Strategy                                 |
| --------------- | -------------------------------- | --------------------------------------------------- |
| **focused**     | User in flow, deep work          | Minimal interruptions, high-quality only, longer OK |
| **rushed**      | Hurrying, needs speed            | Quick, short suggestions, prioritize speed          |
| **exploratory** | Brainstorming, open to ideas     | Offer alternatives, creative suggestions            |
| **frustrated**  | Struggling, needs help           | Extra helpful, offer corrections, simplify          |
| **casual**      | Relaxed, informal context        | Allow informal tone, emoji suggestions OK           |
| **neutral**     | Default state, no strong signals | Standard balanced approach                          |

---

## Multi-Signal Mood Detection

### Signal Weights

| Signal Category      | Weight | Signals                           |
| -------------------- | ------ | --------------------------------- |
| **Typing Behavior**  | 35%    | Speed vs baseline, pauses, rhythm |
| **Editing Patterns** | 25%    | Backspace frequency, corrections  |
| **App Behavior**     | 20%    | App switch frequency, focus time  |
| **Temporal**         | 10%    | Time of day, session duration     |
| **Session Context**  | 10%    | How long working, break patterns  |

### Signal → Mood Mapping

```python
MOOD_SIGNALS = {
    "typing_speed_vs_baseline": {
        "much_faster": {"rushed": 0.7, "focused": 0.3},
        "faster": {"rushed": 0.4, "focused": 0.5},
        "normal": {"neutral": 0.6, "focused": 0.3},
        "slower": {"exploratory": 0.4, "frustrated": 0.3},
        "much_slower": {"frustrated": 0.5, "exploratory": 0.3}
    },

    "backspace_frequency": {
        "very_high": {"frustrated": 0.6, "exploratory": 0.2},
        "high": {"frustrated": 0.3, "exploratory": 0.3},
        "normal": {"neutral": 0.5, "focused": 0.3},
        "low": {"focused": 0.5, "casual": 0.2}
    },

    "app_switch_frequency": {
        "very_high": {"rushed": 0.4, "frustrated": 0.3},
        "high": {"exploratory": 0.4, "rushed": 0.2},
        "normal": {"neutral": 0.5},
        "low": {"focused": 0.6}
    },

    "time_of_day": {
        "early_morning": {"focused": 0.4},
        "morning": {"focused": 0.5},
        "afternoon": {"neutral": 0.5},
        "evening": {"casual": 0.3, "frustrated": 0.2},
        "late_night": {"rushed": 0.3, "frustrated": 0.3}
    }
}
```

### Mood Calculation Algorithm

```python
def calculate_mood(stm: STM) -> MoodEstimate:
    # Collect signals
    signals = extract_all_signals(stm)

    # Calculate weighted mood scores
    mood_scores = defaultdict(float)

    for signal_name, signal_value in signals.items():
        signal_config = MOOD_SIGNALS[signal_name][signal_value]
        weight = SIGNAL_WEIGHTS[signal_name]

        for mood, score in signal_config.items():
            mood_scores[mood] += score * weight

    # Apply momentum (mood shouldn't change too rapidly)
    prev_mood = stm.mood_estimation.current_mood
    mood_scores[prev_mood] += 0.15  # Stability bonus

    # Normalize and select
    total = sum(mood_scores.values())
    mood_probs = {m: s/total for m, s in mood_scores.items()}

    best_mood = max(mood_probs, key=mood_probs.get)
    confidence = mood_probs[best_mood]

    return MoodEstimate(mood=best_mood, confidence=confidence)
```

---

# 3️⃣ LLM PROMPT DESIGN

## Prompt Architecture

```
┌─────────────────────────────────────────┐
│ SYSTEM PROMPT (Cached, rarely changes)  │
│ • Role definition                       │
│ • Output format                         │
│ • General guidelines                    │
└─────────────────────────────────────────┘
              +
┌─────────────────────────────────────────┐
│ CONTEXT PROMPT (Dynamic per-request)    │
│ • Current app & document                │
│ • User preferences                      │
│ • Mood-based guidance                   │
│ • Cross-app context                     │
└─────────────────────────────────────────┘
              +
┌─────────────────────────────────────────┐
│ INPUT (Current text)                    │
└─────────────────────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│ OUTPUT (Suggestion JSON)                │
└─────────────────────────────────────────┘
```

---

## System Prompt

```markdown
You are an intelligent writing assistant embedded in a keyboard.
Provide helpful text completions and suggestions.

## Output Format

Respond ONLY with valid JSON:
{
"suggestion": "the suggested text",
"type": "completion|correction|rewrite",
"confidence": 0.0-1.0,
"alternative": "optional alternative"
}

## Guidelines

1. Match the user's style - never be verbose if they're concise
2. Complete thoughts naturally - don't leave sentences hanging
3. Respect formality level of existing text
4. For corrections, suggest minimal changes
5. Consider cross-app context when relevant
6. If confidence < 0.5, don't suggest
```

---

## Context Prompt Templates

### Document Writing (Word, Docs)

```markdown
## Context

- App: {{app_name}}, Document: "{{document_title}}"
- Section: {{current_section}}
- Style: {{writing_style}}, Formality: {{formality}}/10

## Mood Guidance ({{mood}})

{{mood_instructions}}

## Cross-App Context

{{#each recent_apps}}

- {{app}}: {{context_summary}} ({{minutes_ago}} min ago)
  {{/each}}
  {{#if clipboard}}Clipboard: "{{clipboard_preview}}"{{/if}}

## Session Patterns

- Acceptance rate: {{acceptance_rate}}%
- Preferred length: {{preferred_length}}
- Avoid: {{rejected_patterns}}

## Text

Before: """{{text_before}}"""
After: """{{text_after}}"""

Provide natural completion.
```

### Email Compose

```markdown
## Email Context

- To: {{recipient}}
- Subject: {{subject}}
- Replying to: {{thread_preview}}
- User's email style: {{email_style}}

## Text

"""{{email_body}}"""

Complete naturally, match user's email style.
```

### Code Writing

````markdown
## Code Context

- Language: {{language}}
- File: {{filename}}
- Current scope: {{current_function}}
- Imports: {{relevant_imports}}

## Code

```{{language}}
{{code_before}}█{{code_after}}
```
````

Suggest code completion. Prioritize: correctness > style > brevity

```

---

## Quality Optimization Pipeline

```

Stage 1: SHOULD WE SUGGEST?
Is user paused? Is context stable?
↓
Stage 2: BUILD PROMPT
Transform STM → optimized prompt
↓
Stage 3: LLM INFERENCE
Get suggestion from cloud/local LLM
↓
Stage 4: POST-PROCESS - Validate suggestion - Check for repetition - Apply user preference filters - Calculate final confidence
↓
Stage 5: DELIVERY DECISION
confidence > threshold? → Show
else → Don't interrupt

````

---

## Confidence Threshold Tuning

```python
def should_show_suggestion(suggestion, stm) -> bool:
    base_threshold = 0.6

    # Mood adjustment
    if stm.mood == "focused":
        threshold = base_threshold + 0.1  # Be more selective
    elif stm.mood == "frustrated":
        threshold = base_threshold - 0.1  # Be more helpful

    # Acceptance rate adjustment
    if stm.acceptance_rate > 0.7:
        threshold -= 0.05  # User accepts often
    elif stm.acceptance_rate < 0.3:
        threshold += 0.1   # User rejects often

    return suggestion.confidence >= threshold
````

---

## LTM Integration

### What Gets Learned

| Pattern            | How Learned                 | How Used                    |
| ------------------ | --------------------------- | --------------------------- |
| Writing style      | Analyze accepted text       | Guide suggestion tone       |
| Vocabulary         | Track frequent words        | Prioritize familiar terms   |
| Rejection patterns | Track dismissed suggestions | Avoid similar suggestions   |
| App preferences    | Per-app acceptance rates    | Customize per context       |
| Time patterns      | When most engaged           | Adjust suggestion frequency |

---

# 4️⃣ CLIPBOARD INTELLIGENCE SYSTEM

## What We Capture

| Clipboard Type | Capture?         | What We Store                                  |
| -------------- | ---------------- | ---------------------------------------------- |
| **Text**       | ✅ Yes           | Content preview (first 200 chars), full length |
| **Image**      | ⚠️ Metadata only | Filename, source app, timestamp                |
| **Files**      | ⚠️ Metadata only | Filename, extension, source app                |
| **Rich HTML**  | ✅ Text only     | Extract plain text, ignore formatting          |

**Privacy Rule:** We ANALYZE clipboard for relevance, but DON'T store full content in LTM.

---

## Clipboard Relevance Scoring Algorithm

```python
def score_clipboard_relevance(clipboard_entry, current_context) -> float:
    """
    Score how relevant clipboard content is to current suggestion context.
    Returns: 0.0 (ignore) to 1.0 (highly relevant)
    """
    score = 0.5  # Start neutral

    # ─────────────────────────────────────────────────────────────
    # FACTOR 1: TIME DECAY (older = less relevant)
    # ─────────────────────────────────────────────────────────────
    age_minutes = (now() - clipboard_entry.copied_at).minutes

    if age_minutes < 2:
        score += 0.3   # Very fresh - likely being used right now
    elif age_minutes < 10:
        score += 0.15  # Recent - probably related to current work
    elif age_minutes < 30:
        score += 0.0   # Neutral - might be relevant
    elif age_minutes < 60:
        score -= 0.1   # Getting stale
    else:
        score -= 0.25  # Old - probably from different task

    # ─────────────────────────────────────────────────────────────
    # FACTOR 2: SOURCE APP RELEVANCE
    # ─────────────────────────────────────────────────────────────
    if clipboard_entry.source_app == current_context.app.name:
        score += 0.1   # From same app - internal copy
    elif clipboard_entry.source_app in current_context.workflow_inference.related_apps:
        score += 0.2   # From related app in same workflow
    elif is_browser(clipboard_entry.source_app) and is_document_editor(current_context.app):
        score += 0.15  # Research workflow (browser → document)

    # ─────────────────────────────────────────────────────────────
    # FACTOR 3: CONTENT RELEVANCE (text only)
    # ─────────────────────────────────────────────────────────────
    if clipboard_entry.type == "text":
        # Semantic similarity with current document
        text_similarity = calculate_text_similarity(
            clipboard_entry.content_preview,
            current_context.text_window.text_before_cursor.paragraph
        )
        score += text_similarity * 0.25

        # Check for specific patterns
        if contains_data_pattern(clipboard_entry.content_preview):
            # Numbers, statistics, quotes - likely being referenced
            score += 0.1

        if is_url(clipboard_entry.content_preview):
            # URLs are less directly useful for suggestion
            score -= 0.1

    # ─────────────────────────────────────────────────────────────
    # FACTOR 4: TYPE RELEVANCE
    # ─────────────────────────────────────────────────────────────
    if clipboard_entry.type == "text":
        score += 0.0   # Neutral - text is always potentially useful
    elif clipboard_entry.type == "image":
        score -= 0.15  # Images less useful for text suggestions
    elif clipboard_entry.type == "file":
        score -= 0.2   # Files are even less useful

    # ─────────────────────────────────────────────────────────────
    # FACTOR 5: USER BEHAVIOR
    # ─────────────────────────────────────────────────────────────
    if clipboard_entry.was_pasted_recently:
        score -= 0.2   # Already used, less need to reference again

    # Clamp to valid range
    return max(0.0, min(1.0, score))


def decide_clipboard_inclusion(clipboard_entry, current_context) -> str:
    """Decide how to include clipboard in LLM context."""
    relevance = score_clipboard_relevance(clipboard_entry, current_context)

    if relevance >= 0.65:
        return "include_full"      # Include in context with emphasis
    elif relevance >= 0.45:
        return "include_brief"     # Mention briefly in context
    else:
        return "exclude"           # Don't include, not relevant
```

---

## Enhanced Clipboard Entry in STM

```json
"clipboard_history": [
  {
    "id": "clip_001",
    "content_preview": "Revenue: $2.4M (+15% YoY)",
    "content_length": 28,
    "type": "text",
    "source_app": "Chrome",
    "source_url": "sheets.google.com",
    "copied_at": "2026-01-22T15:08:00Z",
    "was_pasted": false,
    "relevance_score": 0.85,
    "inclusion_decision": "include_full"
  },
  {
    "id": "clip_002",
    "content_preview": "[Image: screenshot.png]",
    "type": "image",
    "source_app": "Snipping Tool",
    "copied_at": "2026-01-22T14:30:00Z",
    "was_pasted": true,
    "relevance_score": 0.25,
    "inclusion_decision": "exclude"
  }
]
```

---

# 5️⃣ FEEDBACK LOOP SYSTEM

## Purpose

Every suggestion the user sees is a learning opportunity. Capturing Accept/Dismiss feedback helps us:

1. Improve future suggestion quality
2. Learn user preferences
3. Adapt suggestion frequency
4. Personalize style

---

## Feedback Event Capture

```python
class FeedbackCapture:

    def on_suggestion_shown(self, suggestion: Suggestion, context: STM):
        """Log when a suggestion is displayed to user."""
        self.pending_feedback = {
            "id": generate_id(),
            "suggestion_text": suggestion.text,
            "suggestion_type": suggestion.type,  # completion, correction, rewrite
            "confidence": suggestion.confidence,
            "shown_at": now(),
            "context_snapshot": {
                "app": context.current_context.app.name,
                "text_before": context.text_window.text_before_cursor.immediate[-50:],
                "mood": context.mood_estimation.current_mood,
                "typing_speed": context.behavioral_signals.typing_patterns.current_speed_wpm
            }
        }

    def on_suggestion_accepted(self, method: str = "click"):
        """
        User accepted the suggestion.
        method: "click", "tab", "enter"
        """
        feedback = self.pending_feedback
        feedback["outcome"] = "accepted"
        feedback["decided_at"] = now()
        feedback["time_to_decision_ms"] = (now() - feedback["shown_at"]).milliseconds
        feedback["accept_method"] = method

        # Classify acceptance speed
        if feedback["time_to_decision_ms"] < 500:
            feedback["decision_speed"] = "instant"      # Obvious good suggestion
        elif feedback["time_to_decision_ms"] < 1500:
            feedback["decision_speed"] = "quick"        # Good suggestion
        elif feedback["time_to_decision_ms"] < 3000:
            feedback["decision_speed"] = "considered"   # Acceptable
        else:
            feedback["decision_speed"] = "hesitant"     # Just barely accepted

        self.save_feedback(feedback)
        self.update_stm(feedback)
        self.trigger_learning(feedback)

    def on_suggestion_dismissed(self, method: str):
        """
        User rejected the suggestion.
        method: "click", "escape", "typed_over", "ignored", "timeout"
        """
        feedback = self.pending_feedback
        feedback["outcome"] = "dismissed"
        feedback["decided_at"] = now()
        feedback["time_to_decision_ms"] = (now() - feedback["shown_at"]).milliseconds
        feedback["dismiss_method"] = method

        # Classify dismissal type
        if method == "escape":
            feedback["dismissal_strength"] = "strong"   # Active rejection
        elif method == "click":
            feedback["dismissal_strength"] = "clear"    # Deliberate rejection
        elif method == "typed_over":
            feedback["dismissal_strength"] = "passive"  # Ignored, kept typing
        elif method == "timeout":
            feedback["dismissal_strength"] = "neutral"  # Just didn't engage

        self.save_feedback(feedback)
        self.update_stm(feedback)
        self.trigger_learning(feedback)
```

---

## What We Learn From Feedback

| Feedback Signal               | What It Means        | How We Adapt                 |
| ----------------------------- | -------------------- | ---------------------------- |
| **Instant Accept** (<500ms)   | Perfect suggestion   | Increase similar suggestions |
| **Quick Accept** (500-1500ms) | Good suggestion      | This is the target speed     |
| **Hesitant Accept** (>3s)     | Barely acceptable    | Investigate why slow         |
| **Escape Dismiss**            | Actively annoyed     | Strongly avoid pattern       |
| **Click Dismiss**             | Not what user wanted | Learn the mismatch           |
| **Typed Over**                | Suggestion ignored   | Maybe wrong timing           |
| **Timeout**                   | Didn't even notice   | Reduce frequency             |

---

## Learning from Feedback

```python
def trigger_learning(feedback: dict):
    """Extract patterns and update preferences."""

    if feedback["outcome"] == "accepted":
        # ─────────────────────────────────────────────────────────
        # POSITIVE LEARNING
        # ─────────────────────────────────────────────────────────

        # Learn preferred suggestion length
        text_length = len(feedback["suggestion_text"])
        update_preference(
            "preferred_length",
            text_length,
            weight=1.0 if feedback["decision_speed"] == "instant" else 0.5
        )

        # Learn vocabulary
        words = extract_significant_words(feedback["suggestion_text"])
        for word in words:
            ltm.vocabulary[word] = ltm.vocabulary.get(word, 0) + 1

        # Learn app-specific success patterns
        app = feedback["context_snapshot"]["app"]
        ltm.app_patterns[app]["successes"] += 1

    else:  # Dismissed
        # ─────────────────────────────────────────────────────────
        # NEGATIVE LEARNING
        # ─────────────────────────────────────────────────────────

        # Strong rejection = remember to avoid
        if feedback["dismissal_strength"] in ["strong", "clear"]:
            pattern = extract_rejection_pattern(feedback)
            if pattern:
                if pattern in ltm.rejection_patterns:
                    ltm.rejection_patterns[pattern]["count"] += 1
                else:
                    ltm.rejection_patterns[pattern] = {
                        "count": 1,
                        "first_seen": now(),
                        "contexts": [feedback["context_snapshot"]["app"]]
                    }

        # Track app-specific failures
        app = feedback["context_snapshot"]["app"]
        ltm.app_patterns[app]["failures"] += 1


def extract_rejection_pattern(feedback: dict) -> Optional[str]:
    """
    Try to understand WHY user rejected.
    Returns a pattern identifier or None.
    """
    suggestion = feedback["suggestion_text"].lower()
    context = feedback["context_snapshot"]["text_before"].lower()

    # Pattern: Too formal for casual context
    if is_formal(suggestion) and is_casual_app(feedback["context_snapshot"]["app"]):
        return "formal_in_casual_context"

    # Pattern: Too casual for formal context
    if is_casual(suggestion) and is_formal_app(feedback["context_snapshot"]["app"]):
        return "casual_in_formal_context"

    # Pattern: Too long
    if len(suggestion) > 100:
        return "too_long"

    # Pattern: Repetitive (suggested same thing before)
    if suggestion in recent_suggestions:
        return "repetitive"

    # Pattern: Non-sequitur (doesn't follow context)
    if text_similarity(suggestion, context) < 0.2:
        return "non_sequitur"

    return None  # Can't identify pattern
```

---

# 6️⃣ AUTOMATIC LLM CALLING STRATEGY

## Core Principle

**Suggestions must be completely automatic.** User should never need to trigger them manually. The system should intelligently decide WHEN to call the LLM for suggestions.

---

## Smart Trigger System

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    AUTOMATIC TRIGGER DECISION TREE                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   User Types Character                                                  │
│         │                                                               │
│         ▼                                                               │
│   ┌─────────────────┐                                                   │
│   │ Reset Debounce  │                                                   │
│   │ Timer           │                                                   │
│   └────────┬────────┘                                                   │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────────────────────────────────────────────────────┐  │
│   │ Is this a NATURAL PAUSE POINT?                                  │  │
│   │                                                                 │  │
│   │  • Sentence end (. ! ?) followed by space    → SHORT DELAY     │  │
│   │  • Paragraph break (Enter twice)             → SHORT DELAY     │  │
│   │  • Comma pause                               → MEDIUM DELAY    │  │
│   │  • Regular typing                            → ADAPTIVE DELAY  │  │
│   └─────────────────────────────────────────────────────────────────┘  │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────────────────────────────────────────────────────┐  │
│   │ Wait for ADAPTIVE DEBOUNCE period...                            │  │
│   │                                                                 │  │
│   │  Fast typer (>80 WPM)  → 600ms                                  │  │
│   │  Normal typer          → 900ms                                  │  │
│   │  Slow typer (<40 WPM)  → 1200ms                                 │  │
│   │  Sentence end          → 400ms (shorter)                        │  │
│   │  User is "rushed"      → 500ms (quicker help)                   │  │
│   │  User is "focused"     → 1200ms (less interruption)             │  │
│   └─────────────────────────────────────────────────────────────────┘  │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────┐                                                   │
│   │ Debounce Timer  │───── User types again ───▶ CANCEL, restart       │
│   │ Expires         │                                                   │
│   └────────┬────────┘                                                   │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────────────────────────────────────────────────────┐  │
│   │ PRE-FLIGHT CHECKS                                               │  │
│   │                                                                 │  │
│   │  □ Is there enough context? (>10 chars typed)                   │  │
│   │  □ Is user in a text field? (not menu/button)                   │  │
│   │  □ Is this app allowed? (not in blocklist)                      │  │
│   │  □ No pending LLM request already?                              │  │
│   │  □ Last suggestion wasn't just dismissed? (avoid spam)          │  │
│   │                                                                 │  │
│   │  All pass? → PROCEED                                            │  │
│   │  Any fail? → SKIP this trigger                                  │  │
│   └─────────────────────────────────────────────────────────────────┘  │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────┐                                                   │
│   │ CALL LLM        │                                                   │
│   └────────┬────────┘                                                   │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────────────────────────────────────────────────────┐  │
│   │ POST-PROCESSING                                                 │  │
│   │                                                                 │  │
│   │  • Validate suggestion                                          │  │
│   │  • Check confidence threshold                                   │  │
│   │  • Verify user still paused                                     │  │
│   │  • Check not duplicate of recent suggestion                     │  │
│   │                                                                 │  │
│   │  All good? → SHOW SUGGESTION                                    │  │
│   │  Any fail? → DISCARD silently                                   │  │
│   └─────────────────────────────────────────────────────────────────┘  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Implementation

```python
class AutomaticSuggestionEngine:
    def __init__(self):
        self.debounce_timer = None
        self.pending_request = False
        self.last_suggestion_time = None
        self.last_dismissal_time = None
        self.typing_buffer = []

    # ═══════════════════════════════════════════════════════════════════
    # KEYSTROKE HANDLING
    # ═══════════════════════════════════════════════════════════════════

    def on_keystroke(self, key: str, stm: STM):
        """Called on every keystroke. Decides if/when to trigger suggestion."""

        # Update typing buffer for pattern detection
        self.typing_buffer.append({
            "key": key,
            "time": now()
        })
        self.typing_buffer = self.typing_buffer[-50:]  # Keep last 50

        # Cancel any pending debounce
        if self.debounce_timer:
            self.debounce_timer.cancel()

        # Calculate adaptive delay
        delay_ms = self.calculate_adaptive_delay(key, stm)

        # Schedule suggestion trigger
        self.debounce_timer = Timer(
            delay_ms / 1000,
            lambda: self.maybe_trigger_suggestion(stm)
        )
        self.debounce_timer.start()

    # ═══════════════════════════════════════════════════════════════════
    # ADAPTIVE DELAY CALCULATION
    # ═══════════════════════════════════════════════════════════════════

    def calculate_adaptive_delay(self, key: str, stm: STM) -> int:
        """
        Calculate how long to wait before triggering LLM.
        Adapts to: typing speed, punctuation, mood, recent behavior.
        """

        # Base delay from typing speed
        wpm = stm.behavioral_signals.typing_patterns.current_speed_wpm
        if wpm > 80:
            base_delay = 600    # Fast typer - short wait
        elif wpm > 60:
            base_delay = 800    # Normal-fast
        elif wpm > 40:
            base_delay = 1000   # Normal
        else:
            base_delay = 1300   # Slow typer - longer wait

        # ─────────────────────────────────────────────────────────────
        # MODIFIER 1: Natural pause points (reduce delay)
        # ─────────────────────────────────────────────────────────────
        text_before = stm.current_context.text_window.text_before_cursor.immediate

        if key in [".", "!", "?"] and not self.is_abbreviation(text_before):
            return int(base_delay * 0.5)  # Sentence end - quick response

        if key == " " and text_before.endswith((".", "!", "?")):
            return int(base_delay * 0.4)  # Space after sentence - very quick

        if key == "\n" and text_before.endswith("\n"):
            return int(base_delay * 0.6)  # Paragraph break

        if key == ",":
            return int(base_delay * 0.7)  # Clause break

        # ─────────────────────────────────────────────────────────────
        # MODIFIER 2: Mood-based adjustment
        # ─────────────────────────────────────────────────────────────
        mood = stm.mood_estimation.current_mood

        if mood == "rushed":
            base_delay = int(base_delay * 0.7)    # Quicker suggestions
        elif mood == "focused":
            base_delay = int(base_delay * 1.3)    # Less interruption
        elif mood == "frustrated":
            base_delay = int(base_delay * 0.8)    # More eager to help
        elif mood == "exploratory":
            base_delay = int(base_delay * 0.9)    # Open to suggestions

        # ─────────────────────────────────────────────────────────────
        # MODIFIER 3: Recent dismissal cooldown
        # ─────────────────────────────────────────────────────────────
        if self.last_dismissal_time:
            since_dismissal = (now() - self.last_dismissal_time).seconds
            if since_dismissal < 5:
                base_delay = int(base_delay * 1.5)  # Back off after rejection
            elif since_dismissal < 15:
                base_delay = int(base_delay * 1.2)  # Slight cooldown

        # ─────────────────────────────────────────────────────────────
        # MODIFIER 4: Session acceptance rate
        # ─────────────────────────────────────────────────────────────
        acceptance_rate = stm.behavioral_signals.interaction_patterns.acceptance_rate_session

        if acceptance_rate > 0.7:
            base_delay = int(base_delay * 0.85)   # User likes suggestions
        elif acceptance_rate < 0.3:
            base_delay = int(base_delay * 1.2)    # User rejecting often

        # Clamp to reasonable range
        return max(400, min(2000, base_delay))

    # ═══════════════════════════════════════════════════════════════════
    # PRE-FLIGHT CHECKS
    # ═══════════════════════════════════════════════════════════════════

    def should_trigger_suggestion(self, stm: STM) -> Tuple[bool, str]:
        """
        Run all pre-flight checks before calling LLM.
        Returns: (should_trigger, reason_if_not)
        """

        # Check 1: Enough context to work with
        text_before = stm.current_context.text_window.text_before_cursor.immediate
        if len(text_before.strip()) < 10:
            return False, "not_enough_context"

        # Check 2: User is in a text field
        if not stm.current_context.app.has_active_text_field:
            return False, "no_text_field"

        # Check 3: App is not blocklisted
        if stm.current_context.app.name in self.blocklist:
            return False, "app_blocked"

        # Check 4: No pending request
        if self.pending_request:
            return False, "request_pending"

        # Check 5: Not immediately after dismissal
        if self.last_dismissal_time:
            since_dismissal = (now() - self.last_dismissal_time).seconds
            if since_dismissal < 2:
                return False, "post_dismissal_cooldown"

        # Check 6: User hasn't started typing again
        if self.typing_buffer:
            last_keystroke = self.typing_buffer[-1]["time"]
            if (now() - last_keystroke).milliseconds < 300:
                return False, "user_still_typing"

        # Check 7: Not too soon after last suggestion
        if self.last_suggestion_time:
            since_last = (now() - self.last_suggestion_time).seconds
            if since_last < 3:
                return False, "suggestion_cooldown"

        return True, "ok"

    # ═══════════════════════════════════════════════════════════════════
    # TRIGGER SUGGESTION
    # ═══════════════════════════════════════════════════════════════════

    def maybe_trigger_suggestion(self, stm: STM):
        """Called when debounce timer expires. Decides whether to actually call LLM."""

        should_trigger, reason = self.should_trigger_suggestion(stm)

        if not should_trigger:
            log_skipped_trigger(reason)
            return

        # All checks passed - call LLM
        self.pending_request = True

        try:
            suggestion = self.call_llm_for_suggestion(stm)

            if suggestion and self.should_show_suggestion(suggestion, stm):
                self.show_suggestion(suggestion)
                self.last_suggestion_time = now()

        finally:
            self.pending_request = False

    # ═══════════════════════════════════════════════════════════════════
    # EFFICIENT CONTEXT BUILDING
    # ═══════════════════════════════════════════════════════════════════

    def build_efficient_context(self, stm: STM) -> dict:
        """
        Build minimal but sufficient context for LLM.
        Optimize for: relevance, token efficiency, latency.
        """

        context = {}

        # ─────────────────────────────────────────────────────────────
        # ALWAYS INCLUDE (Essential)
        # ─────────────────────────────────────────────────────────────
        context["app_type"] = stm.current_context.app.app_type
        context["text_before"] = self.truncate_smart(
            stm.current_context.text_window.text_before_cursor.paragraph,
            max_chars=500,
            keep="end"  # Keep text closest to cursor
        )

        text_after = stm.current_context.text_window.text_after_cursor.immediate
        if text_after:
            context["text_after"] = text_after[:100]

        # ─────────────────────────────────────────────────────────────
        # INCLUDE IF RELEVANT (Conditional)
        # ─────────────────────────────────────────────────────────────

        # Clipboard - only if high relevance
        for clip in stm.cross_app_context.clipboard_history[:1]:
            if clip.get("relevance_score", 0) >= 0.6:
                context["clipboard"] = clip["content_preview"][:100]
                break

        # Cross-app context - only if recent
        recent_apps = [
            a for a in stm.cross_app_context.recent_apps
            if a.get("minutes_ago", 99) < 5
        ]
        if recent_apps:
            context["recent_context"] = [
                f"{a['app']}: {a['context_summary']}"
                for a in recent_apps[:2]
            ]

        # Mood - include as guidance
        context["mood"] = stm.mood_estimation.current_mood

        # User style preferences
        context["style"] = {
            "formality": stm.user_profile_snapshot.preferences.formality_default,
            "verbosity": stm.user_profile_snapshot.preferences.verbosity
        }

        # ─────────────────────────────────────────────────────────────
        # TOKEN BUDGET MANAGEMENT
        # ─────────────────────────────────────────────────────────────
        total_tokens = self.estimate_tokens(context)
        if total_tokens > 1500:
            # Trim least important parts
            if "recent_context" in context:
                context["recent_context"] = context["recent_context"][:1]
            if total_tokens > 1500:
                context["text_before"] = context["text_before"][-300:]

        return context

    def truncate_smart(self, text: str, max_chars: int, keep: str = "end") -> str:
        """Truncate text, keeping the most relevant part."""
        if len(text) <= max_chars:
            return text

        if keep == "end":
            return "..." + text[-(max_chars-3):]
        elif keep == "start":
            return text[:max_chars-3] + "..."
        else:  # middle
            half = (max_chars - 6) // 2
            return text[:half] + " ... " + text[-half:]
```

---

## Request Deduplication & Caching

```python
class SuggestionCache:
    """Avoid redundant LLM calls for similar contexts."""

    def __init__(self, max_size=50, ttl_seconds=30):
        self.cache = OrderedDict()
        self.max_size = max_size
        self.ttl_seconds = ttl_seconds

    def get_cache_key(self, context: dict) -> str:
        """Generate cache key from context."""
        # Key is based on: last 100 chars of text + app type
        text = context.get("text_before", "")[-100:]
        app = context.get("app_type", "")
        return hashlib.md5(f"{app}:{text}".encode()).hexdigest()

    def get(self, context: dict) -> Optional[Suggestion]:
        """Check if we have a cached suggestion for this context."""
        key = self.get_cache_key(context)

        if key in self.cache:
            entry = self.cache[key]
            if (now() - entry["time"]).seconds < self.ttl_seconds:
                return entry["suggestion"]
            else:
                del self.cache[key]  # Expired

        return None

    def set(self, context: dict, suggestion: Suggestion):
        """Cache a suggestion."""
        key = self.get_cache_key(context)
        self.cache[key] = {
            "suggestion": suggestion,
            "time": now()
        }

        # Evict oldest if over capacity
        while len(self.cache) > self.max_size:
            self.cache.popitem(last=False)
```

---

## Summary: LLM Calling Rules

| Scenario                     | Action                  |
| ---------------------------- | ----------------------- |
| User typing continuously     | ❌ DO NOT call          |
| User pauses (adaptive delay) | ✅ CALL (debounced)     |
| Sentence ends                | ✅ CALL (shorter delay) |
| Paragraph break              | ✅ CALL (short delay)   |
| Just dismissed suggestion    | ⏳ WAIT (cooldown)      |
| Already pending request      | ❌ SKIP                 |
| Low context (<10 chars)      | ❌ SKIP                 |
| Cache hit                    | ✅ USE CACHED           |
| User started typing again    | ❌ CANCEL pending       |

---

## Summary: Suggestion Quality Formula (Updated)

```
QUALITY =
    Context(STM)
  × Mood(alignment)
  × Style(match)
  × Timing(adaptive)
  × Personalization(LTM)
  × Feedback(learning)
  × Clipboard(relevance)
```

Each component contributes. The system is **completely automatic** — users never need to manually trigger suggestions.

---

_Last Updated: 2026-01-23 05:55 IST_
