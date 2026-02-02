# claude-config

Configuration repository for a Claude Code assistant.

## Prerequisites

### Claude Code CLI

#### macOS

```bash
# Install via npm
npm install -g @anthropic-ai/claude-code

# Or via Homebrew
brew install anthropic/tap/claude-code
```

#### Linux

```bash
# Install via npm
npm install -g @anthropic-ai/claude-code

# Or download the binary
curl -fsSL https://claude.ai/install.sh | sh
```

## Setup

### VS Code Integration

1. **Install the Claude Code extension**

   Open VS Code and install the Claude Code extension:
   - Press `Cmd+Shift+X` (macOS) or `Ctrl+Shift+X` (Linux) to open Extensions
   - Search for "Claude Code"
   - Click Install

   Or install from the command line:
   ```bash
   code --install-extension anthropic.claude-code
   ```

2. **Configure the extension**

   Open VS Code settings (`Cmd+,` or `Ctrl+,`) and search for "Claude Code" to configure:
   - API key (or sign in via Anthropic account)
   - Default model
   - Other preferences

3. **Open Claude Code panel**

   - Press `Cmd+Shift+P` (macOS) or `Ctrl+Shift+P` (Linux)
   - Type "Claude Code: Open"
   - Or use the keyboard shortcut `Cmd+Escape` (macOS) or `Ctrl+Escape` (Linux)

4. **Link this configuration (optional)**

   To use this repository's configuration with VS Code:
   ```bash
   # Clone this repo
   git clone https://github.com/<your-username>/claude-config.git

   # Symlink to your home directory
   ln -s $(pwd)/claude-config ~/.claude
   ```

   VS Code's Claude Code extension will automatically pick up settings from `~/.claude/`.

### CLI Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/<your-username>/claude-config.git
   ```

2. Symlink to your home directory for global config:
   ```bash
   ln -s $(pwd)/claude-config ~/.claude
   ```

   Or copy to a specific project's `.claude/` directory for project-specific config.

## Structure

```
claude-config/
├── settings.json    # Claude Code settings
├── commands/        # Custom slash commands
└── hooks/           # Event hooks
```

## Usage

### Settings

Edit `settings.json` to configure Claude Code behavior. See the [Claude Code documentation](https://docs.anthropic.com/en/docs/claude-code) for available options.

### Custom Commands

Add custom slash commands in the `commands/` directory. Each command is a markdown file that defines the command's behavior.

#### Available Commands

| Command | Description |
|---------|-------------|
| `/embedded` | Embedded development guidelines - memory management, error handling, Zephyr patterns |

### Hooks

Configure event hooks in the `hooks/` directory to run shell commands in response to Claude Code events.

## Related Projects

- [claude-mcps](../claude-mcps/) - MCP servers (embedded-probe, etc.)
