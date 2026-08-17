# Getting Started with Claude — Setup & Tips

A friendly guide to setting up your Claude account and getting the most out of
Claude Code for your projects.

---

## 1. Set Up Your Claude Account

Start by creating a **Claude Pro** subscription:

1. Go to [claude.ai](https://claude.ai) and sign up (or log in).
2. Open **Settings → Plans** and choose **Pro**.
3. Pro gives you higher usage limits, priority access, and access to the most
   capable models — which you'll want for real coding work.

> 💡 **Tip:** Pro is the recommended plan for regular development work. It
> balances cost with the usage headroom you need for day-to-day projects.

---

## 2. Pick the Right Language *Before* You Start

Decide your tech stack up front — it makes everything downstream smoother.

| What you're building        | Recommended language / framework |
|-----------------------------|----------------------------------|
| Generative / backend app    | **Python**                       |
| User interface (UI)         | **React**                        |

- Use **Python** when the core of your project is *generating* things — data
  processing, APIs, automation, ML, or app logic.
- Use **React** when you're building the *user interface* people click and
  interact with.

> 💡 A common combo is **Python for the backend + React for the frontend**.

---

## 3. Run Claude in YOLO Mode

By default, Claude asks for your permission before running commands or editing
files. **YOLO mode** skips those prompts so Claude can work autonomously —
running tools, editing files, and executing commands without stopping to ask.

Start Claude in YOLO mode with:

```
claude --dangerously-skip-permissions
```

This keeps Claude moving without interruption, which is great when you want it
to power through a task end-to-end.

> 💡 **A git repo is an ideal place for YOLO mode.** Since Git tracks every
> change, you can easily review exactly what Claude did with `git diff`, and
> undo anything you don't want with `git restore` / `git checkout`. That safety
> net makes autonomous edits much less risky.

> ⚠️ **Use with care.** YOLO mode removes the safety checks, so Claude can run
> *any* command without confirmation. Only use it when you:
> - Trust the task and the project,
> - Are working in a safe, isolated environment (e.g. a container, VM, or a repo
>   you can easily reset), and
> - Have your work backed up or under version control.
>
> Avoid it for anything touching sensitive data, production systems, or commands
> you can't easily undo.

---

## 4. Set the Right Effort Level

Claude lets you tune how much reasoning effort it puts into a task. Match the
effort level to your language and complexity:

- For **Python**, `high` tends to work well — it's expressive and forgiving, so
  this level gives strong results without overkill.
- For **C++**, `xhigh` tends to work better, where memory management, types, and
  subtle correctness details benefit from extra reasoning.

> ⚠️ **Note:** These suggested levels are based on experience, not a hard rule.
> They're a starting point — adjust to what works best for your task.

> 💡 Bump the effort level up when a task is tricky, low-level, or
> correctness-critical.

---

## 5. Run `/init` in Existing Repos

When you point Claude at a repository that **already has code**, run:

```
/init
```

This scans the project and generates a `CLAUDE.md` file describing the codebase
— its structure, conventions, and how to build/test it. With that in place,
Claude edits your code more accurately and needs less hand-holding.

> 💡 Run `/init` once when you first start working in a repo. Re-run it if the
> project structure changes significantly.

**Starting your own project from scratch?** There's no code to scan yet, so run
`/init` **after your first major code change** — once there's a real structure
in place. That way the generated `CLAUDE.md` reflects your actual project.

---

## 6. Use Planning Mode for Complex Requests

For anything non-trivial — **especially your first request in a project** — use
**planning mode**. Instead of jumping straight to code, Claude will explore,
think through the approach, and present a plan for your approval first.

**Example prompt:**

```
start planning. I want to do these steps:
1) set up a Python backend with a REST API
2) build a React UI that calls the API
3) add user login
```

This helps you:

- Catch misunderstandings *before* code is written.
- Align on the approach and trade-offs.
- Break big goals into clear, reviewable steps.

> 💡 Numbered steps (`1) ... 2) ...`) make your intent crystal clear and give
> Claude a solid structure to plan against.

---

## 7. Ask Claude to Write Tests

Once a feature works, ask Claude to **write tests** for it. Tests catch simple
mistakes early — the kind that are easy to miss by eye but break things later.

**Example prompt:**

```
write tests for the functions we just added, covering the main cases and edge cases
```

Good tests also make future changes safer: if something breaks, a failing test
tells you exactly what and where.

> 💡 Ask for tests as you build, not just at the end. It's easier to test small
> pieces than one giant one.

---

## 8. Run a Code Review to Debug

Before you consider the work done, run a **code review** to catch bugs, logic
errors, and rough edges:

```
/code-review
```

Claude will look over the changes and flag potential problems — bugs, edge
cases, security issues, and things that could be cleaner. Then ask it to fix
whatever it finds.

> 💡 Combine steps 7 and 8: **write tests, run them, then do a code review.**
> Together they catch far more issues than either one alone.

---

## General Advice

- **Be specific.** Clear, detailed prompts get better results than vague ones.
- **Work in steps.** Tackle big goals as a series of smaller, reviewable tasks.
- **Review as you go.** Read Claude's changes and give feedback — it learns your
  preferences within the conversation.
- **Keep `CLAUDE.md` handy.** It's your project's memory; keep it updated.
- **Ask for explanations.** If you don't understand a change, just ask Claude to
  walk you through it.

---

Happy building! 🚀
