# EditorKit · 0.1.0

A pure-Buoy document model for an IDE, code editor or refactoring tool. It connects
UTF-8 byte offsets used by parsers with UTF-16 positions used by an LSP client,
and groups text changes into atomic operations with undo/redo and dirty tracking.

Copy `editorkit-0.1.0.buipkg` into a project's `packages/` directory and use
`Import editorkit`. The package needs no native library or other package.
See [the console example](../examples/editorkit_demo.bui) for complete usage.

## Document API

| API | Behavior |
| --- | --- |
| `EditorDocument(content = "", historyLimit = 100)` | Validates text; initial version is 1 and the content starts saved. A zero history limit disables undo. Negative limits reject. |
| `Text`, `ByteLength`, `LineCount`, `Version` | Read-only current document information. |
| `LineText(line)`, `LineStartByte(line)`, `LineEndByte(line)` | Line text excludes its terminator; byte ends are exclusive. |
| `PositionToByte(line, character)` | Convert a zero-based UTF-16 code-unit position into a UTF-8 byte offset. |
| `ByteToPosition(byteOffset)` | Return an immutable `EditorPosition` with `Line` and `Character` properties. |
| `ReplaceRange(startByte, endByte, replacement)` | Replace a half-open byte range as one transaction. Returns whether content changed. |
| `ApplyEdits(edits)` | Atomically apply an array of immutable `EditorEdit(startByte, endByte, replacement)` objects. All ranges address the original content. |
| `Undo()`, `Redo()` | Restore a transaction snapshot, returning `False` when unavailable. |
| `CanUndo`, `CanRedo`, `IsDirty` | Read-only editing state. |
| `MarkSaved()` | Record current content as the saved baseline; no disk I/O. |
| `ClearHistory()` | Drop undo and redo snapshots without changing content or saved state. |

## Coordinates and transactions

Every coordinate is zero-based. Byte ranges are half-open. LF, CRLF and lone CR
are recognized as line endings and preserved byte-for-byte. Even an empty
document has one line; a trailing line ending adds an empty final line. A line's
`LineEndByte` points before its terminator.

UTF-16 positions count a supplementary codepoint such as an emoji as two code
units. Combining marks count separately from their base character. These are
codepoint/code-unit coordinates, not grapheme-cluster or display-column positions.
Offsets inside a UTF-8 codepoint, UTF-16 surrogate pair or CRLF pair reject.
A byte offset at the beginning of a line terminator maps to the preceding line's
end; the next line begins after the complete terminator. A line-end position
maps to the beginning of that line's terminator or to the document end.

Positions beyond a line's text reject rather than clamp. This is a strict
conversion API: an LSP integration can explicitly clamp externally supplied
positions if its protocol operation calls for that behavior.

A batch may arrive in any order. EditorKit sorts by byte start, then rejects
overlapping ranges and multiple changes at the same start, including ambiguous
same-position insertions. Adjacent ranges with distinct starts are allowed.
All edits succeed together or leave the content, version, dirty state and
history unchanged. The transaction limit is 4096 edits. Malformed UTF-8 input
rejects with an argument or encoding exception.

Each content-changing transaction, undo and redo advances `Version` monotonically.
An empty batch or byte-identical replacement returns `False`, leaves the version
unchanged and preserves redo history. A new content-changing transaction clears
redo. Dirty state compares the exact current content with the last `MarkSaved`
content, so undoing back to the saved content becomes clean. Saving does not
change the document version.

## Host recovery and ownership

`EditorDocument` is an in-memory document, not a file or workspace revision.
Convert positions and construct every edit against the same `Text` snapshot.
If the host receives edits asynchronously, check its current item revision before
applying them. A detached `EditorDocument` cannot detect that another buffer or
disk file changed after the snapshot was taken.

An invalid coordinate, ambiguous batch or malformed replacement raises an
exception. The document keeps its prior text, version, saved baseline and
undo/redo history. Show the exception to the caller, refresh the current text
and positions, and build a new transaction against that text. A `False` result
means the batch made no content change; it does not report an error. `MarkSaved`
only updates the baseline after the host has completed its own save operation.

When using `ProjectWorkspace`, it owns revision checks, item history and save
state. Build edits from `ReadItem(id).Text` and pass that item's `Version` to
`ApplyHostEdits`; use the workspace's `Undo`, `Redo` and `MarkSaved` for that
item. A temporary `EditorDocument` can convert positions or preview edits, but
its history and saved baseline do not update the workspace.

## Storage and limits

This is a contiguous in-memory text model. Changes build a new string and line
index, and history retains at most `historyLimit` full content snapshots. Batch
sorting uses insertion sort; the explicit edit-count bound limits its worst
case. Byte-to-position lookup first finds the line with a binary search, then
walks that line's codepoints. Position-to-byte walks the requested line.

Confine each document to one thread or synchronize access externally. This
package does not provide a rope, grapheme-aware cursor movement, multi-caret UI,
syntax colors, disk persistence, collaborative editing or automatic LSP version
synchronization. It is an independently reusable model beneath those features.

## Verification


Maintainers run the private validation suite before release. See the repository README for public build instructions.

