# AI Keyboard — Video Presentation Script

## Slides 1-9 Only | 4 Team Members

---

## 🎥 Format

- **Slides covered:** 1 to 9 only
- **Duration:** 10-12 minutes
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

**Duration:** ~3 minutes

---

## SLIDE 1: Problem Statement

> "Hello everyone! Welcome to our presentation on **AI Keyboard — Making Every Keystroke Intelligent**."

> "I'm [Name], and with me are [Name 2], [Name 3], and [Name 4]."

> "Let me start with a simple observation. We all type every day — emails, messages, documents, code. But here's something you might not have realized:"

> "Your keyboard doesn't understand anything. It's completely passive."

> "What do I mean? When you're writing a formal email to your manager, your keyboard doesn't know. When you switch to WhatsApp to chat with a friend, your keyboard doesn't adjust. It shows whatever you type, nothing more."

> "And there's another problem. Think about phrases you type every single day — 'Best regards', 'Looking forward to hearing from you', 'Sounds good'. You type these again and again. Your keyboard never learns that you use these often."

> "Now, what about existing tools?"

> "**Grammarly** — it only fixes spelling and grammar. It doesn't help you write faster or understand which app you're in."

> "**GitHub Copilot** — it only works in code editors. Can't help with emails."

> "**ChatGPT** — you have to copy your text, open ChatGPT, paste it, type a command, wait, copy the result, and paste it back. That breaks your flow completely."

> "So here's the gap: **There's no AI that helps you right where you type, across all your apps.** That's what we built."

---

## SLIDE 2: Proposed Solution

> "Our solution is called **AI Keyboard**."

> "Let me explain it simply. Normally, when you type, the keys go directly to the app. With AI Keyboard, we add an intelligent layer in between."

> "This layer does four things:"

> "**First: Context-Aware Suggestions**"

> "What does 'context-aware' mean? It means the AI understands WHERE you're typing. Gmail gets professional suggestions. Slack gets casual, friendly suggestions. It figures this out automatically."

> "**Second: Voice-to-Formatted-Text**"

> "You can speak instead of typing. But unlike normal voice assistants that just write your exact words, our AI understands what you're TRYING to do."

> "Say 'email john about deadline friday' — you get a proper formatted email, not just those five words."

> "**Third: Automatic Personalization**"

> "The AI learns YOUR writing style. After a few days, it knows: 'This person starts emails with Hi, not Dear. This person uses this phrase a lot.' No configuration needed."

> "**Fourth: Universal Compatibility**"

> "It works everywhere — any website in your browser, any app on your computer. One tool for everything."

> "Now [Member 2] will explain how we built this."

---

# MEMBER 2: Slides 3 & 4

**Duration:** ~3 minutes

---

## SLIDE 3: System Architecture

> "Thanks [Member 1]. I'm [Name]."

> "Let me explain how AI Keyboard is built. I'll keep it simple."

> "Our system has three main parts."

> "**Part 1: The Brain — Core AI Engine**"

> "This is the intelligent center that runs on your own computer as a background program. Think of it as a smart assistant sitting behind your keyboard."

> "It contains all the AI logic — understanding context, generating suggestions, learning your style."

> "**Part 2: Browser Extension**"

> "What's a browser extension? It's a small add-on you install in Chrome or Firefox — like ad blockers or password managers."

> "Our extension watches what you type in any website — Gmail, Slack, LinkedIn, anything — and shows suggestions."

> "**Part 3: Desktop App**"

> "Browser extensions only work in browsers. For apps like Word, Notepad, or VS Code, we have a desktop app that captures typing at the operating system level."

> "**How do they communicate?**"

> "Both clients connect to the brain using **WebSocket**. This is technology that allows instant, real-time communication — like a phone call, not like email."

> "Key insight: **One brain, two clients.** All intelligence is centralized. Both clients just send typing data and receive suggestions."

---

## SLIDE 4: Speed Optimization

> "Speed is critical for a typing assistant. If suggestions take 3 seconds to appear, you've already moved on. They become useless."

> "So we created a **three-layer speed system**."

> "**Layer 1: Cache — 0 to 10 milliseconds**"

> "What's a cache? It's a memory of things you use often. We store your frequently used phrases locally."

> "When you start typing 'Best regards' or 'Sounds good', we retrieve it instantly — faster than a blink of your eye."

> "**Layer 2: Local LLM — 10 to 100 milliseconds**"

> "What's an LLM? It stands for Large Language Model — the AI technology behind ChatGPT."

> "We run a small LLM called TinyLlama directly on your computer using software called Ollama. No internet needed. Fast response."

> "**Layer 3: Cloud LLM — 100 to 500 milliseconds**"

> "For complex tasks like 'rewrite this paragraph professionally', we call powerful AI servers like GPT-4. We stream the response so it feels fast."

> "**Result:** About 70% of suggestions come from Layer 1 or 2 — nearly instant."

> "Now [Member 3] will explain context detection and voice."

---

# MEMBER 3: Slides 5 & 6

**Duration:** ~3 minutes

---

## SLIDE 5: Context Detection

> "Thanks [Member 2]. I'm [Name]."

> "How does AI Keyboard know which app you're in? That's what we call **context detection**."

> "**In browsers**, we look at the URL — the website address. 'mail.google.com' tells us Gmail. 'slack.com' tells us Slack."

> "We also analyze the page — are you in a compose window? A search box? A comment field?"

> "**In desktop apps**, we look at the window title and program name. 'Microsoft Word' tells us Word. 'Visual Studio Code' tells us coding."

> "**What happens after detection?**"

> "Suggestions automatically adapt."

> "Gmail gets formal suggestions: 'I wanted to follow up regarding...'"

> "Slack gets casual suggestions: 'Hey! Quick question...' maybe with emojis."

> "VS Code gets code completions, not chat text."

> "**How fast is switching?**"

> "Under 50 milliseconds. When you switch apps, by the time the window appears, our AI has already adjusted. You won't notice."

---

## SLIDE 6: Voice Integration

> "We also support voice input."

> "**What is Whisper?**"

> "Whisper is an AI model from OpenAI that converts speech to text. We use 'faster-whisper' which runs completely on your computer — private, free, works offline."

> "**Normal voice typing vs. our approach:**"

> "Normal voice assistants just write your exact words."

> "You say: 'email john about pushing the deadline to friday'"

> "Normal result: 'email john about pushing the deadline to friday' — exactly those words."

> "**Our approach — Intent Enhancement:**"

> "We understand what you're TRYING to do. The result becomes:"

> "'Hi John, I wanted to check if we could extend the deadline to Friday. Let me know your thoughts. Best regards.'"

> "A proper, formatted email — not raw words."

> "Now [Member 4] will cover personalization, security, and show a demo."

---

# MEMBER 4: Slides 7, 8, 9 + Demo

**Duration:** ~3 minutes

---

## SLIDE 7: Personalization Engine

> "Thanks [Member 3]. I'm [Name]."

> "AI Keyboard learns your writing style automatically. Let me explain how."

> "**Layer 1: Explicit**"

> "During setup, you can optionally tell us your job role and preferred tone. This is optional."

> "**Layer 2: Observed**"

> "We watch and learn patterns. 'This person starts emails with Hi, not Dear.' 'This person uses sounds good a lot.' 'This person never uses emojis in work emails.'"

> "**Layer 3: Feedback**"

> "When you accept a suggestion, that pattern is reinforced. When you reject, we learn to avoid that."

> "**Important distinction:** We learn PATTERNS, not CONTENT."

> "We save: 'User prefers informal greetings.'"

> "We DON'T save: 'Hi John, about the budget for next quarter...'"

> "Your actual messages are never stored."

---

## SLIDE 8: Security Architecture

> "Privacy is our top priority."

> "**Our principle: Use content, don't store content.**"

> "Step 1: We read your text temporarily — only in memory."

> "Step 2: We extract patterns — 'user prefers formal tone.'"

> "Step 3: We suggest completions."

> "Step 4: Your actual text content is deleted immediately. Never saved."

> "**Automatic protection:**"

> "Banking websites, password managers, login pages — AI Keyboard completely disables itself."

> "**User controls:**"

> "Pause mode: Press Ctrl+Shift+P to instantly pause."

> "Blocklist: Add specific apps or sites."

> "Data wipe: One-click delete all learned data."

---

## SLIDE 9: MCP Integration

> "Finally, MCP — Model Context Protocol."

> "**Simple explanation:** Think about phone chargers. Before USB-C, every phone had a different charger. USB-C standardized everything."

> "**MCP is the USB-C for AI tools.** It's a standard way for AI systems to communicate."

> "We expose four tools through MCP:"

> "**detect_context** — What app is user in?"

> "**complete_intent** — Suggest text completion."

> "**enhance_text** — Improve selected text."

> "**transcribe_voice** — Convert speech to formatted text."

> "Because we use MCP, AI Keyboard works with the Frai ecosystem, Claude Desktop, and any future MCP-compatible platform."

---

## DEMO

> "Now let me show you AI Keyboard working."

**[Share screen with Gmail]**

> "I'm in Gmail. Watch below my text as I type."

**[Type: "Hi John, I wanted to follow up on our"]**

> "A suggestion appeared! It detected Gmail and suggested a professional completion."

**[Accept suggestion]**

> "Text inserted. Now let me switch to Slack."

**[Switch to Slack, type: "Hey team, quick update on"]**

> "See? Casual text with emoji. Same AI, different context."

> "That's AI Keyboard — **making every keystroke intelligent.**"

> "Thank you! We're happy to take questions."

---

# 📊 Summary

| Slide     | Title               | Speaker  | Duration    |
| --------- | ------------------- | -------- | ----------- |
| 1         | Problem Statement   | Member 1 | 1.5 min     |
| 2         | Proposed Solution   | Member 1 | 1.5 min     |
| 3         | System Architecture | Member 2 | 1.5 min     |
| 4         | Speed Optimization  | Member 2 | 1.5 min     |
| 5         | Context Detection   | Member 3 | 1.5 min     |
| 6         | Voice Integration   | Member 3 | 1.5 min     |
| 7         | Personalization     | Member 4 | 1 min       |
| 8         | Security            | Member 4 | 1 min       |
| 9         | MCP + Demo          | Member 4 | 2 min       |
| **Total** |                     |          | **~12 min** |
