# TokoToko User Guide 🐾

**English** | [日本語 (Japanese)](USER_GUIDE.ja.md)

This guide covers basic interactions, the System Core settings window, terminal & Chrome extension setup, and Frequently Asked Questions (FAQ) for the desktop mascot "TokoToko".

---

## 1. Basic Controls & Menus

| Action | Behavior |
| :--- | :--- |
| **Left-Click** | The mascot performs a joyful jump (`jumping`) |
| **Left-Drag** | Grab and move the mascot anywhere across your monitors |
| **Right-Click** | Opens the context menu |
| **Menu Bar / System Tray** | Access all controls (Focus Mode toggle, System Core, Quit) from the tray icon |

### 🛡️ Focus Mode (Click-Through) & How to Exit
When you want to focus on coding or browsing without the mascot intercepting mouse clicks, enable **Focus Mode** from the right-click menu or system tray icon. The mascot turns semi-transparent, allowing all mouse clicks and drags to pass directly through to the windows behind it.

> ⚠️ **How to Disable Focus Mode:**  
> While Focus Mode is active, all mouse clicks (including right-clicks) pass straight through the mascot window.  
> To exit Focus Mode, click the **TokoToko icon in your macOS Menu Bar or Windows System Tray** and uncheck **Focus Mode ✓**.

---

## 2. System Core Settings (`⚙️ System Core...`)

Select **`⚙️ System Core...`** from the menu to open the configuration window. All changes take effect immediately and are saved automatically.

### 🖼️ (1) Character Slots
- **Slot 0 (Toko)**: Dedicated slot for the built-in navigator "Toko".
- **Slot 1 – Slot 4**: Custom character slots. Drag and drop a standalone sprite sheet (`.png` / `.webp`), an open character package (`.toko` / `.zip`), or an official character package (`.tokochar`) onto a card to register it.
- **ATTACH / RESIDENT**: Click **ATTACH** on any card to summon that character as your active desktop companion (the currently active card displays `RESIDENT`).
- **Kizuna (Bond Rank)**: As you spend time working alongside a character, their Kizuna bond title and medal badge at the top of the card will level up (`Novice` ➔ `Good Friend` 🥉 ➔ `Partner` 🥈 ➔ `Best Friend` 🥇 ➔ `Bound Partner` 💎 ➔ `Destined Bond` 👑).

### 🤖 (2) General Settings
- **Display Language**: Switch the UI language between Japanese (`ja`) and English (`en`).
- **Mascot Scale**: Adjust mascot display size from `50%` to `120%`.
- **Action Speed**: Adjust walking and animation playback speed (`0.5x` to `2.0x`).
- **Random Action Interval**: Set the interval between autonomous behaviors (`5s` to `30s`).
- **Enable Autonomous Behavior**: Toggle autonomous strolling and idle poses ON or OFF.
- **Remember Mascot Position**: Save the mascot's screen coordinates upon exit and restore them on next launch.

### 💬 (3) Notification Settings
- **Enable Notifications**: Toggle the overhead frosted-glass notification bar ON or OFF.
- **Corner Radius / Background Opacity**: Customize the border radius (`0px` to `24px`) and opacity (`10%` to `100%`).
- **Glass Color Theme**: Choose a visual theme (`Dark`, `Cyan`, `Holo`, or `Alert`).
- **Text Scroll Speed**: Adjust the marquee scrolling speed for long messages.

### 🧩 (4) Extensions
- **Chrome Native Messaging Host**: Manages the bridge between Chrome extensions and TokoToko. Click **Install** to register the host manifest with your OS.
- **TokoToko Gemini Integration / Gmail Connector**: Enable or disable incoming notifications from each extension individually.

### ℹ️ (5) About
- **Check for Updates**: Queries GitHub Releases to check if a newer version of TokoToko is available.
- **Bit Garden (🔒 Coming Soon)**: A dedicated cybernetic garden window where up to 30 characters can roam together—scheduled for a future update.

---

## 3. Terminal Integration Setup

By adding a short hook to your shell configuration file, TokoToko will automatically celebrate when a command succeeds (`Exit 0`) or sigh when a command fails (`Exit != 0`).

### 🍏 macOS (`zsh`)
Append the following to `~/.zshrc`:
```zsh
# TokoToko terminal integration hook
precmd() {
    local exit_code=$?
    if [ $exit_code -eq 0 ]; then
        echo '{"source":"terminal","action":"jumping"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    else
        echo '{"source":"terminal","action":"failed"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    fi
}
```

### 🍏 macOS (`fish shell`)
Add the following to `~/.config/fish/config.fish`:
```fish
# TokoToko terminal integration hook
function __tokotoko_on_postexec --on-event fish_postexec
    if test $status -eq 0
        echo '{"source":"terminal","action":"jumping"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    else
        echo '{"source":"terminal","action":"failed"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    end
end
```

### 🪟 Windows (`PowerShell`)
Add the following to your `$PROFILE` (typically `~\Documents\PowerShell\Microsoft.PowerShell_profile.ps1`):
```powershell
function Send-TokoToko {
    param([string]$Action)
    try {
        $pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out)
        $pipe.Connect(200)
        $writer = New-Object System.IO.StreamWriter($pipe)
        $writer.WriteLine('{"source":"terminal","action":"' + $Action + '"}')
        $writer.Flush(); $writer.Close(); $pipe.Close()
    } catch {}
}

function Prompt {
    if ($?) { Send-TokoToko -Action 'jumping' } else { Send-TokoToko -Action 'failed' }
    return "PS $($executionContext.SessionState.Path.CurrentLocation)$('>' * ($nestedPromptLevel + 1)) "
}
```

For custom notification messages and Python / Node.js integration examples, see the [Local IPC Communication Protocol Specification](IPC_SPEC.md).

---

## 4. Chrome Extension Integration Setup

To link TokoToko with AI assistants (such as Google Gemini) in Google Chrome:

1. Open **`⚙️ System Core...`** in TokoToko and select the **Extensions** tab.
2. Under **Chrome Native Messaging Host**, click the **Install** button (the status badge will change to `INSTALLED`).
3. Ensure the toggle switch for the extension you want to use is turned **ON**.
4. Install the companion Chrome extension and open Gemini—your mascot will automatically react as you type, while Gemini thinks, and when responses finish!

---

## 5. Frequently Asked Questions (FAQ)

**Q. How do I completely quit the application?**  
A. Right-click the mascot and choose **Quit**, or click the TokoToko icon in your macOS Menu Bar / Windows System Tray and select **Quit**. Your mascot's screen position is saved automatically on exit.

**Q. My mascot disappeared off-screen after changing my monitor layout!**  
A. Quit TokoToko, open the configuration file (`config.json`) at the path below, set `"lastPosition"` to `null`, and relaunch the app:
- **macOS**: `~/Library/Application Support/com.hidepon.tokotoko/config.json`
- **Windows**: `%APPDATA%\com.hidepon.tokotoko\config.json`

**Q. Terminal commands aren't triggering mascot animations.**  
A. Please check the following:
- **macOS**: Verify that TokoToko is running and `/tmp/tokotoko_mascot.sock` exists.
- **Windows**: Ensure PowerShell and TokoToko are running under the same privilege level (if one is running as Administrator and the other as a standard user, Windows may block Named Pipe access).

**Q. How can I create my own custom characters?**  
A. Check out our creator guides:
- Creating a standalone sprite sheet 👉 [Custom Character & Sprite Sheet Specification Guide](SPRITE_SPEC.md)
- Bundling names, roles, and quotes into a package 👉 [Character Package (`.toko` / `.zip`) Creation Guide](CHARACTER_PACKAGE.md)

**Q. Does TokoToko make sound effects or voice lines? Does it jump all over the screen?**  
A. No. TokoToko is intentionally designed with a **"completely silent (no audio output)"** and **"horizontal strolling along screen edges"** philosophy so it will never interrupt your workflow, cover your code or text, or disrupt online calls and music playback.

**Q. Is TokoToko free to use? Are there paid features?**  
A. Yes! All **core features**—including the desktop mascot, Focus Mode, terminal/AI IPC integration, and importing your own custom characters (`.png` / `.toko` / `.zip`)—are **100% free forever**.  
*Note: Admission to the cybernetic garden window ("Bit Garden" via Bit Garden Passport) and select official character packs are planned as optional paid digital items on external creator platforms such as BOOTH.*

**Q. On macOS, why does it say "TokoToko is damaged and can't be opened. You should move it to the Trash"?**  
A. Recent macOS versions (Tahoe / Golden Gate) enforce strict Gatekeeper security for apps distributed without Apple notarization.  
To bypass this warning, open **Terminal.app** and run the following command once to remove the quarantine flag:
```bash
xattr -cr /Applications/TokoToko.app
```
*After running this command, you can launch TokoToko normally with a double-click.*
