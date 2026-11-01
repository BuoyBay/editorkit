---
title: "EditorKit API"
summary: "Public types and operations supplied by EditorKit."
category: "Reference"
order: 20
keywords: ["editor", "document", "UTF-8", "UTF-16", "undo"]
symbols: ["EditorDocument", "EditorPosition", "EditorEdit", "EditorDocument.PositionToByte", "EditorDocument.ByteToPosition", "EditorDocument.ApplyEdits", "EditorDocument.ReplaceRange", "EditorDocument.Undo", "EditorDocument.Redo", "EditorDocument.MarkSaved"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

## Document and state

| Symbol | Contract |
| --- | --- |
| `EditorDocument(content = "", historyLimit = 100)` | Validate initial text; start at version 1 and saved. Negative history limits reject. |
| `Text`, `ByteLength`, `LineCount`, `Version` | Read-only current content and revision. |
| `IsDirty`, `CanUndo`, `CanRedo` | Read-only editing state. |
| `MarkSaved()`, `ClearHistory()` | Set the saved baseline, or discard histories without changing text/baseline. |

## Coordinates

| Symbol | Contract |
| --- | --- |
| `LineText(lineIndex)` | Text without its terminator. |
| `LineStartByte(lineIndex)`, `LineEndByte(lineIndex)` | Half-open line content boundaries in UTF-8 bytes. |
| `PositionToByte(lineIndex, characterIndex)` | Strict UTF-16 position to byte conversion; out-of-line positions reject. |
| `ByteToPosition(byteOffset)` | Return `EditorPosition` with immutable `Line` and `Character`. |

The beginning of a line terminator maps to the preceding line's end. The following line begins after the complete terminator. The implementation binary-searches line starts, then walks codepoints within the requested line.

## Transactions

`EditorEdit(startByte, endByte, replacement)` exposes immutable `StartByte`, `EndByte`, and `Replacement`. `ApplyEdits(edits)` accepts at most 4096 edits against the original content. `ReplaceRange` is a single-edit transaction. Both return whether text changed. `Undo()` and `Redo()` return False when unavailable and advance the revision when they restore content.

Invalid coordinates/overlap reject with an argument exception; malformed UTF-8 can produce an argument or encoding exception. Changes build a new contiguous string and line index, while undo retains full snapshots. The host owns persistence and external synchronization.

Rejected batches preserve text, version, saved baseline and undo/redo history.
After a rejection, read the current text and rebuild coordinates against it.
`False` from `ApplyEdits` means no content changed. The host checks whether a
snapshot is stale before applying asynchronous edits and calls `MarkSaved` only
after its own save succeeds. In a `ProjectWorkspace` consumer, workspace item
revisions, undo/redo and saved state belong to the workspace; a temporary
`EditorDocument` used for position conversion does not synchronize those states.

[Getting started](getting-started.md) · [Package overview](index.md)


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
