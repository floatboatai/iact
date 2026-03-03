# IACT (Inline Action-Clicked Text) 协议规范

## 1. 简介 (Introduction)

**IACT (Inline Action-Clicked Text)** 是一种专为 AI Agent 和 LLM 对话界面设计的超轻量级、基于 Markdown 的行内交互协议。

在传统的 Chat 界面中，当 AI 需要用户做出选择或提供下一步建议时，通常会采用“列表式”呈现（例如：“您可以：1. 这样做；2. 那样做”）或渲染一组独立的 Block 按钮。这种方式不仅打断了对话的自然流畅性，还增加了用户的认知和操作摩擦。

IACT 协议通过扩展标准的 Markdown 链接语法，将**可交互的动作锚点**直接无缝嵌入到自然语言的句子中，真正实现了“阅读即操作”、“所见即发送”，从而极大提升了人机交互的顺滑度与沉浸感。

## 2. 核心理念 (Core Philosophy)

- **行内优于块状 (Inline over Block)**：操作选项应当是自然语言句子的有机组成部分，而不是游离于文本之外的 UI 组件。
- **零操作摩擦 (Zero Friction)**：所见即所得。用户点击文本，即刻触发意图流转。
- **优雅降级 (Seamless Degradation)**：在不支持 IACT 协议的普通 Markdown 渲染器中，它仅仅会被渲染为一个带有 `!指令` 目标地址的普通死链接，不会破坏文本的结构和可读性。
- **安全与可控 (Security & Constraints)**：不支持执行系统脚本或跳转任意外部隐蔽链接，一切输入皆表现为用户的显式意图表达。

## 3. 语法规范 (Syntax Specification)

IACT 完全复用并扩展了标准的 Markdown 链接语法。

**标准格式**：
```markdown
[显示文本](!指令)
```

**解析规则**：
- `[` 与 `]` 之间包裹的是**向用户展示的自然语言文本**，这也是点击后将会被处理的 Payload 字符串。
- `(` 与 `)` 之间，必须以感叹号 `!` 开头，紧跟协议支持的指令字。
- **严禁**在 `()` 内携带任何额外的 URL、参数、脚本或其他复杂格式。

## 4. 支持的指令集 (Supported Directives)

IACT 协议在设计上是**开放且可无限扩展的**。开发者可以根据自身 Agent 的业务场景和客户端能力，自由扩展定义任何以 `!` 开头的自定义指令（例如 `!open_file`, `!apply_code` 等）。

然而，为了保证最大的通用性、跨平台兼容性以及最低的用户认知负担，**协议官方核心推荐两种最基础、最通用的交互指令**。我们期待各大客户端和平台能够**统一实现这两种基础指令**，从而共同提升所有用户在 AI 交互中的基础体验。

### 4.1 直接发送 (`!send`)
- **定义**：将方括号内的“显示文本”作为用户的下一轮对话输入，**直接、立即**发送给 AI。
- **场景**：适用于无需用户额外补充信息、可以直接执行的明确决策或下一步动作。
- **示例**：
  ```markdown
  我可以为您 [深入调研当前市场](!send) 或是直接 [生成一份行业研报草稿](!send)。
  ```

### 4.2 补充代入 (`!add`)
- **定义**：将方括号内的“显示文本”**代入（填充）**到用户的输入框中，但不立即发送。等待用户补充个性化需求或修改后，由用户主动点击发送（二次确认）。
- **场景**：适用于需要用户提供额外参数、细节，或是做出关键修改、或者需要用户二次确认的场景。
- **示例**：
  ```markdown
  你可以[补充更多细节](!add)，比如告诉我你需要哪些[特别的视觉样式](!add)。
  ```

## 5. 最佳实践与反模式 (Best Practices & Anti-patterns)

为了发挥 IACT 的最大价值，开发者和 Prompt 工程师在设计 Agent 响应时应当遵循以下原则：

### ✅ 推荐做法 (Best Practices)
1. **融入语境**：所有的 IACT 锚点必须是自然语言句子的有机组成部分。
   > *“需要我帮你 [提出其他的解决方案](!send) 吗？”*
2. **文本精简且表意明确**：方括号内的文本应该就是一个完整的用户 Prompt 指令。
   > *“好的，接下来我可以 [采用网格布局生成高信息密度的PPT](!send)。”*

### ❌ 严禁的反模式 (Anti-patterns)
1. **禁止列表式呈现**：
   > ❌ *“你可以选择：1. [重新生成](!send) 2. [修改配色](!add)”*
2. **禁止参数化或携带 URL**：
   > ❌ *`[谷歌搜索](!add https://www.google.com)`*
   > ❌ *`[创建文件](!send touch index.js)`*
3. **不可滥用指令**：不得自行生造未经协议定义的指令类型（例如禁用 `!read_file`、`!run` 等带有强副作用或非标准协议的指令）。

## 6. 开发者实现指南 (Implementation Guide)

在前端渲染器（如 React + `react-markdown` 或 Vue）中实现 IACT 非常简单，只需拦截 Markdown Link 的渲染逻辑即可：

```javascript
// 伪代码示例：自定义 Link 组件渲染逻辑
function IACTLink({ href, children }) {
  // 检查是否为 IACT 协议格式
  if (href.startsWith('!')) {
    const directive = href.slice(1); // 获取指令，例如 'send' 或 'add'
    const payload = extractText(children); // 获取显示的文本

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

  // 降级为普通链接渲染
  return <a href={href}>{children}</a>;
}
```

## 7. 协议开源声明
本协议规范基于 MIT 协议完全开源。欢迎社区将其集成到各种 LLM 客户端、Agent 平台和 Chat UI 组件库中，共同推动 AI 人机交互范式的进化。