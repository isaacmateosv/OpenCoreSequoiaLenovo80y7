# Hackintosh Power & Rotation Master Guide

## Part 1: Terminal Power Fixes
These commands stop common Hackintosh sleep crashes and weird Lenovo power button behavior.

### 1. The Core Stability Fix
Prevents the laptop from waking up when you plug/unplug the charger and forces the screen, disk, and system to sleep in the correct order.
```bash
sudo pmset -a acwake 0 displaysleep 10 disksleep 10 sleep 15
```

### 2. The "Real Mac" Feature Killer
Turns off background features that only work on real Apple hardware. Keeping these on usually causes Hackintoshes to wake up and stay on or crash in your bag.
```bash
sudo pmset -a powernap 0 tcpkeepalive 0 networkoversleep 0 proximitywake 0
```

### 3. Hibernation & Bluetooth Fix
Prevents the system from trying to save RAM to the SSD (which fails on non-Apple firmware) and stops accidental mouse bumps from waking the PC.
```bash
sudo pmset -a hibernatemode 0 standby 0 autopoweroff 0 && sudo defaults write /Library/Preferences/com.apple.Bluetooth.plist RemoteWakeEnabled -bool false
```

---

## Part 2: Screen Rotation Lock (xbar)
A lightweight menu bar toggle to override the accelerometer and lock your orientation.

### 1. Setup
Install the controller and the menu bar host:
```bash
brew install displayplacer
brew install --cask xbar
```

### 2. Get Screen ID
Run `displayplacer list` and copy the `id` string for your internal panel.

### 3. The Script
Create a file named `rotation.1h.sh` in your xbar plugins folder. Replace `UUID` with your ID:

```bash
#!/bin/bash
echo "🖥️"
echo "---"
echo "Landscape | bash=/bin/bash param1=-c param2=\"/usr/local/bin/displayplacer 'id:UUID degree:0'\" terminal=false"
echo "Portrait | bash=/bin/bash param1=-c param2=\"/usr/local/bin/displayplacer 'id:UUID degree:90'\" terminal=false"
echo "Flipped | bash=/bin/bash param1=-c param2=\"/usr/local/bin/displayplacer 'id:UUID degree:270'\" terminal=false"
```

### 4. Permissions
Make it runnable:
```bash
chmod +x /path/to/rotation.1h.sh
```

**Note:** If the screen still auto-rotates when you tilt it, disable your accelerometer kext (`VoodooI2CSensorHub.kext`) in your `config.plist` to give this menu bar script absolute control.