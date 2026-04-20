---
name: minimal-bug-reproduction
description: Set up a minimal bug reproduction repository and push it to GitHub. Analyzes the bug description and provided code, detects the stack, builds a minimal repro, and produces a ready-to-paste GitHub issue template. Use when the user says "create a repro", "minimal reproduction", "bug repro", or wants to isolate and share a bug.
---

# Bug Repro

End-to-end workflow: analyze bug → detect stack → build minimal repro → confirm with user → push to GitHub → generate issue template.

## Prerequisites

Check upfront — fail fast with a clear message if missing:

```bash
which gh || echo "gh CLI not found"
gh auth status
```

If `gh` is missing: tell the user to install it via `brew install gh` or https://cli.github.com.
If not authenticated: tell the user to run `gh auth login`.

## Workflow

### 1. Collect bug info

Accept the bug description and code from the user's message. If description or code is missing or insufficient to reproduce the bug, ask targeted questions. Don't proceed until you have:

- What the bug is
- What behavior is expected vs actual
- Enough code to reproduce it

### 2. Detect stack

- Infer the stack from the provided code (imports, syntax, config files mentioned)
- Also inspect the current repo (`package.json`, `Cargo.toml`, `go.mod`, etc.) as a fallback
- Confirm with the user before continuing:

  > "Detected stack: **React 18 + TypeScript + Vite**. Is that correct?"

### 3. Build minimal repro

- Strip the provided code down to the absolute minimum that still reproduces the bug
- Generate missing boilerplate: `package.json`, entrypoint, config files, etc.
- Keep it runnable: `npm install && npm run dev` (or equivalent) should work

### 4. Propose repo name

Generate a short, descriptive name based on the bug (e.g., `repro-react-controlled-input`). Confirm with the user:

> "Repo name: `repro-react-controlled-input`. OK or change it?"

### 5. Preview & confirm local files

Create the files locally at `~/bug-repros/<repo-name>/`. Show the user:

- File tree
- Key file contents (entrypoint, main component, etc.)

Ask for explicit confirmation before touching GitHub:

> "Ready to create a private GitHub repo and push. Proceed?"

### 6. Push to GitHub

```bash
cd ~/bug-repros/<repo-name>
git init
git add .
git commit -m "chore: initial bug repro"
gh repo create <repo-name> --private --source=. --push
```

### 7. Generate deliverable

After push, output two things:

**A. README.md** (already committed in the repo) — technical instructions:

```markdown
# repro-<bug-name>

Minimal reproduction for: <one-line bug description>

## Setup

\`\`\`bash
npm install
npm run dev
\`\`\`

## What to observe

<what you see vs what you expect>
```

**B. Issue template** — ready to paste into a GitHub issue, printed as a fenced markdown block:

```markdown
## Description

<clear description of the bug>

## Steps to reproduce

1. Clone the repro: `git clone <repo-url>`
2. `npm install && npm run dev`
3. <specific interaction that triggers the bug>

## Expected behavior

<what should happen>

## Actual behavior

<what actually happens>

## Environment

- <framework + version>
- <relevant dependency + version>
- Node: <version>

## Repro

<repo-url>
```

Finally, open the repo in the browser:

```bash
gh repo view --web
```
