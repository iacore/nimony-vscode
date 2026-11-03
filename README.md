# Nimony for VSCode

Language support for **Nimony** — the NIF-based Nim successor — in VS Code and
its derivatives (VSCodium, Code - OSS).

It is a fork of [`aoughwl/nim2-nimony-lsp`](https://github.com/aoughwl/nim2-nimony-lsp)'s
VSCode client, repointed at [`aoughwl/aowllsp`](https://github.com/aoughwl/aowllsp),
the Language Server **written in Nimony**. Upstream's own `README` now calls
`nimony-lsp` deprecated in favour of `aowllsp`, so this client speaks to that
server instead: no Nim 2 build step, no `server/bin/nimony-lsp`.

## Features

- Diagnostics (errors / warnings / traces) on open and save
- Go to Definition and Find All References
- Hover — signature + doc comment
- Signature help (parameter hints while calling)
- Document highlight (occurrences of the symbol under the cursor)
- Rename (project-wide, with prepare-rename validation)
- Document symbols (outline / breadcrumbs) and Workspace symbol search
- Completion, including `.`-triggered member completion (fields + UFCS methods)
- Semantic tokens (type-aware highlighting, with declaration/readonly modifiers)
- Inlay type hints, folding ranges, and expand-selection ranges
- Call hierarchy (incoming / outgoing calls)
- Go to type definition / implementation
- Document links (imports), code lens (reference counts)
- Syntax highlighting for `.nim`, `.nims`, and `.nimony` files
- Status bar item showing the server state (starting / running / stopped)
- Commands: `Nimony: Restart Language Server`, `Nimony: Run File` (F6),
  `Nimony: Clear Compile Cache & Restart Server`

## Requirements

1. **`aowllsp`** — the language server. Build it from its repository
   (`bash build.sh` produces `bin/aowllsp`) and either put it on `PATH`, drop it
   next to this extension's `out/` directory, or point `nimony.serverPath` at
   it.
2. **`nimony`** — the compiler, on `PATH` or as `NIMONY_EXE`, or set
   `nimony.nimonyPath`. The server shells out to it for every check.

Optional siblings of `aowllsp` extend the feature set; each stays off when its
path is empty:

| Binary | Enables | Setting |
| --- | --- | --- |
| `aowllens` | symbols, completion, semantic tokens, inlay hints, call/type hierarchy | `nimony.aowllensPath` |
| `aowlsuggest` | syntax diagnostics and quick-fix code actions | `nimony.aowlsuggestPath` |
| `aowlfmt` | document and range formatting | `nimony.aowlfmtPath` |

## Settings

| Setting | Default | Description |
| --- | --- | --- |
| `nimony.serverPath` | `""` | Path to `aowllsp`. Empty = the extension's own directory, then `PATH`. |
| `nimony.nimonyPath` | `""` | Path to `nimony`. Empty = `NIMONY_EXE`, then `PATH`. |
| `nimony.aowllensPath` | `""` | Path to `aowllens`. Empty = disabled. |
| `nimony.aowlsuggestPath` | `""` | Path to `aowlsuggest`. Empty = disabled. |
| `nimony.aowlfmtPath` | `""` | Path to `aowlfmt`. Empty = disabled. |
| `nimony.extraPaths` | `[]` | Extra module search paths (`--path:`) for the compiler. |
| `nimony.daemonPath` | `""` | Optional `nimsem serve` binary for warm cross-module queries. Empty = disabled. |
| `nimony.trace.server` | `off` | LSP trace verbosity (`off` / `messages` / `verbose`). |

### Both Nimony and a Nim extension installed

An extension for Nim 2 also claims `.nim`. Whichever registers last wins the
association, so pin it in your settings:

```json
"files.associations": { "*.nim": "nimony" }
```

The `aowllsp` client attaches to files whose language is `nimony` or `nim`, so
navigation still works either way — only the grammar that highlights comes from
the association.

## Build

```sh
npm ci
npm run bundle          # esbuild -> out/extension.js
npx @vscode/vsce package --no-dependencies
```

That writes `nimony-<version>.vsix`; install it with
`code --install-extension nimony-<version>.vsix` (or the Extensions view →
`...` → **Install from VSIX...**).

## Troubleshooting

- **"Nimony language server not found"** — install `aowllsp`, put it on `PATH`,
  or set `nimony.serverPath` to an absolute path.
- Inspect traffic: set `nimony.trace.server` to `verbose` and open the
  **Nimony Language Server** output channel.
- Use **Nimony: Restart Language Server** (also triggered by clicking the status
  bar item) after rebuilding the server binary.

## Licence

MIT, as upstream. The client code is `aoughwl/nim2-nimony-lsp`'s; the changes
here are the server target, the tool-path settings, and the settings defaults.
