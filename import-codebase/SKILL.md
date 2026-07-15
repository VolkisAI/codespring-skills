---
name: import-codebase
description: Bring an existing codebase into CodeSpring. Use when the user already has an app (built by them, an agency, or a previous AI session) and wants to plan and control it in CodeSpring going forward. Triggers, "import my existing app", "map my current codebase", "I already have a project", "bring this into CodeSpring", "help me understand this code so I can plan it", "reverse engineer my app into features".
version: 0.1
---

# Import an existing codebase into CodeSpring

The user already has a working (or half-working) app and wants to plan and control it in CodeSpring from now on. CodeSpring is a visual workspace where they map an app as feature nodes on a canvas, then generate PRDs the AI builds from. Right now their app exists only as code. Your job is to turn that code into a plain-English feature map the user can paste into CodeSpring as nodes, so their app and their plan finally match.

Do this carefully. The user is likely non-technical. Give them a map they understand, not a code dump.

## Step 1: Inventory the app

Explore the codebase and build a picture of what it actually does. Look for:

- **Routes and screens.** What pages or views exist? What does the user see and do on each one? Check the router, the pages or app folder, and navigation.
- **Features.** Group the screens into features. A feature is a thing the user can accomplish (sign in, create a project, send a message), not a file.
- **Data models.** What does the app store? Look at the schema, models, migrations, or database calls. List each thing it saves and the main fields in plain words.
- **Integrations.** Payments, email, auth, file storage, external APIs. Note each one and what it is for.

Read enough to be accurate. You do not need to read every line, but do not guess at what a feature does. If you are unsure, open the file and check.

## Step 2: Produce a plain-English feature map

Write the user a map they can paste into CodeSpring as nodes. Structure it the way CodeSpring is structured, so it drops in cleanly:

- **Features** (top level, like folders on the canvas). One line each on what the feature does for the user.
- **Sub-features** (the screens or actions inside a feature). One line each.
- **Notes** for anything worth remembering (a quirk, a dependency, a "this is half-built").
- A short **data** section listing what the app stores, in plain words.

Write it for a non-technical reader. "Users can reset their password by email" is good. Naming the controller class is not. Keep each line short so it becomes a clean node.

Example shape:

```
Feature: Accounts
  Sub-feature: Sign up with email
  Sub-feature: Log in
  Sub-feature: Reset password by email
  Note: Passwords are handled by the auth library, not custom code.

Feature: Projects
  Sub-feature: Create a project
  Sub-feature: Invite a teammate
  Note: Invites work but there is no way to remove a teammate yet.

Data the app stores:
  - Users (name, email, plan)
  - Projects (title, owner, created date)
```

## Step 3: Flag the risky and tangled areas

The user needs to know where changes are dangerous. Call out, in plain language:

- **Tangled areas** where one feature is wired into many others, so changing it is likely to break something else.
- **Fragile spots**, code that looks half-finished, has no error handling, or clearly was rewritten several times.
- **Unclear areas** where you could not tell what the code is meant to do. Say so honestly instead of guessing.
- **Duplication**, the same thing done two different ways in two places, which usually means a future change has to be made twice.

Present these as a short "handle with care" list next to the feature map. This is exactly the kind of thing CodeSpring exists to make visible, so be direct about it.

## Step 4: From here on, work from CodeSpring PRDs

Once the user has their feature map in CodeSpring, they will make changes by generating PRDs there, the same as a new project. When they come back with a PRD for a change:

- Build only what that PRD describes. Do not add features that are not asked for.
- Follow the kanban task order if there is one (CodeSpring numbers tasks like 0.1.1, 0.1.2).
- Respect the existing app. You are changing a living codebase, not starting fresh, so reuse what is there rather than rewriting it.

## Step 5: Verify one feature at a time

This is the whole reason the user came to CodeSpring: in the past, fixing one thing broke another. Do not repeat that. For every change:

1. Make the smallest change the PRD asks for.
2. Verify the feature you changed still works.
3. Verify the features you flagged as connected to it in Step 3 still work too.
4. Report what you changed, what you checked, and that it holds together.
5. Then move to the next change.

Never batch several risky changes into one untested pile. One feature, verified, before the next. If a change would force you to touch a tangled area you flagged, stop and tell the user before you do it, with your recommendation.

## What good looks like

- The user has a feature map in plain English that mirrors their real app, ready to paste as nodes.
- They know which areas are risky before they change anything.
- Changes come from CodeSpring PRDs, one feature at a time, each verified.
- Fixing one thing no longer quietly breaks another.
- The code and the CodeSpring plan finally tell the same story.
