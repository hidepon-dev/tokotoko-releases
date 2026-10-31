<div align="center">

# TokoToko 🐾

**From the cyberspace between pixels, straight to your desktop.**  
An ultra-lightweight, autonomous desktop mascot that keeps you company and syncs with your development tools and AI assistants.

**English** | [日本語 (Japanese)](README.ja.md)

![TokoToko](assets/TokoToko_Bit_Garden_Edition_1920x1072.png)

</div>

---

## 🌿 What is TokoToko?

> *"Did you know there's a little garden hidden between the pixels as you write code?*  
> *A world where residents listen to your keystrokes like music and bask in compile heat like sunlight — Bit Garden."*

**TokoToko** is an ultra-lightweight desktop mascot that gently strolls across the edge of your screen.  
Led by its first resident, **Toko**, these companions quietly stay by your side—celebrating successful terminal builds, pondering alongside AI assistants, and never getting in the way of your focused workflow.

Designed with a **"non-intrusive, silent companion"** philosophy, TokoToko produces **no sound effects or voice output** and **walks horizontally along your screen edges** without jumping around or obstructing your windows. It is completely safe to keep running during online meetings, late-night coding sessions, or while listening to music.

---

## ✨ Features

* 🪶 **Ultra-Lightweight & Minimal Footprint**  
  Built with Tauri v2 and Rust, keeping resident memory usage and CPU load to an absolute minimum. Runs smoothly on laptops without draining battery life.
* 🛡️ **Focus Mode (Click-Through)**  
  Toggle click-through transparency so mouse clicks pass straight through the mascot. Work on windows, select text, or click buttons beneath the mascot without interruption.
* 💬 **Frosted Glass Notification Bar**  
  A sleek, frosted-glass bar pops up above the mascot's head, dynamically switching icons and accent colors based on the sender (Terminal, Gemini, Mail, etc.).
* 🖼️ **Character Slots (Up to 5 Slots) & Kizuna Bond**  
  In addition to the built-in "Toko", register up to 4 custom or downloadable characters (`.png` / `.webp` / `.toko` / `.zip` / `.tokochar`). Your Kizuna bond rank grows as you spend time working together.
* 🌿 **Bit Garden (🔒 Coming Soon)**  
  A dedicated window overlooking the cybernetic garden where residents live and wander together. Scheduled for upcoming updates (Admission via Bit Garden Passport will be available on external platforms like BOOTH).
* 🧩 **Smart Integrations & IPC**  
  Seamlessly integrates with Google Chrome extensions ([TokoToko Gemini Integration](https://chromewebstore.google.com/detail/ccchboekidhhodepmlkdddhpmekbhlhc)) and supports one-liner triggers via local inter-process communication (Unix Domain Sockets on macOS / Named Pipes on Windows).

---

## 📦 Installation

Download and install TokoToko for your operating system:

### 🪟 Windows
* **Microsoft Store** (Recommended): One-click installation and automatic updates from the Microsoft Store.  
  <br>
  <a href="https://get.microsoft.com/installer/download/9pdgm8gc5nlf?referrer=appbadge" target="_self">
    <img src="https://get.microsoft.com/images/en-us%20dark.svg" width="200"/>
  </a>
* **GitHub Releases**: Download the `.msi` installer from our [Releases page](https://github.com/hidepon-dev/tokotoko-releases/releases).  
  *(Note: If Windows SmartScreen displays "Windows protected your PC" during installation, click **More info > Run anyway**).*

---

### 🍏 macOS
Download the latest `.dmg` from our [Releases page](https://github.com/hidepon-dev/tokotoko-releases/releases) and drag `TokoToko.app` into your **Applications** folder.

> 💬 **A Quick Note for macOS Users from Developer hidepon**  
> TokoToko for macOS is distributed as an un-notarized app because the developer (hidepon) has not enrolled in the Apple Developer Program (which requires an annual paid subscription and real-name public disclosure).  
> We sincerely apologize for the inconvenience and extra steps required. As this is an independently developed indie project, we kindly ask for your understanding and gracious support!

#### ⚠️ If you see: *"TokoToko is damaged and can't be opened. You should move it to the Trash"*
Due to strict Gatekeeper security in recent macOS versions (Tahoe / Golden Gate), un-notarized apps are automatically blocked and cannot be opened via System Settings.  
Before launching the app for the first time, open **Terminal.app** and run the following command to remove the quarantine flag:

```bash
xattr -cr /Applications/TokoToko.app
```
*After running this command once, you can launch TokoToko normally with a double-click.*

---

## 🚀 Basic Usage

1. **Launch & Exit**  
   Start the application, and Toko will appear on your desktop. Right-click the mascot (or the tray icon) to open the context menu and select **Quit** to exit.
2. **Focus Mode (Click-Through)**  
   Enable **Focus Mode** from the menu to make the mascot click-through. **To disable Focus Mode, click the TokoToko icon in your macOS Menu Bar or Windows System Tray and uncheck Focus Mode.**
3. **System Core Settings (`⚙️ System Core...`)**  
   Open **`⚙️ System Core...`** from the menu to customize walking speed, display scale (50% to 120%), character slots, and notification bar styling.

👉 For full control instructions, shell/extension setup, and troubleshooting FAQ, see the **[TokoToko User Guide](docs/USER_GUIDE.md)** *(or [Japanese](docs/USER_GUIDE.ja.md))*.

---

## 🛠️ Create Custom Characters & Integrations!

TokoToko offers open interfaces designed for creators and hackers who want to build their own companions and workflows.

### 🎨 Custom Characters & Open Packages
Add your own hand-drawn sprite sheets (`.png` / `.webp`) or character packages bundling names and quotes (`.toko` / `.zip`) simply by dragging and dropping them into the app.  
* 👉 **[Custom Character & Sprite Sheet Specification Guide](docs/SPRITE_SPEC.md)** *(or [Japanese](docs/SPRITE_SPEC.ja.md))*
* 👉 **[Character Package (`.toko` / `.zip`) Creation Guide](docs/CHARACTER_PACKAGE.md)** *(or [Japanese](docs/CHARACTER_PACKAGE.ja.md))*

### ⚡ CLI & Terminal Integration (IPC)
TokoToko exposes a local socket (`/tmp/tokotoko_mascot.sock` on macOS / `\\.\pipe\tokotoko_mascot.sock` on Windows). Send a single line of JSON to trigger custom mascot animations and speech bubbles instantly.

* **macOS (zsh / fish / bash) One-Liner:**
  ```bash
  echo '{"source":"terminal","action":"jumping","duration":3000,"text":"Build succeeded!"}' | nc -U /tmp/tokotoko_mascot.sock
  ```

* **Windows (PowerShell) One-Liner:**
  ```powershell
  $pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out); $pipe.Connect(500); $w = New-Object System.IO.StreamWriter($pipe); $w.WriteLine('{"source":"terminal","action":"jumping","duration":3000,"text":"Task finished!"}'); $w.Flush(); $w.Close(); $pipe.Close()
  ```

Hook it into CI/CD build scripts, editor tasks, or home automation webhooks to build your own companion workflow.  
* 👉 **[Local IPC Communication Protocol Specification](docs/IPC_SPEC.md)** *(or [Japanese](docs/IPC_SPEC.ja.md))*

---

## 📄 License, Terms of Use & Privacy

This software is freeware (proprietary) provided free of charge. Source code disclosure, reverse engineering, and unauthorized redistribution are strictly prohibited.

* **Personal & Organizational Use**:  
  Free of charge for standard use on personal and company-issued machines.
* **Commercial & Embedded Use**:  
  Embedding this software into commercial products, advertisements, or reselling/redistributing it is prohibited.  
  *(Note: Having TokoToko appear on screen during live streaming, screen sharing, or presentation videos is warmly welcomed!)*

For detailed licensing conditions, credits, restrictions, and privacy policies, please refer to:
* 📜 **[Software Terms of Use (TERMS_OF_USE.md)](TERMS_OF_USE.md)**
* 🔒 **[Privacy Policy (PRIVACY_POLICY.md)](docs/privacy/PRIVACY_POLICY.md)**

---

<div align="center">
  <sub>Designed & Developed with ❤️ by hidepon & Alice</sub><br>
  <sub>Copyright © 2026 TokoToko Project 🐾. All rights reserved.</sub>
</div>
