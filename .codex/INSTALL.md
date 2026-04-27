# Installing zero-powers for OpenAI Codex

## Prerequisites
- Codex CLI or Codex IDE integration
- Git

## Installation

```bash
# Clone as Codex plugin
git clone https://github.com/zeromicro/zero-powers.git ~/.codex/plugins/zero-powers
```

## Configuration

Add to your Codex plugin configuration:

```json
{
  "plugins": {
    "zero-powers": {
      "enabled": true,
      "skills": ["all"],
      "agents": ["all"]
    }
  }
}
```

## Activation

After installation, Codex will automatically load the appropriate go-zero domain skill when working with:
- `.api` files (REST API definitions)
- `.proto` files (gRPC service definitions)
- `internal/handler/`, `internal/logic/`, `internal/svc/` directories
- go-zero `go.mod` dependencies by zeromicro

Agents are invoked manually: `@gozero-architect`, `@gozero-reviewer`, `@gozero-best-practices`.

## Verification

Ask Codex: "What go-zero skills are available?" to confirm successful installation.
