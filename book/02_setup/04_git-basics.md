---
site:
  outline_maxdepth: 1
---

# Git basics

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Using Git to track, organise and share your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## Why Git matters

{term}`Git` helps you track how your SDS320 project changes over time. This is useful for backup, documentation, transparency and {term}`Reproducibility`.

Your final project should include a public GitHub or GitLab {term}`Repository`. The repository should make it possible for another student to understand what you did, which files matter, how your workflow is organised and how the main results can be reproduced.

This page gives you a practical Git refresher for SDS320. It focuses on the basic workflow you need during the semester. For detailed expectations about the final project repository, see [Repository](../03_project/07_repository.md).

---

## The basic idea

Git records snapshots of your project. These snapshots are called commits.

A Git workflow usually follows this pattern:

```text
work on files
→ check what changed
→ stage selected changes
→ commit with a message
→ push to the online repository
```

In SDS320, Git is not only a technical tool. It also helps you show project development. Your commit history can document when you added data notes, tested preprocessing, improved figures, fixed path problems or updated your report.

```{tip}
Think of Git as a project diary for your files. It does not replace your report or README, but it helps document how your project developed.
```

---

## Key Git concepts

### Repository

A repository is a project folder tracked by Git. It usually contains notebooks, scripts, documentation, environment information and selected outputs.

In SDS320, your assessed project repository should be understandable to someone who did not sit next to you while you worked.

### Commit

A commit is a saved snapshot of selected changes. A good commit should represent one meaningful step, not a random collection of unrelated edits.

Examples of useful commit moments:

- adding an initial README,
- creating the first data inventory,
- testing data loading,
- fixing a coordinate reference problem,
- adding a preprocessing notebook,
- improving a figure,
- documenting a limitation.

### Staging

Staging means selecting which changed files should go into the next commit. This lets you commit related changes together.

### Remote repository

A remote repository is the online version of your project, for example on GitHub or GitLab.

Your local repository lives on your computer. The remote repository makes it possible to back up, share and submit your work.

<!-- TODO: confirm whether SDS320 prefers GitHub, GitLab, or allows both equally. -->

---

## Recommended Git workflow

Use this workflow regularly while working on your project.

### 1. Check what changed

Run this inside your project folder:

```bash
git status
```

This shows which files were changed, added or deleted.

### 2. Stage meaningful files

To stage all current changes:

```bash
git add .
```

For more controlled commits, stage selected files:

```bash
git add README.md
git add notebooks/01_explore_data.ipynb
git add scripts/preprocessing.py
```

### 3. Commit with a clear message

```bash
git commit -m "Add initial data inventory"
```

A good commit message describes the change clearly.

### 4. Push to the remote repository

```bash
git push
```

This uploads your committed changes to the online repository.

### 5. Pull before continuing on another machine

If you work on more than one computer, update your local copy first:

```bash
git pull
```

This downloads changes from the remote repository.

```{warning}
If you work on different computers, get into the habit of pulling before you start and pushing when you finish. This reduces the chance of conflicting versions.
```

---

## Starting a repository

There are two common ways to start.

### Option A: start online first

This is often easiest for beginners.

1. Create a new repository on GitHub or GitLab.
2. Copy the repository URL.
3. Clone it to your computer:

```bash
git clone <repository-url>
```

1. Move into the folder:

```bash
cd <repository-folder>
```

1. Add or copy your SDS320 project files into this folder.

### Option B: start locally first

If you already have a local project folder, open a terminal in that folder and run:

```bash
git init
```

Then connect it to an online repository later.

<!-- TODO: add course-preferred GitHub/GitLab workflow once the final platform recommendation is confirmed. -->

---

## Using Git in VS Code

You can use Git from the {term}`Command Line`, but many students find the VS Code interface easier for everyday work.

VS Code has a **Source Control** view that lets you inspect changes, stage files, write commit messages, commit changes and sync with a remote repository. It still uses the Git installation on your computer, so `git --version` must work first.

### A. Open the right folder

Open the full project folder in VS Code, for example:

```text
sds320/project/
```

Do not open only a single notebook. Opening the full folder lets VS Code detect the repository, show changed files and manage relative paths more clearly.

### B. Use the Source Control view

In VS Code:

1. open the **Source Control** view in the left sidebar,
2. inspect the list of changed files,
3. click a file to see what changed,
4. stage the files that belong together,
5. write a short commit message,
6. commit the changes,
7. sync or push the commit to GitHub/GitLab.

```{tip}
Use the VS Code diff view before committing. It helps you check whether you are committing only the changes you intended.
```

### C. Recommended VS Code extension

If you use GitHub, install the official **GitHub Pull Requests and Issues** extension.

From the VS Code Extensions panel, search for:

```text
GitHub Pull Requests and Issues
```

Or install it from the terminal:

```bash
code --install-extension GitHub.vscode-pull-request-github
```

This extension is useful when you want to review changes, manage pull requests or connect issues to code. For a simple individual SDS320 project, the built-in Source Control view is usually enough.

<!-- TODO: decide whether to recommend a GitLab-specific VS Code extension for students using GitLab. -->

### D. Using VS Code vs. the terminal

| Task | VS Code is useful for | Terminal is useful for |
| --- | --- | --- |
| Checking changed files | Seeing file-by-file differences | Running `git status` quickly |
| Staging changes | Selecting files visually | Staging with `git add` |
| Writing commits | Writing messages in the Source Control panel | Committing with `git commit -m` |
| Pushing and pulling | Using the Sync button | Running `git push` and `git pull` |
| Debugging Git problems | Seeing visual warnings | Copying exact error messages |

You can use both. The important part is that you understand the basic workflow: check, stage, commit, push.

```{warning}
The VS Code Sync button can combine pull and push actions. This is convenient, but if you are unsure what will happen, use the terminal commands separately: `git pull` first, then `git push`.
```

---

## Repository contents

### What should go into your repository

Your SDS320 repository should usually include:

- `README.md`,
- notebooks,
- scripts,
- an {term}`Environment File`,
- small configuration files,
- data download instructions,
- small example data if permitted,
- figures or outputs that help explain the result,
- report material if appropriate.

The repository should make your workflow understandable and rerunnable.

For more detailed expectations, see [Repository](../03_project/07_repository.md).

---

### What should not go into your repository

Avoid committing:

- very large raw data files,
- private or sensitive data,
- access tokens,
- passwords,
- API keys,
- local cache folders,
- temporary files,
- system files such as `.DS_Store`,
- outputs that are huge or easy to recreate.

Use a {term}`Gitignore File` to exclude files that should not be tracked.

Example `.gitignore`:

```text
# system files
.DS_Store
Thumbs.db

# Python
__pycache__/
*.pyc

# environments
.venv/
.env

# large/local data
data/raw/
data/interim/

# temporary outputs
*.tmp
```

```{warning}
Never commit passwords, tokens or private credentials. If this happens, deleting the file in a later commit is not enough because the information may remain in the Git history.
```

---

## Good commit messages

Useful commit messages are short and specific.

| Less useful | More useful |
| --- | --- |
| `update` | `Add first data inventory` |
| `fix` | `Fix CRS mismatch in preprocessing notebook` |
| `stuff` | `Add repository structure and README draft` |
| `final` | `Add final figures and report link` |

A good commit message helps you and others understand the project history.

Use this pattern when you are unsure:

```text
Verb + object + context
```

Examples:

```text
Add preprocessing notebook
Fix broken relative paths
Update README with data source notes
Save first evaluation figure
Document model limitation
```

---

## Flags & checks

Use this table when your Git workflow becomes confusing. Start with the first check before changing many things at once.

| Red flag | First check |
| --- | --- |
| You have not committed for several weeks | Make one small commit now, then commit after each meaningful project step. |
| Your Git history has only one final commit | Commit smaller project steps, such as data inventory, preprocessing, figures or README updates. |
| `git status` shows many unrelated changes | Stage and commit related files separately instead of using one large mixed commit. |
| Your repository contains large raw data | Add or update `.gitignore`; document data sources and download steps in `data/README.md`. |
| Your project only works on your computer | Check for {term}`Absolute Path` values and replace them with {term}`Relative Path` values. |
| Your remote repository is missing recent local work | Commit your changes, then run `git push`. |
| Your local folder is behind the remote repository | Run `git pull` before continuing, especially when working on another computer. |
| VS Code does not show Source Control changes | Check that you opened the full repository folder, not only one file or notebook. |
| Git asks who you are | Configure your Git username and email before committing. |
| You see a merge conflict | Pause, read the conflict markers, compare both versions and ask for help if unsure. |
| Your README is still empty or too vague | Add a project title, research question, data note, setup instructions and run order. |
| A script or notebook is required but not explained | Mention it in the {term}`README` and describe when to run it. |
| You are unsure what will be committed | Use `git status` and the VS Code diff view before committing. |
| You committed a file that should not be tracked | Stop and ask for help before trying to remove sensitive data from Git history. |

For technical problems, see [Troubleshooting](05_troubleshooting.md).

---

## Mini task

Work inside your SDS320 project folder.

Complete this checklist:

- [ ] Open the full project folder in VS Code.
- [ ] Check that Git is available with `git --version`.
- [ ] Check the current state with `git status`.
- [ ] Add or update `README.md`.
- [ ] Add or update `environment.yml`.
- [ ] Add or update `.gitignore`.
- [ ] Stage the related files.
- [ ] Commit them with a meaningful message.
- [ ] Push the commit to your remote repository.
- [ ] Open the online repository and confirm that the files appear there.

Example terminal workflow:

```bash
git status
git add README.md environment.yml .gitignore
git commit -m "Add initial project documentation files"
git push
```

Write one sentence in your project notes:

```text
My next useful Git commit will be: ...
```

---

## Key takeaways

- Git records the development of your project over time.
- Commit regularly after meaningful changes.
- Use clear commit messages.
- VS Code can make staging, committing and reviewing changes easier.
- Do not commit large data, credentials or unnecessary temporary files.
- Your public project repository should support reproducibility, not just file storage.

---

### What to do next

After this page:

- use [Notebooks and scripts](03_notebooks-scripts.md) to decide which files belong in notebooks and which belong in scripts,
- use [Workflow design](../03_project/04_workflow-design.md) to connect repository structure to your analytical steps,
- use [Repository](../03_project/07_repository.md) to prepare the assessed repository,
- use [Reproducibility](../03_project/06_reproducibility.md) to check whether someone else can rerun your workflow.
