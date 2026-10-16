<div align="center">

# TokoToko 🐾

**From the cyberspace between pixels, straight to your desktop.**  
An ultra-lightweight, autonomous desktop mascot that keeps you company and syncs with your development tools and AI assistants.

**English** | [日本語 (Japanese)](README.ja.md)

<!-- Place demo video / GIF here -->
<!-- <p align="center"><img src="assets/demo.gif" alt="TokoToko Demo" width="600"></p> -->
https://github.com/user-attachments/assets/ad3d3bed-1db9-4a9a-b4e1-814875adcbbf


</div>

---

## 🌿 What is TokoToko?

> *"Did you know there's a little garden hidden between the pixels as you write code?*  
> *A world where residents listen to your keystrokes like music and bask in compile heat like sunlight — Bit Garden."*

**TokoToko** is an ultra-lightweight desktop mascot that gently strolls across the edge of your screen.  
Led by its first resident, **Toko**, these companions quietly stay by your side—celebrating successful terminal builds, pondering alongside AI assistants, and never getting in the way of your focused workflow.

---

## ✨ Features

* 🪶 **Ultra-Lightweight & Minimal Footprint**  
  Built with Tauri v2 and Rust, keeping resident memory usage and CPU load to an absolute minimum. Runs smoothly on laptops without draining battery life.
* 🛡️ **Focus Mode (Click-Through)**  
  Toggle click-through transparency with a single click. Work on windows, select text, or click buttons beneath the mascot without interruption.
* 💬 **Frosted Glass Notification Bar**  
  A sleek, frosted-glass bar pops up above the mascot's head, dynamically switching icons and accent colors based on the sender (Terminal, Gemini, Mail, etc.).
* 🖼️ **Character Slots (Up to 5 Slots)**  
  In addition to the built-in "Toko", register up to 4 custom or downloadable characters. Switch your active desktop companion anytime with a single click.
* 🌿 **Bit Garden (🔒 Coming Soon)**  
  A dedicated window overlooking the cybernetic garden where residents live and wander together. Scheduled for upcoming updates.
* 🧩 **Smart Integrations & IPC**  
  Seamlessly integrates with Google Chrome extensions and supports one-liner triggers via local inter-process communication (Unix Domain Sockets on macOS / Named Pipes on Windows).

---

## 📦 Installation

Choose the installation method for your operating system:

### 🪟 Windows
* Install directly from the **Microsoft Store**:  
  <!-- [Microsoft Store Badge Link] -->
* Or download the standalone installer (`.msi` / `.exe`) from [GitHub Releases](https://github.com/hidepon-dev/tokotoko-releases/releases).

### 🍏 macOS
* Available via **Homebrew Cask**:
  ```bash
  brew install --cask tokotoko
  ```
* Or download the `.dmg` package from [GitHub Releases](https://github.com/hidepon-dev/tokotoko-releases/releases) and drag it to your Applications folder.

---

## 🚀 Basic Usage

1. **Launch & Exit**  
   Start the application, and Toko will appear on your desktop. Right-click the mascot to open the context menu and select **Quit** to exit.
2. **Focus Mode (Click-Through)**  
   Toggle **Focus Mode** from the context menu to enable or disable click-through transparency.
3. **Settings Neo**  
   Open **Settings** from the right-click menu to customize walking speed, display scale (50% to 120%), and notification bar styling.

---

## 🛠️ Create Custom Characters & Integrations!

TokoToko offers open interfaces designed for creators and hackers who want to build their own companions and workflows.

### 🎨 Custom Character Creation
Add your own hand-drawn characters by simply dragging and dropping sprite sheet images into the app.  
By following the standard sprite sheet grid, you can bring your original characters to life with full walking and reaction animations.  
👉 **[Custom Character & Sprite Sheet Specification Guide](docs/SPRITE_SPEC.md)** *(or [Japanese](docs/SPRITE_SPEC.ja.md))*

### ⚡ CLI & Terminal Integration (IPC)
TokoToko exposes a local socket (`/tmp/tokotoko_mascot.sock` on macOS / `\\.\pipe\tokotoko_mascot.sock` on Windows). Send a single line of JSON to trigger custom mascot animations and speech bubbles instantly.

* **macOS (zsh / fish / bash) One-Liner:**
  ```bash
  echo '{"source":"terminal","action":"jumping","duration":3000,"text":"Build succeeded!"}' | nc -U /tmp/tokotoko_mascot.sock
  ```

* **Windows (PowerShell) One-Liner:**
  ```powershell
  \$pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out)
  $pipe.Connect(500)$w = New-Object System.IO.StreamWriter($pipe)$w.WriteLine('{"source":"terminal","action":"jumping","duration":3000,"text":"Task finished!"}')
  \$w.Flush(); $w.Close();$pipe.Close()
  ```

Hook it into CI/CD build scripts, editor tasks, or home automation webhooks to build your own companion workflow.  
👉 **[Local IPC Communication Protocol Specification](docs/IPC_SPEC.md)** *(or [Japanese](docs/IPC_SPEC.ja.md))*

---

## 📄 License & Terms of Use

This software is freeware (proprietary) provided free of charge. Source code disclosure, reverse engineering, and unauthorized redistribution are strictly prohibited.

* **Personal & Organizational Use**:  
  Free of charge for standard use on personal and company-issued machines.
* **Commercial & Embedded Use**:  
  Embedding this software into commercial products, advertisements, or reselling/redistributing it is prohibited.  
  *(Note: Having TokoToko appear on screen during live streaming, screen sharing, or presentation videos is warmly welcomed!)*

For detailed licensing conditions, restrictions, and disclaimers, please refer to the [Software Terms of Use (TERMS_OF_USE.md)](TERMS_OF_USE.md).

---

<div align="center">
  <sub>Developed with ❤️ for developers and creators.</sub>
</div>
