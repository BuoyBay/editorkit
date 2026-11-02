# Unicode text editing with transactions, undo and dirty tracking

A source-only document model for an IDE, code editor or refactoring tool. It connects
UTF-8 byte offsets used by parsers with UTF-16 positions used by an LSP client,
and groups text changes into atomic operations with undo/redo and dirty tracking.

Copy `editorkit-0.1.0.buipkg` into a project's `packages/` directory and use
`Import editorkit`. The package needs no native library or other package.
See [the console example](examples/editorkit_demo.bui) for complete usage.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add editorkit --git https://github.com/BuoyBay/editorkit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

```basic
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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer editorkit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
