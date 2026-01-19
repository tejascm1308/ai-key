# AI Keyboard — Video Conference Presentation Script

## Recording Format: 4-Person Video Call with Screen Share

---

## 🎥 Setup

- **Format:** Video conference (Google Meet/Zoom style)
- **Recording:** Screen share showing PPT + 4 webcam faces
- **Duration:** 10-12 minutes
- **Style:** Conversational, engaging, relatable examples

---

# MEMBER 1: Problem & Solution Introduction

**Slides:** 1-2 | **Duration:** ~2.5 minutes

---

### SLIDE 1: Problem Statement

> "Hi everyone! I'm [Name] and along with my team [Name 2], [Name 3], and [Name 4], we're presenting **AI Keyboard** — making every keystroke intelligent."

> "Let me start with a question. How many of you write emails every day? Messages on Slack? Code in VS Code? We all do, right? We spend hours on our keyboards."

> "But here's something interesting — your keyboard doesn't understand anything about what you're doing. It's completely dumb."

> "Think about it. When you're writing a formal email to your manager, your keyboard doesn't know. When you switch to Slack to chat with friends, your keyboard doesn't adjust. When you type 'Best regards' for the hundredth time this week, your keyboard doesn't learn that you always use this phrase."

> "Now, some tools try to help. Grammarly fixes your grammar, but only works in some apps. GitHub Copilot helps with code, but only in code editors. And ChatGPT? You have to copy your text, paste it in, wait, copy the result back. That completely breaks your flow."

> "The real problem is: **there's no AI that helps you right where you type, across all your apps.** That's the gap we saw."

---

### SLIDE 2: Proposed Solution

> "So what did we build? **AI Keyboard** — an intelligent layer that sits between your keyboard and your applications."

> "It does four things:"

> "First, **context-aware suggestions**. It knows you're in Gmail, so it suggests professional language. Switch to Slack, and it suggests casual, emoji-friendly text."

> "Second, **voice-to-formatted-text**. You can speak 'email john about deadline friday' and get a properly formatted professional email."

> "Third, **automatic personalization**. It learns your style silently. After a few days, it knows you prefer 'Hi' over 'Dear' and suggests accordingly."

> "Fourth, **universal compatibility**. It works in your browser, in desktop apps, everywhere."

> "Now let me pass to [Member 2] who will explain how we architected this."

---

# MEMBER 2: Architecture & MCP

**Slides:** 3, 9, 10 | **Duration:** ~2.5 minutes

---

### SLIDE 3: System Architecture

> "Thanks [Member 1]. I'm [Name] and I'll explain how AI Keyboard works under the hood."

> "Our architecture follows a simple principle: **one brain, many clients**."

> "The brain is our **Core AI Engine**. It runs locally on your computer. This is where all the intelligence lives — context detection, suggestion generation, personalization."

> "Now, this brain needs to see what you're typing. That's where our clients come in."

> "**Client 1** is a **Browser Extension**. It captures everything you type in Chrome or Firefox — Gmail, Slack, LinkedIn, any website."

> "**Client 2** is a **Desktop App**. It captures typing in native applications — Word, Notepad, VS Code, any app on Windows or Mac."

> "Both clients connect to the same brain using WebSocket — that's a real-time communication protocol. So when the AI learns something from your Gmail usage, that knowledge also applies when you're using Microsoft Word."

---

### SLIDE 9: MCP Integration

> "Now, why did we build this using MCP — Model Context Protocol?"

> "Let me explain simply. Before USB, every device had a different cable. Phones, cameras, printers — all different connectors. USB standardized everything."

> "**MCP is like USB for AI tools.** It's a standard protocol for AI systems to communicate."

> "Because we built AI Keyboard as an MCP server, it can plug into the Frai ecosystem — which is part of this hackathon's requirement. It can also work with Claude Desktop or any future MCP-compatible platform."

> "We defined four tools that any MCP client can use: detect context, suggest completions, transcribe voice, and enhance text."

---

### SLIDE 10: Technology Stack

> "Quick overview of our tech stack: Python and FastAPI for the core engine, JavaScript for the browser extension, Ollama for running AI locally, and WebSocket for real-time communication."

> "Now [Member 3] will explain how we made this fast and context-aware."

---

# MEMBER 3: Speed, Context & Features

**Slides:** 4, 5, 6, 7 | **Duration:** ~3 minutes

---

### SLIDE 4: Speed Optimization

> "Thanks [Member 2]. I'm [Name] and I'll talk about speed — because for a typing assistant, speed is everything."

> "If you finish typing and wait 2-3 seconds for a suggestion, you've already moved on. The suggestion becomes useless."

> "So we built a **three-layer speed system**."

> "**Layer 1 is Cache** — response time under 10 milliseconds, basically instant. We store your frequently used phrases locally. 'Best regards', 'Sounds good', 'Let me know' — these come up immediately."

> "**Layer 2 is Local AI** — response time 10 to 100 milliseconds. We run a small language model called TinyLlama directly on your computer using Ollama. No internet needed. This handles most completions."

> "**Layer 3 is Cloud AI** — response time 100 to 500 milliseconds. For complex requests like 'rewrite this email professionally', we call GPT-4. Even then, we stream the response so it feels fast."

> "The result? **70% of suggestions come from Layer 1 or 2**, meaning they're nearly instant."

---

### SLIDE 5: Context Detection

> "Now, how does AI Keyboard know what app you're in?"

> "For browsers, we read the URL. 'mail.google.com' tells us you're in Gmail. We also analyze the page structure — are you in a compose window? A reply?"

> "For desktop apps, we read the window title and process name."

> "Here's what's cool: when you switch from Gmail to Slack, within 50 milliseconds — faster than you can blink — the AI adjusts its behavior. Gmail gets formal suggestions. Slack gets casual suggestions with emojis."

> "This happens automatically. You don't configure anything."

---

### SLIDE 6: Voice Integration

> "We also support voice input. But not just basic transcription."

> "Imagine you're walking and want to draft an email. You speak: 'email john about pushing the meeting to friday'."

> "Normal voice assistants would give you exactly that text. But AI Keyboard understands your **intent**. It produces:"

> "'Hi John, I wanted to check if we could reschedule our meeting to Friday. Let me know if that works. Best regards.'"

> "It understood you wanted an email, detected the casual instruction, and formatted it properly. We use Whisper for transcription — it runs locally, completely free."

---

### SLIDE 7: Personalization Engine

> "Finally, personalization. AI Keyboard learns your style automatically."

> "There are three layers of learning. First, **explicit** — during onboarding, you can tell us your role and preferred tone. Second, **observed** — we notice patterns. You always start emails with 'Hi', you use 'LGTM' in code reviews, you prefer formal tone in Gmail but casual in Slack."

> "Third, **feedback** — when you accept a suggestion, we learn. When you reject one, we learn not to suggest that again."

> "And importantly — we learn **patterns**, not content. We store 'user prefers Hi greeting', not 'Hi John, about the project...'."

> "Now [Member 4] will cover privacy and show a demo."

---

# MEMBER 4: Privacy, Security & Demo

**Slides:** 8, 11, 12 + Demo | **Duration:** ~3 minutes

---

### SLIDE 8: Security Architecture

> "Thanks [Member 3]. I'm [Name] and I want to address the elephant in the room — privacy."

> "You might be thinking: 'This AI is reading my emails and messages? That sounds concerning.'"

> "Let me explain our core principle: **Use content, don't store content.**"

> "When you type an email, here's what happens. The AI reads your text temporarily — just in memory — to understand context. It extracts patterns like 'this user prefers formal tone in Gmail.' These patterns are stored. But your actual email content? Deleted immediately. Never saved. Never sent to any server."

> "We save: 'User tends to use formal greetings.'"

> "We DON'T save: 'Dear Mr. Smith, regarding our contract negotiation...'"

> "Additionally, banking websites, password managers, and login pages are automatically blocked. AI Keyboard disables itself on sensitive sites."

> "And there's manual control too. Press Ctrl+Shift+P anytime to pause AI Keyboard instantly. One more press to resume."

---

### SLIDE 11: Implementation Status

> "What have we actually built for this hackathon?"

> "We completed: the Core MCP Server with all four tools, the Chrome Browser Extension that works on all websites, the three-layer speed system, context detection, and basic personalization."

> "This isn't just a concept. It's a working product."

---

### SLIDE 12: Value Proposition

> "What makes AI Keyboard special? Let me summarize:"

> "**Universal** — Two clients cover all apps, browser and desktop."

> "**Contextual** — Adapts to each application automatically."

> "**Fast** — Sub-100ms for most suggestions."

> "**Personal** — Learns your style without configuration."

> "**Private** — Local-first processing, content never stored."

> "**Extensible** — MCP-compatible, plugs into Frai ecosystem."

---

### DEMO (Screen Share)

> "Let me quickly show you how this looks in action."

**[Share screen showing Gmail]**

> "I'm in Gmail compose. Watch the bottom of the screen as I type."

> "I'll type: 'Hi John, I wanted to follow up on'"

**[Tooltip appears with suggestion]**

> "See? A suggestion appeared instantly. I can press Tab to accept or Escape to dismiss."

**[Accept suggestion]**

> "The text was inserted right at my cursor. Now let me switch to Slack."

**[Switch to Slack]**

> "Same thing here. I type: 'Hey team, quick'"

**[Suggestion appears with emoji]**

> "Notice the suggestion is more casual and includes an emoji. AI Keyboard detected I'm in a chat app and adjusted automatically."

> "That's AI Keyboard — making every keystroke intelligent."

---

### Closing

> "Thank you all for listening! To summarize: AI Keyboard puts AI right at the point of input, works everywhere, learns your style, and respects your privacy."

> "We're happy to answer any questions."

---

# 📋 Quick Reference for Recording

### Member Assignments

| Member | Slides           | Minutes | Key Topic                              |
| ------ | ---------------- | ------- | -------------------------------------- |
| 1      | 1-2              | 2.5     | Problem story, solution overview       |
| 2      | 3, 9, 10         | 2.5     | Architecture, MCP, tech stack          |
| 3      | 4-7              | 3       | Speed, context, voice, personalization |
| 4      | 8, 11, 12 + Demo | 3       | Privacy, status, value + live demo     |

### Handoff Lines

- Member 1 → 2: "Now let me pass to [Name] who will explain how we architected this."
- Member 2 → 3: "Now [Name] will explain how we made this fast and context-aware."
- Member 3 → 4: "Now [Name] will cover privacy and show a demo."

### Recording Tips

1. Look at camera when speaking, not at screen
2. Speak at normal conversational pace
3. Slight pauses after key points
4. Demo should be practiced 2-3 times beforehand
5. Have backup: If demo fails, describe what would happen
