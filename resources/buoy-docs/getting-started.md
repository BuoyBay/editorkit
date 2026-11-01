---
title: "EditorKit getting started"
summary: "Install EditorKit and try a complete usage example."
category: "Guides"
order: 10
keywords: ["editor", "document", "UTF-8", "UTF-16", "undo"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Copy `editorkit-0.1.0.buipkg` into the consumer's `packages/` directory and import `editorkit`.

## Edit using a UTF-16 position

Convert externally supplied positions through the document before constructing byte edits. This example replaces one emoji, then restores the initial saved text with undo.

```buoy
Import editorkit

Sub Main()
    Dim document As New EditorDocument("Hello 😀\r\nBuoy\n")
    Dim startByte As Integer = document.PositionToByte(0, 6)
    Dim endByte As Integer = document.PositionToByte(0, 8)
    document.ReplaceRange(startByte, endByte, "🌍")
    Print document.Text
    Print "Changed version: " + String.From(document.Version)
    Print "Dirty: " + String.From(document.IsDirty)
    document.Undo()
    Print "Restored: " + document.LineText(0)
    Print "Dirty after undo: " + String.From(document.IsDirty)
End Sub
```

## Apply a transaction

Use `ApplyEdits` when several replacements must succeed together. Every `EditorEdit` addresses the original text. Input order is arbitrary; the document sorts edits and rejects overlapping ranges or duplicate start positions. Adjacent edits with distinct starts are allowed. A failing transaction leaves text, version, history, and dirty state unchanged.

`MarkSaved()` records the current baseline only after the host successfully saves it. It does not write a file. Byte-identical edits preserve the version and redo history. A new content-changing transaction clears redo. Histories retain full text snapshots, so choose `historyLimit` for the size of your documents; zero disables undo.

[Package overview](index.md) · [API reference](api.md)
