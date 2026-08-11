---
site:
 outline_maxdepth: 2 
---

<!-- Add Git on VS Code-->

# Git basics

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Using Git to track and share your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## Why Git matters

Git helps you track changes in your SDS320 project. It supports backup, transparency, reproducibility and the final public repository.

Your final project must include a public GitHub or GitLab repository. The repository should allow another student to understand and reproduce your work on a different machine.

This page gives a short Git refresher. It does not replace the more detailed repository guidance in the Project handbook.

---

## What Git does

Git records snapshots of your project over time. These snapshots are called commits.

A Git repository is a folder whose changes are tracked by Git. A remote repository is the online version of that project, for example on GitHub or GitLab.

The basic idea is:

```text
work on files
→ check what changed
→ stage selected changes
→ commit with a message
→ push to the online repository
```

---

## Basic workflow

Run these commands inside your project folder.

Check what changed:

```bash
git status
```

Stage changes:

```bash
git add .
```

Commit changes:

```bash
git commit -m "Add initial data exploration notebook"
```

Push changes:

```bash
git push
```

A simple habit is to commit after a meaningful step, such as:

- adding a first README,
- testing data loading,
- creating a preprocessing notebook,
- adding a workflow diagram,
- improving a figure,
- documenting limitations.

```{tip}
Commit messages should describe the change, not your mood. “Add preprocessing function” is more useful than “update”.
```

---

## Starting a repository

If you create the repository online first, you will usually clone it:

```bash
git clone <repository-url>
```

Then move into the folder:

```bash
cd <repository-folder>
```

If you start locally first, you can initialise Git:

```bash
git init
```

Then connect a remote repository later.

<!-- TODO: add course-preferred GitHub/GitLab workflow if needed -->

---

## What should go into your repository

Your SDS320 repository should usually include:

- `README.md`,
- notebooks,
- scripts,
- `environment.yml`,
- small configuration files,
- data download instructions,
- small example data if permitted,
- figures or outputs that help explain the result,
- report material if appropriate.

The repository should make your workflow understandable and rerunnable.

---

## What should not go into your repository

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

Use a `.gitignore` file to exclude files that should not be tracked.

Example:

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
Never commit passwords, tokens or private credentials. If this happens, deleting the file in a later commit is not enough because it may remain in the Git history.
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

---

## Common pitfalls

### Not committing regularly

If you commit only once at the end, Git cannot help you understand how the project developed.

### Committing large data files

Large data files can make repositories slow or difficult to clone. Add data instructions instead, unless the data are small and allowed to be shared.

### Broken file paths

A repository should not depend on local paths from your own computer. Use relative paths where possible.

### Missing README

A repository without a README is difficult to understand. Start your README early and improve it throughout the semester.

### Merge conflicts

Merge conflicts can happen when the same lines are changed in different places. Do not panic. Read the conflict markers carefully and ask for help if needed.

---

## Mini task

Make one meaningful commit to your SDS320 project repository.

For example:

```bash
git status
git add README.md environment.yml
git commit -m "Add initial README and environment file"
git push
```

Then check the online repository and confirm that the files appear there.

## Key takeaways

- Git records the development of your project over time.
- Commit regularly after meaningful changes.
- Use clear commit messages.
- Do not commit large data, credentials or unnecessary temporary files.
- Your final public repository should support reproducibility, not just file storage.
