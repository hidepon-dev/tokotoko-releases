# TokoToko 利用者ガイド (User Guide) 🐾

[English](USER_GUIDE.md) | **日本語**

このガイドでは、デスクトップマスコット「TokoToko」の基本操作、設定画面（System Core）の各機能、ターミナル・Chrome 拡張機能との連携セットアップ、およびよくある質問（FAQ）を詳しく解説します。

---

## 1. 基本操作とメニュー

| 操作 | 動作 |
| :--- | :--- |
| **左クリック** | その場で嬉しそうにジャンプします（`jumping`） |
| **左ドラッグ** | マスコットを掴んでデスクトップ上の好きな位置へ移動します |
| **右クリック** | コンテキストメニューを開きます |
| **メニューバー / タスクトレイ** | 常駐アイコンからすべてのメニュー操作（集中モード切替・設定・終了）を行えます |

### 🛡️ 作業集中モード（Focus Mode）と解除方法
コーディングやブラウジングに集中したいときは、右クリックメニューまたはトレイアイコンから **「作業集中モード (Focus Mode)」** をオンにしてください。マスコットが半透明になり、マウスクリックが完全に背後のウィンドウへ透過されます。

> ⚠️ **作業集中モードを解除するときは:**  
> 作業集中モード中はマスコット自体へのクリック（右クリックを含む）がすべて背後のウィンドウへ透過するため、マスコットを直接右クリックすることはできません。  
> **画面上部メニューバー（macOS）または右下タスクトレイ（Windows）の TokoToko アイコン** をクリックし、「作業集中モード ✓」を選択して解除してください。

---

## 2. 設定画面（`⚙️ System Core...`）

メニューから **`⚙️ System Core...`** を開くと、専用設定ウィンドウが表示されます。すべての変更は即座に反映・自動保存されます。

### 🖼️ (1) Character Slots（キャラクタースロット）
- **Slot 0（Toko）**: 看板案内人「Toko」の固定スロットです。
- **Slot 1 〜 Slot 4**: 追加キャラクター用のスロットです。スプライト画像（`.png` / `.webp`）、オープンパッケージ（`.toko` / `.zip`）、または公式キャラクターパッケージ（`.tokochar`）をドラッグ＆ドロップして登録できます。
- **ATTACH / RESIDENT**: カードの **「ATTACH」** ボタンを押すと、デスクトップ上のマスコットがそのキャラクターに切り替わります（現在呼び出し中のカードは `RESIDENT` と表示されます）。
- **キズナ度（Kizuna）**: キャラクターと一緒に過ごした累計時間に応じて、カード上部のキズナランク称号（かけだし ➔ なかよし 🥉 ➔ 相棒 🥈 ➔ 親友 🥇 ➔ 絆 💎 ➔ 運命の絆 👑）が成長していきます。

### 🤖 (2) General Settings（一般設定）
- **Display Language**: UI の表示言語（日本語 / English）を切り替えます。
- **Mascot Scale**: マスコットの表示サイズを `50%` 〜 `120%` の範囲で調整します。
- **Action Speed**: 歩行やアニメーションの再生速度（`0.5x` 〜 `2.0x`）を調整します。
- **Random Action Interval**: 自律行動を起こす間隔（`5秒` 〜 `30秒`）を調整します。
- **Enable Autonomous Behavior**: 自律歩行・ランダムアクションの ON / OFF を切り替えます。
- **Remember Mascot Position**: アプリ終了時の位置を記憶し、次回起動時に同じ場所へ表示します。

### 💬 (3) Notification Settings（通知バー設定）
- **Enable Notifications**: 頭上のすりガラス通知バー表示の ON / OFF を切り替えます。
- **Corner Radius / Background Opacity**: 通知バーの角丸（`0px` 〜 `24px`）と背景の不透明度（`10%` 〜 `100%`）を調整します。
- **Glass Color Theme**: 通知バーのベーステーマ（`Dark` / `Cyan` / `Holo` / `Alert`）を選択します。
- **Text Scroll Speed**: 長文通知時の横スクロール速度を調整します。

### 🧩 (4) Extensions（外部拡張機能連携）
- **Chrome Native Messaging Host**: Chrome 拡張機能と TokoToko 本体を繋ぐブリッジ設定です。**「Install」** ボタンを押すことで OS に連携マニフェストが自動登録されます。
- **TokoToko Gemini Integration / Gmail Connector**: 各拡張機能からの通知受け入れを個別に ON / OFF できます。

### ℹ️ (5) About（バージョン情報・更新確認）
- **Check for Updates（更新を確認）**: GitHub 上の最新リリースを確認し、新しいバージョンがある場合はダウンロード先をご案内します。
- **Bit Garden (🔒 近日公開予定)**: 最大30体のキャラクターたちが暮らす電脳庭園ビューは、今後のアップデートで提供予定です。

---

## 3. ターミナル連携のセットアップ

シェル設定ファイルに以下のフックを追記すると、コマンド実行結果（成功 `Exit 0` / 失敗 `Exit != 0`）に合わせてマスコットが自動でジャンプしたり落ち込んだりします。

### 🍏 macOS (`zsh`)
`~/.zshrc` の末尾に追記します：
```zsh
# TokoToko ターミナル連携フック
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
`~/.config/fish/config.fish` に追記します：
```fish
# TokoToko ターミナル連携フック
function __tokotoko_on_postexec --on-event fish_postexec
    if test $status -eq 0
        echo '{"source":"terminal","action":"jumping"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    else
        echo '{"source":"terminal","action":"failed"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    end
end
```

### 🪟 Windows (`PowerShell`)
`$PROFILE`（通常は `~\Documents\PowerShell\Microsoft.PowerShell_profile.ps1`）に追記します：
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

より高度な通知テキスト送信や Python / Node.js からの連携については、[ローカルIPC通信プロトコル仕様書](IPC_SPEC.ja.md) をご覧ください。

---

## 4. Chrome 拡張機能連携のセットアップ

Google Chrome 上の AI アシスタント（Google Gemini）の思考状態や進捗に合わせてマスコットを連動させるには、以下の手順で初期設定を行ってください。

1. TokoToko の設定画面（`⚙️ System Core...`）を開き、**「Extensions」** タブを選択します。
2. **「Chrome Native Messaging Host」** 欄にある **「Install」** ボタンをクリックします（ステータスバッジが `INSTALLED` に変わります）。
3. 連携したい拡張機能のトグルスイッチが **ON** になっていることを確認します。
4. Chrome に拡張機能をインストールして Gemini ページを開くと、入力中・思考中・回答完了に合わせてマスコットがリアクションします。

---

## 5. よくある質問（FAQ）

**Q. アプリを完全に終了するにはどうすればいいですか？**  
A. マスコットを右クリックして **「終了 (Quit)」** を選ぶか、メニューバー／タスクトレイのアイコンから **「終了 (Quit)」** を選択してください。終了時に現在の表示位置が自動保存されます。

**Q. モニター構成を変えたら、マスコットが画面外に消えてしまいました。**  
A. アプリを一度終了し、以下の設定ファイル（`config.json`）内の `"lastPosition"` の値を `null` に書き換えてから再起動すると、初期位置に復帰します。
- **macOS**: `~/Library/Application Support/com.hidepon.tokotoko/config.json`
- **Windows**: `%APPDATA%\com.hidepon.tokotoko\config.json`

**Q. ターミナル連携を送ってもマスコットが反応しません。**  
A. 以下をご確認ください：
- **macOS**: TokoToko 本体が起動しているか、および `/tmp/tokotoko_mascot.sock` が存在するか確認してください。
- **Windows**: PowerShell と TokoToko 本体が同じユーザー権限で起動しているか確認してください（片方だけが「管理者として実行」されている場合、名前付きパイプの通信がブロックされることがあります）。

**Q. 自作キャラクターはどうやって作ればいいですか？**  
A. 以下のガイドをご用意しています：
- 画像1枚から作る場合 👉 [自作キャラクター制作・スプライトシート仕様ガイド](SPRITE_SPEC.ja.md)
- 名前やセリフも同梱してパッケージ化する場合 👉 [キャラクターパッケージ（`.toko` / `.zip`）制作ガイド](CHARACTER_PACKAGE.ja.md)

**Q. 音声は鳴りますか？ 画面中を飛び回りますか？**  
A. いいえ。TokoToko は Web 会議中や深夜作業、音楽鑑賞中でも安心して常駐できるよう、**「音声なし（完全静音）」** かつ **「画面端を水平に歩くのみ（画面上のテキストやウィンドウを隠さない）」** という控えめな設計になっています。

**Q. TokoToko は無料で使えますか？ 有料の機能はありますか？**  
A. はい。デスクトップマスコットの常駐、作業集中モード、外部連携、および自作キャラクター（`.png` / `.toko` / `.zip`）の追加登録など、**コア機能はすべて完全無料**でご利用いただけます。  
※電脳庭園ウィンドウ「Bit Garden」の解放パス（Bit Garden Passport）や一部の公式追加キャラクターは、BOOTH 等の外部ショップにて有償提供を予定しています。

**Q. macOS で「壊れているため開けません（ゴミ箱に入れる必要があります）」と表示されて起動できません。**  
A. 近年の macOS（Tahoe / Golden Gate）のセキュリティ仕様により、Apple 未公証のアプリに対して警告ダイアログが表示されます。  
お手数ですが、ターミナル（Terminal.app）を開いて以下のコマンドを実行し、セキュリティ隔離属性（quarantine）を解除してください：
```bash
xattr -cr /Applications/TokoToko.app
```
※コマンド実行後は、通常通りダブルクリックで起動できるようになります。
