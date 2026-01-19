# AI Keyboard — Video Presentation Script

## "Making Every Keystroke Intelligent"

---

## 🎬 Video Setup

- **Total Duration:** 10-12 minutes
- **Team:** 4 members
- **Format:** PPT slides displayed + speakers presenting + enactments
- **Style:** Conversational, engaging, with real-life scenarios acted out

---

# 🎤 MEMBER 1: The Problem We All Face

**Covers:** Slide 1 (Problem) + Slide 2 (Solution Introduction)  
**Duration:** ~3 minutes

---

### [SLIDE 1: Problem Statement]

**[MEMBER 1 walks in, sits at a laptop, pretends to type]**

> "Hey everyone! Let me start with something we all do every single day."

**[Types on keyboard dramatically]**

> "We type. A lot. Emails to our boss, messages to friends, code for our projects. But here's the funny thing..."

**[Stops typing, looks at audience]**

> "Our keyboard has NO idea what we're doing. Zero. Nothing."

**[Enactment: Pretend switching between apps]**

> "Watch this. I'm writing a formal email to my manager..."

_[Types and says aloud]_ "Dear Sir, I would like to request..."

> "Now I switch to Slack to message my friend..."

_[Types and says aloud]_ "Dear Sir, I would like to..."

**[Pauses, looks confused]**

> "Wait, that doesn't sound right for a Slack message, does it? But my keyboard doesn't know the difference. It doesn't care if I'm in Gmail or Slack or VS Code."

**[Stands up, walks toward screen]**

> "And it gets worse. I type the same things EVERY day."

_[Counts on fingers]_

> "'Best regards.' 'Sounds good.' 'Looking forward to hearing from you.' Again and again. My keyboard never learns."

**[Points to slide showing "Existing Tools"]**

> "Now, you might think — what about Grammarly? What about ChatGPT?"

> "Grammarly only fixes grammar. It doesn't help me write faster. And ChatGPT? Let me show you what that's like..."

**[Enactment: Using ChatGPT]**

_[Pretends to write email, then stops]_

> "Hmm, I want to make this sound more professional."

_[Opens new tab, pretends to copy-paste]_

> "Let me copy this... open ChatGPT... paste... type 'make this professional'... wait... wait... copy the result... go back to Gmail... paste..."

**[Looks exhausted]**

> "By the time I'm done, I could have just written it myself! This breaks my flow completely."

**[Pause for effect]**

> "So here's the gap: **There's no AI that helps you at the point of input — right where you type — across ALL your apps.**"

---

### [SLIDE 2: Proposed Solution]

**[MEMBER 1 smiles, gestures to new slide]**

> "And that's exactly what we built. **AI Keyboard.**"

> "Think of it like this: we put an intelligent layer between your keyboard and your apps. This layer..."

_[Points to each bullet as it appears]_

> "**Understands context** — It knows you're in Gmail, so it suggests professional language. Switch to Slack? It gets casual."

> "**Learns your style** — After a few days, it knows you always start emails with 'Hi' not 'Dear Sir'."

> "**Works everywhere** — Browser, desktop apps, code editors. One solution for everything."

> "Let me hand over to [Member 2] who will explain HOW we built this magic."

**[MEMBER 1 steps aside, MEMBER 2 takes over]**

---

# 🎤 MEMBER 2: How It All Works

**Covers:** Slide 3 (Architecture) + Slide 9 (MCP) + Slide 10 (Tech Stack)  
**Duration:** ~3 minutes

---

### [SLIDE 3: Architecture]

**[MEMBER 2 points to architecture diagram]**

> "Okay, so how does AI Keyboard actually work? Let me break it down simply."

> "Imagine you have a really smart friend who sits behind your keyboard. Every time you type, this friend watches and thinks: 'What is this person trying to say? How can I help?'"

**[Points to diagram]**

> "That smart friend is our **Core AI Engine**. It runs right here on your computer — not on some server far away."

> "Now, this engine needs eyes and ears to see what you're typing. That's where our clients come in."

**[Points to Browser Extension on diagram]**

> "**Client 1: Browser Extension.** This watches everything you type in Chrome, Firefox, any browser. Gmail, Slack, LinkedIn — all covered."

**[Points to Desktop App on diagram]**

> "**Client 2: Desktop App.** This watches native apps — Word, Notepad, VS Code, any app on your computer."

**[Makes connecting gesture with hands]**

> "Both clients talk to the same brain. So when you write 'Best regards' in Gmail and the AI learns it, that learning also applies when you're writing in Microsoft Word."

> "**One brain. Two clients. Every app covered.**"

---

### [SLIDE 9: MCP Integration]

**[MEMBER 2 looks curious, as if asking a question]**

> "Now, some of you might be wondering — what is this 'MCP' thing?"

> "MCP stands for **Model Context Protocol**. Let me explain with an analogy."

**[Enactment: Hold up imaginary cables]**

> "Back in the day, every phone had a different charger. iPhone had one cable. Samsung had another. It was chaos!"

> "Then USB-C came along — one standard cable that works with everything."

> "**MCP is USB-C for AI.** It's a standard way for AI tools to communicate."

> "Because we built AI Keyboard using MCP, it can plug into the Frai ecosystem, Claude Desktop, or any future AI platform that supports MCP."

> "We're not building a standalone tool. We're building something that fits into the larger AI ecosystem."

---

### [SLIDE 10: Technology Stack]

**[MEMBER 2 points to tech list]**

> "Just quickly — here's what we used to build this:"

> "**Python** for the core engine. **JavaScript** for the browser extension. **Ollama** for running AI models locally. **WebSocket** for real-time communication."

> "And now, let me hand over to [Member 3] who will show you how we made this FAST."

**[MEMBER 2 steps aside]**

---

# 🎤 MEMBER 3: Speed, Context & Features

**Covers:** Slide 4 (Speed) + Slide 5 (Context) + Slide 6 (Voice) + Slide 7 (Personalization)  
**Duration:** ~3 minutes

---

### [SLIDE 4: Speed Optimization]

**[MEMBER 3 snaps fingers]**

> "Speed. If there's one thing that can make or break a typing assistant, it's speed."

> "If I finish typing and have to wait 3 seconds for a suggestion... I've already moved on. Useless."

> "So we built a **three-layer speed system**."

**[Points to Layer 1 on slide]**

> "**Layer 1: Cache.** Response time? Under 10 milliseconds. That's basically instant."

> "How? We store your common phrases locally. 'Best regards.' 'Sounds good.' 'Let me know.' When you start typing these, boom — instant suggestion."

**[Points to Layer 2]**

> "**Layer 2: Local AI.** Response time? 10 to 100 milliseconds."

> "We run a small AI model called TinyLlama right on your computer. No internet needed. For simple completions, this is enough."

**[Points to Layer 3]**

> "**Layer 3: Cloud AI.** Response time? 100 to 500 milliseconds."

> "For complex tasks like 'rewrite this email professionally', we call GPT-4 or Claude. Even then, we stream the response so it feels fast."

**[Shows percentage]**

> "Result? **70% of suggestions come from Layer 1 or 2.** Most of the time, you get suggestions instantly."

---

### [SLIDE 5: Context Detection]

**[MEMBER 3 pretends to switch apps]**

> "Next — context. Let me show you something cool."

**[Enactment: Switching between apps]**

> "I'm in Gmail, writing to my boss..."

_[Types formally]_ "I hope this email finds you well..."

> "Now I switch to Slack, messaging my teammate..."

_[Types casually]_ "Hey! Quick question..."

**[Stops, looks at audience]**

> "Notice how the tone changed? AI Keyboard does this automatically."

> "When I'm in Gmail, it detects: 'This is a professional email app. Suggest formal language.'"

> "When I switch to Slack: 'This is casual messaging. Suggest friendly language. Maybe add an emoji 😊'"

> "This context switch happens in under **50 milliseconds**. You don't even notice it."

---

### [SLIDE 6: Voice Integration]

**[MEMBER 3 holds hand like speaking into phone]**

> "And we also support voice."

> "Let's say I'm walking and want to draft an email. I can speak:"

_[Speaks casually]_ "email john about pushing the meeting to friday"

> "What does AI Keyboard produce?"

_[Shows formatted output on screen]_

> "'Hi John, I wanted to check if we could reschedule our meeting to Friday. Let me know if that works. Best regards, [Your Name]'"

> "It didn't just transcribe my words. It **understood my intent** and formatted it as a proper email."

---

### [SLIDE 7: Personalization]

**[MEMBER 3 touches heart area]**

> "And finally — personalization. AI Keyboard learns YOU."

> "After using it for a few days, it knows:"

> "'This person always starts emails with Hi, not Dear.'"
> "'This person uses 'sounds good' a lot in Slack.'"
> "'This person never uses emojis in Gmail.'"

> "We don't ask you to configure anything. It just learns by watching what you accept and reject."

> "Now [Member 4] will cover privacy — and then show you a live demo!"

**[MEMBER 3 steps aside]**

---

# 🎤 MEMBER 4: Privacy, Security & Live Demo

**Covers:** Slide 8 (Security) + Slide 11 (Implementation) + Slide 12 (Value) + Demo  
**Duration:** ~3 minutes

---

### [SLIDE 8: Security Architecture]

**[MEMBER 4 looks serious]**

> "Now, I know what some of you are thinking."

> "'Wait — this AI is reading my emails? My messages? That sounds scary!'"

**[Holds hands up reassuringly]**

> "Let me address this directly. Our #1 principle is: **Use content, don't store content.**"

**[Enactment: Showing data flow with hand gestures]**

> "Here's what happens when you type an email:"

> "Step 1: AI Keyboard **reads** your text temporarily — just to understand context."

> "Step 2: It **learns patterns** — like 'this user prefers formal tone in Gmail.' These patterns are saved."

> "Step 3: It **suggests** a completion."

> "Step 4: Your actual email content? **DELETED.** Never saved. Never sent anywhere."

**[Counts on fingers]**

> "We save: 'User likes formal greetings.'"

> "We DON'T save: 'Hi John, about the project deadline...'"

> "Your actual words are never stored."

---

**[MEMBER 4 points to security features]**

> "Plus, we have automatic protection."

> "Banking websites? Blocked automatically."

> "Password managers? Blocked automatically."

> "Login pages? Blocked automatically."

> "And if you ever want to pause AI Keyboard completely — just press Ctrl+Shift+P. One press to pause, one press to resume."

---

### [SLIDE 11: Implementation Status]

**[MEMBER 4 smiles]**

> "What have we actually built for this hackathon?"

_[Checks off items]_

> "✅ Core AI Engine with all four MCP tools"

> "✅ Chrome Browser Extension — works on all websites"

> "✅ Three-layer speed system — cache, local, cloud"

> "✅ Context detection — knows which app you're in"

> "✅ Personalization — learns your style automatically"

> "We didn't just make a plan. We built a working product."

---

### [SLIDE 12: Value Proposition]

> "So what makes AI Keyboard special? Six things."

_[Points to each as slide animates]_

> "**Universal** — Works everywhere. Browser. Desktop. Any app."

> "**Contextual** — Adapts to each app automatically."

> "**Fast** — Under 100 milliseconds for most suggestions."

> "**Personal** — Learns your style without configuration."

> "**Private** — Your content is never stored."

> "**Extensible** — MCP-compatible, plugs into Frai ecosystem."

---

### LIVE DEMO

**[MEMBER 4 moves to demo laptop]**

> "Enough talking. Let me SHOW you."

**[Opens Gmail Compose]**

> "I'm opening Gmail. Watch the bottom of my screen — that's where AI Keyboard suggestions appear."

_[Types slowly]_ "Hi John, I wanted to follow up on our conversation from"

**[Suggestion tooltip appears: "yesterday about the project timeline."]**

> "See that? It suggested a natural completion. Let me accept it."

_[Presses Ctrl+Enter or Tab]_

> "Boom. Text inserted. Now let me switch to Slack."

**[Opens Slack]**

_[Types]_ "Hey team, quick"

**[Suggestion appears: "update on the project 🚀"]**

> "Notice the emoji! AI Keyboard knows Slack is casual, so it suggests friendly language with emoji."

> "And if I don't like a suggestion, I just press Escape or click the X."

_[Shows dismissing suggestion]_

> "That's AI Keyboard in action. It's that simple."

---

### [SLIDE: Thank You]

**[All 4 members gather together]**

**MEMBER 1:** "AI Keyboard — making every keystroke intelligent."

**MEMBER 2:** "One brain, many clients. Universal AI assistance."

**MEMBER 3:** "Fast, contextual, personal."

**MEMBER 4:** "And privacy-first. Your content stays yours."

**ALL TOGETHER:** "Thank you! We're happy to answer any questions."

---

# 📋 Production Checklist

### Before Recording

- [ ] PPT slides loaded and tested
- [ ] Demo laptop ready with extension installed
- [ ] Server running (`python main.py`)
- [ ] Gmail and Slack tabs open for demo
- [ ] Each member knows their cue to speak

### Slide-to-Speaker Mapping

| Slide | Speaker  | Topic              |
| ----- | -------- | ------------------ |
| 1     | Member 1 | Problem Statement  |
| 2     | Member 1 | Solution Overview  |
| 3     | Member 2 | Architecture       |
| 9     | Member 2 | MCP Explanation    |
| 10    | Member 2 | Tech Stack         |
| 4     | Member 3 | Speed System       |
| 5     | Member 3 | Context Detection  |
| 6     | Member 3 | Voice Integration  |
| 7     | Member 3 | Personalization    |
| 8     | Member 4 | Privacy & Security |
| 11    | Member 4 | Implementation     |
| 12    | Member 4 | Value + Demo       |

### Enactment Moments

1. **Member 1:** Pretend typing and switching apps (Gmail/Slack confusion)
2. **Member 1:** Copy-paste ChatGPT frustration act
3. **Member 2:** USB-C cable analogy with hand gestures
4. **Member 3:** Switching apps showing tone change
5. **Member 3:** Speaking voice command for email
6. **Member 4:** Hand gestures showing data flow (read → learn → delete)
7. **Member 4:** Live demo typing in Gmail and Slack

### Timing Guide

| Member   | Duration | Cumulative |
| -------- | -------- | ---------- |
| Member 1 | 3 min    | 3 min      |
| Member 2 | 3 min    | 6 min      |
| Member 3 | 3 min    | 9 min      |
| Member 4 | 3 min    | 12 min     |

---

# 💡 Tips for Great Delivery

1. **Speak slowly** — Especially during technical explanations
2. **Pause after key points** — Let information sink in
3. **Make eye contact with camera** — Creates connection
4. **Use hand gestures** — Makes explanation visual
5. **Practice handoffs** — "Now [Name] will explain..." should be smooth
6. **Demo is the star** — Spend time making demo perfect
7. **End with tagline** — "Making every keystroke intelligent"
