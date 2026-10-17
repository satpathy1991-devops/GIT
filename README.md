# Git & GitHub Practice Notes 🚀

Welcome to my Git and GitHub learning repository. This repository contains commands, concepts, practical examples, troubleshooting steps, and notes from my hands-on practice.

## 📚 Topics Covered

### 1. Git Basics
- Git configuration
- Repository initialization
- Checking repository status
- Adding and committing files
- Viewing commit history
- Working with branches

### 2. GitHub & Remote Repositories
- Creating GitHub repositories
- Connecting local repositories to GitHub
- Understanding remote repositories
- `git remote add`, `git remote -v`, and `git remote set-url`
- Pushing local branches to GitHub
- Understanding `main` and `master` branches

### 3. Fetch, Pull & Clone
- `git clone`
- `git fetch`
- `git pull`
- Understanding the differences between fetch and pull
- Synchronizing local and remote repositories

### 4. SSH Authentication
- Generating SSH key pairs
- Understanding private and public keys
- Adding public keys to GitHub
- Testing SSH authentication
- Using the standard SSH key location
- Using a custom SSH key location
- Configuring SSH with `~/.ssh/config`
- Pushing code using custom SSH keys

### 5. Git Troubleshooting
- `Permission denied (publickey)`
- Git push and authentication errors
- Branch naming issues
- Remote repository configuration
- GitHub Push Protection and secret-scanning errors
- Resolving accidental file tracking

## 📄 Detailed Notes

| File | Description |
|---|---|
| [Git-Commands.md](Git-Commands.md) | Git commands, branches, commits, remotes, fetch, pull, and push |
| [Git-SSH-Authentication.md](Git-SSH-Authentication.md) | Standard SSH, custom SSH key locations, SSH configuration, and troubleshooting |

## 🔐 Security Best Practices

- Never commit private SSH keys, passwords, or access tokens.
- Keep private keys outside Git repositories.
- Use appropriate permissions for private keys.
- Use `.gitignore` to help prevent accidental tracking of sensitive files.
- Remember that `.gitignore` does not remove secrets already committed.
- Replace exposed credentials and remove secrets from affected Git history.

## 🎯 Learning Goal

Build strong Git and GitHub fundamentals through daily hands-on practice, command-line exercises, troubleshooting, and documentation.

I use this repository to organize my notes, revise important commands, and strengthen my practical knowledge for DevOps engineering.

---

**Learning by doing. Practising every day. Growing towards DevOps.** 💻
