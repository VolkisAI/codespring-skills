# CodeSpring Skills

Official skill files for [CodeSpring](https://codespring.app). These teach your AI coding agent (Claude Code, Codex, or Cursor) how to build the CodeSpring way: from a plan you can see and control, instead of guesses the AI makes on its own.

CodeSpring is a visual workspace that sits on top of your AI coding agent. You map your app as nodes on a canvas (features, sub-features, notes, mood boards), turn each feature into a PRD, and export a task list your agent works through. These skills make your agent respect that plan.

## What is a skill file?

A skill file is a plain text file (Markdown) that lives inside your project, in a folder called `.claude/skills/`. Your AI coding agent reads it and follows the instructions inside. Think of it as a set of house rules the agent loads automatically so you do not have to re-explain how you want it to work every session. Each skill is one folder with a `SKILL.md` file inside.

## Install

Clone this repo anywhere on your computer:

```
git clone https://github.com/VolkisAI/codespring-skills
```

## Install a single skill into your project

1. Open your own project folder (the app you are building).
2. If it does not already have one, make a folder called `.claude/skills/` inside it.
3. Copy the skill folder you want (for example `getting-started`) from this repo into your project's `.claude/skills/` folder.
4. Start your agent in your project. It will pick up the skill on its own.

That is it. Your project should end up looking like this:

```
your-project/
  .claude/
    skills/
      getting-started/
        SKILL.md
  ... your app code ...
```

You can install more than one skill. Copy each folder you want.

## Available skills

| Skill | What it does |
|-------|--------------|
| `getting-started` | Teaches your agent to start a brand-new app from your CodeSpring PRDs: read the plan, respect the feature list, build task by task in kanban order, and report progress so you can move cards. |
| `import-codebase` | Teaches your agent to bring an existing app into CodeSpring: inventory what you already have into a plain-English feature map you can paste in as nodes, flag risky areas, then make changes from your CodeSpring plan one feature at a time. |

## Updating

To get the latest version of the skills, pull the repo again:

```
git clone https://github.com/VolkisAI/codespring-skills
```

Then recopy any skill folders you use into your project.
