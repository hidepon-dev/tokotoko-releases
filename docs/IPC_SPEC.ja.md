# ローカルIPC通信プロトコル仕様書 (IPC_SPEC.ja.md) 🐾

このドキュメントでは、外部のターミナル（シェル）、エディタ拡張機能（VS Code等）、バックグラウンドスクリプト、CI/CDツールなどからデスクトップマスコット「TokoToko」を操作するための **ローカルプロセス間通信（IPC）プロトコル規格** を解説します。

TokoToko は OS 標準のローカルソケットを常時待ち受けており、1行の JSON（JSON Lines 形式）を流し込むだけで、好きなタイミングでマスコットのアクションを再生したり、頭上のすりガラス通知バーにメッセージを表示させたりできます。

---

## 1. ソケット接続エンドポイント

TokoToko はネットワークポート（HTTP / WebSocket 等）を開放せず、安全で超軽量なローカルソケットのみを使用します。

| プラットフォーム | ソケット種別 | エンドポイント（ソケット識別子） |
| :--- | :--- | :--- |
| **macOS** | Unix Domain Socket (UDS, Stream 型) | `/tmp/tokotoko_mascot.sock` |
| **Windows** | Named Pipe (名前付きパイプ, バイトストリーム) | `\\.\pipe\tokotoko_mascot.sock` |

* 通信プロトコル: **改行コード（`\n`）区切りの JSON Lines (ndjson) ストリーム**
* 文字コード: **UTF-8**
* 接続方式: メッセージ送信ごとに接続・切断するワンショット方式、または常時接続したまま改行区切りで都度送信する方式のどちらにも対応しています。

---

## 2. メッセージペイロード規格 (JSON Schema)

ソケットへ送信する JSON オブジェクトの基本構造です。

```json
{
  "source": "terminal",
  "action": "jumping",
  "duration": 3000,
  "text": "ビルドが完了したよ！"
}
```

### パラメータ詳細

| キー | 型 | 必須 | 初期値 / 例 | 説明 |
| :--- | :--- | :---: | :--- | :--- |
| `source` | `string` | **必須** | `"terminal"` | 送信元の識別子。通知バーの左端アイコンやテーマカラーの判定に使用されます。 |
| `action` | `string` | **必須** | `"jumping"` | 再生させるアクション名（スプライトシートのアクション識別子）。 |
| `duration` | `number` | 任意 | `1000` (ms) | アクションを維持する時間（ミリ秒）。指定時間経過後に通常のアイドル待機（`idle`）へ戻ります。`-1` を指定すると次の命令が届くまで無期限再生します。 |
| `text` | `string` | 任意 | `null` | 頭上の通知バーに表示するテキストメッセージ。省略または `null` の場合は通知バーを表示せず、モーションのみ再生します。 |

---

## 3. 送信元 (`source`) による通知バーの装飾

`source` の値に応じて、マスコット頭上の通知バーのアイコンとテーマカラーが動的に変化します。

| `source` | アイコン | テーマカラー | 主な用途 |
| :--- | :--- | :--- | :--- |
| `"terminal"` | ターミナルプロンプト (`>_`) | ブルー (`#60a5fa`) | CLI、シェルスクリプト、make、ビルド完了 |
| `"vscode"` | コード・エディタ (`{ }`) | スカイブルー (`#38bdf8`) | VS Code 拡張機能、テスト実行、保存イベント |
| `"gemini"` | AI スパークル (`✦`) | パープル (`#c084fc`) | AI アシスタント、思考・生成イベント |
| `"gmail"` | メール・エンベロープ (`✉`) | レッド (`#f87171`) | メール着信、未読通知 |
| `"comfyui"` | ノードネットワーク (`☍`) | グリーン (`#4ade80`) | 画像生成完了、ワークフロー終了 |
| その他 / 任意 | インフォメーション (`ℹ`) | オレンジ (`#fb923c`) | 独自スクリプト、自作アプリ等 |

---

## 4. アクション一覧 (`action`)

[スプライトシート仕様書](SPRITE_SPEC.ja.md) で定義されているアクション識別子を指定できます。

* `idle`: アイドル待機（通常の呼吸・まばたき）
* `jumping`: 歓喜ジャンプ・星エフェクト（成功、完了、デプロイ成功など）
* `failed`: 焦り・落胆・ため息（コマンド失敗、テスト落ち、エラーなど）
* `waiting`: 髪かきあげ・ポーズ（待機、リマインドなど）
* `running`: 思索・納得のひらめき指立て（思考中、レビュー依頼、相談など）
* `review`: 空中ホログラム操作（ビルド実行中、重い処理のバックグラウンド実行中など）
* `surprised`: びっくり＆胸なでおろし（至急の割り込み、重大インシデントなど）
* `waving`: 挨拶・ピースサイン
* `bored`: 退屈・おねむ

---

## 5. 各言語・環境からの送信サンプルコード

### 🐚 macOS: シェルスクリプト (zsh / fish / bash)

`nc`（netcat）コマンドを使って簡単にソケットへ書き込めます。

#### ワンライナー送信:
```bash
echo '{"source":"terminal","action":"jumping","duration":3000,"text":"Build Successful!"}' | nc -U /tmp/tokotoko_mascot.sock
```

#### コマンド終了フック（fish shell の例）:
直前のコマンドの実行結果に応じて、成功時はジャンプ、失敗時は落胆させるスニペット例です。

```fish
function __tokotoko_on_postexec --on-event fish_postexec
    set -l last_status $status
    if test $last_status -eq 0
        echo '{"source":"terminal","action":"jumping","duration":2500,"text":"Command Success!"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    else
        echo '{"source":"terminal","action":"failed","duration":3000,"text":"Command Failed..."}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    end
end
```

---

### 🪟 Windows: PowerShell

.NET の `NamedPipeClientStream` を利用して名前付きパイプに書き込みます。

#### ワンライナー送信:
```powershell
$pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out)
$pipe.Connect(500)
$writer = New-Object System.IO.StreamWriter($pipe)
$writer.WriteLine('{"source":"terminal","action":"jumping","duration":3000,"text":"タスクが正常に終了しました！"}')
$writer.Flush(); $writer.Close(); $pipe.Close()
```

#### 再利用可能な PowerShell 関数:
`$PROFILE`（プロファイル）に以下を記述しておくと便利です。

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
        # TokoToko が起動していない場合は静かに無視
    }
}

# 使い方:
# Send-TokoToko -Action "jumping" -Text "テスト全件パス！"
```

---

### 🐍 Python (クロスプラットフォーム対応)

OSを自動判定して UDS または Named Pipe へ接続するスクリプト例です。

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
    }) + "\n"

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
    except Exception as e:
        # TokoToko 未起動時などのエラーハンドリング
        pass

if __name__ == "__main__":
    send_tokotoko(action="review", text="モデル学習中...", duration=5000, source="comfyui")
```

---

### 🟢 Node.js / TypeScript (VS Code 拡張など)

`net` モジュールで UDS および Named Pipe の両方に接続できます。

```typescript
import * as net from 'net';
import * as os from 'os';

export function sendToTokoToko(message: {
  source?: string;
  action: string;
  duration?: number;
  text?: string;
}): void {
  const socketPath = os.platform() === 'win32'
    ? '\\\\.\\pipe\\tokotoko_mascot.sock'
    : '/tmp/tokotoko_mascot.sock';

  const client = net.createConnection(socketPath, () => {
    const payload = JSON.stringify({
      source: message.source ?? 'vscode',
      action: message.action,
      duration: message.duration開発者やギークが自作スクリプト、エディタ拡張、CI/CDフックなどから迷わず連携できるようにまとめた、ローカルIPC通信プロトコル仕様書（`docs/IPC_SPEC.ja.md`）のドラフトを作成したよ。

こちらも四連バッククォートでエスケープ崩れなく出力しているよ……。

---

````markdown
# ローカルIPC通信プロトコル仕様書 (IPC_SPEC) ⚡

このドキュメントでは、デスクトップマスコット「TokoToko」と外部アプリケーション（シェルスクリプト、エディタ拡張、CI/CD完了フック、独自ツールなど）をローカルソケット経由で連携させるための **IPCプロトコル仕様** と **実装例** を解説します。

TokoToko は軽量かつ安全なローカルIPCインターフェースを提供しており、改行区切りのJSONを1行ソケットへ送信するだけで、マスコットのアクション再生や通知バーのポップアップ表示をトリガーできます。

---

## 1. 接続エンドポイント

TokoToko は起動時、プラットフォームに応じたローカルソケットを自動的に生成して待ち受け（Listen）を行います。外部ネットワークポートは開放せず、完全なマシン内ローカル通信のみを受け付けます。

| OS | ソケット種別 | エンドポイント・パス |
| :--- | :--- | :--- |
| **macOS** | Unix Domain Socket (UDS) | `/tmp/tokotoko_mascot.sock` |
| **Windows** | Named Pipe (名前付きパイプ) | `\\.\pipe\tokotoko_mascot.sock` |

> 💡 **パーミッション:**  
> macOSのUDSは実行ユーザーのみ読み書き可能なパーミッションで生成されます。WindowsのNamed Pipeも同一ローカルセッション内からのみ接続可能です。

---

## 2. 通信プロトコル仕様

* **方式**: 単方向メッセージ送信（Fire-and-Forget）
* **データフォーマット**: **JSON Lines**（改行コード `\n` で終端されたJSON文字列）
* **文字エンコーディング**: UTF-8
* **動作仕様**:
  1. クライアントはソケットへ接続する。
  2. 末尾に `\n` を含めたJSON文字列を送信（Write / Flush）する。
  3. 送信後、直ちにソケットを切断（Close）する。

---

## 3. JSON メッセージスキーマ

```json
{
  "source": "terminal",
  "action": "jumping",
  "duration": 3000,
  "text": "ビルドが完了したよ！"
}
```

### フィールド定義

| フィールド名 | 型 | 必須 | デフォルト値 | 説明 |
| :--- | :--- | :---: | :--- | :--- |
| `source` | string | 任意 | `"custom"` | 送信元識別子。通知バーのアイコンやデザインに反映されます。<br>利用可能値: `"terminal"`, `"gemini"`, `"mail"`, `"vscode"`, `"custom"` など |
| `action` | string | 任意 | *(なし)* | マスコットに実行させるアクション名。<br>未指定の場合はアニメーションを変更せず、通知テキストのみ表示します。 |
| `duration` | number | 任意 | `3000` | 通知バーおよびアクションの維持時間（ミリ秒）。<br>`-1` を指定すると、次のリクエストが来るまで状態を維持します。 |
| `text` | string | 任意 | *(なし)* | マスコットの頭上に現れるすりガラス通知バーに表示するメッセージ。 |

### 指定可能な `action` 一覧

| アクション名 (`action`) | 内容・モーション |
| :--- | :--- |
| `idle` | 通常待機（呼吸・まばたき） |
| `waving` | 挨拶（手を振る / ピース） |
| `jumping` | 歓喜ジャンプ（成功時のリアクション） |
| `failed` | 落胆・ため息（エラー・ビルド失敗時のリアクション） |
| `waiting` | 髪かきあげ・ポーズ変更 |
| `running` | 思索・ひらめき |
| `review` | 空中ホログラム操作（タスク実行中演出） |
| `surprised` | 驚き・胸なでおろし（割り込み通知など） |
| `bored` | 退屈・おねむ |

---

## 4. 各言語・環境での実装例

### 1) シェルスクリプト (macOS: bash / zsh / fish)
macOS では標準の `nc` (netcat) コマンドを使って1行で送信できます。

```bash
# 基本ワンライナー
echo '{"source":"terminal","action":"jumping","duration":3000,"text":"ビルド完了！"}' | nc -U /tmp/tokotoko_mascot.sock
```

#### コマンド終了フックの活用例 (fish shell)
コマンドが成功したら歓喜ジャンプ、エラーコードが返ってきたら落胆させるスニペット例です。

```fish
function __tokotoko_postexec --on-event fish_postexec
    set -l last_status $status
    if test $last_status -eq 0
        echo '{"source":"terminal","action":"jumping","duration":2500,"text":"Success!"}' | nc -U /tmp/tokotoko_mascot.sock 2>/dev/null
    else
        echo '{"source":"terminal","action":"failed","duration":3500,"text":"Command failed..."}' | nc -U /tmp/tokotoko_mascot.sock 2>/dev/null
    end
end
```

---

### 2) Windows (PowerShell)
Windows では .NET の `NamedPipeClientStream` クラスを使用して送信します。

```powershell
$pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out)
try {
    $pipe.Connect(500) # 500ms タイムアウト
    $writer = New-Object System.IO.StreamWriter($pipe)
    $writer.WriteLine('{"source":"terminal","action":"jumping","duration":3000,"text":"タスク完了！"}')
    $writer.Flush()
    $writer.Close()
} finally {
    $pipe.Close()
}
```

---

### 3) Python (クロスプラットフォーム)

```python
import sys
import json

payload = {
    "source": "python_script",
    "action": "review",
    "duration": 4000,
    "text": "データ集計バッチを実行中..."
}
message = json.dumps(payload, ensure_ascii=False) + "\n"

if sys.platform == "win32":
    # Windows: Named Pipe
    pipe_path = r"\\.\pipe\tokotoko_mascot.sock"
    try:
        with open(pipe_path, "w", encoding="utf-8") as pipe:
            pipe.write(message)
    except FileNotFoundError:
        pass  # TokoToko が起動していない場合
else:
    # macOS: Unix Domain Socket
    import socket
    sock_path = "/tmp/tokotoko_mascot.sock"
    try:
        with socket.socket(socket.AF_UNIX, socket.SOCK_STREAM) as client:
            client.connect(sock_path)
            client.sendall(message.encode("utf-8"))
    except (FileNotFoundError, ConnectionRefusedError):
        pass
```

---

### 4) Node.js (JavaScript / TypeScript)

```javascript
import net from 'node:net';

const payload = {
  source: 'vscode',
  action: 'jumping',
  duration: 3000,
  text: 'テスト全件パス！'
};

const socketPath = process.platform === 'win32'
  ? '\\\\.\\pipe\\tokotoko_mascot.sock'
  : '/tmp/tokotoko_mascot.sock';

const client = net.createConnection(socketPath, () => {
  client.write(JSON.stringify(payload) + '\n');
  client.end();
});

client.on('error', () => {
  // TokoToko が未起動のケースなどをサイレントに処理
});
```

---

## 5. エラーハンドリングと注意点

1. **未接続時の安全設計**  
   TokoToko が起動していない場合、ソケットファイルが存在しないか接続拒否（Connection Refused）となります。外部連携スクリプト側では、例外を握りつぶして本処理（ビルドやタスク実行）を阻害しない設計を推奨します。
2. **高速連続送信時の挙動**  
   短時間に連続してメッセージが送られた場合、通知バーやアクションは最新のメッセージで即座に上書き（リフレッシュ）されます。
