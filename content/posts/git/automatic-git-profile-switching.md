---
date: '2025-12-03'
draft: false
title: 'Stop Committing with the Wrong Email: Automatic Git Profile Switching'
description: 'Tired of accidentally pushing personal commits to work repositories? Learn how to use Git’s includeIf feature to automatically switch identities based on your project directory.'
tags:
  - Git
  - Version Control
  - Git Workflow
  - Productivity
  - Best Practices
categories:
  - Software Engineering
  - Productivity
  - Tutorials
cover:
  image: "posts/git/git-logo-with-title.jpg"
  alt: "Git logo with title"
  relative: true
---

## Introduction

Git has become the industry standard for version control, allowing developers to track changes, collaborate effectively, and manage history. [Throughout my career as a Software Engineer](https://jeffersonbsilva.com/about), I have managed a wide variety of repositories. Some were personal projects, while others were work-related.

If you use a single machine for both work and personal development, you likely commit using different emails and usernames. You might use a personal address like <john1337@gmail.com> for side projects, but a professional alias like <john.doe@company.com> for corporate work.

Committing to a client's repository with a personal handle is unprofessional and can lead to compliance issues. If you face this scenario, **this guide provides the solution**. This article demonstrates how you could use Git’s includeIf directive to automatically switch between profiles based on your project directory, ensuring you never commit with the wrong identity again.

## How Git Identifies You

Before you start pushing code, you must identify yourself with at least a username and an email address. Every commit uses this information to record who made the change, ensuring accountability and history tracking.

The username and email address are variables set using a built-in tool called `git config`. Git uses these configuration variables to control how it operates. Usually, these configuration variables are stored in two locations:

1. `~/.gitconfig`: Values specific to the current user
2. `example-project/.git/config`: Values specific to that single repository

## The Standard Global Configuration

With Git installed, you typically set your username and email address globally:

```bash
git config --global user.name "John Doe"
git config --global user.email john.doe@company.com
```

Using the `--global` option ensures Git uses this information for every new commit by adding variables to your `~/.gitconfig` file:

```text/plain
[user]
    name = John Doe
    email = john.doe@company.com
```

If you want to override this with a different name or email for specific projects, you can run the command without the `--global` option while inside a project directory. Git then stores this information in the `.git/config` file for that specific repository.

> **Tip:** You can view the origin of your settings using:
>
> ```bash
> git config --list --show-origin
> ```

## Why Local Overrides Aren’t Enough

Suppose you want to commit modifications to a personal project, but you don’t want to use your work email. You might think setting up the variables locally for that repository (without the `--global` option) is a good solution. While this works for isolated cases, **it isn’t scalable**.

Imagine managing 10 or 20 different projects. Manually configuring every single repository is prone to human error.

## The Real Solution: Multiple Configuration Files

Git allows for multiple configuration files. The strategy is to create two distinct config files – `~/.gitconfig-work` and `~/.gitconfig-personal` – and include them in your main `~/.gitconfig` file.

To make this work, we organize projects into a specific directory structure:

```text/plain
~/
└── Workspace/
    ├── Work/      <-- All company repos go here
    └── Personal/  <-- All side projects go here
```

Based on where your projects are located, the correct variables will be applied automatically.

## Step-by-Step Setup

### 1. Create the Workspace Structure

```bash
mkdir Workspace/
mkdir Workspace/Work
mkdir Workspace/Personal
```

Move your projects into their respective directories.

### 2. Create the Work Configuration File

Create a file specifically for your work identity:

```bash
nano ~/.gitconfig-work
```

Paste the following content:

```text/plain
[user]
    name = John Doe
    email = john.doe@company.com
```

### 3. Create the Personal Configuration File

Create a file for your personal identity:

```bash
nano ~/.gitconfig-personal
```

Paste the following content:

```text/plain
[user]
    name = Awesome John
    email = john1337@gmail.com
```

### 4. Create the Main .gitconfig with includeIf

Open your main global configuration file:

```bash
nano ~/.gitconfig
```

Add the includeIf directives. These tell Git to load a specific config file only if the current repository matches the directory path.

```text/plain
[includeIf "gitdir:~/Workspace/Work/**"]
path = ~/.gitconfig-work

[includeIf "gitdir:~/Workspace/Personal/**"]
path = ~/.gitconfig-personal
```

Now, depending on which directory a project is in, Git will apply the corresponding configuration file.

## Final Result

With this setup, Git automatically switches your identity based on the directory you are working in. You no longer need to change your config manually or worry about committing with the wrong email.

Here is what it looks like in practice:

### Committing Inside Workspace/Work

```bash
$ cd ~/Workspace/Work/my-company-repo
$ git config user.name
John Doe
$ git config user.email
john.doe@company.com

$ git commit -m "Fix authentication flow"
[main 3d92ac1] Fix authentication flow
 Author: John Doe <john.doe@company.com>
  2 files changed, 12 insertions(+), 3 deletions(-)
```

Git automatically applied the work profile (`~/.gitconfig-work`).

### Committing Inside Workspace/Personal

```bash
$ cd ~/Workspace/Personal/my-side-project
$ git config user.name
Awesome John
$ git config user.email
john1337@gmail.com

$ git commit -m "Refactor UI components"
[main a81fbc2] Refactor UI components
 Author: Awesome John <john1337@gmail.com>
  5 files changed, 42 insertions(+), 8 deletions(-)
```

Git automatically used your personal profile (`~/.gitconfig-personal`).

## How This Works Behind the Scenes

When you run Git inside a project, it builds your final configuration by merging several files in a specific order: system config, user config, and repository config.

Your main `~/.gitconfig` resides at the global user level, which is where the `includeIf` rules are evaluated.

The `includeIf` directive tells Git to load another configuration file only when a condition matches. If the repository is inside `Workspace/Work`, Git automatically includes your work identity. If it is inside `Workspace/Personal`, it includes your personal identity. This happens instantly and requires no manual switching.

For more details, see the official Git documentation on includes: [https://git-scm.com/docs/git-config#_includes](https://git-scm.com/docs/git-config#_includes).

## Conclusion

This setup keeps your personal and work identities completely separate and removes the risk of committing with the wrong email.

If you want to learn more about Git, I strongly recommend the [“Pro Git” book](https://git-scm.com/book/en/v2).

What do you think about this solution? Do you have a better approach? Share your knowledge in the comments.
