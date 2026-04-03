# 001 — Initial Setup

Generate a terminal help system tailored to this student's machine.

## Step 1: Detect environment

Run ALL of these and note the results:

```
uname -s
echo $MSYSTEM
echo $SHELL
echo $0
echo $BASH_VERSION
echo $TERM
echo $LANG
echo $LC_ALL
command -v less
command -v glow
command -v bat
tput lines
tput cols
```

## Step 2: Generate help.sh

Create `help.sh` in this directory (the repo root, where CLAUDE.md lives).

### Platform rules

- macOS (Darwin): file manager command is `open .`
- Windows/Git Bash (MSYSTEM set, or uname shows MINGW/MSYS): file manager command is `explorer .`
- Linux: file manager command is `xdg-open .`

### Compatibility rules

- Shebang: `#!/usr/bin/env bash`
- Color variables: use `$'\033[...]'` syntax (dollar-sign single-quote). NOT `'\033[...]'`. Heredocs won't render escape sequences otherwise.
- Lowercasing: use `tr '[:upper:]' '[:lower:]'`, NOT `${var,,}` (requires Bash 4+, macOS ships Bash 3).
- Pager: `less -RFX` (-R color, -F skip pager if output fits, -X don't clear on exit). Fall back to `cat` if less is unavailable.

### Color rules

Only if `$TERM` is not "dumb" or empty:
- Section headers: bold cyan
- Command names: bold yellow
- Descriptions: light gray
- Examples: green
- Comments: dim gray
- Warnings: bold red

If `$TERM` is "dumb" or empty, set all color variables to empty strings.

### Display — two modes

1. `help.sh` (no args) — full cheat sheet piped through `less -RFX`. Student scrolls with arrows, quits with `q`.
2. `help.sh <topic>` — search by topic keyword, show matching sections only, also through `less -RFX`.

### Search system

Each section has a hidden `#TAGS:` line with comma-separated keywords. Search matches the user's query against tags (case-insensitive). Strip tag lines from display.

Example searches that must work:
- `help.sh folder` → pwd, cd, ls, mkdir, cp (folders), mv (folders), rm -r, open/explorer
- `help.sh delete` → rm section
- `help.sh file` → touch, cat, echo (to file), cp, mv, rm, grep
- `help.sh search` → grep
- `help.sh tips` → beginner tips

### Content — cover these commands, grouped by concept

- WHERE AM I: pwd
- MOVING AROUND: cd (into folder, up one level, home, absolute path)
- WHAT'S IN HERE: ls, ls -la
- MAKING FOLDERS: mkdir, mkdir -p
- MAKING FILES: touch
- READING FILES: cat
- WRITING TEXT: echo, > (write to file), >> (append)
- COPYING: cp, cp -r
- MOVING & RENAMING: mv (rename and move)
- DELETING: rm, rm -r, rm -i (warn: no undo!)
- SEARCHING: grep, grep -r
- OPEN IN FILE MANAGER: correct command for their OS
- CLEAR SCREEN: clear
- TIPS: Tab to autocomplete, Up arrow for history, Ctrl+C to cancel, q to exit help

For each command: bold name, plain-English description, 1-3 examples prefixed with `$`.

## Step 3: Make it executable

```
chmod +x help.sh
```

## Step 4: Ask what they want to call the command

Ask the student: "What do you want to call your help command? The default is `cca`. You could also use `help`, `h`, or anything you like."

- If they pick `help`, warn them: "Note: `help` is a built-in bash command. Your alias will override it, which is fine — you won't need the built-in. Just letting you know."
- If they just press enter or say "default", use `cca`.
- Store their choice in `.config` (a local gitignored file) as `COMMAND_NAME=<their choice>` so future updates know what they picked.

## Step 5: Add the shell function

Determine the correct rc file:
- zsh → `~/.zshrc`
- bash → `~/.bashrc` (also Git Bash)

Check if the rc file already contains a function for this tool (search for `# CCA — Claude Code Architects`). If it does, skip this step.

If not, append this function, replacing `__CCA_DIR__` with the absolute path to this directory and `__CMD__` with their chosen command name:

```bash

# CCA — Claude Code Architects
__CMD__() {
    local dir="__CCA_DIR__"
    case "${1:-}" in
        update) cd "$dir" && git pull && claude "/update" ;;
        *)      "$dir/help.sh" "$@" ;;
    esac
}
```

Then source the rc file so it works immediately.

## Step 6: Test

Run `./help.sh` and `./help.sh folder` to verify colors render and search works.

## Step 7: Tell the student

Show them a short summary of what was set up. Use their chosen command name (from `.config`) in the table:

| Command | What it does |
|---|---|
| `<cmd>` | Show the full terminal cheat sheet (arrows to scroll, q to quit) |
| `<cmd> folder` | Search for folder-related commands |
| `<cmd> delete` | Search for delete commands |
| `<cmd> tips` | Show beginner tips |
| `<cmd> update` | Pull latest updates + customize your setup |
