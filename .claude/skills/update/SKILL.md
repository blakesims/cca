---
name: update
description: Checks for new prompts from the instructor and runs them, then offers to customize the student's setup
---

# Update

## Process

1. Read `.completed` in this directory (create it if missing). This lists prompt filenames already run, one per line.

2. List all files in `prompts/todo/`. Check each against `.completed`.

3. If there ARE pending prompts:
   - Tell the student how many updates are pending.
   - Run them in numeric order (001 before 002, etc).
   - After each completes, append its filename to `.completed`.
   - Summarize what was done.

4. If `changelog.md` has entries newer than `.local-version`, show what changed in plain English.

5. Update `.local-version` to match `version.txt`.

6. If there are NO pending prompts (or after running them):
   - Tell the student they're up to date, show version from `version.txt`.
   - Ask: "Want to customize anything?" and suggest:
     - Add or remove commands from your help sheet
     - Change your command name
     - Add a shell alias
     - Ask anything about the terminal
   - If they want to edit help.sh, read it first, make changes, test.
   - If they rename their command, update the function in their rc file and re-source.
