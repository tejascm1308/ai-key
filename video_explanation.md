# AI Keyboard — Video Presentation Script

## Slides 1-9 | Engaging + Clear Explanation

---

## 🎥 Format

- **Slides:** 1 to 9
- **Duration:** ~12 minutes
- **Speakers:** 4 members
- **Style:** Conversational, engaging, with clear explanations

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

> "Hello everyone! We're Team [Name], and we're presenting **AI Keyboard — Making Every Keystroke Intelligent**."

> "I'm [Name], and joining me today are [Name 2], [Name 3], and [Name 4]."

> "Let me start with something we all experience every single day."

> "You wake up, open your laptop, and start typing. An email to your manager. A message to your friend. Some code for a project. We spend hours on our keyboards."

> "But here's something interesting — your keyboard has zero understanding of what you're doing. Nothing."

> "Think about this: When you're drafting a formal email to your boss, you use professional language. 'Dear Sir, I hope this email finds you well.' But when you switch to WhatsApp to message a friend, you type very differently. 'Hey! What's up?'"

> "Your keyboard doesn't help with this switch. It doesn't suggest formal words when you're in Gmail or casual words when you're in WhatsApp. It just shows whatever keys you press."

> "And here's another frustration. Phrases you type every single day — 'Best regards', 'Looking forward to hearing from you', 'Sounds good, let's proceed' — you type them again and again. Your keyboard never learns that you use these constantly."

> "Now, you might think about existing tools. What about Grammarly? Grammarly is great for fixing grammar, but it doesn't help you write faster or understand which app you're in."

> "What about GitHub Copilot? Copilot only works in code editors. It can't help you with emails or chat messages."

> "What about ChatGPT? To use ChatGPT, you have to open a new browser tab, copy your text, paste it, type what you want, wait for a response, copy the result, go back to your app, and paste. That's a lot of steps! It completely breaks your flow."

> "So here's the gap we identified: **There's no AI that helps you right at the moment of typing, across all your applications.** And that's exactly what we built."

---

## SLIDE 2: Proposed Solution

> "Our solution is called **AI Keyboard**."

> "Let me explain it clearly. Normally, when you press keys, those keystrokes go directly to the application you're using — Gmail, Slack, Word. The app shows your text. That's it."

> "What we've done is add an **intelligent layer** in between. This layer watches what you're typing, understands the situation, and offers helpful suggestions."

> "AI Keyboard has four main capabilities. Let me walk through each one."

> "**First: Context-Aware Suggestions**"

> "This means the AI understands WHERE you're typing. If you're in Gmail writing to a client, it suggests professional language. If you're in Slack chatting with teammates, it suggests casual, friendly text — maybe even with emojis. The beautiful thing is, it figures this out automatically. You don't have to tell it 'I'm writing a formal email now.'"

> "**Second: Voice-to-Formatted-Text**"

> "You can speak instead of typing. But here's what makes it special — normal voice assistants just convert your words to text exactly as you said them. Our AI understands your intent. If you say 'email john about deadline friday', it doesn't just type those words. It creates a properly formatted email: 'Hi John, I wanted to discuss adjusting our deadline to Friday. Let me know your thoughts.'"

> "**Third: Automatic Personalization**"

> "The AI learns YOUR unique writing style. After using it for a few days, it notices your patterns. You always start emails with 'Hi', not 'Dear'. You use 'sounds good' a lot. You never use emojis in work emails. It learns all of this automatically."

> "**Fourth: Universal Compatibility**"

> "It works everywhere. Any website in your browser — Gmail, Slack, LinkedIn. Any app on your computer — Word, Notepad, VS Code. One solution that covers everything."

> "Now let me hand over to [Member 2] who will explain how we actually built this system."

---

# MEMBER 2: Slides 3 & 4

**Duration:** ~3 minutes

---

## SLIDE 3: System Architecture

> "Thanks [Member 1]. I'm [Name]."

> "So how did we build AI Keyboard? Let me walk you through the architecture."

> "Our system follows a simple but powerful principle: **One brain, many clients.**"

> "At the center is what we call the **Core AI Engine**. This is the brain of our system. It runs locally on your computer as a background service — you don't even see it running. This brain contains all the intelligence — it detects which app you're in, generates suggestions, and learns your style."

> "Now, this brain needs eyes and hands — ways to see what you're typing and show you suggestions. That's where our two clients come in."

> "**Client 1: Browser Extension**"

> "This is a Chrome or Firefox extension that watches everything you type in any website. Gmail, Slack, LinkedIn, Twitter — any website. It captures your typing, sends it to the brain, and displays the suggestions you get back."

> "**Client 2: Desktop App**"

> "The browser extension only works in browsers. But what about Microsoft Word? Notepad? VS Code? For those, we have a desktop application that captures keyboard input at the operating system level — which means it works with ANY app on your computer."

> "Both clients connect to the brain using WebSocket — a technology that enables instant, real-time communication. When you type a letter, within milliseconds, the brain can send back a suggestion."

> "The key benefit of this architecture: We write all the AI logic once in the brain, and both clients benefit. When we improve the AI, both browser and desktop users get better suggestions automatically."

---

## SLIDE 4: Speed Optimization

> "Now let's talk about something critical: **speed**."

> "Imagine this scenario. You finish typing a sentence and you're waiting for a suggestion. If it takes 3 seconds to appear, what happens? You've already started typing the next sentence. The suggestion becomes useless."

> "For a typing assistant to be helpful, suggestions need to appear almost instantly — ideally in under one-tenth of a second. That's really fast."

> "So we designed a **three-layer speed system** to achieve this."

> "**Layer 1: Cache — Response time: 0 to 10 milliseconds**"

> "This is basically instant. How do we achieve this? We store your frequently used phrases locally. Things like 'Best regards', 'Sounds good', 'Looking forward to your reply'. When you start typing one of these, we pull it from local storage immediately."

> "**Layer 2: Local AI — Response time: 10 to 100 milliseconds**"

> "We run a small AI model directly on your computer using Ollama. Because it's local, there's no internet delay. For most simple completions, this layer handles it quickly."

> "**Layer 3: Cloud AI — Response time: 100 to 500 milliseconds**"

> "For more complex tasks — like 'rewrite this paragraph to sound more professional' — we need a more powerful AI. That's when we call cloud services like GPT-4. Even then, we stream the response so it starts appearing immediately."

> "The result of this design? About **70% of suggestions come from Layer 1 or 2**, which means most of the time, you get suggestions almost instantly."

> "Now [Member 3] will explain how we detect context and handle voice input."

---

# MEMBER 3: Slides 5 & 6

**Duration:** ~3 minutes

---

## SLIDE 5: Context Detection

> "Thanks [Member 2]. I'm [Name]."

> "Let me explain one of the coolest features of AI Keyboard: **context detection**."

> "The question is: How does our AI know which app you're using? How does it know to be formal in Gmail and casual in Slack?"

> "**For browsers**, we look at the URL — the website address in your browser. If the URL contains 'mail.google.com', we know you're in Gmail. If it contains 'slack.com', we know you're in Slack. We also analyze the specific part of the page you're typing in — is it a compose window? A search box? A comment section?"

> "**For desktop apps**, we read the window title and the program name. If the window says 'Document1 - Microsoft Word', we know you're in Word. If it says 'app.py - Visual Studio Code', we know you're coding."

> "Once we detect the context, our AI automatically adjusts its behavior."

> "In Gmail, we detect a professional email context. Suggestions become formal: 'I wanted to follow up regarding our previous conversation...'"

> "In Slack, we detect a casual chat context. Suggestions become friendly: 'Hey! Quick question about the project...' — maybe with a thumbs up emoji."

> "In VS Code, we detect a coding context. Suggestions become code-aware completions instead of casual text."

> "And here's the impressive part: This context switching happens in under **50 milliseconds**. When you Alt+Tab from Gmail to Slack, by the time the Slack window fully appears on screen, our AI has already switched its behavior. You won't even notice the transition."

---

## SLIDE 6: Voice Integration

> "We also built voice input into AI Keyboard. Let me explain how it works."

> "We use **Whisper**, which is OpenAI's speech-to-text AI model. But here's what's special — we run it locally on your computer using a library called faster-whisper. This means your voice never leaves your device. It's completely private and works even without internet."

> "But we didn't stop at basic transcription. We added something we call **intent enhancement**."

> "Let me show you the difference. Say you're walking and you want to draft an email. You speak into your phone: 'email john about pushing the deadline to friday'"

> "A normal voice assistant would give you exactly those words: 'email john about pushing the deadline to friday'. That's not a proper email."

> "Our AI understands that you WANT to send an email. So it produces this:"

> "'Hi John, I wanted to check if we could extend the deadline to Friday. Let me know if that works for you. Best regards.'"

> "See the difference? We understood the intent behind your words and formatted the output properly. It's not just transcription — it's understanding what you're trying to accomplish."

> "Now [Member 4] will cover personalization, our security approach, and show you a live demo."

---

# MEMBER 4: Slides 7, 8, 9 + Demo

**Duration:** ~3 minutes

---

## SLIDE 7: Personalization Engine

> "Thanks [Member 3]. I'm [Name]."

> "Let me explain how AI Keyboard learns your personal writing style."

> "We have a three-layer learning system."

> "**Layer 1: Explicit Preferences**"

> "During setup, you can optionally tell us a bit about yourself. What's your role — developer, writer, manager? What tone do you prefer — direct, friendly, formal? This helps us start with reasonable suggestions. But this step is completely optional."

> "**Layer 2: Observed Patterns**"

> "This is where the real magic happens. As you use AI Keyboard, we quietly observe your patterns. We notice that you always start emails with 'Hi [Name]' instead of 'Dear Sir'. We notice that you use the phrase 'sounds good' a lot in Slack. We notice that you're formal in Gmail but casual in Microsoft Teams."

> "We learn all of this automatically — you don't configure anything."

> "**Layer 3: Feedback Loop**"

> "Every time you accept a suggestion by pressing Tab, that pattern gets reinforced. Every time you dismiss a suggestion by pressing Escape or continue typing, we learn that maybe that wasn't a good suggestion for you."

> "Over time, suggestions become more and more tailored to YOUR style."

> "Now, an important point: We store **patterns**, not content. We remember 'this user prefers Hi over Dear'. We do NOT store 'Hi John, about the project budget for Q3...' Your actual emails and messages are never saved."

---

## SLIDE 8: Security Architecture

> "This brings me to privacy and security — which is our highest priority."

> "You might be wondering: 'This AI is reading my emails and messages. Is my data safe?'"

> "Let me explain exactly what happens to your data."

> "Our core principle is: **Use content, don't store content.**"

> "Here's the process step by step:"

> "Step 1: When you type, our AI reads your text. This happens only in temporary memory."

> "Step 2: We extract general patterns — like 'user prefers formal tone' or 'user starts with Hi greeting'."

> "Step 3: We generate suggestions based on context and patterns."

> "Step 4: Your actual text content — the email you wrote, the message you typed — is immediately discarded. It's never saved to disk. It's never sent to any external server."

> "We also have automatic protection built in. Banking websites, password managers, and login pages are automatically blocked. AI Keyboard completely disables itself on sensitive sites."

> "And you have manual controls too. Press Ctrl+Shift+P to instantly pause AI Keyboard. Add specific apps or websites to a blocklist. Or do a complete data wipe with one click."

---

## SLIDE 9: MCP Integration

> "Finally, let me explain **MCP — Model Context Protocol**."

> "MCP is a **standardized protocol** for AI tools to communicate with each other. Think about it like this: Before USB became standard, every device had a different connector — phones, cameras, printers all needed different cables. USB standardized that."

> "MCP is doing the same thing for AI. It's creating a common language so different AI systems can work together."

> "We built AI Keyboard as an **MCP server**. This means we expose our capabilities as 'tools' that any MCP-compatible system can use."

> "We have four tools:"

> "**detect_context** — tells you which app the user is currently in"

> "**complete_intent** — provides text suggestions based on input"

> "**enhance_text** — improves selected text"

> "**transcribe_voice** — converts speech to properly formatted text"

> "Why does this matter? First, it's a requirement for the Frai hackathon — our AI Keyboard plugs directly into the Frai ecosystem. Second, it means Claude Desktop and any future MCP-compatible platform can also use our AI Keyboard capabilities."

---

## DEMO

> "Now let me show you AI Keyboard in action."

**[Share screen showing browser with Gmail]**

> "I'm opening Gmail to compose a new email. Watch the area below my text as I type."

**[Start typing slowly]**

> "I'm typing: 'Hi John, I wanted to follow up on our discussion about'"

**[Pause — tooltip appears with suggestion]**

> "You can see a suggestion appeared! The AI detected that I'm in Gmail, understood I'm writing an email, and suggested a professional completion."

> "I can press Tab to accept this or Escape to dismiss. Let me accept it."

**[Press Tab — text is inserted]**

> "The text was inserted right at my cursor. Now let me show you context switching."

**[Switch to Slack]**

> "I'm switching to Slack. Same AI, but watch how the behavior changes."

**[Type in Slack: "Hey team, quick update on the"]**

**[Tooltip appears — more casual with emoji]**

> "See that? The suggestion is now more casual and includes an emoji. The AI detected I'm in a chat app and automatically adjusted its tone."

> "That's AI Keyboard. It understands where you are, adapts to the context, and helps you type faster and smarter."

> "**Making every keystroke intelligent.**"

> "Thank you for watching! We're happy to answer any questions."

---

# ⏱️ Summary

| Member    | Slides         | Duration    |
| --------- | -------------- | ----------- |
| Member 1  | 1, 2           | 3 min       |
| Member 2  | 3, 4           | 3 min       |
| Member 3  | 5, 6           | 3 min       |
| Member 4  | 7, 8, 9 + Demo | 3 min       |
| **Total** |                | **~12 min** |
