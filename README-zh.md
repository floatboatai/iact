# IACT (Inline Action-Clicked Text) 协议

<div align="center">

**一种专为 AI Agent 设计的超轻量级行内交互协议**

[English](./README.md) | 简体中文

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

</div>

## 📖 简介

**IACT (Inline Action-Clicked Text)** 是一种基于 Markdown 的行内交互协议，专为 AI Agent 和 LLM 对话界面设计。

传统 Chat 界面中，AI 提供选项时通常采用列表或按钮块，打断对话流畅性。IACT 通过扩展 Markdown 链接语法，将**可交互的动作锚点**直接嵌入自然语言句子中，实现"阅读即操作"。

## ✨ 核心特性

- 🎯 **行内优于块状** - 操作选项是自然语言的有机组成部分
- ⚡ **零操作摩擦** - 点击文本即刻触发意图流转
- 🔄 **优雅降级** - 在普通 Markdown 渲染器中显示为普通链接
- 🔒 **安全可控** - 不支持执行脚本或跳转隐蔽链接
- 🔧 **无限扩展** - 开发者可根据业务场景自由定义自定义指令

## 🚀 快速开始

### 基础语法

```markdown
[显示文本](!指令)
```

### 核心指令

#### `!send` - 直接发送
将文本作为用户输入直接发送给 AI：

```markdown
我可以为您 [深入调研当前市场](!send) 或是直接 [生成一份行业研报草稿](!send)。
```

#### `!add` - 补充代入
将文本填充到输入框，等待用户补充后发送：

```markdown
你可以[补充更多细节](!add)，比如告诉我你需要哪些[特别的视觉样式](!add)。
```

## 📸 示例演示

下图展示了 IACT 协议在实际对话中的应用效果：

![IACT 协议示例](./assets/iact-demo-zh.jpg)

在这个示例中，AI 在回复用户时自然地嵌入了多个可交互的文本锚点：

- **`[生成一个完整的 PPT 大纲](!send)`** - 使用 `!send` 指令，用户点击后会立即将这段文本作为新的对话输入发送给 AI，无需手动输入
- **`[调整某个具体章节的内容](!add)`** - 使用 `!add` 指令，点击后会将文本填充到输入框中，用户可以继续补充具体要调整哪个章节

这种设计的优势在于：
1. **流畅性**：交互选项完全融入自然语言句子，不打断阅读体验
2. **直观性**：用户一眼就能看到可以做什么，无需理解复杂的按钮布局
3. **高效性**：点击即可触发动作，减少了"思考→输入→发送"的操作步骤

## 🔗 相关项目

- [Selfware](https://github.com/floatboatai/selfware) - IACT 协议的实际应用场景和实现示例

## 📚 完整规范

详细的协议规范请参阅：
- [中文规范](./IACT_Protocol_Specification.md)
- [English Specification](./IACT_Protocol_Specification_EN.md)

## 💻 实现指南

在前端渲染器中实现 IACT 非常简单：

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

## 🔧 协议扩展

IACT 协议在设计上是**开放且可无限扩展的**。开发者可以根据自身 Agent 的业务场景和客户端能力，自由扩展定义任何以 `!` 开头的自定义指令。

### 扩展示例

除了核心的 `!send` 和 `!add` 指令，你可以定义更多符合业务需求的指令：

```markdown
<!-- 文件操作 -->
[打开配置文件](!open_file:config.json)

<!-- 代码应用 -->
[应用这段代码](!apply_code)

<!-- 工作流触发 -->
[启动部署流程](!trigger_workflow:deploy)

<!-- 多媒体交互 -->
[播放演示视频](!play_media:demo.mp4)
```

### 实现自定义指令

在你的客户端实现中，只需扩展指令处理逻辑：

```javascript
function IACTLink({ href, children }) {
  if (href.startsWith('!')) {
    const [directive, ...params] = href.slice(1).split(':');
    const payload = extractText(children);

    const handleClick = (e) => {
      e.preventDefault();

      // 核心指令
      if (directive === 'send') {
        chatInterface.sendMessage(payload);
      } else if (directive === 'add') {
        chatInterface.appendToInputBox(payload);
      }
      // 自定义指令
      else if (directive === 'open_file') {
        fileSystem.openFile(params[0]);
      } else if (directive === 'apply_code') {
        codeEditor.applyCode(payload);
      } else if (directive === 'trigger_workflow') {
        workflowEngine.trigger(params[0]);
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

### 扩展原则

在扩展自定义指令时，建议遵循以下原则：

1. **语义清晰**：指令名称应直观表达其功能
2. **参数简洁**：使用 `:` 分隔指令和参数，保持语法简洁
3. **安全优先**：避免执行任意代码或访问敏感资源
4. **向后兼容**：确保自定义指令不与核心指令冲突

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 开源协议

本协议基于 [MIT License](./LICENSE) 完全开源。

---

<div align="center">
Made with ❤️ by <a href="https://floatboat.ai">FloatBoat AI</a>
</div>
