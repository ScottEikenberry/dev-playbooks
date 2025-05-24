a --tree# macOS Development Setup with Dotfiles

This guide walks through setting up a new macOS machine with dotfiles managed by GNU Stow and oh-my-zsh, with SSH authentication for GitHub.

## Prerequisites

- Fresh macOS machine
- Admin access to install software
- GitHub account with your dotfiles repository

## Step 1: Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the instructions to add Homebrew to your PATH:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

## Step 2: Install Essential Tools

```bash
# Install development tools
brew install git stow

# Install optional but recommended tools
brew install fzf pyenv git-delta eza bat ripgrep tree htop jq wget
```

## Step 3: Configure Git with Personal Information

```bash
# Set your personal git configuration
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
git config --global init.defaultbranch main
git config --global credential.helper osxkeychain
```

## Step 4: Set Up SSH Keys for GitHub

### Generate SSH Key

```bash
# Generate new SSH key (use your email)
ssh-keygen -t ed25519 -C "your-email@example.com"
# Press Enter for default location, optionally set a passphrase
```

### Configure SSH Agent

```bash
# Start SSH agent and add key
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_ed25519

# Copy public key to clipboard
pbcopy < ~/.ssh/id_ed25519.pub
```

### Add SSH Key to GitHub

1. Go to **GitHub.com** → Settings → SSH and GPG keys → New SSH key
2. Give it a descriptive title (e.g., "MacBook-Pro-2025")
3. Paste your public key and save

### Test SSH Connection

```bash
ssh -T git@github.com
# Should respond: "Hi username! You've successfully authenticated..."
```

## Step 5: Make SSH Agent Persistent

Add this to your shell profile to start SSH agent automatically:

```bash
echo '# Start SSH agent if not running
if [ -z "$SSH_AUTH_SOCK" ] || ! ssh-add -l >/dev/null 2>&1; then
  eval "$(ssh-agent -s)"
fi' >> ~/.zshrc
```

## Step 6: Install oh-my-zsh

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

When installation completes, exit the new shell session:

```bash
exit
```

## Step 7: Clone and Apply Dotfiles

### Clone Your Dotfiles Repository

```bash
# Clone using SSH (replace with your repository)
git clone git@github.com:YourUsername/dotfiles.git ~/dotfiles
cd ~/dotfiles
```

### Remove Conflicting Files

```bash
# Remove oh-my-zsh generated files that conflict with dotfiles
rm ~/.zshrc
rm ~/.zshrc.backup
rm ~/.zshrc.pre-oh-my-zsh
rm ~/.zprofile 2>/dev/null || true  # Remove if exists
```

### Apply Dotfiles with Stow

```bash
# Install all dotfile packages
stow bash emacs git zsh

# Verify symlinks were created
ls -la ~/.zshrc ~/.gitconfig
# Should show arrows pointing to dotfiles/package/file
```

## Step 8: Install Tool Dependencies

Install any tools your dotfiles expect:

```bash
# Set up fzf shell integrations
$(brew --prefix)/opt/fzf/install
# Answer 'y' to key bindings and fuzzy completion

# If using pyenv, set up environment
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init --path)"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

## Step 9: Load Your Configuration

```bash
# Reload shell with your dotfiles configuration
source ~/.zshrc
```

## Step 10: Test Everything

```bash
# Test Git with SSH
cd ~/dotfiles
git pull

# Test shell aliases and tools
ls      # Should use eza if aliased
cat     # Should use bat if aliased
fzf --version
pyenv --version

# Check your custom prompt shows hostname
# Should display: hostname:~/dotfiles (main ✔)
```

## Step 11: Convert Existing Repositories to SSH

For any existing repositories using HTTPS:

```bash
cd /path/to/repo
git remote set-url origin git@github.com:username/repository.git
```

## Troubleshooting

### SSH Agent Issues

If Git prompts for credentials after setup:

```bash
# Check if SSH agent is running and key is loaded
ssh-add -l

# If empty, add your key
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

### Stow Conflicts

If stow reports conflicts:

```bash
# Remove conflicting files
rm ~/.filename

# Or use --adopt to merge existing files into dotfiles
stow --adopt package-name
```

### Missing Dependencies

If you get "command not found" errors after loading .zshrc:

```bash
# Install the missing tool
brew install tool-name

# Reload shell
source ~/.zshrc
```

## Adding New Machines

To set up another machine:

1. Follow Steps 1-6 (install tools, SSH keys, oh-my-zsh)
2. Clone dotfiles repository
3. Remove conflicting files and stow
4. Install dependencies and test

## Customizing for Your Setup

### Repository Structure

Your dotfiles should be organized like:

```
dotfiles/
├── bash/
│   └── .bash_profile
├── git/
│   └── .gitconfig
├── zsh/
│   ├── .zshrc
│   └── .zshenv
└── README.md
```

### Essential .zshrc Elements

Your .zshrc should include:

- oh-my-zsh configuration
- Custom prompt with hostname
- Aliases for enhanced tools (eza, bat, etc.)
- Tool initialization (fzf, pyenv)

### Git Configuration

Your .gitconfig should include:

- Personal name and email
- Preferred merge/diff tools
- Useful aliases
- Security settings

## Maintenance

### Updating Dotfiles

```bash
cd ~/dotfiles
# Make changes to configuration files
git add .
git commit -m "Update configuration"
git push
```

### Syncing Across Machines

```bash
cd ~/dotfiles
git pull
# Changes are automatically applied via symlinks
```

### Backing Up Before Changes

```bash
# Backup current configs before major changes
cp ~/.zshrc ~/.zshrc.backup
cp ~/.gitconfig ~/.gitconfig.backup
```

## Security Notes

- Keep SSH private keys secure and never share them
- Use unique SSH keys per machine for better security
- Regularly rotate Personal Access Tokens if using any
- Be cautious about storing sensitive information in dotfiles
- Consider using separate dotfiles repositories for work vs personal

---

**Time Investment:** ~30 minutes for initial setup  
**Future Setup Time:** ~10 minutes per new machine  
**Maintenance:** Minimal - changes sync automatically
