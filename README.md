# claude-tmux

`claude-tmux` runs [Claude Code](https://docs.claude.com/en/docs/claude-code) conversations inside tmux. Claude keeps working when you close the terminal or lose your SSH connection, and you can return to a conversation later, from any folder, by its name.

## Why

`claude-tmux` follows one simple rule: each folder is a project you work on with Claude. You refer to the project or the conversation by name, from anywhere, and the script opens tmux in the right folder.

This helps because `claude --resume` only finds a conversation when you are in the folder where it was created. `claude-tmux` reads Claude Code's history in `~/.claude/projects`, finds that folder, opens tmux there and resumes the conversation.

## Requirements

- bash
- tmux
- python3 (standard library only)
- Claude Code (`claude`)

## Installation

```bash
curl -fsSL https://raw.githubusercontent.com/lucianovk/claude-tmux/main/claude-tmux -o ~/.local/bin/claude-tmux
chmod +x ~/.local/bin/claude-tmux
```

Any folder on your `PATH` works in place of `~/.local/bin`.

## Usage

Refer to a project by its folder name, or name a conversation inside Claude Code with `/rename NAME` (or start it with `claude --name NAME`). Then:

| Command | What it does |
|---|---|
| `claude-tmux start [NAME]` | opens the conversation in tmux and attaches |
| `claude-tmux detach [NAME]` | leaves tmux; Claude keeps running |
| `claude-tmux attach [NAME]` | goes back to the tmux session |
| `claude-tmux stop [NAME]` | closes the tmux session and Claude |
| `claude-tmux status [NAME]` | shows whether the session exists and is attached |
| `claude-tmux list` | lists tmux sessions |
| `claude-tmux conversations` | lists named conversations with their folder and ID |

NAME can be, in order of precedence:

| NAME | What `start` does |
|---|---|
| a conversation name (`/rename` or `--name`) | resumes that conversation |
| a conversation ID | resumes that conversation |
| the name of a folder where you have used Claude | resumes that folder's most recent conversation |
| a folder path (contains `/`) | the same; also works for a project with no conversations yet |

The tmux session takes NAME, or the folder name when NAME is a path. When a name is ambiguous, the script uses the most recent match and prints a warning; pass the ID to pick another conversation.

Without NAME, `start` uses the current folder and runs `claude --continue`, which resumes the folder's most recent conversation or starts a new one. The tmux session is then named after the folder.

Inside tmux, Ctrl+B followed by D also detaches.

### Example

```bash
claude-tmux start ~/projects/analysis   # new project: opens Claude in tmux
# Ctrl+B, D to leave; Claude keeps running
claude-tmux attach analysis             # back to the session
claude-tmux stop analysis               # close everything
claude-tmux start analysis              # another day, from any folder: resumes the last conversation
```

## Caveats

- Close a conversation before resuming it in tmux. If the same conversation is open in two processes, both write to the same history.
- The script relies on Claude Code's internal history format (`.jsonl` files in `~/.claude/projects`), which may change in future versions.

## Environment variables

| Variable | Effect |
|---|---|
| `CLAUDE_TMUX_DIR` | forces the working folder used by `start` |
| `CLAUDE_CONFIG_DIR` | Claude Code's configuration folder (default: `~/.claude`) |

## License

MIT. This is a personal project, not affiliated with Anthropic.
