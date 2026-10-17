# GitHub SSH Authentication – Complete Notes

**Topics:** Standard SSH, Custom SSH Key Location, SSH Config, Git Push  
**Practice Environment:** Linux EC2, Git, GitHub  
**GitHub Username:** `satpathy1991-devops`

---

## 1. How SSH Authentication Works

SSH allows a Linux server to authenticate with GitHub using a public/private key pair.

- **Private key:** Stays securely on your Linux server.
- **Public key:** Added to your GitHub account.
- **SSH:** Uses the private key to prove your identity.
- **Git:** Uses SSH to authenticate when pushing to a repository with an SSH remote URL.

Example SSH remote:

```bash
git@github.com:satpathy1991-devops/training.git
```

## 2. Standard SSH Method — Default Key Location

By default, SSH looks for keys in standard locations, including `/root/.ssh/`.

### Step 1: Generate an SSH key pair

```bash
ssh-keygen -t ed25519
```

When prompted for a file location, press `Enter` to use the default:

```text
/root/.ssh/id_ed25519
/root/.ssh/id_ed25519.pub
```

### Step 2: Set permissions

```bash
chmod 700 /root/.ssh
chmod 400 /root/.ssh/id_ed25519
```

### Step 3: Display the public key

```bash
cat /root/.ssh/id_ed25519.pub
```

Copy the public key and add it to:

**GitHub → Settings → SSH and GPG keys → New SSH key**

Never upload the private key.

### Step 4: Test authentication

```bash
ssh -T git@github.com
```

Expected successful message:

```text
Hi satpathy1991-devops! You've successfully authenticated, but GitHub does not provide shell access.
```

### Step 5: Configure the Git remote

```bash
git remote add origin git@github.com:satpathy1991-devops/training.git
```

Check the remote:

```bash
git remote -v
```

### Step 6: Push your code

If the local branch is `master`:

```bash
git push -u origin master
```

If the local branch is `main`:

```bash
git push -u origin main
```

Once upstream tracking is configured, subsequent pushes can usually use:

```bash
git push
```

---

## 3. Customized SSH Key Location — Generate a Key Anywhere

A custom key location means storing the private key somewhere other than the default SSH key path.

Example location:

```text
/root/annakonda/keys
/root/annakonda/keys.pub
```

### Step 1: Generate the key at a custom location

```bash
ssh-keygen -t ed25519 -f /root/annakonda/keys
```

This creates:

- `/root/annakonda/keys` — private key
- `/root/annakonda/keys.pub` — public key

### Step 2: Set permissions

```bash
chmod 700 /root/annakonda
chmod 400 /root/annakonda/keys
```

The directory permissions above are suitable for this example if you are using the directory exclusively for this purpose. Avoid changing permissions on shared directories without checking their needs.

### Step 3: Add the public key to GitHub

```bash
cat /root/annakonda/keys.pub
```

Copy the output and add it to GitHub under **Settings → SSH and GPG keys**.

### Step 4: Test using the custom key

```bash
ssh -i /root/annakonda/keys -o IdentitiesOnly=yes -T git@github.com
```

Here:

- `-i` specifies the private key file.
- `IdentitiesOnly=yes` tells SSH to use the explicitly configured identity.
- `-T` tests authentication without requesting an interactive shell.

### Step 5: Push using the custom key for one command

```bash
GIT_SSH_COMMAND="ssh -i /root/annakonda/keys -o IdentitiesOnly=yes" git push -u origin master
```

For a `main` branch:

```bash
GIT_SSH_COMMAND="ssh -i /root/annakonda/keys -o IdentitiesOnly=yes" git push -u origin main
```

**Important:** `GIT_SSH_COMMAND` applies only to that command. It is not a permanent configuration.

---

## 4. Customized SSH Key Location — Permanent Configuration

Instead of specifying `GIT_SSH_COMMAND` every time, configure SSH to use the custom key automatically.

### Step 1: Create or edit the SSH config file

```bash
mkdir -p /root/.ssh
vi /root/.ssh/config
```

Add:

```text
Host github.com
    HostName github.com
    User git
    IdentityFile /root/annakonda/keys
    IdentitiesOnly yes
```

### Step 2: Set SSH config permissions

```bash
chmod 700 /root/.ssh
chmod 600 /root/.ssh/config
chmod 400 /root/annakonda/keys
```

### Step 3: Test authentication

```bash
ssh -T git@github.com
```

SSH should now use `/root/annakonda/keys` automatically when connecting to `github.com`.

### Step 4: Push normally

```bash
git push -u origin master
```

Or:

```bash
git push -u origin main
```

You no longer need `GIT_SSH_COMMAND` for this connection.

**Note:** This example assumes the SSH commands are running as `root`. SSH configuration is per-user, so another Linux user needs their own appropriate configuration.

---

## 5. Git Branch and Push Commands

### Check the current branch

```bash
git branch
```

### Rename the current branch

```bash
git branch -M main
```

### Push `master` to remote `master`

```bash
git push -u origin master
```

### Push `main` to remote `main`

```bash
git push -u origin main
```

### Push local `main` to remote `master`

```bash
git push -u origin main:master
```

In `main:master`, the name before the colon is the local branch; the name after it is the remote branch.

### Check remote URLs

```bash
git remote -v
```

### Add a remote

```bash
git remote add origin git@github.com:satpathy1991-devops/training.git
```

If `origin` already exists, update it instead:

```bash
git remote set-url origin git@github.com:satpathy1991-devops/training.git
```

---

## 6. Git Commands Used During Practice

### Check status

```bash
git status
```

### Add a specific file

```bash
git add shellscript.sh
```

### Add all eligible changes

```bash
git add .
```

Always check the files before committing.

### Commit changes

```bash
git commit -m "Add shell script"
```

### Check commit history

```bash
git log --oneline
```

### Show the last three commits

```bash
git log --oneline -3
```

### Initialize a repository

```bash
git init
```

### Remove a file from Git tracking but keep it on disk

```bash
git rm --cached filename
```

This is useful for removing a file from the index, but it does not remove that file from previous commits.

---

## 7. Using `.gitignore` to Exclude Private Keys

Example `.gitignore` entries:

```text
keys
keys.pub
id_ed25519
id_ed25519.pub
*.pem
```

These entries help prevent matching, untracked files from being added accidentally.

Check ignored files:

```bash
git status --ignored
```

Check whether a file is ignored and which rule applies:

```bash
git check-ignore -v keys
```

**Important limitations:**

- `.gitignore` does not untrack files already tracked by Git.
- `.gitignore` does not remove a secret from commit history.
- A public key is not secret, but it can still be excluded if you prefer.
- Never commit private SSH keys, passwords, access tokens, or other credentials.

For real projects, store private keys outside the repository.

---

## 8. Common SSH Errors and Their Meaning

### Error: `Permission denied (publickey)`

Possible causes:

- The correct public key has not been added to GitHub.
- SSH is selecting the wrong private key.
- The private key has incorrect permissions.
- SSH is running under a different Linux user.
- The SSH configuration points to the wrong file.

Useful diagnostic commands:

```bash
ssh -T git@github.com
```

```bash
ssh -i /root/annakonda/keys -o IdentitiesOnly=yes -T git@github.com
```

```bash
ssh -vT git@github.com
```

The verbose command helps show which keys SSH attempts to use.

### Error: `GH013: Repository rule violations found`

This can occur when GitHub Push Protection detects a secret in a commit.

Removing the file from the current directory is not enough if the secret remains in commit history.

Do not bypass the protection for a private key. Replace the exposed key, remove the secret from the affected history, and verify the corrected commits before pushing again.

---

## 9. Standard vs. Custom SSH Method

| Feature | Standard key location | Custom key location |
|---|---|---|
| Example private key | `/root/.ssh/id_ed25519` | `/root/annakonda/keys` |
| Public key registered with GitHub | Yes | Yes |
| Authentication test | `ssh -T git@github.com` | Use `ssh -i` initially |
| Normal `git push` | Usually works automatically | Requires explicit key selection or SSH config |
| Permanent setup | Default SSH behavior or config | Configure `IdentityFile` in SSH config |
| Recommended security practice | Keep key outside Git repository | Keep key outside Git repository |

---

## 10. Final Workflow to Remember

### Standard SSH

```bash
ssh -T git@github.com
git status
git add .
git commit -m "Your commit message"
git push -u origin master
```

### Custom SSH — One Command

```bash
ssh -i /root/annakonda/keys -o IdentitiesOnly=yes -T git@github.com
GIT_SSH_COMMAND="ssh -i /root/annakonda/keys -o IdentitiesOnly=yes" git push -u origin master
```

### Custom SSH — Permanent Config

```bash
vi /root/.ssh/config
ssh -T git@github.com
git push -u origin master
```

### Key Takeaways

1. SSH authenticates GitHub connections using a private/public key pair.
2. The private key stays on your machine; the public key is registered with GitHub.
3. A custom key path works when SSH is explicitly told where the key is.
4. `GIT_SSH_COMMAND` is temporary; `/root/.ssh/config` can configure persistent key selection.
5. Branch names in the push command must match the intended local and remote branches.
6. Never commit private keys. Keep them outside Git repositories and protect their permissions.
