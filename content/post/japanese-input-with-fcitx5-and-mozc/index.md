---
title: "Japanese Input with Mozc + Fcitx5 on Arch Linux (Wayland + Hyprland)"
description: A step-by-step guide to setting up Japanese input on Arch Linux using Fcitx5 and Mozc. Perfect for new Linux users who need reliable Japanese text input for study, work, or communication.
date: 2025-04-18 02:05:00+0700
slug: japanese-input-with-fcitx5-and-mozc
image: nihongo.png
categories:
    - Tech
tags:
    - Technology
    - Linux
    - Japanese
    - Tutorial
author: Nishimiya
---


---

This guide specifically covers installation on Arch Linux (and derivatives like Manjaro) using pacman. If you're using a different distribution (Ubuntu, Fedora, etc.), the package names and installation commands will vary. For example:

- Debian/Ubuntu: `apt install fcitx5-mozc`
- Fedora: `dnf install fcitx5-mozc`
- OpenSUSE: `zypper install fcitx5-mozc`

Always check your distro's documentation for the correct packages and setup steps. The Fcitx5 configuration (environment variables, themes, etc.) will remain similar across Linux systems.

# **1. Install Packages**  
Run the following command to install Fcitx5 and Mozc:  
```bash  
sudo pacman -S fcitx5 fcitx5-configtool fcitx5-qt fcitx5-mozc-ut  
```  

---

# **2. Set Environment Variables**  
Add these lines to your shell config file (`~/.bashrc`, `~/.zshrc`, or `~/.profile`):  
```bash  
export GTK_IM_MODULE="fcitx"  
export QT_IM_MODULE="fcitx"  
export XMODIFIERS=@im=fcitx  
```  

**Apply the changes**:  
```bash  
source ~/.zshrc  # or source ~/.bashrc/etc  
```  

Log out and back in for the variables to take effect.  

---

# **3. Configure Fcitx5**  
1. Open `fcitx5-configtool` from your app menu or terminal.  
2. Click **+** → Uncheck *"Only Show Current Language"* → Search **"mozc"** → Add it.  
3. Remove unnecessary input methods (e.g., "Keyboard - English") if desired.  

---

# **4. Enable Japanese Input**  
Press **Ctrl + Space** to toggle between English and Japanese (Hiragana). Test by typing `nihongo` → `にほんご`.  

---

# **5. Install Themes (Optional)**  
With `yay`:  
```bash  
# Dark theme  
yay -S fcitx5-skin-fluentdark-git  

# Light theme  
yay -S fcitx5-skin-fluentlight-git  

# Material Color  
yay -S fcitx5-material-color  
```  

**Apply themes**:  
- Right-click the Fcitx5 tray icon → **Configure** → **Addons** → **Classic UI** → Select a skin.  

---

# **6. Customize Mozc**  
Right-click the Fcitx5 tray icon → **Mozc Settings** to adjust:  
- Input mode (Romaji/Kana).  
- Dictionaries.  
- Keyboard shortcuts.  

---

# **Troubleshooting**  
- **Fcitx5 not starting?** Add it to your desktop environment’s autostart.  
- **Input not working in apps?** For Electron apps (e.g., VS Code), add these flags to their `.desktop` file:  
  ```  
  --enable-features=UseOzonePlatform --ozone-platform=wayland  
  ```  

---

Done! You now have a functional Japanese input setup. For advanced options, refer to the [Fcitx5 Wiki](https://wiki.archlinux.org/title/Fcitx5).
