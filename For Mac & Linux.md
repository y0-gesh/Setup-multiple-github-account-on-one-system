# Multiple GitHub Accounts Setup on Same Device (macOS/Linux)

This guide explains how to configure and use **multiple GitHub accounts** on the same machine using **SSH keys** and **Git configuration**.

---

# Goal

Use different GitHub accounts for different repositories without repeatedly changing credentials.

Example:

|Account Type|Email|
|---|---|
|Personal|personal@gmail.com|
|Work|work@company.com|

---

# Recommended Approach

Use:

- Separate SSH keys
- SSH host aliases
- Local Git config per repository

Avoid:

- Changing global Git email repeatedly
- Using HTTPS authentication for multiple accounts
- Using the same SSH key for both accounts

---

# Step 1 — Check Existing SSH Keys

Open terminal:

```
ls -al ~/.ssh
```

Example output:

```
id_ed25519id_ed25519.pubknown_hosts
```

---

# Step 2 — Generate Separate SSH Keys

## Personal GitHub Account

```
ssh-keygen -t ed25519 -C "personal@gmail.com"
```

When prompted for filename:

```
~/.ssh/id_ed25519_personal
```

---

## Work GitHub Account

```
ssh-keygen -t ed25519 -C "work@company.com"
```

When prompted for filename:

```
~/.ssh/id_ed25519_work
```

---

# Step 3 — Start SSH Agent

```
eval "$(ssh-agent -s)"
```

---

# Step 4 — Add SSH Keys to Agent

```
ssh-add ~/.ssh/id_ed25519_personalssh-add ~/.ssh/id_ed25519_work
```

Verify:

```
ssh-add -l
```

---

# Step 5 — Configure SSH

Edit SSH config file:

```
nano ~/.ssh/config
```

Add the following configuration:

```
# Personal GitHubHost github-personal    HostName github.com    User git    IdentityFile ~/.ssh/id_ed25519_personal    IdentitiesOnly yes# Work GitHubHost github-work    HostName github.com    User git    IdentityFile ~/.ssh/id_ed25519_work    IdentitiesOnly yes
```

Save and exit:

```
CTRL + OENTERCTRL + X
```

---

# Step 6 — Add SSH Public Keys to GitHub

## Copy Personal Public Key

```
cat ~/.ssh/id_ed25519_personal.pub
```

Copy the output.

Go to:

[GitHub SSH Settings](https://github.com/settings/keys?utm_source=chatgpt.com)

Click:

```
New SSH Key
```

Paste the key and save.

---

## Copy Work Public Key

```
cat ~/.ssh/id_ed25519_work.pub
```

Add this key to the second GitHub account.

---

# Step 7 — Test SSH Authentication

## Personal Account

```
ssh -T git@github-personal
```

Expected output:

```
Hi username! You've successfully authenticated...
```

---

## Work Account

```
ssh -T git@github-work
```

Expected output:

```
Hi company-user! You've successfully authenticated...
```

---

# Step 8 — Clone Repositories Correctly

This step is critical.

Do NOT use:

```
git clone git@github.com:user/repo.git
```

This bypasses your SSH aliases.

---

## Clone Personal Repository

```
git clone git@github-personal:username/repository.git
```

---

## Clone Work Repository

```
git clone git@github-work:company/repository.git
```

---

# Step 9 — Configure Git Identity Per Repository

Inside each repository:

---

## Personal Repository

```
git config user.name "Your Name"git config user.email "personal@gmail.com"
```

---

## Work Repository

```
git config user.name "Your Name"git config user.email "work@company.com"
```

---

# Verify Configuration

Check Git identity:

```
git config user.namegit config user.email
```

Check remote URL:

```
git remote -v
```

Expected:

```
git@github-personal:...
```

or

```
git@github-work:...
```

---

# Example Folder Structure

```
Projects/├── personal/│   ├── portfolio/│   └── side-project/│└── work/    ├── client-dashboard/    └── internal-tools/
```

---

# Common Mistakes

## Using Same SSH Key for Both Accounts

Result:

- Authentication conflicts
- Permission issues

---

## Using `github.com` Instead of Alias

Wrong:

```
git@github.com:user/repo.git
```

Correct:

```
git@github-personal:user/repo.git
```

---

## Changing Global Git Email Frequently

Wrong approach:

```
git config --global user.email "..."
```

Use local repository config instead.

---

# Optional — Automatic Git Identity Switching

Global Git config:

```
nano ~/.gitconfig
```

Add:

```
[includeIf "gitdir:~/Projects/work/"]    path = ~/.gitconfig-work[includeIf "gitdir:~/Projects/personal/"]    path = ~/.gitconfig-personal
```

---

## Work Git Config

File:

```
~/.gitconfig-work
```

```
[user]    name = Your Name    email = work@company.com
```

---

## Personal Git Config

File:

```
~/.gitconfig-personal
```

```
[user]    name = Your Name    email = personal@gmail.com
```

Now Git automatically selects the correct identity based on folder location.

---

# Useful Commands

## List Loaded SSH Keys

```
ssh-add -l
```

---

## Remove All SSH Keys

```
ssh-add -D
```

---

## Re-add SSH Keys

```
ssh-add ~/.ssh/id_ed25519_personalssh-add ~/.ssh/id_ed25519_work
```

---

# Final Result

You can now:

- Use multiple GitHub accounts on the same device
- Push to different repositories with different identities
- Avoid authentication conflicts
- Maintain clean commit history