# Arch-Personal-Build 🚀

[![Arch Linux](https://img.shields.io/badge/OS-Arch%20Linux-blue.svg?logo=arch-linux&logoColor=white)](https://archlinux.org/)
[![Hyprland](https://img.shields.io/badge/WM-Hyprland-blueviolet.svg?logo=wayland&logoColor=white)](https://hyprland.org/)
[![Wayland](https://img.shields.io/badge/Display-Wayland-28a745.svg?logo=wayland&logoColor=white)](https://wayland.freedesktop.org/)
[![Lua](https://img.shields.io/badge/Config-Lua-2C2D72.svg?logo=lua&logoColor=white)](https://www.lua.org/)
[![Neovim](https://img.shields.io/badge/Editor-Neovim-57A143.svg?logo=neovim&logoColor=white)](https://neovim.io/)
[![Kitty](https://img.shields.io/badge/Terminal-Kitty-000000.svg?logo=kitty&logoColor=white)](https://sw.kovidgoyal.net/kitty/)
[![Hardware](https://img.shields.io/badge/Hardware-ASUS%20TUF%20A15-red.svg?logo=asus&logoColor=white)](https://www.asus.com/)

A streamlined, minimal, and keyboard-driven personal desktop environment and configuration setup for **Arch Linux**, built on the **Hyprland** Wayland compositor. Features Lua-based window manager configurations, modular Waybar styling, Catppuccin themes, custom CLI productivity scripts, and fine-tuned support for hybrid **AMD + NVIDIA** laptop graphics.

---

## 📑 Table of Contents

- [Overview & Highlights](#-overview--highlights)
- [Hardware & System Specifications](#-hardware--system-specifications)
- [Repository Structure](#-repository-structure)
- [Desktop Environment](#-desktop-environment)
- [Custom Utility Scripts (`bin/`)](#-custom-utility-scripts-bin)
- [Keyboard Shortcuts Reference](#-keyboard-shortcuts-reference)
- [Installation & Bootstrap Guide](#-installation--bootstrap-guide)
- [Bash Aliases & Git Workflow (`gp`)](#-bash-aliases--git-workflow-gp)

---

## ✨ Overview & Highlights

- **Window Manager**: [Hyprland](https://hyprland.org/) configured with [Lua](.config/hypr/hyprland.lua) via `hyprconf2lua`, featuring dwindle tiling, thin 1px gaps, subtle shadows, and 3-finger horizontal workspace gestures.
- **Status Bar & Notifications**: Modular [Waybar](.config/waybar/) layout split into reusable JSON components and CSS design tokens, paired with [SwayNC](.config/swaync/) for a unified notification center and quick-setting sliders.
- **Terminal & Shell**: [Kitty](.config/kitty/) with integrated Catppuccin theme, Bash prompt displaying dynamic git branch status, and automated temporary file cleanup.
- **Development Environment**: [Neovim](.config/nvim/) with Lua plugin architecture (Catppuccin, LSPConfig, None-LS, Nvim-Cmp, Treesitter, Neo-tree, and Telescope).
- **Custom Tooling**: Handcrafted CLI utilities in [`bin/`](bin/) including an interactive fuzzy shortcuts launcher (`shortcuts`), VPN Gate auto-scraper (`vpngate`), video wallpaper switcher (`livewp`), and direct kernel brightness controls (`bright`).
- **Hardware Integration**: Optimized for ASUS TUF / ROG laptops with hybrid graphics (AMD Renoir iGPU + NVIDIA GTX 1650 dGPU), AMD P-State CPU driver, and low-latency PipeWire audio with real-time microphone noise cancellation.

---

## 💻 Hardware & System Specifications

Optimized and tested on the following setup:

| Component | Specification |
| :--- | :--- |
| **Model** | ASUSTeK ASUS TUF Gaming A15 (`FA506IHRB`) |
| **CPU** | AMD Ryzen 5 4600H (6 Cores / 12 Threads @ 3.00 GHz, boost up to 4.00 GHz) |
| **Integrated GPU** | AMD Radeon Vega Series / Renoir (`amdgpu` + `vulkan-radeon`) |
| **Dedicated GPU** | NVIDIA GeForce GTX 1650 Mobile / Max-Q 4GB GDDR6 (`nvidia-dkms` + `egl-wayland`) |
| **RAM** | 8 GB DDR4 + Dynamic ZRAM Swap |
| **Storage** | 512 GB NVMe SSD (`SAMSUNG MZVLQ512HBLU-00B00`) |
| **Audio Stack** | PipeWire (ALSA / PulseAudio / JACK / WirePlumber) + RNNoise Denoising |

### System Optimization Details
- **Kernel Parameters**: Mode-setting enabled via `nvidia-drm.modeset=1 nvidia_drm.fbdev=1`, silent boot (`loglevel=3 quiet`), and native AMD power scaling (`amd_pstate=active`).
- **Display Brightness**: Direct non-root hardware control through `/sys/class/backlight` via custom udev video rules.
- **Microphone Denoising**: Filter chain plugin (`99-input-denoising.conf`) loaded automatically into PipeWire for crystal clear voice input.
- **Direct TTY1 Autologin**: Fast boot directly to `tty1`, automatically launching Hyprland upon login via `.profile`.

---

## 📂 Repository Structure

```
.
├── .bashrc                         # Shell configuration with custom git prompt & cleanup
├── .bash_aliases                   # Package management, system update & git aliases
├── .profile                        # Login profile auto-launching start-hyprland on tty1
├── .config/
│   ├── fuzzel/                     # Minimal Wayland application & shortcut launcher
│   ├── hypr/
│   │   ├── hyprland.lua            # Hyprland window manager configuration (Lua)
│   │   ├── hyprlock.conf           # Screen lock styling and layout
│   │   └── hyprpaper.conf          # Static wallpaper configuration
│   ├── kitty/                      # Kitty terminal settings and Catppuccin colors
│   ├── nvim/                       # Modular Neovim Lua setup with LSP & plugins
│   ├── pipewire/                   # PipeWire configuration and RNNoise input filter
│   ├── qutebrowser/                # Keyboard-focused browser configuration
│   ├── rog/                        # ASUS ROG Control Center configurations
│   ├── swaync/                     # Notification center schema, tokens & styles
│   └── waybar/                     # Modular status bar (modules/ and tokens/)
├── bin/                            # Custom helper scripts & workflow automation
├── install/
│   ├── aurs                        # List of essential AUR packages
│   ├── drivers                     # Video, Vulkan, and microcode drivers
│   ├── note                        # Automated system tweaks (GRUB, udev, sudoers)
│   ├── packages/                   # Modular pacman package lists (apps, code, desktop, etc.)
│   └── services                    # Systemd services to enable
└── wallpaper/                      # Static backgrounds and video loop wallpapers
```

---

## ⚙️ Desktop Environment

### 1. Hyprland ([`.config/hypr/hyprland.lua`](.config/hypr/hyprland.lua))
- Configured using Lua through `hyprconf2lua`.
- **Layout**: Dwindle layout with `preserve_split = true` and compact 1px inner/outer gaps.
- **Input**: 3-finger swipe gesture for seamless workspace navigation.
- **Autostart**: Automatically starts Waybar, Fcitx5 (IME), Live Wallpaper daemon (`livewp`), and `hyprpolkitagent`.

### 2. Waybar ([`.config/waybar/`](.config/waybar/))
- Fully modular design: individual module definitions in `modules/*.jsonc` (battery, audio, clock, network, system stats, workspace overview).
- Style variables isolated into reusable design tokens in `tokens/*.css`.

### 3. SwayNC ([`.config/swaync/`](.config/swaync/))
- Catppuccin-themed notification drawer with interactive action buttons.
- Real-time volume slider, display brightness slider, and MPRIS media playback controls.

### 4. Neovim ([`.config/nvim/`](.config/nvim/))
- Modern Lua plugin ecosystem.
- Integrated LSP (`nvim-lspconfig`) for language diagnostics and autocompletion (`nvim-cmp`).
- `neo-tree` for file management and `telescope` for fuzzy searching across projects.

---

## 🛠️ Custom Utility Scripts (`bin/`)

The [`bin/`](bin/) folder contains lightweight, purposeful scripts added directly to the user's `PATH`:

| Script | Language | Description |
| :--- | :--- | :--- |
| **[`shortcuts`](bin/shortcuts)** | Python | Interactive launcher (`fuzzel` / `fzf`) that dynamically parses and runs Hyprland keybinds, bash aliases, and bin scripts. |
| **[`vpngate`](bin/vpngate)** | Python | Scrapes the public VPNGate API, filters and scores servers by ping, bandwidth, and country, then outputs ready-to-use OpenVPN configs. |
| **[`vpn`](bin/vpn)** | Bash | Unified CLI for WireGuard (`wg-quick`) and OpenVPN, with built-in integration with `vpngate`. |
| **[`livewp`](bin/livewp)** | Bash | Video wallpaper daemon using `mpvpaper` that randomly shuffles and loops background video playlists. |
| **[`toggle`](bin/toggle)** | Bash | Single-command toggle to hide or show both Waybar and the live wallpaper simultaneously. |
| **[`shot`](bin/shot)** | Bash | Region-selection screenshot tool (`grim` + `slurp`) that writes to `~/Pictures/`, copies to clipboard, and triggers an OSD alert. |
| **[`vol`](bin/vol)** | Bash | Volume controller via `pactl` that provides synchronous, low-latency notification progress bars. |
| **[`bright`](bin/bright)** | Bash | Direct sysfs brightness adjuster (`/sys/class/backlight`) supporting increment/decrement steps and min/max toggles. |
| **[`wifi`](bin/wifi)** | Bash | Terminal wrapper around `nmtui` and `nmcli` with a quick toggle for hardware airplane mode (`rfkill`). |
| **[`envact`](bin/envact)** | Bash | Quick helper to create/activate Python virtual environments and register custom Jupyter kernels. |
| **[`extract`](bin/extract)** | Bash | Universal archive extractor handling `.tar.*`, `.zip`, `.7z`, `.rar`, and `.gz`. |
| **[`gp`](bin/gp)** | Bash | Git commit and push automation with flags (`-a`, `-m`) and automated token clipboard copy via `pass`. |
| **[`gi`](bin/gi)** | Bash | Appends specified paths or patterns to `~/.gitignore` safely avoiding duplicates. |
| **[`asm`](bin/asm)** | Bash | Compiles, links, and executes x86_64 NASM assembly source files in a single pass. |
| **[`cpp`](bin/cpp)** | Bash | Compiles and executes C/C++ files on the fly with automatic cleanup. |
| **[`tmp`](bin/tmp)** | Bash | Ephemeral scratch directory manager; works in tandem with `.bashrc` to purge files older than 7 days. |

---

## ⌨️ Keyboard Shortcuts Reference

The default modifier key is **`ALT`** (mapped as `mainMod` in [`hyprland.lua`](.config/hypr/hyprland.lua)).

### 🚀 Application & Launcher Shortcuts

| Shortcut | Action |
| :--- | :--- |
| `ALT + Q` | Open Kitty terminal emulator |
| `ALT + R` | Launch Fuzzel application runner |
| `ALT + K` | Launch fuzzy **Shortcuts & Command Finder** ([`shortcuts`](bin/shortcuts)) |
| `ALT + A` | Open Qutebrowser |
| `ALT + D` | Open Firefox |
| `ALT + S` | Open Spotify |
| `ALT + F` | Launch Neovim in Kitty |
| `ALT + Y` | Launch Yazi terminal file manager |
| `ALT + T` | Open qBittorrent |
| `ALT + B` | Open Pavucontrol audio settings |
| `ALT + O` | Open OBS Studio |
| `ALT + I` | Capture interactive region screenshot ([`shot`](bin/shot)) |

### 🪟 Window & Workspace Controls

| Shortcut | Action |
| :--- | :--- |
| `ALT + SHIFT + W` | Close active window |
| `ALT + V` | Toggle floating mode for active window |
| `ALT + P` | Toggle pseudo-tiling mode |
| `ALT + J` | Toggle layout split (dwindle) |
| `ALT + E` | Toggle fullscreen |
| `ALT + Space` | Cycle focus to next window |
| `ALT + Left / Right / Up / Down` | Move window focus directionally |
| `ALT + SHIFT + Left / Right / Up / Down` | Move active window position directionally |
| `ALT + 1..0` | Switch to workspace 1 through 10 |
| `ALT + SHIFT + 1..0` | Move active window to workspace 1 through 10 |
| `ALT + Tab` / `ALT + SHIFT + Tab` | Switch workspace forward / backward |
| `ALT + Scroll Up / Down` | Scroll through active workspaces |
| `ALT + Left Mouse Drag` | Move window interactively |
| `ALT + Right Mouse Drag` | Resize window interactively |

### 🎛️ Media, Hardware & Desktop Toggles

| Shortcut | Action |
| :--- | :--- |
| `F1` | Audio: Mute / Unmute toggle |
| `ALT + F1` | Audio: Set volume to 100% |
| `F2` / `F3` | Audio: Volume -1% / Volume +1% (repeating) |
| `F4` | Microphone: Mute / Unmute capture |
| `ALT + F5` | Open ASUS ROG Control Center |
| `F6` | Display: Toggle brightness (0% / 100%) |
| `F7` / `F8` | Display: Brightness -5% / Brightness +5% |
| `ALT + F9` | Submap: Enter "clean" distraction-free mode |
| `ALT + F10` | Lock screen (`hyprlock`) |
| `ALT + F11` | Put system into sleep / suspend |
| `ALT + F12` | Toggle Wi-Fi / Airplane mode (`wifi -t`) |
| `ALT + X` | Toggle Waybar visibility |
| `ALT + Z` | Shuffle / Toggle live wallpaper (`livewp`) |
| `ALT + CTRL + Z` | Kill all wallpapers and Waybar |
| `ALT + G` | Toggle Waybar & Live Wallpaper simultaneously (`toggle`) |
| `CTRL + D` | Clear SwayNC notifications |
| `CTRL + SHIFT + D` | Toggle SwayNC notification panel |

---

## 🛠️ Installation & Bootstrap Guide

The [`install/`](install/) directory provides an automated, organized setup for bootstrapping a fresh Arch Linux installation.

### Automated Setup (via Dotfile Aliases)

Once dotfiles are cloned to `$HOME`, you can run the full setup using the built-in `install` alias:

```bash
# Clone the repository to $HOME
git clone https://github.com/FYI1411/Arch-Personal-Build.git ~
cd ~

# Source aliases
source .bash_aliases

# Run full automated deployment
install
```

### Manual Step-by-Step Installation

If you prefer applying each stage manually:

#### 1. Hardware Drivers & Kernel Headers
**The drivers are dependant on your hardware and should be changed accordingly
```bash
sudo pacman -S - < install/drivers
```
*Installs Mesa, `xf86-video-amdgpu`, `amd-ucode`, `vulkan-radeon`, `nvidia-dkms`, `nvidia-utils`, `egl-wayland`, and `linux-headers`.*

#### 2. Apply System Configurations & Tweaks
Execute the post-install configuration script:
```bash
sudo ./install/note
```
*Applies the following optimizations:*
- **GRUB Configuration**: Appends `nvidia-drm.modeset=1 nvidia_drm.fbdev=1 amd_pstate=active` to default boot parameters, sets timeout, and enables os-prober.
- **TTY1 Autologin**: Configures `systemd-getty` for passwordless instant login to TTY1.
- **Passwordless Wheel**: Configures `NOPASSWD` in `/etc/sudoers`.
- **Backlight Permissions**: Adds udev rule granting `video` group write permissions to `/sys/class/backlight`.
- **Locale Generation**: Generates `en_US.UTF-8`.

#### 3. Install Package Groups
Packages are categorized under [`install/packages/`](install/packages/):
```bash
sudo pacman -S - < install/packages/desktop    # Hyprland, Wayland, Waybar, PipeWire, fonts
sudo pacman -S - < install/packages/apps       # MPV, OBS, qBittorrent, Qutebrowser
sudo pacman -S - < install/packages/code       # Neovim, Python, Clang, NPM
sudo pacman -S - < install/packages/commands   # Grim, Slurp, Pass, Yazi
sudo pacman -S - < install/packages/utils      # FZF, TLDR, NCDU, Reflector
sudo pacman -S - < install/packages/vpn        # WireGuard tools, openresolv
```

#### 4. Install AUR Packages
Using `yay`:
```bash
yay -S - < install/aurs
```
*Installs `mpvpaper`, `neovim-web-devicons-git`, `rog-control-center`, `spotify`, and `spotify-adblock`.*

#### 5. Enable System Services
```bash
sudo systemctl enable --now NetworkManager cronie bluetooth
```

---

## 💡 Bash Aliases & Git Workflow (`gp`)

Pre-configured shortcuts defined in [`.bash_aliases`](.bash_aliases) and helper scripts for fast system maintenance, navigation, and git operations.

### 📦 Complete List of Bash Aliases

#### 1. System Maintenance & Bootstrapping
| Alias | Exact Command | Purpose |
| :--- | :--- | :--- |
| `install` | `driver && package && aur_package && service && sudo ~/install/note` | Full system bootstrap sequence (drivers, packages, AUR, services, and system tweaks) |
| `update` | `aur --noconfirm && clean` | Upgrades all official and AUR packages, followed by complete cache cleaning |
| `clean` | `aur -Rns --noconfirm $(pacman -Qtdq); aur -Scc --noconfirm; sudo pacman -Scc --noconfirm` | Removes orphaned dependencies and purges `pacman` and `yay` cache |
| `mirror` | `sudo reflector --latest 10 --protocol https --sort rate --save /etc/pacman.d/mirrorlist` | Benchmarks and updates the 10 fastest HTTPS Arch mirrors |
| `update-grub` | `sudo grub-mkconfig -o /boot/grub/grub.cfg` | Regenerates the GRUB bootloader configuration |
| `driver` | `sudo pacman -S $(cat ~/install/drivers)` | Installs all hardware, Vulkan, and graphics drivers |
| `package` | `sudo pacman -S $(cat ~/install/packages/*)` | Installs all official repository packages |
| `aur_package` | `yay -S $(cat ~/install/aurs)` | Installs all configured AUR packages |
| `service` | `sudo systemctl enable --now $(cat ~/install/services)` | Enables and immediately starts all defined systemd services |
| `aur` | `yay` | Convenience shortcut to invoke `yay` AUR helper |
| `sdn` | `shutdown now` | Immediate system shutdown |

#### 2. Productivity & Daily Utilities
| Alias | Exact Command | Purpose |
| :--- | :--- | :--- |
| `open` | `xdg-open` | Opens any file or URL with the system default application |
| `mf` | `cd ~/Desktop` | Quick jump to the Desktop directory ("move to folder") |
| `cron` | `crontab ~/.crontab` | Installs/reloads cron schedule from the repository `.crontab` file |

#### 3. Git Shortcuts
| Alias | Exact Command | Purpose |
| :--- | :--- | :--- |
| `gs` | `git status` | Quick status check of staged and unstaged changes |
| `ga` | `git add -A` | Stages all modified, deleted, and untracked files |
| `gd` | `git diff` | Shows unstaged changes against HEAD |
| `gc` | `git checkout -- ` | Reverts unstaged modifications in the working tree |
| `gr` | `git restore --staged .` | Unstages all staged changes |

---

### 🔑 Git Push Automation & Password Manager (`pass` for `gp`)

The [`bin/gp`](bin/gp) script works as the primary commit and push utility alongside the `ga` / `gs` / `gd` / `gc` aliases. It integrates directly with **`pass`** (the standard Unix password manager) to securely copy your GitHub credentials before pushing.

#### How `gp` Works
```bash
gp          # Prompts for a commit comment, stages all changes (`git add -A`), commits, copies token, and pushes
gp -a       # Auto-commits with default message ("auto commit from gp command") and pushes
gp -m       # Skips commit and immediately pushes existing local commits
```

#### Under the Hood: `pass -c github`
At the end of [`bin/gp`](bin/gp), the script executes:
```bash
pass -c github && echo "Pass copied" && git push && git status
```
1. **Clipboard Decryption**: `pass -c github` decrypts the `github` credential and copies your GitHub Personal Access Token (PAT) directly to the system clipboard (`wl-clipboard` on Wayland).
2. **Terminal Notification**: Prints `Pass copied` to notify you that the credential is in the clipboard buffer (auto-cleared after 45 seconds).
3. **Push & Authenticate**: Runs `git push`. When prompted for your password or token by Git, simply paste it from your clipboard (`Ctrl+Shift+V` or middle-click).
4. **Verification**: Finishes with `git status` to verify synchronization with `origin`.

#### ⚙️ Initial Setup for `pass`
To use `gp` seamlessly, `pass` must have a `github` entry configured:

1. **Initialize `pass` with your GPG Key** (if not already done):
   ```bash
   gpg --full-generate-key
   pass init <your-gpg-id-or-email>
   ```

2. **Insert your GitHub Personal Access Token**:
   ```bash
   pass insert github
   # When prompted, paste your GitHub Personal Access Token (classic or fine-grained)
   ```

3. **Verify clipboard retrieval**:
   ```bash
   pass -c github
   # Should output: Copied github to clipboard. Will clear in 45 seconds.
   ```

> [!NOTE]
> `pass` requires `wl-clipboard` (included in [`install/packages/desktop`](install/packages/desktop)) to interact with the Wayland clipboard. If the `github` entry is missing, `gp` will print an error from `pass` and stop before pushing.

---

## 📄 License & Notes

Personal configuration files and utility scripts are open for personal adaptation, sharing, and modification. Feel free to fork and customize to suit your own machine!
