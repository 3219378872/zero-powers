# Installing zero-powers for Codex

## Prerequisites
- Codex CLI or Codex IDE integration
- Git

## Installation

### Referenced by an existing Codex plugin repository

This repository is the plugin package, not the marketplace catalog. Keep the
marketplace entry in your existing Codex plugin repository and point it at the
location where this plugin is checked out, for example:

```json
{
  "name": "zero-powers",
  "source": {
    "source": "local",
    "path": "./plugins/zero-powers"
  },
  "policy": {
    "installation": "AVAILABLE",
    "authentication": "ON_INSTALL"
  },
  "category": "Developer Tools"
}
```

Clone or copy this repository to the path referenced by that external
marketplace entry.

### As a direct local plugin

Clone or copy this repository into a Codex plugin location that preserves the
plugin root, including:

```text
.codex-plugin/plugin.json
skills/
```

## Activation

The Codex manifest exposes the `skills/` directory. Codex can load the
appropriate go-zero domain skill when working with:
- `.api` files (REST API definitions)
- `.proto` files (gRPC service definitions)
- `internal/handler/`, `internal/logic/`, `internal/svc/` directories
- go-zero `go.mod` dependencies by zeromicro

The `agents/` files are kept for platforms that support agent markdown files.
They are not declared as Codex marketplace capabilities in this version.

## Verification

Ask Codex: "What go-zero skills are available?" to confirm successful installation.
