# AI Keyboard — Video Presentation Script

## Complete Beginner-Friendly Explanation

---

## 🎥 Format

- **Screen share:** PPT slides displayed
- **Audio:** 4 members speaking
- **Duration:** 12-15 minutes
- **Goal:** Anyone can understand, even with zero technical background

---

# MEMBER 1: Slides 1, 2, 3

## The Problem, Solution & How It's Built

---

## SLIDE 1: Problem Statement

> "Hello everyone! Welcome to our presentation on **AI Keyboard**."

> "I'm [Name], and with me today are [Name 2], [Name 3], and [Name 4]."

> "Before we start, let me ask you something. How many hours do you spend typing every day? Emails, messages, documents, code. We all type a lot."

> "But here's something you might not have thought about: your keyboard is completely dumb. It doesn't understand anything."

> "Let me explain what I mean."

> "When you're writing an important email to your boss, you use formal language — 'Dear Sir, I would like to request...'. But when you switch to WhatsApp to message your friend, you type casually — 'Hey! What's up?'"

> "Your keyboard doesn't know the difference. It doesn't help you switch between formal and casual. It doesn't suggest appropriate words. It just shows whatever keys you press."

> "And there's another problem. Think about phrases you type every single day. 'Best regards.' 'Looking forward to hearing from you.' 'Sounds good, let's do it.' You type these again and again. Your keyboard never learns."

> "Now, you might think — what about tools like Grammarly or ChatGPT?"

> "**Grammarly** only fixes spelling and grammar mistakes. It doesn't help you write faster or understand context."

> "**GitHub Copilot** only works in code editors. It can't help you with emails or messages."

> "**ChatGPT** requires you to open a new tab, copy your text, paste it, type a command, wait for a response, copy the result, and paste it back. That's so much work! It breaks your flow."

> "So here's the gap we identified: **There's no AI assistant that helps you right where you type, in every app you use.** That's the problem we solved."

---

## SLIDE 2: Proposed Solution

> "Our solution is called **AI Keyboard**."

> "Let me explain what it does in simple terms."

> "Normally, when you type, the keys go directly to the app — Gmail, Word, Slack. The app shows your text. That's it."

> "With AI Keyboard, we add an **intelligent layer** in between. When you type, our AI sees what you're typing, understands the context, and suggests helpful completions."

> "Now let me explain the four main things AI Keyboard can do:"

> "**First: Context-Aware Suggestions**"

> "What does 'context-aware' mean? It means the AI understands WHERE you're typing. If you're in Gmail writing to your manager, it suggests professional language. If you're in Slack chatting with teammates, it suggests casual, friendly text — maybe even with emojis."

> "The AI figures this out automatically. You don't have to tell it 'I'm writing a formal email now.'"

> "**Second: Voice-to-Formatted-Text**"

> "This means you can speak instead of typing. But here's what makes it special — normal voice assistants just convert your words to text exactly as you said them."

> "Our AI understands what you're TRYING to do. If you say 'email john about deadline friday', it doesn't just type those words. It creates a proper email: 'Hi John, I wanted to discuss adjusting our deadline to Friday. Let me know your thoughts.'"

> "**Third: Automatic Personalization**"

> "What is personalization? It means the AI learns YOUR style. After using it for a few days, it notices: 'This person always starts emails with Hi, not Dear. This person uses LGTM in code reviews. This person never uses emojis in work emails.'"

> "You don't configure anything. It learns by watching what you accept and reject."

> "**Fourth: Universal Compatibility**"

> "This means it works everywhere — any website in your browser, any app on your computer. One tool for everything."

---

## SLIDE 3: System Architecture

> "Now let me explain HOW we built this. I'll keep it simple — no deep technical knowledge required."

> "Our system has three parts."

> "**Part 1: The Brain (Core AI Engine)**"

> "This is the intelligent center. It runs on your own computer as a background program. Think of it like having a really smart assistant sitting behind your keyboard, watching what you type and thinking: 'How can I help?'"

> "This brain contains all the AI logic — understanding context, generating suggestions, learning your style."

> "**Part 2: Browser Extension**"

> "What is a browser extension? It's a small add-on you install in Chrome or Firefox. You've probably seen extensions like ad blockers or password managers."

> "Our extension watches what you type in any website — Gmail, Slack, LinkedIn, Twitter, anything. It sends that information to the brain-ашнара and shows you the suggestions that come back."

> "**Part 3: Desktop App**"

> "The browser extension only works in browsers. But what about Microsoft Word? Notepad? VS Code?"

> "For those, we have a desktop app. It captures keyboard input at the operating system level — meaning it can see what you type in ANY app on your computer."

> "**How do they talk to each other?**"

> "Both the browser extension and desktop app connect to the brain using something called **WebSocket**. This is just a technology that allows real-time, instant communication — like a phone call where you get instant responses, not like email where you wait."

> "The key insight: **One brain, two clients.** All the intelligence is in one place. Both clients just send typing data and receive suggestions."

> "Now I'll pass to [Member 2] who will explain how we made this fast."

---

# MEMBER 2: Slides 4, 5, 6

## Speed, Context Detection & Voice

---

## SLIDE 4: Speed Optimization

> "Thanks [Member 1]. I'm [Name]."

> "Speed is extremely important for a typing assistant. Here's why."

> "Imagine you finish typing a sentence and want a suggestion. If you have to wait 3 seconds, you've already started typing the next sentence. The suggestion becomes useless."

> "For suggestions to be helpful, they need to appear almost instantly — in less than one-tenth of a second."

> "So we created a **three-layer speed system**. Let me explain each layer."

> "**Layer 1: Cache (0-10 milliseconds)**"

> "First, what is a cache? It's like a memory of things you use often. Your computer stores frequently accessed data so it doesn't have to search for it every time."

> "We store your common phrases locally on your computer. Things like 'Best regards', 'Sounds good', 'Looking forward to hearing from you'."

> "When you start typing one of these, we retrieve it instantly — in under 10 milliseconds. That's faster than a blink of your eye."

> "**Layer 2: Local LLM (10-100 milliseconds)**"

> "Now, what is an LLM? LLM stands for Large Language Model. It's the AI technology behind ChatGPT and similar tools — AI that understands and generates human language."

> "Normally, these AI models run on big servers far away. But we use a small LLM that runs right on your computer. It's called TinyLlama."

> "Because it runs locally, there's no internet delay. For simple completions, this layer responds in 10 to 100 milliseconds — still very fast."

> "**Layer 3: Cloud LLM (100-500 milliseconds)**"

> "For complex tasks — like 'rewrite this entire paragraph professionally' — we need a more powerful AI. That's when we call cloud servers running GPT-4 or Claude."

> "This takes longer, 100 to 500 milliseconds, but we use a technique called streaming — the response arrives piece by piece, so it feels fast."

> "**The result:** About 70% of your suggestions come from Layer 1 or 2, meaning most suggestions are nearly instant."

---

## SLIDE 5: Context Detection

> "Now, how does AI Keyboard know which app you're in?"

> "Let me explain what **context detection** means."

> "When you type, the AI doesn't just look at your text. It also looks at WHERE you're typing. This is called context."

> "For example: Are you in Gmail? Slack? VS Code? Are you writing an email, chatting, or coding?"

> "**How do we detect this?**"

> "In browsers, we look at the URL — the website address. 'mail.google.com' tells us you're in Gmail. 'slack.com' tells us you're in Slack."

> "We also look at what part of the page you're typing in. Is it a compose window? A search box? A comment field?"

> "For desktop apps, we look at the window title and the program name. 'Microsoft Word' tells us you're in Word. 'Visual Studio Code' tells us you're coding."

> "**What happens after detection?**"

> "We automatically adjust suggestions based on context."

> "For Gmail, we detect: formal context. We suggest professional language like 'I wanted to follow up regarding...'"

> "For Slack, we detect: casual context. We suggest friendly language like 'Hey! Quick question...' and maybe add emojis."

> "For VS Code, we detect: coding context. We suggest code completions, not casual text."

> "**How fast is this switching?**"

> "Under 50 milliseconds. When you Alt+Tab to a different app, by the time the window appears, our AI has already switched behavior. You won't even notice."

---

## SLIDE 6: Voice Integration

> "We also support voice input. Let me explain how this works."

> "**What is Whisper?**"

> "Whisper is an AI model created by OpenAI — the same company that made ChatGPT. It converts speech to text. What makes it special is that it's very accurate and it can run offline on your computer."

> "We use a library called 'faster-whisper' which runs this AI locally. This means your voice never leaves your computer — it's completely private."

> "**Normal voice typing vs. our approach**"

> "Normal voice assistants just transcribe — they write down exactly what you said."

> "If you say: 'email john about pushing the deadline to friday'"

> "Normal transcription gives you: 'email john about pushing the deadline to friday' — exactly those words."

> "**Our approach: Intent Enhancement**"

> "We understand what you're TRYING to do. You said 'email john' — so you want to write an email. We detect this intent and format the output properly:"

> "'Hi John, I wanted to discuss adjusting our timeline. Would it be possible to extend the deadline to Friday? Let me know your thoughts. Best regards.'"

> "This is a complete, professional email — not just raw words."

> "Now [Member 3] will explain personalization and security."

---

# MEMBER 3: Slides 7, 8, 9

## Personalization, Security & MCP

---

## SLIDE 7: Personalization Engine

> "Thanks [Member 2]. I'm [Name]."

> "Let me explain **personalization** — how AI Keyboard learns your unique writing style."

> "We have three layers of learning:"

> "**Layer 1: Explicit — You Tell Us**"

> "During setup, you can optionally tell us about yourself. What's your job? Developer, writer, manager? What tone do you prefer? Direct, friendly, formal?"

> "This is optional. You can skip it and let the AI learn automatically."

> "**Layer 2: Observed — We Watch and Learn**"

> "This is where the magic happens. As you use AI Keyboard, we observe patterns."

> "We notice: 'This person always starts emails with Hi [Name], never with Dear Sir.'"

> "We notice: 'This person uses the phrase sounds good a lot in chat.'"

> "We notice: 'This person is formal in Gmail but casual in Slack.'"

> "We notice: 'This person never uses emojis in work emails.'"

> "We learn these patterns automatically — you don't configure anything."

> "**Layer 3: Feedback — We Learn from Your Choices**"

> "Every time you accept a suggestion, that pattern is reinforced. Every time you reject a suggestion, we learn not to suggest that again."

> "Over time, suggestions become more and more tailored to you."

> "**Important: We learn PATTERNS, not CONTENT**"

> "What do I mean by this? Let me give an example."

> "We save: 'User prefers informal greetings in Slack.'"

> "We do NOT save: 'Hey John, about the meeting tomorrow at 3pm...'"

> "Your actual emails, messages, and documents are never stored. Only your writing patterns."

---

## SLIDE 8: Security Architecture

> "Now let me address the biggest concern: **privacy and security**."

> "You might be thinking: 'Wait, this AI is reading everything I type? That sounds scary!'"

> "Let me explain exactly what happens to your data."

> "**Our Core Principle: Use Content, Don't Store Content**"

> "Here's the step-by-step process:"

> "**Step 1: Read Temporarily.** When you type, the AI reads your text. This happens only in memory — temporary computer memory that disappears when you close the app."

> "**Step 2: Extract Patterns.** From your text, we extract general patterns. 'User seems to prefer formal tone.' 'User uses greetings like Hi [Name].' These patterns are saved."

> "**Step 3: Generate Suggestions.** Based on patterns and context, we suggest completions."

> "**Step 4: Discard Content.** Your actual text — the email content, the message, the document — is immediately deleted. Never saved to disk. Never sent to any server."

> "**What gets stored vs. what doesn't:**"

> "✅ Stored: 'User prefers formal tone in work emails'"

> "❌ NOT stored: 'Dear Mr. Smith, regarding our confidential contract...'"

> "**Automatic Protection**"

> "Some areas are automatically protected:"

> "Banking websites — AI Keyboard completely disables itself."

> "Password managers — disabled automatically."

> "Login pages — disabled automatically."

> "Password fields — never captured in any app."

> "**User Controls**"

> "You have full control:"

> "**Pause Mode:** Press Ctrl+Shift+P to instantly pause AI Keyboard. One more press to resume."

> "**Blocklist:** Add specific apps or websites where you don't want AI Keyboard to work."

> "**Data Wipe:** One-click button to delete all learned patterns and start fresh."

---

## SLIDE 9: MCP Integration

> "Finally, let me explain **MCP** — an important technical concept."

> "**What is MCP?**"

> "MCP stands for **Model Context Protocol**. Don't worry about the fancy name. Let me explain it simply."

> "Think about phone chargers. A few years ago, every phone had a different charger. iPhone had one. Samsung had another. Nokia had yet another. It was chaos."

> "Then USB-C came along — one cable that works with almost every device. USB-C is a **standard**."

> "**MCP is a standard for AI tools.** It's a common way for AI systems to communicate with each other."

> "**Why did we use MCP?**"

> "Two reasons."

> "First, this hackathon requires building a plugin for the Frai ecosystem. Frai uses MCP. By building our AI Keyboard as an MCP server, it plugs directly into Frai."

> "Second, MCP makes our tool work with other AI platforms too — like Claude Desktop. Any app that supports MCP can use our AI Keyboard."

> "**What 'tools' do we provide?**"

> "In MCP, you expose 'tools' — functions that other systems can call. We expose four tools:"

> "**detect_context** — 'What app is the user in right now?'"

> "**complete_intent** — 'Suggest text based on what user typed.'"

> "**enhance_text** — 'Improve this selected text.'"

> "**transcribe_voice** — 'Convert this speech to formatted text.'"

> "Any MCP-compatible system can call these tools and get intelligent keyboard assistance."

> "Now [Member 4] will cover our tech stack and show a demo."

---

# MEMBER 4: Slides 10, 11, 12 + Demo

## Tech Stack, Status & Live Demo

---

## SLIDE 10: Technology Stack

> "Thanks [Member 3]. I'm [Name]."

> "Let me quickly explain the technologies we used to build AI Keyboard. I'll keep it high-level."

> "**Core Server:** We used **Python** — a popular programming language. We used a framework called **FastAPI** which makes it easy to build web services."

> "**Local AI Models:** We used **Ollama** — this is software that lets you run AI models on your own computer. We run small, fast models like TinyLlama."

> "**Cloud AI:** For complex requests, we can optionally call **OpenAI GPT-4** — the powerful AI that runs ChatGPT."

> "**Voice Processing:** We used **faster-whisper** — a fast version of OpenAI's Whisper model that runs locally."

> "**Browser Extension:** Built with **JavaScript** using standard Chrome Extension APIs."

> "**Desktop App:** Built with **Python** using libraries called pynput (for capturing keyboard input) and pywinauto (for inserting text into other apps)."

> "**Communication:** **WebSocket** — technology that allows real-time, instant back-and-forth communication between the clients and server."

---

## SLIDE 11: Implementation Status

> "What have we actually built for this hackathon?"

> "✅ **Core MCP Server** — All four tools working: detect context, complete intent, enhance text, transcribe voice."

> "✅ **Chrome Browser Extension** — Works on all websites. Shows tooltip with suggestions."

> "✅ **Three-Layer Speed System** — Cache for instant responses, local LLM for fast responses, cloud LLM for complex requests."

> "✅ **Context Detection** — Automatically detects Gmail, Slack, VS Code, and other apps."

> "✅ **Personalization Engine** — Learns patterns from your usage."

> "✅ **Desktop Client** — Captures typing in any Windows app and shows suggestions."

> "**What's next?**"

> "Phase 1: Complete desktop app with better UI."

> "Phase 2: Advanced voice features."

> "Phase 3: Enterprise features for companies."

---

## SLIDE 12: Value Proposition

> "Let me summarize what makes AI Keyboard special."

> "**1. Universal** — Works everywhere. Any website. Any desktop app. Two clients cover everything."

> "**2. Contextual** — Automatically adapts to each app. Gmail gets formal suggestions. Slack gets casual suggestions. No manual switching."

> "**3. Fast** — Most suggestions appear in under 100 milliseconds. You won't notice any delay."

> "**4. Personal** — Learns your unique writing style over time. No configuration needed."

> "**5. Private** — All processing happens locally on your computer. Your content is never stored. Local-first design."

> "**6. Extensible** — Built on MCP standard. Works with Frai ecosystem and other AI platforms."

---

## DEMO

> "Now let me show you AI Keyboard in action."

**[Share screen showing browser with Gmail open]**

> "I'm opening Gmail to compose a new email. You can see the compose window here."

> "Now I'll start typing. Watch the area below my text."

**[Type slowly: "Hi John, I wanted to follow up on our"]**

> "I typed 'Hi John, I wanted to follow up on our' — and look, a suggestion appeared!"

**[Point to tooltip]**

> "The tooltip shows a continuation: 'discussion from yesterday about the project timeline.'"

> "The AI detected I'm in Gmail, saw I'm writing an email, and suggested a professional completion."

> "I can press Ctrl+Enter to accept this suggestion..."

**[Press shortcut]**

> "And the text is inserted right where my cursor was."

**[Switch to Slack]**

> "Now let me switch to Slack. Same AI, different context."

**[Type: "Hey team, quick update on"]**

> "I'm typing 'Hey team, quick update on' — and look at the suggestion."

**[Point to tooltip - shows casual text with emoji]**

> "It suggested casual text with an emoji! The AI detected I'm in Slack and adjusted automatically."

> "That's AI Keyboard. It understands where you are and helps you type faster, smarter."

---

## CLOSING

> "Thank you for watching our presentation!"

> "To summarize: AI Keyboard puts artificial intelligence right at the point of input — your keyboard. It works everywhere, learns your style, and keeps your data private."

> "**Making every keystroke intelligent.**"

> "We're happy to answer any questions!"

---

# 📊 Summary Table

| Slide | Title               | Speaker  | Key Definitions                         |
| ----- | ------------------- | -------- | --------------------------------------- |
| 1     | Problem Statement   | Member 1 | Explained context gap                   |
| 2     | Proposed Solution   | Member 1 | Defined: context-aware, personalization |
| 3     | System Architecture | Member 1 | Defined: browser extension, WebSocket   |
| 4     | Speed Optimization  | Member 2 | Defined: cache, LLM, milliseconds       |
| 5     | Context Detection   | Member 2 | Defined: context, URL detection         |
| 6     | Voice Integration   | Member 2 | Defined: Whisper, intent enhancement    |
| 7     | Personalization     | Member 3 | Defined: patterns vs content            |
| 8     | Security            | Member 3 | Defined: temporary memory, blocklist    |
| 9     | MCP                 | Member 3 | Defined: MCP, tools, standard           |
| 10    | Tech Stack          | Member 4 | Brief, high-level only                  |
| 11    | Implementation      | Member 4 | Status checklist                        |
| 12    | Value + Demo        | Member 4 | Summary + live demo                     |

---

# ⏱️ Timing Guide

| Member    | Slides            | Duration        |
| --------- | ----------------- | --------------- |
| Member 1  | 1, 2, 3           | 4 minutes       |
| Member 2  | 4, 5, 6           | 4 minutes       |
| Member 3  | 7, 8, 9           | 4 minutes       |
| Member 4  | 10, 11, 12 + Demo | 4 minutes       |
| **Total** |                   | **~16 minutes** |
