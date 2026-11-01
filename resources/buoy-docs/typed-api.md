---
title: "editorkit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["EditorDocument", "EditorEdit", "EditorPosition"]
dependencies: []
symbols: ["EditorDocument", "EditorEdit", "EditorPosition"]
related: ["api.md", "getting-started.md", "index.md"]
---

# editorkit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## EditorPosition

`Public Class EditorPosition`

A zero-based line/UTF-16 code-unit position, compatible with LSP's default encoding.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Line` | `Integer` | Yes | No | — | — |
| `Character` | `Integer` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(lineIndex As Integer, characterIndex As Integer)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `lineIndex` | `Integer` | `ByVal` | Required |
| `characterIndex` | `Integer` | `ByVal` | Required |

Returns: no value.

## EditorEdit

`Public Class EditorEdit`

A replacement of the half-open UTF-8 byte range [StartByte, EndByte).

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `StartByte` | `Integer` | Yes | No | — | — |
| `EndByte` | `Integer` | Yes | No | — | — |
| `Replacement` | `String` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(startByte As Integer, endByte As Integer, replacement As String)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `startByte` | `Integer` | `ByVal` | Required |
| `endByte` | `Integer` | `ByVal` | Required |
| `replacement` | `String` | `ByVal` | Required |

Returns: no value.

## EditorDocument

`Public Class EditorDocument`

A contiguous text document with transactional edits and bounded snapshot history.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Text` | `String` | Yes | No | — | — |
| `ByteLength` | `Integer` | Yes | No | — | — |
| `LineCount` | `Integer` | Yes | No | — | — |
| `Version` | `Integer` | Yes | No | — | — |
| `IsDirty` | `Boolean` | Yes | No | — | — |
| `CanUndo` | `Boolean` | Yes | No | — | — |
| `CanRedo` | `Boolean` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(content As String = "", historyLimit As Integer = 100)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `content` | `String` | `ByVal` | `""` |
| `historyLimit` | `Integer` | `ByVal` | `100` |

Returns: no value.

### MarkSaved

`Public Sub MarkSaved()`

No parameters.

Returns: no value.

### ClearHistory

`Public Sub ClearHistory()`

No parameters.

Returns: no value.

### LineStartByte

`Public Function LineStartByte(lineIndex As Integer) As Integer`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `lineIndex` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

### LineEndByte

`Public Function LineEndByte(lineIndex As Integer) As Integer`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `lineIndex` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

### LineText

`Public Function LineText(lineIndex As Integer) As String`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `lineIndex` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `String` |

### ByteToPosition

`Public Function ByteToPosition(byteOffset As Integer) As EditorPosition`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `byteOffset` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `EditorPosition` |

### PositionToByte

`Public Function PositionToByte(lineIndex As Integer, characterIndex As Integer) As Integer`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `lineIndex` | `Integer` | `ByVal` | Required |
| `characterIndex` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

### ReplaceRange

`Public Function ReplaceRange(startByte As Integer, endByte As Integer, replacement As String) As Boolean`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `startByte` | `Integer` | `ByVal` | Required |
| `endByte` | `Integer` | `ByVal` | Required |
| `replacement` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### ApplyEdits

`Public Function ApplyEdits(edits As Array Of EditorEdit) As Boolean`

All ranges refer to the original snapshot. Input order does not matter.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `edits` | `Array Of EditorEdit` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### Undo

`Public Function Undo() As Boolean`

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### Redo

`Public Function Redo() As Boolean`

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |
