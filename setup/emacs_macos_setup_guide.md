# Emacs Setup Guide for macOS

A comprehensive guide to setting up a modern Emacs development environment on macOS with enhanced completion, Swift support, and proper key bindings.

## Prerequisites

- macOS 10.15+ (tested on macOS 15.5 Sequoia)
- Homebrew package manager
- Command line tools for Xcode
- Git for configuration management

## Installation

### 1. Install Emacs via Homebrew

```bash
# Add the emacs-plus tap for enhanced macOS integration
brew tap d12frosted/emacs-plus

# Install Emacs with native compilation and imagemagick support
brew install emacs-plus@29 --with-imagemagick --with-no-frame-refocus

# Alternative: Use emacs-mac for even better macOS integration
# brew install emacs-mac --with-native-comp
```

### 2. Verify Installation

```bash
# Check version
emacs --version

# Test GUI mode
emacs &

# Test terminal mode  
emacs -nw
```

## Configuration

### 1. Create Enhanced Configuration File

Create `~/.emacs` with the following content:

```elisp
;; ====================
;; Enhanced .emacs Configuration
;; ====================

;; ====================
;; Window Size & Position (GUI only)
;; ====================
(setq initial-frame-alist
      '((top . 45) (left . 300) (width . 120) (height . 80)))
(setq default-frame-alist
      '((top . 100) (left . 100) (width . 80) (height . 30)))

;; ====================
;; Package Management
;; ====================
(require 'package)
(setq package-enable-at-startup nil)
(add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/"))
(add-to-list 'package-archives '("melpa-stable" . "https://stable.melpa.org/packages/"))
(package-initialize)

;; Bootstrap use-package
(unless (package-installed-p 'use-package)
  (package-refresh-contents)
  (package-install 'use-package))
(eval-when-compile (require 'use-package))
(setq use-package-always-ensure t)

;; Refresh package contents on first run
(unless package-archive-contents
  (package-refresh-contents))

;; ====================
;; Basic Configuration
;; ====================
(setq inhibit-startup-message t)
(tool-bar-mode -1)
(scroll-bar-mode -1)
(menu-bar-mode -1)
(setq make-backup-files nil)
(show-paren-mode 1)
(setq-default indent-tabs-mode nil)
(setq-default tab-width 4)

;; ====================
;; Editor Improvements
;; ====================
(global-display-line-numbers-mode t)
(global-hl-line-mode t)
(column-number-mode t)
(size-indication-mode t)
(setq scroll-margin 3
      scroll-conservatively 10000
      scroll-step 1)
(global-auto-revert-mode t)

;; ====================
;; Terminal Enhancements
;; ====================
(unless (display-graphic-p)
  (xterm-mouse-mode 1)
  (global-set-key [mouse-4] 'scroll-down-line)
  (global-set-key [mouse-5] 'scroll-up-line))

(setq select-enable-clipboard t
      select-enable-primary t
      save-interprogram-paste-before-kill t)

;; ====================
;; macOS Key Modifiers
;; ====================
(when (eq system-type 'darwin)
  (if (display-graphic-p)
      (progn
        (setq mac-command-modifier 'meta)
        (setq mac-option-modifier 'super)
        (setq ns-function-modifier 'hyper))
    (setq mac-option-modifier 'meta)))

;; ====================
;; Essential Packages
;; ====================

;; Show available keybindings
(use-package which-key
  :config (which-key-mode))

;; Better completion framework
(use-package ivy
  :config 
  (ivy-mode 1)
  (setq ivy-use-virtual-buffers t
        enable-recursive-minibuffers t))

;; Enhanced commands using ivy
(use-package counsel
  :after ivy
  :config (counsel-mode))

;; Better search within buffers
(use-package swiper
  :after ivy
  :bind (("C-s" . swiper)))

;; Better undo system
(use-package undo-tree
  :config (global-undo-tree-mode))

;; ====================
;; Development Packages
;; ====================

;; .editorconfig file support
(use-package editorconfig
  :config (editorconfig-mode +1))

;; Swift editing support
(use-package swift-mode
  :mode "\\.swift\\'"
  :interpreter "swift")

;; Rainbow delimiters for better code readability
(use-package rainbow-delimiters
  :hook ((prog-mode . rainbow-delimiters-mode)))

;; YAML mode
(use-package yaml-mode
  :mode "\\.ya?ml\\'")

;; Markdown support
(use-package markdown-mode
  :mode "\\.md\\'")

;; ====================
;; LSP Configuration
;; ====================

;; Language Server Protocol support
(use-package lsp-mode
  :hook ((swift-mode . lsp))
  :commands lsp)

;; Enhanced LSP UI
(use-package lsp-ui
  :after lsp-mode)

;; Auto-completion
(use-package company
  :hook (after-init . global-company-mode))

;; ====================
;; Utility Functions
;; ====================

;; Find sourcekit-lsp for Swift development
(defun find-sourcekit-lsp ()
  (or (executable-find "sourcekit-lsp")
      (and (eq system-type 'darwin)
           (string-trim (shell-command-to-string "xcrun -f sourcekit-lsp")))
      "/usr/local/swift/usr/bin/sourcekit-lsp"))
```

### 2. Configure Terminal Key Bindings

**For Terminal.app:**
1. Terminal → Settings → Profiles → Keyboard
2. Check "Use Option as Meta key"

**For iTerm2 (Recommended):**
1. Download and install iTerm2
2. Preferences → Profiles → Keys → General
3. Set "Left Option Key" to "Esc+"

### 3. First Launch and Package Installation

```bash
# Start Emacs - packages will auto-install
emacs

# If you see package installation errors, try:
# M-x package-refresh-contents
# Then restart Emacs
```

## Key Features

### Enhanced Completion System
- **Ivy/Counsel/Swiper**: Modern completion framework
- **Which-key**: Shows available key bindings
- **Company**: Auto-completion for code

### Development Tools
- **Swift support**: Syntax highlighting and LSP integration
- **LSP Mode**: Language server protocol support
- **Rainbow delimiters**: Color-coded parentheses/brackets
- **EditorConfig**: Consistent coding styles

### User Experience Improvements
- **Line numbers**: Visible in all buffers
- **Current line highlighting**: Easy cursor tracking
- **Better scrolling**: Smooth scroll behavior
- **Auto-revert**: Files update when changed externally

## Key Bindings Reference

### GUI Mode (emacs)
- `CMD+x` → M-x (command prompt)
- `CMD+w` → M-w (copy)
- `CMD+y` → M-y (yank/paste)

### Terminal Mode (emacs -nw)
- `OPT+x` → M-x (command prompt)
- `OPT+w` → M-w (copy)
- `OPT+y` → M-y (yank/paste)
- `ESC x` → M-x (fallback - always works)

### Universal Bindings
- `C-x C-f` → Find file
- `C-x C-s` → Save file
- `C-x C-c` → Quit Emacs
- `C-s` → Swiper search (enhanced search)
- `C-g` → Cancel command

## Usage Examples

### Enhanced File Navigation
```
M-x counsel-find-file    # Better file finding
M-x counsel-git          # Git-aware file search
M-x swiper               # Search within current buffer
```

### Development Workflow
```
M-x lsp                  # Start language server
M-x company-complete     # Manual completion
M-x rainbow-delimiters-mode  # Toggle colored brackets
```

## Troubleshooting

### Common Issues

**Package installation errors:**
```bash
# Refresh package contents
M-x package-refresh-contents

# Install missing packages manually
M-x package-install RET ivy RET
```

**Key bindings not working:**
- GUI mode: Check `mac-command-modifier` setting
- Terminal mode: Verify terminal app Option key settings

**LSP not working for Swift:**
```bash
# Ensure Swift toolchain is installed
xcrun -f sourcekit-lsp

# Install Swift development snapshot if needed
```

### Reset Configuration

**Start fresh if needed:**
```bash
# Backup current config
mv ~/.emacs ~/.emacs.backup
mv ~/.emacs.d ~/.emacs.d.backup

# Start with clean slate
emacs
```

## Customization

### Themes
```elisp
;; Add to your .emacs for dark theme in terminal
(unless (display-graphic-p)
  (load-theme 'modus-vivendi t))
```

### Additional Packages
```elisp
;; Example additional packages
(use-package magit          ; Git integration
  :bind (("C-x g" . magit-status)))

(use-package projectile     ; Project management
  :config (projectile-mode +1))
```

## File Management with Dotfiles

### Using GNU Stow (Optional)

```bash
# If managing with dotfiles repo
cd ~/dotfiles
stow emacs

# Important: Never stow auto-generated directories
# Add to .gitignore:
emacs/.emacs.d/elpa/
emacs/.emacs.d/auto-save-list/
emacs/.emacs.d/eln-cache/
```

## Additional Resources

- [Emacs Manual](https://www.gnu.org/software/emacs/manual/)
- [MELPA Package Archive](https://melpa.org/)
- [Emacs Wiki](https://www.emacswiki.org/)
- [Use-package Documentation](https://github.com/jwiegley/use-package)

---

*Last updated: May 2025*  
*Tested on: macOS 15.5 Sequoia, Emacs 29.x*  
*Author: [Your Name/Team]*