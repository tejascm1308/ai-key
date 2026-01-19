# AI Keyboard — Video Presentation Script

## Slides 1-9 | 4 Team Members

---

## 🎥 Format

- **Slides:** 1 to 9
- **Duration:** ~10 minutes
- **Speakers:** 4 members

---

## Speaker Assignment

| Member       | Slides         | Topics                                  |
| ------------ | -------------- | --------------------------------------- |
| **Member 1** | 1, 2           | Problem Statement, Proposed Solution    |
| **Member 2** | 3, 4           | System Architecture, Speed Optimization |
| **Member 3** | 5, 6           | Context Detection, Voice Integration    |
| **Member 4** | 7, 8, 9 + Demo | Personalization, Security, MCP + Demo   |

---

# MEMBER 1: Slides 1 & 2

**Duration:** ~2.5 minutes

---

## SLIDE 1: Problem Statement

> "Hello everyone! We're presenting **AI Keyboard — Making Every Keystroke Intelligent**."

> "I'm [Name], with me are [Name 2], [Name 3], and [Name 4]."

> "Here's the problem. We type every day — emails, messages, code. But our keyboard is completely passive. It doesn't know if you're in Gmail or Slack. It doesn't help you adjust tone between a formal email and a casual chat."

> "And phrases you type daily — 'Best regards', 'Sounds good' — you have to type them every single time."

> "Existing tools don't solve this. Grammarly fixes grammar but doesn't understand context. Copilot works only in code editors. ChatGPT requires copy-pasting back and forth."

> "The gap: **No AI works right where you type, across all apps.** That's what we built."

---

## SLIDE 2: Proposed Solution

> "**AI Keyboard** is an intelligent middleware between your keyboard and applications."

> "It has four capabilities:"

> "**Context-Aware Suggestions** — It detects which app you're in. Gmail gets professional suggestions. Slack gets casual, emoji-friendly text. Automatic."

> "**Voice-to-Formatted-Text** — Speak naturally and get properly formatted output, not just raw transcription."

> "**Automatic Personalization** — It learns your writing style over time. No configuration needed."

> "**Universal Compatibility** — Works in any browser or desktop app."

> "Now [Member 2] will explain the architecture."

---

# MEMBER 2: Slides 3 & 4

**Duration:** ~2.5 minutes

---

## SLIDE 3: System Architecture

> "Thanks [Member 1]. I'm [Name]."

> "Our architecture follows one principle: **One brain, many clients.**"

> "The **Core AI Engine** runs locally as a background service. It contains all intelligence — context detection, suggestion generation, personalization."

> "Two clients connect to this brain:"

> "**Browser Extension** — Watches typing in all websites. Gmail, Slack, LinkedIn — everything."

> "**Desktop App** — Captures typing in native apps like Word, VS Code, Notepad."

> "Both communicate via WebSocket for real-time, instant responses."

> "Key benefit: All intelligence is centralized. Updates to the brain benefit both clients automatically."

---

## SLIDE 4: Speed Optimization

> "Speed is critical. If suggestions take 2-3 seconds, they're useless."

> "We built a **three-layer response system**:"

> "**Layer 1: Cache** — 0-10ms. Pre-stored common phrases. Instant retrieval."

> "**Layer 2: Local LLM** — 10-100ms. TinyLlama running on your machine via Ollama. No network latency."

> "**Layer 3: Cloud LLM** — 100-500ms. GPT-4 for complex requests. Streamed for responsiveness."

> "Result: **70% of requests served from Layer 1 or 2** — nearly instant."

> "[Member 3] will cover context detection and voice."

---

# MEMBER 3: Slides 5 & 6

**Duration:** ~2.5 minutes

---

## SLIDE 5: Context Detection

> "Thanks [Member 2]. I'm [Name]."

> "How does AI Keyboard know which app you're in?"

> "**In browsers:** We analyze the URL. 'mail.google.com' = Gmail. 'slack.com' = Slack. We also check input field types."

> "**In desktop apps:** We read window title and process name."

> "Based on detection, behavior adapts automatically:"

> "Gmail → Formal: 'I wanted to follow up regarding...'"

> "Slack → Casual: 'Hey! Quick question...' with emojis"

> "VS Code → Code-aware completions"

> "Context switching happens in under **50 milliseconds** — faster than you can notice."

---

## SLIDE 6: Voice Integration

> "We support voice input with a twist."

> "We use **Whisper** — OpenAI's speech-to-text model — running locally via faster-whisper. Completely private, works offline."

> "But we don't just transcribe. We do **intent enhancement**."

> "You say: 'email john about pushing deadline to friday'"

> "Normal transcription gives you exactly those words."

> "Our output: 'Hi John, I wanted to check if we could extend the deadline to Friday. Let me know your thoughts. Best regards.'"

> "We understood the intent and formatted it properly."

> "[Member 4] will cover personalization, security, and MCP."

---

# MEMBER 4: Slides 7, 8, 9 + Demo

**Duration:** ~2.5 minutes

---

## SLIDE 7: Personalization Engine

> "Thanks [Member 3]. I'm [Name]."

> "AI Keyboard learns your style through three layers:"

> "**Explicit:** Optional — you tell us your role and tone preference during setup."

> "**Observed:** We notice patterns. 'User starts with Hi, not Dear. Uses sounds good often. Formal in Gmail, casual in Slack.'"

> "**Feedback:** Accept signals reinforce patterns. Reject signals teach us to avoid."

> "Importantly: We store **patterns**, not content. We remember 'user prefers informal greetings' — not your actual emails."

---

## SLIDE 8: Security Architecture

> "Privacy is our priority. Our principle: **Use content, don't store content.**"

> "We read text temporarily in memory, extract patterns, generate suggestions, then immediately discard the content. Your actual messages are never saved."

> "Automatic protection: Banking sites, password managers, and login pages are auto-blocked."

> "User controls: Pause mode with Ctrl+Shift+P. Custom blocklist. One-click data wipe."

---

## SLIDE 9: MCP Integration

> "Now let me explain **MCP — Model Context Protocol**."

> "MCP is a **standard protocol for AI tools to communicate**. Think of it like how USB standardized device connections — MCP standardizes how AI systems talk to each other."

> "We built AI Keyboard as an **MCP server** that exposes four tools:"

> "**detect_context** — Returns current application and context"

> "**complete_intent** — Returns text suggestions"

> "**enhance_text** — Returns improved text"

> "**transcribe_voice** — Returns formatted speech-to-text"

> "Why MCP? First, it's the hackathon requirement for Frai ecosystem integration. Second, it means our AI Keyboard works with any MCP-compatible client — Frai Platform, Claude Desktop, or future tools."

---

## DEMO

> "Quick demo."

**[Share screen: Gmail]**

> "I'm in Gmail. Typing: 'Hi John, I wanted to follow up on our'"

> "Suggestion appears — professional completion detected from Gmail context."

**[Accept]**

> "Inserted. Now switching to Slack."

**[Type in Slack: "Hey team, quick update on"]**

> "Casual suggestion with emoji. Same AI, different context."

> "That's **AI Keyboard — making every keystroke intelligent.**"

> "Thank you! Questions?"

---

# ⏱️ Timing

| Member    | Slides         | Duration    |
| --------- | -------------- | ----------- |
| Member 1  | 1, 2           | 2.5 min     |
| Member 2  | 3, 4           | 2.5 min     |
| Member 3  | 5, 6           | 2.5 min     |
| Member 4  | 7, 8, 9 + Demo | 2.5 min     |
| **Total** |                | **~10 min** |
