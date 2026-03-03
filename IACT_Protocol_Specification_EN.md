# IACT (Inline Action-Clicked Text) Protocol Specification

## 1. Introduction

**IACT (Inline Action-Clicked Text)** is an ultra-lightweight, Markdown-based inline interaction protocol designed specifically for AI Agents and LLM chat interfaces.

In traditional chat interfaces, when an AI needs the user to make a choice or suggests next steps, it typically presents a "list-style" output (e.g., "You can: 1. Do this; 2. Do that") or renders a group of independent block buttons. This approach not only disrupts the natural flow of the conversation but also increases cognitive load and interaction friction for the user.

The IACT protocol solves this by extending standard Markdown link syntax. It embeds **interactive action anchors** directly and seamlessly into natural language sentences, truly achieving "reading is interacting" and "what you see is what you send". This greatly enhances the smoothness and immersion of human-computer interaction.

## 2. Core Philosophy

- **Inline over Block**: Action options should be organic components of natural language sentences, not isolated UI components detached from the text.
- **Zero Friction**: What you see is what you get (WYSIWYG). The user clicks the text, instantly triggering the intent flow.
- **Seamless Degradation**: In standard Markdown renderers that do not support the IACT protocol, it will simply render as a normal dead link pointing to a `!directive` destination, preserving the text's structure and readability.
- **Security & Constraints**: It does not support executing system scripts or jumping to arbitrary hidden external links. All inputs are manifested as explicit intent expressions from the user.

## 3. Syntax Specification

IACT fully reuses and extends standard Markdown link syntax.

**Standard Format**:
```markdown
[Display Text](!directive)
```

**Parsing Rules**:
- The content between `[` and `]` is the **natural language text displayed to the user**. This is also the Payload string that will be processed upon clicking.
- The content between `(` and `)` MUST start with an exclamation mark `!`, immediately followed by a directive supported by the protocol.
- It is **strictly forbidden** to include any additional URLs, parameters, scripts, or other complex formats inside the `()`.

## 4. Supported Directives

By design, the IACT protocol is **open and infinitely extensible**. Developers can freely define any custom directives starting with `!` (e.g., `!open_file`, `!apply_code`) based on their specific Agent business scenarios and client capabilities.

However, to ensure maximum universality, cross-platform compatibility, and the lowest possible cognitive burden for users, **the official protocol core recommends two foundational and universal interaction directives**. We expect major clients and platforms to **uniformly implement these two basic directives**, thereby collectively improving the baseline experience for all users in AI interactions.

### 4.1 Send Directly (`!send`)
- **Definition**: Takes the "Display Text" inside the square brackets and **directly and immediately** sends it to the AI as the user's next conversational input.
- **Scenario**: Suitable for clear decisions or next actions that do not require additional information from the user and can be executed immediately.
- **Example**:
  ```markdown
  I can [conduct a deep market research](!send) for you, or directly [generate an industry report draft](!send).
  ```

### 4.2 Add to Input (`!add`)
- **Definition**: **Substitutes (fills)** the "Display Text" inside the square brackets into the user's input box, but does *not* send it immediately. It waits for the user to supplement personalized requirements or make edits, followed by the user actively clicking send (a secondary confirmation).
- **Scenario**: Suitable for scenarios that require the user to provide additional parameters, details, make crucial modifications, or require secondary confirmation from the user.
- **Example**:
  ```markdown
  You can [provide more details](!add), for example, tell me what [specific visual styles](!add) you need.
  ```

## 5. Best Practices & Anti-patterns

To maximize the value of IACT, developers and Prompt Engineers should follow these principles when designing Agent responses:

### ✅ Best Practices
1. **Blend into Context**: All IACT anchors must be organic parts of the natural language sentences.
   > *"Would you like me to [propose other solutions](!send)?"*
2. **Concise and Clear Text**: The text inside the square brackets should be a complete user Prompt instruction itself.
   > *"Alright, next I can [generate a high-density PPT using a grid layout](!send)."*

### ❌ Anti-patterns (Strictly Forbidden)
1. **No List-Style Presentation**:
   > ❌ *"You can choose: 1. [Regenerate](!send) 2. [Change color scheme](!add)"*
2. **No Parameters or URLs**:
   > ❌ *`[Google Search](!add https://www.google.com)`*
   > ❌ *`[Create file](!send touch index.js)`*
3. **Do Not Abuse Directives**: Avoid inventing non-standard directives not defined by the protocol (e.g., avoid using directives with strong side-effects or non-standard protocols like `!read_file`, `!run` unless explicitly managed by the specific client ecosystem).

## 6. Developer Implementation Guide

Implementing IACT in front-end renderers (such as React + `react-markdown` or Vue) is very straightforward. You only need to intercept the rendering logic of Markdown Links:

```javascript
// Pseudo-code Example: Custom Link Component Rendering Logic
function IACTLink({ href, children }) {
  // Check if it matches the IACT protocol format
  if (href.startsWith('!')) {
    const directive = href.slice(1); // Extract directive, e.g., 'send' or 'add'
    const payload = extractText(children); // Extract display text

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

  // Degrade to standard link rendering
  return <a href={href}>{children}</a>;
}
```

## 7. Open Source License
This protocol specification is fully open-sourced under the MIT License. The community is welcome to integrate it into various LLM clients, Agent platforms, and Chat UI component libraries to jointly drive the evolution of AI human-computer interaction paradigms.