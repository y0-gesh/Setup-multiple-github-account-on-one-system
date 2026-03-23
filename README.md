# Using Multiple GitHub Accounts on One System (Work + Personal)

This guide explains how to configure **two GitHub accounts on the same machine** using SSH.  
Example identities used in this setup:

- **Work Account:** ritesh@eigenstudio.com  
- **Personal Account:** ritesh-avkalan@gmail.com  

This approach prevents authentication conflicts and ensures commits are attributed to the correct account.

---

# Overview

The setup consists of:

1. Generating **separate SSH keys** for each account
2. Adding the keys to the **SSH agent**
3. Configuring **SSH aliases**
4. Adding the keys to **GitHub**
5. Cloning repositories using the correct alias
6. Setting Git identity per repository

---

# 1. Generate SSH Keys

Create separate SSH keys for each account.

### Work Account

```bash
ssh-keygen -t ed25519 -C "ritesh@eigenstudio.com"

Save the key as:

~/.ssh/id_ed25519_eigen
Personal Account
ssh-keygen -t ed25519 -C "ritesh-avkalan@gmail.com"

Save the key as:

~/.ssh/id_ed25519_personal

After generation, the .ssh directory should contain:

~/.ssh/
│
├── id_ed25519_eigen
├── id_ed25519_eigen.pub
├── id_ed25519_personal
├── id_ed25519_personal.pub
2. Start SSH Agent and Add Keys

Start the SSH agent and register both keys.

eval "$(ssh-agent -s)"

ssh-add ~/.ssh/id_ed25519_eigen
ssh-add ~/.ssh/id_ed25519_personal

Verify:

ssh-add -l
3. Configure SSH

Create or edit the SSH config file.

~/.ssh/config

Example configuration:

# Work GitHub Account
Host github-eigen
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_eigen

# Personal GitHub Account
Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal

This configuration allows you to authenticate to GitHub using different SSH identities.

4. Add SSH Keys to GitHub

Copy the public keys and add them to the corresponding GitHub accounts.

Work Account
cat ~/.ssh/id_ed25519_eigen.pub

Add it to:

GitHub → Settings → SSH and GPG Keys
Personal Account
cat ~/.ssh/id_ed25519_personal.pub

Add it to:

GitHub → Settings → SSH and GPG Keys
5. Clone Repositories Using the Correct Host

Use the SSH host aliases defined in the config file.

Work Repositories
git clone git@github-eigen:EigenStudio/repository-name.git
Personal Repositories
git clone git@github-personal:username/repository-name.git
6. Configure Git Identity Per Repository

Set the Git identity inside each repository.

Work Repository
git config user.name "Ritesh"
git config user.email "ritesh@eigenstudio.com"
Personal Repository
git config user.name "Ritesh"
git config user.email "ritesh-avkalan@gmail.com"

Verify configuration:

git config --list
7. Test SSH Authentication

Test each account connection.

Work
ssh -T git@github-eigen
Personal
ssh -T git@github-personal

Expected output:

Hi <username>! You've successfully authenticated.
Recommended Folder Structure

Organizing repositories by account can simplify management.

~/work/
   eigenplus-frontend
   eigenplus-backend
   eigenplus-infra

~/personal/
   portfolio
   side-project
Common Issues
Wrong Account Used for Push

Ensure repositories were cloned using the correct host:

git@github-eigen:...
git@github-personal:...

Not:

git@github.com:...
SSH Key Not Loaded

Run:

ssh-add ~/.ssh/id_ed25519_eigen
ssh-add ~/.ssh/id_ed25519_personal
Summary

This configuration allows a single machine to use multiple GitHub accounts without conflicts by combining:

Separate SSH keys
SSH host aliases
Per-repository Git identity configuration

If you want, I can also provide a **more advanced README used in production teams** that includes:

- automatic identity switching  
- Windows/Linux differences  
- GitHub organization workflows.
