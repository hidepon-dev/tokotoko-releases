# Local IPC Communication Protocol Specification (IPC_SPEC) 🐾

This document describes the **local Inter-Process Communication (IPC) protocol specification** and **implementation examples** for controlling the desktop mascot "TokoToko" from external tools such as terminals (shells), editor extensions (VS Code, etc.), background scripts, and CI/CD pipelines.

TokoToko listens on OS-standard local sockets, allowing you to trigger mascot action animations and pop up frosted-glass notification bar messages simply by piping a single line of JSON (JSON Lines format).

---

## 1. Connection Endpoints

TokoToko does not open any network ports (such as HTTP or WebSocket); it exclusively uses lightweight and secure local OS sockets.

| Platform | Socket Type | Endpoint (Socket Identifier) |
| :--- | :--- | :--- |
| **macOS** | Unix Domain Socket (UDS, Stream) | `/tmp/tokotoko_mascot.sock` |
| **Windows** | Named Pipe (Byte stream) | `\\.\pipe\tokotoko_mascot.sock` |

* **Communication Format**: Newline-delimited (`\n`) JSON Lines (`ndjson`) stream
* **Character Encoding**: UTF-8
* **Connection Lifecycle**: A one-shot connection (Fire-and-Forget) per message is recommended, but long-lived persistent connections sending newline-delimited messages are also fully supported.

---

## 2. Protocol Overview

* **Mode**: Unidirectional message transmission (Fire-and-Forget)
* **Data Format**: **JSON Lines** (JSON string terminated with a newline `\n`)
* **Standard Sequence**:
  1. The client connects to the platform-specific socket.
  2. The client writes (and flushes) the JSON string terminated with `\n`.
  3. The client immediately closes the socket connection.

---

## 3. Message Payload Specification (JSON Schema)

Standard structure of the JSON object sent to the socket:

```json
{
  "source": "terminal",
  "action": "jumping",
  "duration": 3000,
  "text": "Build completed successfully!"
}
```

### Parameter Reference

| Key | Type | Required | Default / Example | Description |
| :--- | :--- | :---: | :--- | :--- |
| `source` | `string` | **Required** | `"terminal"` | Origin identifier. Used to determine the left icon and accent color theme of the notification bar. |
| `action` | `string` | **Required** | `"jumping"` | Action name to execute (corresponds to an action identifier in the sprite sheet). |
| `duration` | `number` | Optional | `1000` (ms) | Duration to sustain the action in milliseconds. Returns to `idle` once elapsed. Set to `-1` to loop indefinitely until the next command arrives. |
| `text` | `string` | Optional | `null` | Text message to display on the frosted-glass notification bar. If omitted or `null`, only the animation plays without showing the notification bar. |

### Notification Bar Styling by `source`

The icon and color theme of the notification bar adapt dynamically based on `source`:

| `source` | Icon | Color Theme | Common Use Cases |
| :--- | :--- | :--- | :--- |
| `"terminal"` | Terminal prompt (`>_`) | Blue (`#60a5fa`) | CLI, shell scripts, make, build completions |
| `"vscode"` | Code editor (`{ }`) | Sky Blue (`#38bdf8`) | VS Code extensions, test runners, file save events |
| `"gemini"` | AI Sparkle (`✦`) | Purple (`#c084fc`) | AI assistants, thinking / generation events |
| `"gmail"` | Envelope (`✉`) | Red (`#f87171`) | Incoming emails, unread alerts |
| `"comfyui"` | Node graph (`☍`) | Green (`#4ade80`) | Image generation complete, workflow finished |
| Other / Custom | Information (`ℹ`) | Orange (`#fb923c`) | Custom automation, external scripts |

---

## 4. Supported Action Names (`action`)

Accepts action identifiers defined in the [Sprite Sheet Specification](SPRITE_SPEC.md):

| Action Name (`action`) | Motion / Behavior |
| :--- | :--- |
| `idle` | Idle standing (gentle breathing & blinking) |
| `jumping` | Joyful jump with star effects (success, deployment finished, etc.) |
| `failed` | Disappointment & sigh (command error, test failed, crash, etc.) |
| `waiting` | Tucking hair behind ear / pose change (standing by, reminders) |
| `running` | Pondering with finger pointing (thinking, review request, consultation) |
| `review` | Typing on floating aerial hologram (in-progress background builds/tasks) |
| `surprised` | Startled hop & hand to chest (urgent notifications, critical alerts) |
| `waving` | Greeting / peace sign |
| `bored` | Drowsy & bored idling |

---

## 5. Implementation Examples

### 1) Shell Script (macOS: bash / zsh / fish)

Use the standard `nc` (netcat) command to write directly to the socket.

#### One-liner:
```bash
echo '{"source":"terminal","action":"jumping","duration":3000,"text":"Build Successful!"}' | nc -U /tmp/tokotoko_mascot.sock
```

#### Command post-execution hook (fish shell example):
Triggers joyful jumping on zero exit status and disappointment on failure:

```fish
function __tokotoko_postexec --on-event fish_postexec
    set -l last_status $status
    if test $last_status -eq 0
        echo '{"source":"terminal","action":"jumping","duration":2500,"text":"Command Success!"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    else
        echo '{"source":"terminal","action":"failed","duration":3500,"text":"Command Failed..."}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    end
end
```

---

### 2) Windows (PowerShell)

Use .NET's `NamedPipeClientStream` class to write to the named pipe.

#### One-liner:
```powershell
$pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out)
$pipe.Connect(500)
$writer = New-Object System.IO.StreamWriter($pipe)
$writer.WriteLine('{"source":"terminal","action":"jumping","duration":3000,"text":"Task completed successfully!"}')
$writer.Flush(); $writer.Close(); $pipe.Close()
```

#### Reusable PowerShell Function:
Add this helper to your `$PROFILE`:

```powershell
function Send-TokoToko {
    param (
        [string]$Action = "jumping",
        [string]$Text = "",
        [int]$Duration = 3000,
        [string]$Source = "terminal"
    )
    try {
        $pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out)
        $pipe.Connect(200)
        $writer = New-Object System.IO.StreamWriter($pipe)
        $payload = @{
            source   = $Source
            action   = $Action
            duration = $Duration
            text     = $Text
        } | ConvertTo-Json -Compress
        $writer.WriteLine($payload)
        $writer.Flush(); $writer.Close(); $pipe.Close()
    } catch {
        # Silently ignore if TokoToko is not running
    }
}

# Example usage:
# Send-TokoToko -Action "jumping" -Text "All tests passed!"
```

---

### 3) Python (Cross-Platform)

Automatically detects platform and connects to either UDS or Named Pipe:

```python
import json
import os
import platform
import sys

def send_tokotoko(action: str, text: str = None, duration: int = 3000, source: str = "terminal"):
    payload = json.dumps({
        "source": source,
        "action": action,
        "duration": duration,
        "text": text
    }, ensure_ascii=False) + "\n"

    try:
        if platform.system() == "Windows":
            pipe_path = r"\\.\pipe\tokotoko_mascot.sock"
            with open(pipe_path, "w", encoding="utf-8") as pipe:
                pipe.write(payload)
        else:
            import socket
            sock_path = "/tmp/tokotoko_mascot.sock"
            if not os.path.exists(sock_path):
                return
            with socket.socket(socket.AF_UNIX, socket.SOCK_STREAM) as client:
                client.connect(sock_path)
                client.sendall(payload.encode("utf-8"))
    except Exception:
        # Gracefully handle when TokoToko is not running
        pass

if __name__ == "__main__":
    send_tokotoko(action="review", text="Training ML model...", duration=5000, source="comfyui")
```

---

### 4) Node.js (JavaScript / TypeScript: VS Code Extension, etc.)

Connect using `node:net`:

```typescript
import * as net from 'node:net';
import * as os from 'node:os';

export function sendToTokoToko(message: {
  source?: string;
  action: string;
  duration?: number;
  text?: string;
}): void {
  const socketPath = os.platform() === 'win32'
    ? '\\\\.\\pipe\\tokotoko_mascot.sock'
    : '/tmp/tokotoko_mascot.sock';

  const payload = JSON.stringify({
    source: message.source ?? 'vscode',
    action: message.action,
    duration: message.duration ?? 3000,
    text: message.text ?? null,
  }) + '\n';

  const client = net.createConnection(socketPath, () => {
    client.write(payload);
    client.end();
  });

  client.on('error', () => {
    // Silently handle connection failure if TokoToko is not open
  });
}
```

---

## 6. Error Handling & Best Practices

1. **Non-blocking Safe Integration**  
   If TokoToko is not running, the socket file will be missing or the connection refused. External scripts should always catch and ignore socket errors so they never interrupt primary development tasks (like compilation or scripts).
2. **Rapid Sequential Invocations**  
   When messages are received in rapid succession, the notification bar and active action state are immediately refreshed and overwritten by the newest incoming payload.
3. **Payload Line Length Limit**  
   The per-line buffer limit is 64 KB, which is more than sufficient for standard JSON action payloads.
