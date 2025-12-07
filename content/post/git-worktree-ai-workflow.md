---
title: "Git Worktree: Scaling Your AI Workflow"
date: 2025-12-07T19:15:00+05:30
lastmod: 2025-12-07T19:15:00+05:30
draft: false
keywords: ["git", "worktree", "ai", "workflow", "productivity", "parallel processing", "cursor", "claude code"]
description: "Learn how to use git worktree to run parallel AI coding sessions with Cursor and Claude Code. Implement multiple ideas simultaneously without context switching pain."
tags: ["git", "workflow", "productivity", "AI"]
categories: ["Git", "Productivity"]
author: "Harisankar P S"
comment: true
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false
hideHeaderAndFooter: false
flowchartDiagrams:
  enable: false
  options: ""
sequenceDiagrams:
  enable: false
  options: ""
---

I've been using `git` for years, but to be honest, I wasn't even aware of `git worktree` until I started using AI for development.

Before this, my workflow for context switching was pretty standard. If I was in the middle of something and needed to check another branch or fix a bug, I'd rely on `git stash` or `git stash -u` (to catch those untracked files). Sometimes I'd just commit what I had, knowing I would eventually "Squash and Merge" my Pull Requests anyway, so a few messy "WIP" commits didn't really matter.

But recently, I found a new bottleneck: **I have more ideas than I have open AI contexts.**

We often treat AI as a faster pair programmer, but we still tend to work sequentially. We ask Cursor or Claude Code to do X, watch it generate code, review it, and then move to Y.

But what if you could implement Idea A, Idea B, and Fix C all at the same time?

Enter `git worktree`.

<!--more-->

## What is Git Worktree?

Most of us use a single working directory associated with our git repository. When you switch branches, the files in that directory change.

`git worktree` allows you to have multiple working directories (worktrees) attached to the same repository. This means you can have:

*   `feature-a` checked out in `project-folder/`
*   `feature-b` checked out in `project-folder-b/`
*   `hotfix` checked out in `project-folder-hotfix/`

All of them share the same `.git` history and object database, but they are independent directories on your disk.

## The AI Multi-Threading Workflow

In the age of AI, this feature transforms from "nice-to-have" to a superpower. Here is how I use it to parallelize my development with tools like **Cursor** and **Claude Code**.

### 1. The Setup: Sibling Directories

Let's say I'm working on my main app (`my-app`), but I want to try out a major refactor and also implement a new UI component. Or basically two ways to solve the same problem, and choose which one is best.

A common question is: **"Can I put the worktree inside my current repo folder?"**
Technically, yes. But it is **highly discouraged**. If you create a worktree folder inside your main repo, git sees it as a new directory. You would have to add it to `.gitignore` to prevent your main repo from tracking the files of the other branch. It gets messy fast.

The best practice is to create **sibling directories**.

```bash
# From inside your main repo folder (e.g., my-app)

# A. If the branch ALREADY exists:
git worktree add ../my-app-refactor feature/refactor-user-model

# B. If you want to create a NEW branch (use -b):
git worktree add -b feature/new-dashboard ../my-app-ui
```

Now I have three folders side-by-side: `my-app` (main), `my-app-refactor`, and `my-app-ui`.

### 2. Delegating to AI Agents

This is where it gets fun. I treat each folder as a separate workspace for a different AI agent.

You can launch your preferred AI tool in each folder independently:

*   **Option A (Terminal):** Open two terminal windows. In one, `cd my-app-refactor` and run `claude .`. In the other, `cd my-app-ui` and run `claude .`. Now you have two independent chat sessions working on different parts of the codebase.
*   **Option B (IDE):** Open two windows of **Cursor** (or your preferred IDE). Point one to `my-app-refactor` and the other to `my-app-ui`.

1.  **Agent A (Refactor):** I give it a complex instruction: *"Refactor the User model to use single-table inheritance. Update all associated controllers."* I hit enter and let it run.
2.  **Agent B (UI):** I tell the second agent: *"Build a dashboard component using Tailwind CSS based on these requirements."*

While those two are churning out code, I can stay in my main terminal (`my-app`), reviewing PRs, handling small tasks, or just monitoring the other processes. I don't have to worry about `git stash` popping the wrong files or my language server getting confused because I switched branches mid-generation.

### 3. Review and Merge

Once the AI in the refactor branch is done, I can review the code in isolation. I can run tests specifically for that branch without affecting my other work.

Since all worktrees share the same repo, I don't need to push to a remote to sync them. I can just commit my changes in the worktree, and they are immediately available in my main repo.

```bash
# In the worktree
git add .
git commit -m "Refactor complete"
```

Then I go back to my main folder to merge:

```bash
cd ../my-app
git merge feature/refactor-user-model
```

## Cleaning Up: The `--force` flag

When you are done with a worktree, you'll want to remove it to keep your disk clean.

```bash
git worktree remove ../my-app-refactor
```

However, git is protective. If you have **uncommitted changes** or **untracked files** (which happens often when AI generates new files you haven't decided to keep yet), git will refuse to remove the worktree to prevent data loss.

If you are sure you want to delete it and discard those experimental changes, you need to use the `--force` flag:

```bash
git worktree remove --force ../my-app-refactor
# OR
git worktree remove -f ../my-app-refactor
```

## Why not just `git clone`?

You might ask, "Why not just clone the repo into different folders?"

1.  **Disk Space:** `git worktree` shares the object database. You don't duplicate the entire `.git` folder (which can be huge for large projects).
2.  **Syncing:** With clones, you have to push/pull to sync branches between them. With worktrees, the state is local and instant.

## Cheatsheet

**Create a new worktree from an EXISTING branch:**
```bash
git worktree add ../<folder-name> <branch-name>
```

**Create a new worktree and a NEW branch:**
```bash
git worktree add -b <new-branch-name> ../<folder-name>
```

**List active worktrees:**
```bash
git worktree list
```

**Remove a worktree (Safe):**
```bash
git worktree remove <path>
```

**Remove a worktree (Force - for uncommitted/untracked changes):**
```bash
git worktree remove -f <path>
```

## Conclusion

AI allows us to generate code faster, but `git worktree` allows us to **think and execute in parallel**. By treating different branches as different workspaces, you can effectively manage a team of AI agents—whether via Cursor or Claude Code—working on your codebase simultaneously, all while keeping your main environment clean and stable.
