# IACT (Inline Action-Clicked Text) Protocol

<div align="center">

**An ultra-lightweight inline interaction protocol designed for AI Agents**

English | [简体中文](./README-zh.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

</div>

## 📖 Introduction

**IACT (Inline Action-Clicked Text)** is a Markdown-based inline interaction protocol designed specifically for AI Agents and LLM chat interfaces.

In traditional chat interfaces, when AI provides options, it typically uses lists or button blocks, disrupting conversation flow. IACT extends Markdown link syntax to embed **interactive action anchors** directly into natural language sentences, achieving "reading is interacting".

## ✨ Core Features

- 🎯 **Inline over Block** - Action options are organic parts of natural language
- ⚡ **Zero Friction** - Click text to instantly trigger intent flow
- 🔄 **Seamless Degradation** - Displays as normal links in standard Markdown renderers
- 🔒 **Security & Constraints** - No script execution or hidden link jumping

## 🚀 Quick Start

### Basic Syntax

```markdown
[Display Text](!directive)
```

### Core Directives

#### `!send` - Send Directly
Send text as user input directly to AI:

```markdown
I can [conduct a deep market research](!send) for you, or directly [generate an industry report draft](!send).
```

#### `!add` - Add to Input
Fill text into input box, waiting for user to supplement before sending:

```markdown
You can [provide more details](!add), for example, tell me what [specific visual styles](!add) you need.
```

## 📸 Example Demo

The image below demonstrates the IACT protocol in action:

![IACT Protocol Example](./assets/iact-demo.jpg)

In this example, the AI naturally embeds multiple interactive text anchors in its response:

- **"[生成一个完整的 PPT 大纲](!send)"** (Generate a complete PPT outline) - Uses the `!send` directive. When clicked, this text is immediately sent as a new conversation input to the AI, without manual typing
- **"[调整某个具体章节的内容](!add)"** (Adjust specific section content) - Uses the `!add` directive. When clicked, the text is filled into the input box, allowing the user to continue specifying which section to adjust

The advantages of this design:
1. **Fluency**: Interactive options are fully integrated into natural language sentences without disrupting the reading experience
2. **Intuitiveness**: Users can see at a glance what they can do, without understanding complex button layouts
3. **Efficiency**: Clicking triggers actions immediately, reducing the "think → type → send" workflow

## 🔗 Related Projects

- [Selfware](https://github.com/floatboatai/selfware) - Practical application scenarios and implementation examples of IACT protocol

## 📚 Full Specification

For detailed protocol specification, please refer to:
- [English Specification](./IACT_Protocol_Specification_EN.md)
- [中文规范](./IACT_Protocol_Specification.md)

## 💻 Implementation Guide

Implementing IACT in front-end renderers is straightforward:

```javascript
function IACTLink({ href, children }) {
  if (href.startsWith('!')) {
    const directive = href.slice(1);
    const payload = extractText(children);

    const handleClick = (e) => {
      e.preventDefault();
      if (directive === 'send') {
        chatInterface.sendMessage(payload);
      } else if (directive === 'add') {
        chatInterface.appendToInputBox(payload);
      }
    };

    return (
      <a href="#" className="iact-anchor" onClick={handleClick}>
        {children}
      </a>
    );
  }

  return <a href={href}>{children}</a>;
}
```

## 🤝 Contributing

Issues and Pull Requests are welcome!

## 📄 License

This protocol is fully open-sourced under the [MIT License](./LICENSE).

---

<div align="center">
Made with ❤️ by <a href="https://floatboat.ai">FloatBoat AI</a>
</div>
