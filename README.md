# MarkdownToolkit

A reusable iOS / iPadOS editing toolkit for notes apps: continuous rich text, durable attachments, and zoomable Canvas boards (drawing / mind maps).

Built for **AnyNote** and available as a local Swift package for other host apps.

---

## Demo

<p align="center">
  <img src="Docs/demo-note.png" alt="Rich note: code, todo, table, audio, drawing" width="280" />
  &nbsp;&nbsp;
  <img src="Docs/demo-canvas.png" alt="Canvas board with handwriting and stickers" width="280" />
</p>

| | |
|:--|:--|
| **Note editor** | Code blocks, checklists, tables, audio, handwriting / drawing in one continuous note |
| **Canvas** | Dot-grid board, PencilKit strokes, stickers / widgets, zoom and pan |

---

## Supported platforms

| Platform | Support |
|:--|:--|
| **iOS** | ✅ iOS 17+ |
| **iPadOS** | ✅ (same package; adaptive toolbar / layout) |
| **macOS / watchOS / tvOS / visionOS** | ❌ Not supported (UIKit editor) |

**Build requirements:** Swift 6, iOS 17+.

### TestFlight (AnyNote)

Try the editor (powered by MarkdownToolkit) in the AnyNote beta:

**[Join TestFlight](https://testflight.apple.com/join/sXvaJgDs)**

1. Install [TestFlight](https://apps.apple.com/app/testflight/id899247664) on iPhone / iPad  
2. Open the link on your device → Accept → Install  

---

## Features

### Rich text editor

- One continuous document: text + images + tables + checklists + audio + drawings + Canvas previews
- Paragraph styles: **Title**, **Heading**, **Subheading**, **Body**, **Bullet**, **Numbered**, **Dashed**, **Checklist**, **Code Block**
- Character styles: **bold**, *italic*, underline, strikethrough, text color, highlight
- Keyboard accessory toolbar: styles, lists, links, indent, photo / camera / scan, tables, audio, drawing, Canvas, dismiss keyboard
- Host apps can add **custom toolbar items** (`EditorToolbarCustomItem` / `EditorBridge`)
- Find & replace
- Hashtag `#tag` detection and styling
- **RTFD** encode / decode for host-owned persistence

### Code block

- Rounded code cards with language header and Copy button
- Type `"""` / `'''` (optional language) then **Return** to create a block (works well on iPhone keyboards that lack `` ` ``)
- Return inside a block continues code; Return on the empty trailing line / tap below the card returns to Body
- Pasted fenced Markdown (`` ```swift ``) keeps the language label
- Basic syntax highlighting (Swift: keywords, types, strings, comments, attributes)

### Lists & checklists

- Bullet `•`, Numbered `1. 2. 3.`, Dashed `–`, Checklist (tap to toggle; strikethrough when done)
- Return on an empty list row exits the list back to Body

### Built-in attachments

| Type | Description |
|:--|:--|
| Images / photos | Fit to editor width, pinch to resize |
| Document scans | Host inserts as fitted images |
| Tables | Editable cells, inline preview |
| Checklists | Tap-to-toggle markers |
| Audio | Playback / edit chip for recordings |
| Drawing | Drawing preview blocks |
| Canvas / mind map | Board preview embedded in the note |

Hosts can add custom media types via `MediaAttachmentTemplate` (save/load tokens).

### Canvas boards

- Zoom / pan, paper-style grid, PencilKit strokes
- Text cards, mind nodes, connectors
- Stickers: SF Symbols, emoji, photos, custom collections (+ subject lift on iOS 17+)
- Paper / grid settings, export for preview / share
- Separate JSON persistence; the host owns file storage

---

## Slash commands

Type at the **start of a line**, then confirm with **Space** or **Return**. Each command has a full name and a short alias.

### Text styles

| Command | Alias | Result |
|:--|:--|:--|
| `/title` | `/t` | Title |
| `/heading` | `/h` | Heading |
| `/subheading` | `/sh` | Subheading |
| `/body` | `/p` | Body |
| `/code` | `/cb` | Code Block |

### Lists

| Command | Alias | Result |
|:--|:--|:--|
| `/bullet` | `/b` | Bullet |
| `/dash` | `/d` | Dashed |
| `/number` | `/n` | Numbered |
| `/check` | `/c` | Checklist |

### Insert

| Command | Alias | Result |
|:--|:--|:--|
| `/table` | `/tbl` | Table |
| `/image` | `/img` | Photo library image |
| `/camera` | `/cam` | Camera |
| `/scan` | `/doc` | Document scan |
| `/audio` | `/rec` | Audio recording |
| `/draw` | `/dr` | Drawing |
| `/canvas` | `/cv` | Canvas / mind map |
| `/link` | `/ln` | Link editor |

---

## Markdown shortcuts (typed)

| Input | Behavior |
|:--|:--|
| `"""` or `'''` (+ `swift`, `python`, …) then Return | Create a Code Block |
| `1. ` + text then Return | Numbered list (continues as `2.`, `3.`, …) |
| `- ` + text then Return | Dashed list |
| `#tag` | Inline hashtag styling — `#` / `##` / `###` are **not** auto-converted to headings (so hashtags keep working) |
| Bare URL / email paste | Becomes a link |
| Toolbar `Aa` | Title / Heading / Subheading / Body / Code |

---

## Copy / paste Markdown

The editor **converts pasted Markdown plain text** into note styles when it looks like Markdown:

| Markdown | In the editor |
|:--|:--|
| `#` / `##` / `###+` | Title / Heading / Subheading |
| `` ```lang `` … `` ``` `` | Code Block (+ language + highlighting) |
| `*`, `-`, `+` lists | Bullet |
| `1. ` lists | Numbered |
| `- [ ]` / `- [x]` | Checklist |
| `**bold**`, `*italic*`, `` `code` `` | Bold / italic / inline mono |
| `[label](url)` | Link |

Also:

- Paste **inside** a Code Block → plain monospace (Markdown is not parsed)
- Clipboard **images** / RTFD with images → fitted image attachments
- Casual text without clear Markdown structure → plain Body
- File import: `MarkdownImport.attributedString(from:)` / `looksLikeMarkdown(_:)`

---

## Custom toolbar (host)

```swift
let ai = EditorToolbarCustomItem(
    id: "ai",
    systemImageName: "sparkles",
    accessibilityLabel: "AI Assist",
    menu: [
        EditorToolbarCustomMenuEntry(
            id: "ai.continue",
            title: "Continue Writing",
            systemImageName: "text.append"
        )
    ],
    placement: .afterInsert
)

let bridge = EditorBridge(customItems: [ai])
// Handle EditorToolbarAction.custom("ai.continue") in the host.
```

---

## Persistence

- Rich text: **RTFD**
- Checklist / table / audio / drawing / Canvas / custom media → flattened tokens on save, restored as interactive attachments on load
- Canvas: separate JSON + media files (host owns storage: Core Data, Files, CloudKit, …)

---

## Local setup

```swift
dependencies: [
    .package(path: "../MarkdownToolkit")
]
```

Main source layout:

```
Sources/MarkdownToolkit/
  Editor/     TextView, Toolbar, Attachments, Formatting, Utilities
  Freeform/   Canvas board
  Stickers/   Sticker collections
  Shared/     Shared helpers
```

---

## Contact

- **Email:** [Huynhvohoangnam714@gmail.com](mailto:Huynhvohoangnam714@gmail.com)
- **WhatsApp:** scan the QR code below

<p align="center">
  <img src="Docs/whatsapp-qr.png" alt="WhatsApp QR" width="200" />
</p>

- **AnyNote TestFlight:** https://testflight.apple.com/join/sXvaJgDs
