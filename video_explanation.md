# AI Keyboard — Video Presentation Script

## Recording: Screen Share PPT + Audio Explanation

---

## 🎥 Setup

- **Format:** Screen share PPT slides with voice-over
- **Duration:** 10-12 minutes
- **Speakers:** 4 members taking turns
- **Style:** Conversational, engaging, easy to understand

---

## Slide Order & Speaker Assignment

| Slide # | Title                    | Speaker  |
| ------- | ------------------------ | -------- |
| 1       | Problem Statement        | Member 1 |
| 2       | Proposed Solution        | Member 1 |
| 3       | System Architecture      | Member 1 |
| 4       | Speed Optimization       | Member 2 |
| 5       | Context Detection        | Member 2 |
| 6       | Voice Integration        | Member 2 |
| 7       | Personalization Engine   | Member 3 |
| 8       | Security Architecture    | Member 3 |
| 9       | MCP Integration          | Member 3 |
| 10      | Technology Stack         | Member 4 |
| 11      | Implementation Status    | Member 4 |
| 12      | Value Proposition + Demo | Member 4 |

---

# MEMBER 1: Slides 1, 2, 3

**Duration:** ~3 minutes

---

## SLIDE 1: Problem Statement

> "Hello everyone! We are Team [Name] presenting **AI Keyboard — Making Every Keystroke Intelligent**."

> "I'm [Name] and with me are [Name 2], [Name 3], and [Name 4]."

> "Let me start with a simple question. What does your keyboard understand about what you're doing right now? The answer is: nothing."

> "Your keyboard is a passive device. It doesn't know if you're writing a formal email to your boss or a casual message to a friend. It doesn't know if you're in Gmail or Slack or VS Code."

> "And this creates real problems. When you switch from drafting a professional email to chatting with colleagues, your keyboard doesn't help you adjust your tone. When you type the same phrases every day — 'Best regards', 'Sounds good', 'Looking forward to your reply' — you have to type them out every single time."

> "Now, tools like Grammarly help with grammar, but only in some apps. GitHub Copilot helps with code, but only in code editors. ChatGPT requires you to copy-paste text back and forth, which completely breaks your flow."

> "The gap is clear: there's no AI that works right where you type, across all your applications. That's the opportunity we saw."

---

## SLIDE 2: Proposed Solution

> "So what is AI Keyboard?"

> "AI Keyboard is an intelligent middleware layer that sits between your keyboard input and the applications you use. It intercepts what you type, understands the context, and provides relevant suggestions."

> "It has four core capabilities:"

> "First, **context-aware suggestions**. It detects which app you're in and adapts. Gmail gets professional suggestions. Slack gets casual, emoji-friendly suggestions."

> "Second, **voice-to-formatted-text**. Speak naturally — 'email john about the deadline' — and get a properly formatted email, not just raw transcription."

> "Third, **automatic personalization**. It learns your writing patterns over time. No configuration needed."

> "Fourth, **universal compatibility**. It works in browsers through our extension and in desktop apps through our desktop client. One solution, all apps covered."

---

## SLIDE 3: System Architecture

> "Now let me explain how this is built."

> "Our architecture follows a simple principle: **one brain, many clients**."

> "At the center is our **Core AI Engine**. This runs locally on your computer as a background service. It contains all the intelligence — the MCP server with our tools, the LLM router, the personalization engine, and the cache layer."

> "Connected to this brain are two clients:"

> "The **Browser Extension** captures everything you type in Chrome or Firefox — all websites, all web apps."

> "The **Desktop App** captures typing in native applications — Word, Notepad, VS Code, any Windows or Mac app."

> "Both clients communicate with the core engine via WebSocket — that's a real-time protocol that gives us sub-second response times."

> "The beauty of this architecture is that all intelligence is centralized. We write the AI logic once, and both clients benefit."

> "Now I'll hand over to [Member 2] who will explain our speed optimization."

---

# MEMBER 2: Slides 4, 5, 6

**Duration:** ~3 minutes

---

## SLIDE 4: Speed Optimization

> "Thanks [Member 1]. I'm [Name]."

> "For a typing assistant, speed is critical. If suggestions take 2-3 seconds to appear, you've already moved on. The suggestion becomes useless."

> "So we designed a **three-layer response system** to achieve sub-100 millisecond response times."

> "**Layer 1 is Cache** — response time 0 to 10 milliseconds. This is essentially instant. We pre-store commonly used phrases locally. When you start typing 'Best regards' or 'Sounds good', we retrieve it from cache immediately."

> "**Layer 2 is Local LLM** — response time 10 to 100 milliseconds. We use Ollama running TinyLlama or Phi-3 on your computer. No network latency. This handles most simple completions."

> "**Layer 3 is Cloud LLM** — response time 100 to 500 milliseconds. For complex requests like full rewrites, we call GPT-4 or Claude. We use streaming responses so text appears progressively."

> "Our performance target is that 70% of requests are served from Layer 1 or 2 — meaning most suggestions are nearly instant."

---

## SLIDE 5: Context Detection

> "Now, how does AI Keyboard know what app you're in and adapt its behavior?"

> "We call this **application-specific adaptation**."

> "The system detects context through multiple sources. For browsers, we analyze URL patterns — 'mail.google.com' tells us Gmail. We also look at input field attributes — is this a compose window or a search box?"

> "For desktop apps, we read the window title and process name."

> "Based on this detection, behavior changes automatically. Let me give examples:"

> "When you're in Gmail Compose, we detect formal context and suggest professional language."

> "When you're in Slack, we detect casual context and suggest brief, emoji-friendly text."

> "When you're in VS Code or any IDE, we detect technical context and provide code-aware completions."

> "The switching latency is under 50 milliseconds. When you Alt+Tab between apps, by the time the window loads, our AI has already adjusted."

---

## SLIDE 6: Voice Integration

> "We also support voice input — but not just basic transcription."

> "Our pipeline works like this: Audio input goes through Whisper — OpenAI's speech-to-text model which we run locally using the faster-whisper library. This gives us raw transcription."

> "But we don't stop there. The raw text then goes through **intent enhancement** using our LLM."

> "For example, you say: 'email john about deadline friday'"

> "Normal transcription would give you exactly that text. But our system understands the intent and produces:"

> "'Hi John, Could we extend the deadline to Friday? Let me know if that works.'"

> "The key point is this runs locally. It's free, it's private, and it works offline."

> "Now [Member 3] will cover personalization and security."

---

# MEMBER 3: Slides 7, 8, 9

**Duration:** ~3 minutes

---

## SLIDE 7: Personalization Engine

> "Thanks [Member 2]. I'm [Name]."

> "AI Keyboard learns your writing style automatically. No configuration dialogs to fill out."

> "We have a **three-layer learning system**:"

> "**Layer 1 is Explicit** — during onboarding, you can optionally tell us your role — developer, writer, manager — and your preferred tone — direct, friendly, formal."

> "**Layer 2 is Observed** — we learn patterns from your usage. We notice that you start emails with 'Hi [Name]' instead of 'Dear Sir'. We notice you use 'sounds good' a lot in Slack. We notice you're more formal in Gmail than in Teams."

> "**Layer 3 is Feedback** — we learn from your accept and reject signals. When you accept a suggestion, that pattern is reinforced. When you reject, we learn not to suggest that again."

> "Importantly, we store **patterns**, not content. We remember 'user prefers informal greetings' — we don't store 'Hi John, about the meeting yesterday...'."

---

## SLIDE 8: Security Architecture

> "This brings me to privacy — which is our top priority."

> "Our core principle is: **Use content, don't store content.**"

> "When you type an email, here's what happens. We read your text temporarily in memory to understand context. We extract patterns — like 'formal tone detected'. We generate suggestions. Then the actual content is discarded — never saved to disk, never sent to any server."

> "We also have data classification. Sensitive fields like passwords and OTPs are never captured — we detect and exclude them automatically. Email and chat content is ephemeral — used in memory only. Only user preferences are stored persistently, and those are encrypted locally."

> "Banking websites, password managers, and login pages are auto-blocked. AI Keyboard completely disables itself on sensitive sites."

> "And users have manual controls too. Pause mode with Ctrl+Shift+P instantly disables AI Keyboard. There's also a blocklist where you can add specific apps or websites."

---

## SLIDE 9: MCP Integration

> "Now let me explain why we built this as an MCP server."

> "MCP stands for **Model Context Protocol**. It's a standardized way for AI tools to communicate and share context."

> "Think of it like USB. Before USB, every device had a different connector. USB standardized everything. MCP is doing the same for AI tools."

> "We expose four tools through our MCP server:"

> "**detect_context** — returns information about the current application and context."

> "**complete_intent** — returns text suggestions based on input."

> "**enhance_text** — returns improved versions of selected text."

> "**transcribe_voice** — returns formatted text from speech input."

> "Any MCP-compatible client can use these tools. This includes the Frai Platform — which is the hackathon requirement — as well as Claude Desktop and any future MCP-compatible applications."

> "Now [Member 4] will cover our tech stack and show a demo."

---

# MEMBER 4: Slides 10, 11, 12 + Demo

**Duration:** ~3 minutes

---

## SLIDE 10: Technology Stack

> "Thanks [Member 3]. I'm [Name]."

> "Quick overview of our implementation:"

> "The Core Server is built with Python and FastAPI, using the MCP SDK."

> "For Local LLM, we use Ollama with TinyLlama or Phi-3 models."

> "For Cloud LLM, we integrate with OpenAI GPT-4 API — this is optional and only used for complex requests."

> "Voice processing uses local Whisper through the faster-whisper library."

> "The Browser Extension is JavaScript using Chrome Extension APIs."

> "The Desktop App is Python with OS-level keyboard hooks using pynput and pywinauto."

> "Communication between clients and server is via WebSocket."

---

## SLIDE 11: Implementation Status

> "What have we actually completed for this hackathon?"

> "Core MCP Server with all four tools — complete."

> "Chrome Browser Extension — complete."

> "Three-layer speed system with cache, local LLM, and cloud LLM — complete."

> "Context detection module — complete."

> "Personalization engine with observed and feedback layers — complete."

> "For our roadmap, Phase 1 after the hackathon is the full Desktop App for all native applications. Phase 2 is advanced voice features. Phase 3 is enterprise features."

---

## SLIDE 12: Value Proposition

> "Let me summarize what makes AI Keyboard special."

> "**One — Universal.** Two clients cover all applications. Browser extension for web, desktop app for native apps."

> "**Two — Contextual.** Automatically adapts to each application. No manual switching."

> "**Three — Fast.** Sub-100 millisecond response time for most suggestions."

> "**Four — Personal.** Learns your writing style silently over time."

> "**Five — Private.** Local-first processing. Content never stored."

> "**Six — Extensible.** MCP-compatible, so it plugs into the Frai ecosystem and other AI platforms."

---

## DEMO (Screen Share)

> "Now let me quickly show you AI Keyboard in action."

**[Switch screen share to browser with Gmail]**

> "I'm in Gmail, composing a new email. Watch the bottom of the screen."

> "I'll type: 'Hi John, I wanted to follow up on our discussion about'"

**[Tooltip appears with suggestion]**

> "You can see a suggestion appeared. It detected I'm in Gmail and suggested a professional completion. I can press Tab to accept or Escape to dismiss."

**[Accept]**

> "Text inserted. Now let me switch to Slack."

**[Switch to Slack]**

> "I'll type: 'Hey team, quick update'"

**[Suggestion appears — more casual with emoji]**

> "Notice the suggestion is more casual and includes an emoji. Same AI, but it adapted to the chat context automatically."

> "That's AI Keyboard — making every keystroke intelligent."

---

## CLOSING

> "Thank you for watching our presentation. AI Keyboard puts AI assistance right at the point of input, works across all applications, and respects your privacy."

> "We're happy to answer any questions!"

---

# 📋 Summary

| Slide | Title                  | Speaker  | Duration |
| ----- | ---------------------- | -------- | -------- |
| 1     | Problem Statement      | Member 1 | 1 min    |
| 2     | Proposed Solution      | Member 1 | 1 min    |
| 3     | System Architecture    | Member 1 | 1 min    |
| 4     | Speed Optimization     | Member 2 | 1 min    |
| 5     | Context Detection      | Member 2 | 1 min    |
| 6     | Voice Integration      | Member 2 | 1 min    |
| 7     | Personalization Engine | Member 3 | 1 min    |
| 8     | Security Architecture  | Member 3 | 1 min    |
| 9     | MCP Integration        | Member 3 | 1 min    |
| 10    | Technology Stack       | Member 4 | 30 sec   |
| 11    | Implementation Status  | Member 4 | 30 sec   |
| 12    | Value + Demo           | Member 4 | 2 min    |

**Total: ~12 minutes**

---

# 🎬 Recording Tips

1. Each member records their slides separately
2. Practice reading the script aloud 2-3 times before recording
3. Speak at a natural pace — not too fast
4. Advance slide when you start talking about that slide's content
5. Demo: Practice 3-4 times to ensure it works smoothly
6. Have backup plan for demo: If it fails, describe what would happen
