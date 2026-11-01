---
title: "EditorKit"
summary: "Own text documents with strict Unicode positions and atomic undoable edits."
category: "Overview"
order: 0
keywords: ["editor", "document", "UTF-8", "UTF-16", "undo"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# EditorKit

EditorKit provides an in-memory document model beneath an editor or refactoring tool. It connects UTF-8 byte offsets to UTF-16 positions, applies atomic edit batches, and tracks undo, redo, revisions, and the saved-content baseline. It performs no filesystem I/O and has no native or package dependencies.

## Coordinate rules

Every coordinate is zero-based. Byte ranges are half-open. Position characters count UTF-16 code units, so an emoji can occupy two units. These are not grapheme clusters or visual columns. Offsets inside UTF-8 codepoints, UTF-16 surrogate pairs, or CRLF pairs reject instead of being rounded.

LF, CRLF, and lone CR are recognized and retained. An empty document has one line; a trailing terminator creates an empty last line. `LineText` excludes the terminator, while `LineEndByte` points just before it.

## Ownership

Each content-changing operation advances `Version`. Dirty state compares exact text against the last `MarkSaved()` baseline. Keep each document on one thread or synchronize access externally. This package does not supply a UI, rope, syntax colors, multi-caret interaction, collaborative editing, or automatic LSP version synchronization.

## Read next

- [Getting started](getting-started.md)
- [API reference](api.md)
