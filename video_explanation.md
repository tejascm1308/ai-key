# AI Keyboard — Video Presentation Script

## Slides 1-9 | 4 Team Members

---

## Speaker Assignment

| Member       | Slides         | Duration |
| ------------ | -------------- | -------- |
| **Member 1** | 1, 2           | 2.5 min  |
| **Member 2** | 3, 4           | 2.5 min  |
| **Member 3** | 5, 6           | 2.5 min  |
| **Member 4** | 7, 8, 9 + Demo | 2.5 min  |

**Total: ~10-11 minutes**

---

# MEMBER 1: Slides 1 & 2

---

## SLIDE 1: Problem Statement

> "Hello everyone! We're presenting **AI Keyboard — Making Every Keystroke Intelligent**."

> "I'm [Name], with me are [Name 2], [Name 3], and [Name 4]."

> "Here's the problem. We type every day — emails, messages, code. But our keyboard is completely passive."

> "When you're writing a formal email to your boss, your keyboard doesn't help. When you switch to WhatsApp, it doesn't adjust to a casual tone. And phrases you type daily — 'Best regards', 'Sounds good' — you type them every single time. Your keyboard never learns."

> "Existing tools don't solve this. Grammarly fixes grammar but doesn't understand context. Copilot works only in code editors. ChatGPT requires copy-pasting, which breaks your flow."

> "The gap: **No AI helps you right where you type, across all apps.** That's what we built."

---

## SLIDE 2: Proposed Solution

> "**AI Keyboard** is an intelligent layer between your keyboard and applications."

> "It has four capabilities:"

> "**Context-Aware Suggestions** — It detects which app you're in. Gmail gets professional suggestions. Slack gets casual text with emojis. All automatic."

> "**Voice-to-Formatted-Text** — Speak 'email john about deadline friday' and get a properly formatted email — not just those raw words."

> "**Automatic Personalization** — It learns your style. After a few days, it knows you prefer 'Hi' over 'Dear', without any configuration."

> "**Universal Compatibility** — Works in any browser or desktop app. One tool for everything."

> "Now [Member 2] will explain how we built this."

---

# MEMBER 2: Slides 3 & 4

---

## SLIDE 3: System Architecture

> "Thanks [Member 1]. I'm [Name]."

> "Our architecture: **One brain, many clients.**"

> "The **Core AI Engine** runs locally on your computer. It contains all the intelligence — context detection, suggestions, personalization."

> "Two clients connect to this brain:"

> "**Browser Extension** — Captures typing in all websites. Gmail, Slack, LinkedIn."

> "**Desktop App** — Captures typing in native apps like Word, VS Code, Notepad."

> "Both communicate via WebSocket for real-time responses. All intelligence is centralized, so improvements benefit both clients automatically."

---

## SLIDE 4: Speed Optimization

> "Speed is critical. If suggestions take 2-3 seconds, they're useless."

> "We built a **three-layer system**:"

> "**Layer 1: Cache** — 0-10ms. Your common phrases stored locally. Instant retrieval."

> "**Layer 2: Local AI** — 10-100ms. Small model running on your machine via Ollama. No network delay."

> "**Layer 3: Cloud AI** — 100-500ms. GPT-4 for complex requests. Streamed for responsiveness."

> "Result: **70% of requests** come from Layer 1 or 2 — nearly instant."

> "[Member 3] will cover context detection and voice."

---

# MEMBER 3: Slides 5 & 6

---

## SLIDE 5: Context Detection

> "Thanks [Member 2]. I'm [Name]."

> "How does AI Keyboard know which app you're in?"

> "**In browsers**, we analyze the URL. 'mail.google.com' = Gmail. 'slack.com' = Slack."

> "**In desktop apps**, we read window title and process name."

> "Based on this, suggestions adapt automatically:"

> "Gmail → Formal: 'I wanted to follow up regarding...'"

> "Slack → Casual: 'Hey! Quick question...' with emojis"

> "VS Code → Code-aware completions"

> "Context switching happens in under **50 milliseconds**."

---

## SLIDE 6: Voice Integration

> "We support voice input using **Whisper** — OpenAI's speech-to-text model running locally. Private, works offline."

> "But we go beyond transcription. We do **intent enhancement**."

> "You say: 'email john about deadline friday'"

> "Normal transcription gives exactly those words."

> "Our output: 'Hi John, I wanted to check if we could extend the deadline to Friday. Let me know. Best regards.'"

> "We understood the intent and formatted it as a proper email."

> "[Member 4] will cover personalization, security, and show a demo."

---

# MEMBER 4: Slides 7, 8, 9 + Demo

---

## SLIDE 7: Personalization Engine

> "Thanks [Member 3]. I'm [Name]."

> "AI Keyboard learns your style through three layers:"

> "**Explicit** — Optional setup where you tell us your role and tone preference."

> "**Observed** — We notice patterns. 'Uses Hi not Dear. Formal in Gmail, casual in Slack.'"

> "**Feedback** — Accepted suggestions reinforce patterns. Rejected ones teach us to avoid."

> "Important: We store **patterns**, not content. We remember 'prefers informal greetings' — not your actual emails."

---

## SLIDE 8: Security Architecture

> "Privacy is our priority. Our principle: **Use content, don't store content.**"

> "We read text temporarily in memory, extract patterns, generate suggestions, then immediately discard your content. Nothing is saved."

> "Banking sites, password managers, and login pages are auto-blocked."

> "User controls: Pause with Ctrl+Shift+P. Custom blocklist. One-click data wipe."

---

## SLIDE 9: MCP Integration — Plugin for Frai

> "Now, this is important — **AI Keyboard is built as a plugin for the Frai ecosystem.**"

> "Frai uses something called **MCP — Model Context Protocol**. Think of MCP like USB — a standard way for AI tools to connect and communicate with each other."

> "We designed AI Keyboard as an **MCP server** that exposes four tools any Frai client can use:"

> "**detect_context** — Tells Frai which app the user is currently in"

> "**complete_intent** — Provides intelligent text suggestions"

> "**enhance_text** — Improves selected text on demand"

> "**transcribe_voice** — Converts speech to properly formatted text"

> "Because we built it as an MCP plugin, AI Keyboard integrates seamlessly with the Frai platform. Any Frai user can access intelligent keyboard assistance directly through the ecosystem."

> "And the beauty of MCP is extensibility — the same plugin also works with Claude Desktop and any future MCP-compatible platform."

---

## DEMO

> "Quick demo."

**[Share screen: Gmail]**

> "In Gmail, I type: 'Hi John, I wanted to follow up on our'"

> "Suggestion appears — professional completion. I'll accept with Tab."

**[Switch to Slack]**

> "In Slack: 'Hey team, quick update on'"

> "Casual suggestion with emoji. Same AI, different context."

> "**AI Keyboard — making every keystroke intelligent.**"

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
