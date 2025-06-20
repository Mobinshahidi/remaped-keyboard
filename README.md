# Advanced Kanata Configuration

<div align="center">
  <h3>Optimized for Fast Typists (70+ WPM)</h3>
  <p>Smart home row modifiers with fast typing protection</p>
</div>

## What does this do?

This is an advanced [Kanata](https://github.com/jtroo/kanata) configuration designed for fast typists who want the productivity benefits of home row modifiers without the typical typing conflicts. 

**Key innovation:** Automatic detection of fast typing sequences to prevent accidental modifier activation while preserving full shortcut functionality.

### Features

- 🏠 **Smart Home Row Modifiers** - `asdfjkl;` act as modifiers when held, letters when tapped
- ⚡ **Fast Typing Protection** - Automatically disables modifiers during rapid typing (no more "as" → Meta+Alt)
- 🎯 **Chord Support** - Multiple modifiers work seamlessly (`a+d+spc+l` for workspace switching)
- 🔢 **Numbers + Arrows Layer** - Hold Space for instant access to numbers, symbols, and navigation
- 🔄 **Default Mode Toggle** - Press Insert to switch to normal keyboard for others to use
- 🚀 **Zero Delays** - Optimized timing for 70+ WPM typing speeds

## Quick Start

### Prerequisites

- [Kanata 1.8.1+](https://github.com/jtroo/kanata/releases) installed
- Root/Administrator privileges for keyboard access

### Installation

1. **Download the configuration:**
   ```bash
   wget https://raw.githubusercontent.com/mobinshahidi/kanata-config/main/kanata.kbd
   ```

2. **Run Kanata with this config:**
   ```bash
   # Linux/macOS
   sudo kanata --cfg kanata.kbd
   
   # Windows (run as Administrator)
   kanata.exe --cfg kanata.kbd
   ```

3. **For permanent setup, see the [Installation Guide](#installation-guide) below.**

## Key Mappings

### Home Row Modifiers
| Key | Tap | Hold |
|-----|-----|------|
| `a` | a | Left Meta/Cmd ⌘ |
| `s` | s | Left Alt ⌥ |
| `d` | d | Left Shift ⇧ |
| `f` | f | Left Ctrl ⌃ |
| `j` | j | Right Ctrl ⌃ |
| `k` | k | Right Shift ⇧ |
| `l` | l | Right Alt ⌥ |
| `;` | ; | Right Meta/Cmd ⌘ |

### Special Keys
| Key | Function |
|-----|----------|
| `Space` | Tap: Space, Hold: Numbers + Arrows Layer |
| `Caps Lock` | Backspace |
| `Left Shift` | Esc |
| `Right Shift` | Delete |
| `Insert` | Toggle Default Mode (normal keyboard) |

### Numbers + Arrows Layer (Hold Space)
| Keys | Output | With Shift |
|------|--------|------------|
| `qwertyuiop[]` | `1234567890-=` | `!@#$%^&*()_+` |
| `hjkl` | `←↓↑→` | - |

## Usage Examples

### Fast Typing (Automatic Protection)
```
Type "also flask ask" → Normal letters (no accidental modifiers)
Type "asdfjkl" rapidly → Normal letters
```

### Shortcuts
```
Hold a + t          → ⌘T (new tab)
Hold a + d + spc + l → ⌘⇧Space+⌥ (workspace switch)
Hold d + spc + -     → _ (underscore)
Hold l + tab + k     → ⌥Tab+⇧ (reverse tab)
```

### Numbers and Navigation
```
Hold Space + qwert → 12345
Hold Space + ⇧qwe  → !@#
Hold Space + hjkl  → ←↓↑→
```

## Installation Guide

### Linux

#### Using systemd (Recommended)

1. **Install Kanata:**
   ```bash
   # Fedora
   sudo dnf install kanata
   
   # Ubuntu/Debian
   wget https://github.com/jtroo/kanata/releases/latest/download/kanata
   sudo chmod +x kanata && sudo mv kanata /usr/local/bin/
   
   # Arch
   yay -S kanata-bin
   ```

2. **Set up configuration:**
   ```bash
   sudo mkdir -p /etc/kanata
   sudo wget -O /etc/kanata/kanata.kbd https://raw.githubusercontent.com/mobinshahidi/kanata-config/main/kanata.kbd
   ```

3. **Create systemd service:**
   ```bash
   sudo tee /etc/systemd/system/kanata.service > /dev/null <<EOF
   [Unit]
   Description=Kanata keyboard remapper
   After=graphical-session.target

   [Service]
   Type=simple
   ExecStart=/usr/bin/kanata --cfg /etc/kanata/kanata.kbd
   Restart=always
   RestartSec=1
   User=root

   [Install]
   WantedBy=multi-user.target
   EOF
   ```

4. **Enable and start:**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable --now kanata.service
   ```

### Windows

1. **Download Kanata** from [releases](https://github.com/jtroo/kanata/releases)
2. **Download configuration** to the same folder
3. **Run as Administrator:** `kanata.exe --cfg kanata.kbd`
4. **For auto-start:** Add to Windows startup or use Task Scheduler

### macOS

1. **Install Kanata:**
   ```bash
   brew install kanata
   ```

2. **Download configuration:**
   ```bash
   mkdir -p ~/.config/kanata
   wget -O ~/.config/kanata/kanata.kbd https://raw.githubusercontent.com/mobinshahidi/kanata-config/main/kanata.kbd
   ```

3. **Grant accessibility permissions** in System Preferences → Privacy & Security
4. **Run:** `sudo kanata --cfg ~/.config/kanata/kanata.kbd`

## Customization

### Timing Adjustments

Edit the configuration file to adjust sensitivity:

```lisp
(defvar
  tap-time 200      ; Increase for slower tap recognition
  hold-time 400     ; Increase for slower modifier activation
)

;; Fast typing detection (increase for more protection)
((key-timing 3 less-than 200))
```

### Key Remapping

Modify the `defalias` section:

```lisp
a (t! charmod a lmet)  ; Change lmet to your preferred modifier
lsft esc               ; Change to your preferred key
```

## Technical Details

### Smart Detection Algorithm

Based on proven research from [Kanata Discussion #1455](https://github.com/jtroo/kanata/discussions/1455):

- **Key-timing detection:** Monitors last 3 key presses within 200ms
- **Automatic switching:** Disables modifiers during fast typing sequences  
- **Chord preservation:** Allows multiple modifiers for shortcuts

```lisp
(deftemplate charmod (char mod)
  (switch 
    ((key-timing 3 less-than 200)) $char break  ; Fast = letters
    () (tap-hold-release 150 400 $char $mod) break  ; Slow = modifiers
  )
)
```

## Troubleshooting

### Modifiers not working
```bash
# Check Kanata status
sudo systemctl status kanata.service

# View logs  
sudo journalctl -u kanata.service -f

# Test configuration
sudo kanata --cfg kanata.kbd --dry-run
```

### Still having fast typing conflicts?
- Increase threshold: `((key-timing 3 less-than 250))`
- Increase hold time: `hold-time 500`

### Modifiers too slow?
- Decrease hold time: `hold-time 300`
- Decrease tap time: `tap-time 150`

## Contributing

Issues and pull requests welcome! Please test thoroughly on your system before submitting changes.

When reporting issues, include:
- Your operating system
- Kanata version
- Typing speed (approximate WPM)
- Specific key sequences that cause problems

## Related Projects

- [Kanata](https://github.com/jtroo/kanata) - The amazing keyboard remapper this configuration is built for
- [QMK](https://docs.qmk.fm/) - Keyboard firmware that inspired many of these features
- [kmonad](https://github.com/kmonad/kmonad) - Alternative keyboard remapper

## License

MIT License - Feel free to modify and share!

## Acknowledgments

- **jtroo** and the Kanata community for creating such a powerful tool
- **argenkiwi** and **gerhard-h** for the smart detection research in [Discussion #1455](https://github.com/jtroo/kanata/discussions/1455)
- The QMK and kmonad communities for pioneering advanced keyboard customization

---

<div align="center">
  <strong>Transform your typing experience! 🚀</strong><br>
  <em>Star this repo if it helps you type faster and more comfortably</em>
</div>
