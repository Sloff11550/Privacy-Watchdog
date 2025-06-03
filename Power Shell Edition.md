### 🛡️ Repair Shop Watchdog - PowerShell Edition

> 💬 **Why this exists:**
>
> In a perfect world, you could trust every repair technician with your data. But in reality, not all techs are honest—and not all laptops allow you to remove your hard drive. Many modern systems have **soldered internal storage**, meaning you can't just pop out the drive before handing your device over.
>
> **Privacy Watchdog** acts like a dashcam for your PC. It silently watches for activity—USB connections, opened folders, and screen behavior—so you have proof of what happened while your machine was out of your sight. It creates accountability in places where it usually doesn't exist.


This is a lightweight PowerShell version of the Repair Shop Watchdog script. It focuses on what PowerShell can do effectively without external dependencies:

- 🔌 Logs USB connection/disconnection events
- 📸 Captures screenshots when USB events are detected
- 📁 Stores logs and screenshots in hidden AppData folders

This script is useful for users who prefer a native, built-in solution without Python dependencies. While it lacks keylogging and video recording due to PowerShell limitations, it is still a powerful and silent tool for monitoring suspicious activity during repair scenarios.

> ⚠️ Use this responsibly. This tool is for protecting your own data and system while it's in someone else’s hands (e.g. repair shops). Do not use it for spying or unethical surveillance.

---

```powershell
# 🛡️ Repair Shop Watchdog - PowerShell Edition
# Logs USB device activity and takes screenshots silently

$LogDir = "$env:APPDATA\SystemMonitor\Logs"
$ScreenshotDir = "$env:APPDATA\SystemMonitor\Screenshots"

# Create directories
New-Item -ItemType Directory -Force -Path $LogDir | Out-Null
New-Item -ItemType Directory -Force -Path $ScreenshotDir | Out-Null

$LogFile = Join-Path $LogDir "usb_log.txt"

Function Log-USBChange {
    $usbDrives = Get-WmiObject Win32_DiskDrive | Where-Object { $_.InterfaceType -eq "USB" }
    $connected = $usbDrives | ForEach-Object { $_.DeviceID }
    return $connected
}

$previousState = @()

Function Capture-Screenshot {
    Add-Type -AssemblyName System.Windows.Forms
    Add-Type -AssemblyName System.Drawing

    $bounds = [System.Windows.Forms.Screen]::PrimaryScreen.Bounds
    $bitmap = New-Object System.Drawing.Bitmap $bounds.Width, $bounds.Height
    $graphics = [System.Drawing.Graphics]::FromImage($bitmap)
    $graphics.CopyFromScreen($bounds.Location, [System.Drawing.Point]::Empty, $bounds.Size)

    $timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
    $filename = Join-Path $ScreenshotDir "screenshot_$timestamp.png"
    $bitmap.Save($filename, [System.Drawing.Imaging.ImageFormat]::Png)
    $graphics.Dispose()
    $bitmap.Dispose()
}

# Start monitoring
while ($true) {
    $current = Log-USBChange
    $added = $current | Where-Object { $_ -notin $previousState }
    $removed = $previousState | Where-Object { $_ -notin $current }

    foreach ($dev in $added) {
        Add-Content $LogFile "[+] $(Get-Date -Format u): USB Connected: $dev"
    }

    foreach ($dev in $removed) {
        Add-Content $LogFile "[-] $(Get-Date -Format u): USB Disconnected: $dev"
    }

    # Log currently opened folders in File Explorer
    try {
        $shell = New-Object -ComObject Shell.Application
        $folders = $shell.Windows() | Where-Object { $_.Name -eq 'File Explorer' -or $_.Name -eq 'Windows Explorer' }
        foreach ($folder in $folders) {
            $path = $folder.Document.Folder.Self.Path
            if ($path) {
                Add-Content $LogFile "[~] $(Get-Date -Format u): Folder Opened: $path"
            }
        }
    } catch {}

    if ($added.Count -gt 0 -or $removed.Count -gt 0) {
        Capture-Screenshot
    }

    $previousState = $current
    Start-Sleep -Seconds 5
}
    Start-Sleep -Seconds 5
}
