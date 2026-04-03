# /update — Check for and run pending updates

This skill is triggered by `cca update` which runs `git pull` then `claude "/update"`.

## Process

1. Read `.completed` in this directory (create it if it doesn't exist). This file lists prompt filenames that have already been run, one per line.

2. List all files in `prompts/todo/`. For each one, check if its filename appears in `.completed`.

3. If there are NO pending prompts:
   - Tell the student: "You're up to date! No new updates."
   - Show the current version from `version.txt`.
   - Exit.

4. If there ARE pending prompts:
   - Tell the student how many updates are pending.
   - Run them in numeric order (001 before 002, etc).
   - After each prompt completes successfully, append its filename to `.completed`.
   - At the end, summarize what was done.

5. If `changelog.md` exists and has entries newer than the student's `.local-version`, show them what changed in plain English.

6. Update `.local-version` to match `version.txt`.
