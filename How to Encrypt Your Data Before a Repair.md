# 🔐 How to Encrypt Your Data Before a Repair

When you're handing your device to a repair shop—especially one you don't know well—it's smart to protect your private data *before* it ever leaves your sight. This guide walks you through simple, effective ways to encrypt your files and still be able to unlock them later—even if you're using a different device.

---

## 🧠 Why You Should Encrypt

* Prevents repair technicians from accessing sensitive data (photos, financial records, saved passwords, etc.)
* Shields you from accidental or intentional data theft
* Gives you peace of mind even if you're not able to remove your hard drive

---

## 🔐 Recommended Encryption Tools

### ✅ **1. VeraCrypt** (Best Overall)

* Cross-platform (Windows, Mac, Linux)
* Creates a password-protected “vault” file
* You can move the vault between devices

**How to Use:**

1. Download VeraCrypt: [https://www.veracrypt.fr/](https://www.veracrypt.fr/)
2. Create an encrypted volume (container)
3. Mount it as a virtual drive
4. Move your private files into it
5. Dismount and back up the volume to a USB or external drive

🧠 **Tip:** You can even disguise the vault as a video or photo file using advanced settings.

---

### ✅ **2. 7-Zip or WinRAR** (Quick + Easy)

* Great for encrypting folders fast
* AES-256 bit encryption (with a strong password)

**How to Use:**

1. Right-click any folder ➜ `Add to archive`
2. Choose `.7z` or `.zip`
3. Set a strong password
4. Enable **“Encrypt file names”**
5. Save the file to a USB or external location

📌 Works on Windows out of the box with 7-Zip: [https://www.7-zip.org/](https://www.7-zip.org/)

---

### ✅ **3. BitLocker (Windows Only)**

* Encrypts your entire drive or a USB stick
* Secure, but less portable unless you have the recovery key

**To Use It Safely:**

* Go to `Control Panel > BitLocker Drive Encryption`
* Turn on BitLocker for a specific drive
* When prompted, **SAVE your recovery key** to a file or print it

🧠 **Important:** If you use TPM-only mode and lose access to the hardware, you’re locked out without the recovery key.

---

### 🚫 Avoid These Mistakes

* Don’t rely on login passwords alone—they don’t protect against direct file access
* Don’t use FileVault or TPM-only BitLocker without exporting the recovery key
* Don’t leave vaults unlocked before handing the device over

---

## 🛡️ Stealth Tips

* Rename encrypted files to something boring: `update.pkg` or `drivers.zip`
* Store vaults in hidden or system folders
* Use a USB with hidden partitions if you’re extra paranoid

---

## 🔓 How to Access Later

* Make sure the decryption software is installed on the recovery device
* Always keep at least 1 backup of your password/recovery key in a safe place
* Test unlocking your vault *before* sending your machine off

---

## 🔐 Using a Security Key for Maximum Protection

> 🔎 **What does a hardware encryption code look like?**
>
> Most modern security keys use 256-bit or 512-bit cryptographic material. These aren’t human-readable codes like a password—instead, they’re long cryptographic strings used behind the scenes.
>
> For example, a 512-bit key might look like this when represented in hexadecimal:
>
> `D6EFA91F04C2B16E93A2F1889C3E79E1A5415E6C87BC4D930F7DEB5F27A1C9486E9A4E5537C5BB1F7AFB2E44CB63D5FF`
>
> You never need to memorize this—the key stays stored in the chip of the hardware device, and is only released during cryptographic handshake operations (never as plaintext).

For next-level protection, you can use a **hardware security key** (like a YubiKey, SoloKey, or Nitrokey) to control access to your encrypted files. These physical devices store encryption keys securely and never expose them directly to your system—making it impossible for anyone to access your data without the physical key.

### 🛡️ How It Works:

* You store a **keyfile** or unlock code on the security key
* Your encrypted container (e.g., VeraCrypt) requires both a **password and the hardware key** to unlock
* No key = no access, even if someone clones your drive

### 🛠️ Setup Example (VeraCrypt + YubiKey):

1. Create a keyfile using YubiKey or generate one yourself
2. Store the keyfile on the YubiKey’s storage partition
3. Create a VeraCrypt volume that requires the keyfile to mount
4. Optionally, add a password on top for dual-factor protection

🧠 **Tip:** Rename your keyfile to something boring like `driver_cache.sys` or `nvdiag.cfg` for stealth.

### ❗ A Note on Titan Security Keys

While Google's Titan Security Keys are technically strong and use modern FIDO2/U2F standards, some users prefer alternatives like YubiKey or SoloKey due to privacy concerns. Titan Keys are Google-branded and produced by third-party manufacturers, and their firmware is not fully transparent. If your goal is to protect your data from *everyone*—including big tech—then choosing a vendor like Yubico offers a higher level of independence and trust.

---

### ✅ Why It’s Powerful:

* 🔒 The encryption key is **never stored on the computer**
* 👀 Even an advanced technician can't find or steal the key
* 🛑 Physically blocks unauthorized access unless the key is present

---

## 🧪 Test Before the Repair

* ✅ Try mounting your VeraCrypt volume on another machine using your keyfile
* ✅ Confirm your encrypted ZIP file opens with your password
* ✅ Verify your BitLocker recovery key works on a different system
* ✅ Check that your files are removed or hidden from plain sight

---

## 🔗 Tools Mentioned

* [VeraCrypt](https://www.veracrypt.fr) – Windows, macOS, Linux
* [7-Zip](https://www.7-zip.org) – Windows
* [WinRAR](https://www.win-rar.com/start.html?&L=0) – Windows/macOS
* [BitLocker Info](https://support.microsoft.com/en-us/help/4028713) – Windows
* [YubiKey](https://www.yubico.com/products/) – Multi-platform
* [SoloKeys](https://solokeys.com) – Open source hardware keys

---

## ⚖️ Legal Note

Unauthorized access to your data—even during a repair—is often illegal. Most jurisdictions consider it a privacy violation or data breach. You have the right to protect your information and hold others accountable if they misuse it.

---

## ✅ Summary

You don’t have to be a hacker to protect your data. With just 10 minutes and the right tools, you can lock down your files so tightly that even the nosiest repair tech won’t get in.

**Encrypt smart. Backup once. Sleep easier.**
