# GitHub SSH Authentication

This server has SSH key authentication configured for GitHub via the GitHub SSH Setup webapp.

## Key Location

```
~/.ssh/id_ed25519_github      # Private key
~/.ssh/id_ed25519_github.pub  # Public key
~/.ssh/config                 # SSH config (maps github.com to this key)
```

## Check Status

```bash
# Verify SSH connection to GitHub
ssh -T git@github.com

# Show the public key
cat ~/.ssh/id_ed25519_github.pub

# Check git identity
git config --global user.name
git config --global user.email
```

## Clone Private Repos

```bash
# SSH URLs work automatically
git clone git@github.com:owner/repo.git

# Or set a specific directory
git clone git@github.com:owner/repo.git /root/projects/repo
```

## Common Operations

```bash
# Set remote to SSH (if repo was cloned via HTTPS)
git remote set-url origin git@github.com:owner/repo.git

# Push changes
git add -A && git commit -m "message" && git push

# Pull latest
git pull origin main
```

## Troubleshooting

```bash
# Permission denied
# → Key may not be added to GitHub. Show the public key and add at https://github.com/settings/ssh/new
cat ~/.ssh/id_ed25519_github.pub

# Wrong key being used
# → Check SSH config has the IdentityFile line
grep -A3 github.com ~/.ssh/config

# Fix permissions if needed
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519_github ~/.ssh/config
chmod 644 ~/.ssh/id_ed25519_github.pub

# Test with verbose output
ssh -vT git@github.com
```

## Generate a New Key (if needed)

```bash
ssh-keygen -t ed25519 -C "email@example.com" -f ~/.ssh/id_ed25519_github -N ""
```

Then add the public key to GitHub at https://github.com/settings/ssh/new.
