# Emacs Key Bindings on macOS: GUI vs Terminal Mode

## Overview

Emacs key bindings behave differently between GUI mode (`emacs`) and terminal mode (`emacs -nw`) on macOS due to how the terminal application handles key combinations before passing them to Emacs.

## The Problem

- **GUI Emacs**: CMD key works as Meta (M-x, M-w, etc.)
- **Terminal Emacs**: CMD key is intercepted by Terminal.app, only ESC sequences work

## Key Mapping Solutions

### GUI Emacs Configuration

```elisp
;; GUI Emacs - CMD key as Meta
(when (and (eq system-type 'darwin) (display-graphic-p))
  (setq mac-command-modifier 'meta)
  (setq mac-option-modifier 'super)
  (setq ns-function-modifier 'hyper))
```

### Terminal Emacs Configuration

**Option 1: Terminal.app Settings**
1. Terminal → Settings → [Profile] → Keyboard
2. Check "Use Option as Meta key"
3. Now `OPT+x` works as `M-x` in `emacs -nw`

**Option 2: iTerm2 Settings** (Recommended)
1. Preferences → Profiles → Keys → General
2. Set "Left Option Key" to "Esc+"
3. More reliable than Terminal.app

### Combined Emacs Configuration

```elisp
;; Handle both GUI and terminal modes
(when (eq system-type 'darwin)
  (if (display-graphic-p)
      ;; GUI Emacs settings
      (progn
        (setq mac-command-modifier 'meta)
        (setq mac-option-modifier 'super)
        (setq ns-function-modifier 'hyper))
    ;; Terminal Emacs settings
    (setq mac-option-modifier 'meta)))
```

## Key Binding Reference

### GUI Mode (emacs)
- `CMD+x` → Meta-x (M-x)
- `CMD+w` → Meta-w (M-w) 
- `CMD+y` → Meta-y (M-y)

### Terminal Mode (emacs -nw)
- `OPT+x` → Meta-x (M-x)
- `OPT+w` → Meta-w (M-w)
- `OPT+y` → Meta-y (M-y)
- `ESC x` → Meta-x (M-x) - Always works as fallback

### Universal Bindings (Work in Both Modes)
- `C-x C-f` → Find file
- `C-x C-s` → Save file  
- `C-x C-c` → Quit Emacs
- `C-g` → Cancel command

## Why CMD Doesn't Work in Terminal

macOS Terminal.app reserves CMD key combinations for system functions:
- `CMD+C` → Copy (terminal function)
- `CMD+V` → Paste (terminal function)
- `CMD+T` → New tab
- `CMD+W` → Close window

These are intercepted by Terminal.app and never reach Emacs.

## Workarounds and Best Practices

### 1. Use Appropriate Mode for Task
```bash
alias em='emacs &'        # GUI mode - use CMD keys
alias emt='emacs -nw'     # Terminal mode - use OPT keys
```

### 2. Learn Both Key Sets
- **GUI work**: Use CMD as Meta
- **Terminal work**: Use OPT as Meta
- **SSH/Remote**: Use ESC sequences

### 3. Fallback Options
- `ESC x` always works as `M-x` in any mode
- `C-x` prefix commands work universally

## Testing Your Setup

### Verify GUI Mode
```bash
emacs &
# Test: CMD+x should open M-x prompt
```

### Verify Terminal Mode
```bash
emacs -nw
# Test: OPT+x should open M-x prompt
# Fallback: ESC x should always work
```

## Troubleshooting

### Common Issues

**"Option key doesn't work in terminal"**
- Check Terminal.app → Settings → Keyboard → "Use Option as Meta key"
- Try iTerm2 for more reliable key handling

**"CMD key doesn't work in GUI"**
- Verify `mac-command-modifier` is set to `'meta`
- Check that config applies only when `(display-graphic-p)` is true

**"Keys work inconsistently"**
- Ensure terminal profile settings are saved
- Restart terminal application after changes

### Debug Key Events

```elisp
;; Check what Emacs sees when you press keys
M-x describe-key
;; Then press the key combination to see what Emacs receives
```

## Related Configuration Files

- **Emacs config**: `~/.emacs` or `~/.emacs.d/init.el`
- **Terminal settings**: Terminal.app → Settings → Profiles
- **iTerm2 settings**: Preferences → Profiles → Keys

## Additional Resources

- [Emacs Wiki: Mac OS X](https://www.emacswiki.org/emacs/MacOSX)
- [Terminal.app Documentation](https://support.apple.com/guide/terminal/)
- [iTerm2 Documentation](https://iterm2.com/documentation.html)

---

*Last updated: May 2025*
*Platform: macOS 15.5 Sequoia with Emacs 29+*
