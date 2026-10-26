# ローカルIPC通信プロトコル仕様書 (IPC_SPEC) 🐾

[English](IPC_SPEC.md) | **日本語**

このドキュメントでは、外部のターミナル（シェル）、エディタ拡張機能（VS Code等）、バックグラウンドスクリプト、CI/CDツールなどからデスクトップマスコット「TokoToko」を操作するための **ローカルプロセス間通信（IPC）プロトコル規格** と **実装例** を解説します。

TokoToko は OS 標準のローカルソケットを常時待ち受けており、1行の JSON（JSON Lines 形式）を流し込むだけで、好きなタイミングでマスコットのアクションを再生したり、頭上のすりガラス通知バーにメッセージを表示させたりできます。

---

## 1. ソケット接続エンドポイント

TokoToko はネットワークポート（HTTP / WebSocket 等）を開放せず、安全で超軽量な OS ローカルソケットのみを使用します。

| プラットフォーム | ソケット種別 | エンドポイント（ソケット識別子） |
| :--- | :--- | :--- |
| **macOS** | Unix Domain Socket (UDS, Stream 型) | `/tmp/tokotoko_mascot.sock` |
| **Windows** | Named Pipe (名前付きパイプ, バイトストリーム) | `\\.\pipe\tokotoko_mascot.sock` |

* **通信フォーマット**: 改行コード（`\n`）区切りの JSON Lines (`ndjson`) ストリーム
* **文字エンコーディング**: UTF-8
* **接続ライフサイクル**: メッセージ送信ごとに接続・切断するワンショット方式（Fire-and-Forget）を推奨しますが、常時接続したまま改行区切りで都度送信する方式にも対応しています。

---

## 2. プロトコル概要

* **通信方式**: 単方向メッセージ送信（Fire-and-Forget）
* **データ形式**: **JSON Lines**（末尾に改行コード `\n` を付与した1行の JSON 文字列）
* **基本シーケンス**:
  1. クライアントがプラットフォーム別のソケットへ接続する。
  2. クライアントが末尾に `\n` を付けた JSON 文字列を書き込み（Flush）する。
  3. クライアントがソケット接続を閉じる。

---

## 3. メッセージペイロード規格 (JSON Schema)

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
| `source` | `string` | 任意 | `"default"` | 送信元の識別子（例: `"terminal"`）。通知バーの左端アイコンやテーマカラーの判定に使用されます。 |
| `action` | `string` | 任意 | `"idle"` | 再生させるアクション名（スプライトシートのアクション識別子）。省略時は通知バーのみ表示します。 |
| `duration` | `number` | 任意 | `1000` (ms) | アクションを維持する時間（正の整数ミリ秒）。指定時間経過後に通常のアイドル待機（`idle`）へ戻ります。 |
| `text` | `string` | 任意 | `null` | 頭上の通知バーに表示するテキストメッセージ。省略または `null` の場合は通知バーを表示せず、モーションのみ再生します。 |

### 送信元 (`source`) による通知バーの装飾

`source` の値に応じて、マスコット頭上の通知バーのアイコンとテーマカラーが動的に変化します。

| `source` | アイコン | テーマカラー | 主な用途 |
| :--- | :--- | :--- | :--- |
| `"terminal"` | ターミナルプロンプト (`>_`) | ブルー (`#60a5fa`) | CLI、シェルスクリプト、make、ビルド完了 |
| `"vscode"` | コード・エディタ (`{ }`) | スカイブルー (`#38bdf8`) | VS Code 拡張機能、テスト実行、保存イベント |
| `"gemini"` | AI スパークル (`✦`) | パープル (`#c084fc`) | AI アシスタント、思考・生成イベント |
| `"gmail"` | メール・エンベロープ (`✉`) | レッド (`#f87171`) | メール着信、未読通知 |
| `"comfyui"` | ノードネットワーク (`☍`) | グリーン (`#4ade80`) | 画像生成完了、ワークフロー終了 |
| `"browser"` | 地球儀 (`🌐`) | ピンク (`#f472b6`) | Web ブラウザ連携、ブックマークレット等 |
| `"chatgpt"` | クローバー (`✿`) | ホワイト (`#ffffff`) | ChatGPT 連携イベント |
| 未指定 / その他 | ベル（通知） (`🔔`) | オレンジ (`#fb923c`) | 独自スクリプト、自作アプリ等の汎用通知 |

---

## 4. 指定可能なアクション一覧 (`action`)

[スプライトシート仕様書](SPRITE_SPEC.ja.md) で定義されているアクション識別子を指定できます。

| アクション名 (`action`) | モーション / 仕草の内容 |
| :--- | :--- |
| `idle` | アイドル待機（通常の呼吸・まばたき） |
| `jumping` | 歓喜ジャンプ（ビルド成功、タスク完了、デプロイ成功など） |
| `failed` | 焦り・落胆・ため息（コマンド失敗、テスト落ち、エラーなど） |
| `waiting` | 髪かきあげ・ポーズ（入力待機、リマインドなど） |
| `running` | 思索・納得のひらめき指立て（思考中、レビュー依頼、相談など） |
| `review` | 空中ホログラム操作（ビルド実行中、バックグラウンド処理中など） |
| `surprised` | びっくり＆胸なでおろし（至急の割り込み通知、新着メールなど） |
| `waving` | 挨拶・ピースサイン |
| `bored` | 退屈・おねむ待機 |

---

## 5. 各言語・環境からの送信サンプルコード

### 1) シェルスクリプト (macOS: bash / zsh / fish)

標準の `nc`（netcat）コマンドを使って簡単にソケットへ書き込めます。

#### ワンライナー送信:
```bash
echo '{"source":"terminal","action":"jumping","duration":3000,"text":"Build Successful!"}' | nc -U /tmp/tokotoko_mascot.sock
```

#### コマンド終了フック（fish shell の例）:
直前のコマンドの実行結果に応じて、成功時はジャンプ、失敗時は落胆させるスニペット例です。

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

### 3) Python (クロスプラットフォーム対応)

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
        # TokoToko 未起動時などは静かに無視
        pass

if __name__ == "__main__":
    send_tokotoko(action="review", text="モデル学習中...", duration=5000, source="comfyui")
```

---

### 4) Node.js (JavaScript / TypeScript: VS Code 拡張など)

標準の `node:net` モジュールで UDS および Named Pipe の両方に接続できます。

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
    // TokoToko が起動していない場合は静かに無視
  });
}
```

---

## 6. エラーハンドリング & ベストプラクティス

1. **非ブロッキングで安全な組み込み**  
   TokoToko が起動していない場合、ソケットファイルが存在しないか接続エラーとなります。外部スクリプト側ではソケット接続エラーを常にキャッチして無視（またはバックグラウンド実行 `&`）し、本来の開発タスク（ビルドやシェルスクリプト等）を止めないように実装してください。
2. **連続送信時の上書き挙動**  
   短時間に複数のメッセージが連続して届いた場合、通知バーの表示および再生中のアクションは最新のメッセージ内容で即座に上書き・リフレッシュされます。
3. **1行あたりのペイロードサイズ上限**  
   1メッセージ（1行）あたりの受信バッファ上限は **64 KB** です。通常の JSON ペイロードであれば十分に余裕がありますが、改行を含まない巨大なデータは破棄されます。
