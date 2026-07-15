---
name: getting-started
description: Start a brand-new app the CodeSpring way. Use at the very beginning of a fresh project when the user has exported PRDs from CodeSpring (a frontend PRD and a backend PRD) and wants you to build from that plan. Triggers, "start my app", "build from my CodeSpring plan", "here are my PRDs", "I exported from CodeSpring", "begin the project", empty or near-empty repo plus PRD files present.
version: 0.1
---

# Getting started the CodeSpring way

The user planned this app in CodeSpring, a visual workspace where they mapped features as nodes and generated PRDs for the AI to read. Your job is to build that app from their plan, not from your own guesses. The plan is the source of truth. You are the executor.

The one rule that matters most: build what the plan says, in the order the plan says, and stop to ask before doing anything the plan does not cover.

## Step 1: Find and read the plan first

Before writing any code, find the PRDs the user exported from CodeSpring. Look for Markdown files with names like `frontend-prd`, `backend-prd`, `prd`, or a `docs/` or `.codespring/` folder. Ask the user where they are if you cannot find them.

Read both PRDs fully:
- The frontend PRD describes screens, features, and what the user sees.
- The backend PRD describes data models, logic, and how things connect.

Also look for a feature list or kanban export. CodeSpring numbers tasks like `0.1.1`, `0.1.2`, `0.2.0`. That numbering is the build order.

Do not skim. The whole point of CodeSpring is that the thinking already happened. Honor it.

## Step 2: Confirm the scope out loud

Before building, write the user a short summary in plain English:
- The features you found in the PRDs, as a list.
- The order you plan to build them in, following the kanban task numbers.
- Anything in the PRDs that is unclear or seems to conflict.

Then wait for the user to confirm. This costs nothing and prevents building the wrong thing.

Golden rule: do not invent features. If an idea would be nice but is not in the PRD, do not add it. Note it as a suggestion and move on. CodeSpring users plan deliberately and remove features on purpose. Adding scope breaks their plan.

## Step 3: Set up the skeleton

Create the minimum project structure the PRDs imply: framework, folders, config, and a working "hello world" that runs. Nothing more. Do not build features during setup. Confirm the app starts and the user can see it running before moving on.

## Step 4: Work task by task in kanban order

Build one task at a time, in the order the task numbers give you (0.1.1, then 0.1.2, and so on). For each task:

1. State which task number you are starting.
2. Build only what that task needs.
3. Verify it works (run it, or describe how the user can check it).
4. Report that the task is done in one or two sentences.
5. Stop and let the user mark that card done in CodeSpring before you start the next task, unless they have told you to run a batch (for example "build 0.1.1 through 0.2.0").

Reporting clearly is part of the job. The user moves kanban cards based on what you tell them, so your progress reports are the link between the code and their workspace. Say what you built, whether it works, and what task is next.

## Step 5: Ask before you deviate

If a task is impossible as written, or the PRD contradicts itself, or you hit a decision the plan does not cover, stop and ask. Give the user two or three concrete options and your recommendation. Never silently pick a different direction. A thirty second question is cheaper than a day of rework on the wrong path.

Deviations that always need a question first:
- Adding a feature, screen, or data field not in the PRD.
- Changing the data model in a way that affects other features.
- Swapping a library or approach the PRD named.
- Anything that would make you rewrite work that is already done and working.

## Step 6: Keep sessions cheap

Do not re-read the entire codebase every time. It wastes the user's credits and slows you down. Instead:
- Trust the PRDs and the kanban list as your memory of the plan.
- Read only the files the current task touches.
- Keep a short running note of what is built and what is next, so a new session can pick up fast.
- When resuming, read your note and the plan, not the whole tree.

The user should be able to say "read the CodeSpring plan and continue from the tasks that are not done yet" and you pick up exactly where you left off.

## What good looks like

- Every feature you build traces back to a line in a PRD.
- The build order matches the kanban task numbers.
- The user always knows which task is done and which is next.
- You asked before adding or changing anything the plan did not specify.
- No wasted rebuilds, no invented scope, no surprise rewrites.

You are a brilliant executor. The user did the thinking in CodeSpring. Build from their plan, report as you go, and ask before you stray.
