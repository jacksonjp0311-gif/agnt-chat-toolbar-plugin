# AGNT Chat Toolbar Plugin

Pinned, theme-aware action strip for AGNT assistant messages — **Regenerate**, **Copy**, **Share/Upload**, **Copy Conversation (α)**, and **Thumbs Up/Down** — designed to match AGNT’s neon palette across themes (including theme switching).

This project ships two deliverables:

1) **UI integration (recommended)**: a small patch that pins the toolbar under every assistant message in AGNT’s chat UI.
2) **Optional `.agnt` tool**: a widget tool (`chat-actions-strip`) that can be embedded in a response and can POST to webhooks.

> Note: Today, the AGNT marketplace “plugin” system (the `.agnt` format) extends tools/nodes/widgets. It does **not** automatically inject UI into the core chat renderer by itself. That’s why this repo includes a UI patch + adapter.

---

## Features

- **Pinned under assistant messages** (always visible)
- **Semi-translucent glass** + **darkens on hover**
- Uses **AGNT theme variables** (e.g. `--color-background-rgb`, `--color-text-muted`, etc.)
- Futuristic neon accent hover states using AGNT tokens (primary pink / secondary cyan / success green / warning yellow / indigo)
- Works across:
  - Main orchestrator chat
  - Unified chats (agent/workflow/tool/widget/artifact panels)

---

## Installation (AGNT UI patch)

### 1) Apply the patch

From your AGNT repo root:

<pre><code>git apply patches/agnt-chat-toolbar.patch</code></pre>

Files touched:
- <pre><code>frontend/src/views/Terminal/CenterPanel/screens/Chat/components/MessageItem.vue</code></pre>
- <pre><code>frontend/src/views/Terminal/CenterPanel/screens/Chat/Chat.vue</code></pre>
- <pre><code>frontend/src/views/_components/chat/UnifiedChatContainer.vue</code></pre>

### 2) Build the frontend

<pre><code>cd frontend
npm install
npm run build</code></pre>

### 3) Restart AGNT

Quit + relaunch the Electron app.

---

## Adapter (make any agent chat box support Regenerate)

The message component emits an event:

<pre><code>@assistant-action="onAssistantAction"</code></pre>

Payload:

<pre><code>{
  action: "regenerate" | "feedback",
  messageId: "&lt;assistant-message-id&gt;",
  vote?: "up" | "down"
}</code></pre>

Use the helper:

- <pre><code>adapter/wireAssistantToolbar.js</code></pre>

It provides:
- `findPreviousUserMessage(messages, assistantMessageId)`
- `regenerateFromAssistant({ messages, assistantMessageId, resendUserMessage })`

See: `adapter/README.md`

---

## Optional `.agnt` Tool: Chat Actions Strip Widget

Folder:
- <pre><code>agnt-plugin/chat-actions-strip</code></pre>

This is a self-contained AGNT tool that returns a renderable HTML strip.

### Build (inside an AGNT repo)

Copy the folder to:

<pre><code>backend/plugins/dev/chat-actions-strip</code></pre>

Then run:

<pre><code>node backend/plugins/build-plugin.js chat-actions-strip</code></pre>

Install in AGNT:

<pre><code>POST /api/plugins/install-file</code></pre>

(AGNT UI also supports plugin install from the Plugins screen.)

---

## Customization

- Change translucency: tweak alpha values in `.assistant-actions` (UI patch) or in the widget tool.
- Change accents: update `data-accent` mappings.
- Add new buttons: extend the markup and emit additional actions.

---

## License

MIT
