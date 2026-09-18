# 🐾 TokoToko（トコトコ）

[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Windows-blue)]()
[![Framework](https://img.shields.io/badge/built%20with-Tauri%202.0-orange)]()
[![License](https://img.shields.io/badge/license-Freeware%20%2F%20Proprietary-blue)](./LICENSE.txt)

> **Autonomous desktop companion mascot that reacts to your terminal CLI and AI assistant activity.**  
> *(Currently, documentation is primarily provided in Japanese.)*

> **作業環境とAIの動きに寄り添う、自律型デスクトップ相棒マスコット**

TokoToko（トコトコ）は、開発者のターミナル（CLI）やAIアシスタント（Gemini等）の作業ステータスに連動してデスクトップ上を駆け回る、軽量な自律型デスクトップマスコットです。

ビルドが通れば一緒に大喜びし、エラーが出れば一緒に落ち込む——あなたの毎日のコーディングや作業に、ささやかな癒やしと賑やかさをお届けします。

---

## ✨ 主な特徴

- ⚡ **ターミナル連携（macOS）**: コマンドの成否（Exit Code）に即座に反応してジャンプやため息アクション！
- 🤖 **AIアシスタント連携（Gemini）**: 思考中・生成完了・エラーなどのステータスに合わせたリアクション（※専用Chrome拡張機能を準備中）。
- 🖥️ **賢いお散歩ロジック**: 画面端だけでなく、マルチモニターの隙間や「崖」を自動判定して安全に折り返します。
- 🎨 **着せ替え・カスタム対応**: 初期キャラ「Toko」に加え、好みの自作キャラクター画像へGUIから即座に差し替え可能。
- ⚙️ **再起動いらずの設定画面**: サイズ変更（50%〜120%）や動作速度、自律移動の頻度をリアルタイムに調整できます。
- 🪶 **超軽量設計**: Rust + Tauri 2.0 ベース。透過ウィンドウでデスクトップ作業の邪魔をしません。

---

## 🚀 インストール & 起動方法

インストーラー不要・管理者権限不要で動くポータブル版（ZIP）を配布しています。

### ダウンロード
[Releases ページ](https://github.com/your-repo/tokotoko/releases) より、お使いのOSに合った最新のZIPファイルをダウンロードしてください。

- **macOS（Apple Silicon）**: `TokoToko_macOS_aarch64.zip`
- **Windows**: `TokoToko_Windows_x64.zip`

### 起動手順

#### macOS の場合
1. ダウンロードした ZIP を解凍します。
2. 展開された `TokoToko.app` をダブルクリック（または「アプリケーション」フォルダに移動して起動）します。
   > **初回起動時の注意:**  
   > 「開発元を検証できないため開けません」と表示された場合は、アプリアイコンを **右クリック（または Control + クリック）→「開く」** を選択し、ダイアログで「開く」をクリックしてください。

#### Windows の場合
1. ダウンロードした ZIP を適当なフォルダに解凍します。
2. フォルダ内の `tokotoko.exe` をダブルクリックして起動します。
   > **初回起動時の注意:**  
   > 「Windows によって PC が保護されました（SmartScreen）」が表示された場合は、**「詳細情報」→「実行」** をクリックしてください。

---

## 🎮 基本的な操作

| 操作 | 動作 |
| :--- | :--- |
| **左ドラッグ** | マスコットを掴んでデスクトップ上の好きな場所へ移動 |
| **左クリック** | その場で嬉しそうに大ジャンプ！ |
| **右クリック** | コンテキストメニューの表示（Jump / Settings... / Quit） |
| **メニューバー / トレイ** | アイコンから設定画面の起動や終了が可能 |

---

## 🔌 外部連携機能（本アプリの醍醐味！）

### 1. ターミナル連携（macOS / Linux）
お使いのシェル設定ファイルに数行追記するだけで、直前のコマンドが成功したか（Exit 0）失敗したか（Exit != 0）を検知して自動でマスコットがリアクションします。

#### zsh の場合
`~/.zshrc` の末尾に以下を追記します。

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

#### fish shell の場合
`~/.config/fish/config.fish` に以下を追記します。

```fish
# TokoToko ターミナル連携フック
function __tokotoko_on_success --on-event fish_postexec
    if test $status -eq 0
        echo '{"source":"terminal","action":"jumping"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    else
        echo '{"source":"terminal","action":"failed"}' | nc -U /tmp/tokotoko_mascot.sock >/dev/null 2>&1 &
    end
end
```

設定を再読み込み（`source ~/.zshrc` 等）後、適当なコマンドを実行してみてください。  
`ls`（成功）で跳びはね、存在しないコマンドの入力（失敗）で落ち込みます。

---

### 2. Gemini（ブラウザ）連携（Coming Soon）
Webブラウザ上で Gemini と対話している状態（思考中、生成完了、エラー等）を検知し、マスコットが空中ホログラムを操作したりひらめいたりします。

> **📢 近日公開予定:**  
> 現在、専用の Chrome 拡張機能を公開準備中です。公開次第、こちらの README および Releases にてセットアップ手順をご案内します。

---

## ⚙️ 設定（Settings）

マスコットを右クリックして **「Settings...」** を選ぶと、設定ウィンドウが開きます（設定は保存され、次回起動時も引き継がれます）。

- **Mascot Scale**: 表示サイズ（50%〜120%）。作業スペースに合わせて拡大・縮小可能。
- **Action Speed**: アニメーションの再生速度（0.5x〜2.0x）。
- **Random Action Interval**: 自律行動を起こす間隔（5〜30秒）。
- **Enable Autonomous Behavior**: 自律行動の ON / OFF（OFFにするとその場でおとなしく待機します）。
- **Remember Mascot Position**: 次回起動時に前回終了した座標へ復帰するかどうか。
- **Mascot Appearance**: カスタムスプライト画像のアップロードおよびデフォルト（Toko）への復帰。

---

## 🎨 追加キャラクター & カスタマイズ

### 🎁 公式追加キャラクター・限定衣装（Coming Soon）
「自分で描くのは大変だけど、いろんなキャラクターを動かしたい！」という方向けに、公式・コラボキャラクターの追加スプライト配信を準備中です。

- **通常衣装パック**
- **季節限定・特別モーション付きパック**（晴れ着、水着、ハロウィンなど）

配信プラットフォームやラインナップが決定次第、順次お知らせします。

---

### 🛠️ クリエイター向け：自作スプライトシートの仕様
ご自身で描いたオリジナルキャラクターを動かすことも可能です。設定画面の「Upload Custom Mascot」から画像（WebP または PNG）を選択するだけで即座に反映されます。

- **画像サイズ**: 全体 `2048px × 2816px`（透過PNG または ロスレスWebP）
- **コマ割り**: 横8コマ × 縦11行（1コマあたり `256px × 256px`）

#### 各行（Row）のアクション定義
| 行番号 | アクション名 | 概要 / 仕草 | 主なトリガー |
| :--- | :--- | :--- | :--- |
| **Row 00** | `idle` | デフォルトの静かな待機（呼吸・瞬き） | 通常待機 |
| **Row 01** | `running-right` | 右方向への歩行ループ | 自律移動（右） |
| **Row 02** | `running-left` | 左方向への歩行ループ | 自律移動（左） |
| **Row 03** | `waving` | 笑顔でピース・挨拶 | アイドル時 / 接続開始時 |
| **Row 04** | `jumping` | 歓喜のジャンプ・星エフェクト | クリック / コマンド成功 |
| **Row 05** | `failed` | ため息・落胆 | コマンド失敗 / エラー |
| **Row 06** | `waiting` | 耳元への髪かきあげ | 長時間待機時のアイドル行動 |
| **Row 07** | `running` | 思索〜ひらめき指立て | プロンプト送信直後 / 思考中 |
| **Row 08** | `review` | 空中ホログラム操作 | ビルド実行中 / 生成中 |
| **Row 09** | `surprised` | びっくり＆胸なでおろし | 割り込み通知 / 予期せぬイベント |
| **Row 10** | `bored` | ぼんやりおねむな退屈待機 | 長時間無操作 |

> **Tips:** アニメーションを作らず1枚絵の立ち絵だけ使いたい場合、各行に同じ画像を8コマ並べるだけでも動作します。

---

## ❓ よくある質問（FAQ）

**Q. アプリを終了するには？**  
A. マスコットを右クリックして「Quit」を選ぶか、メニューバー/タスクトレイのアイコンから「Quit」を選択してください（ショートカット: `Cmd + Q` / `Alt + F4`）。

**Q. 画面外に消えてしまった！**  
A. マスコットを一度終了し、設定ファイル（下記パス）の `"lastPosition"` を `null` に書き換えて再起動すると、初期位置に戻ります。
- macOS: `~/Library/Application Support/com.hidepon.tokotoko/config.json`
- Windows: `%APPDATA%/com.hidepon.tokotoko/config.json`

**Q. ターミナル連携が反応しません（macOS）**  
A. アプリが起動しているか確認してください。また、ターミナルで `nc`（netcat）コマンドが利用可能か、ソケットファイル `/tmp/tokotoko_mascot.sock` が存在するかを確認してください。

---

## 📜 ライセンス / 利用規約

本ソフトウェアは無料でお使いいただけるフリーウェア（プロプライエタリ）です。ソースコードの開示や無断再配布は行っておりません。

- **個人利用・法人利用**: 自身の端末（私物PC・業務用PC）上での通常利用は、個人・法人を問わず無償です。
- **商用・営利目的での利用**: 本ソフトウェアの自社製品・広告等への組み込みや再販売は禁止しています（※個人の配信・画面共有での映り込みは歓迎します）。

詳細な利用条件、禁止事項、および免責事項については、[ソフトウェア利用規約（TERMS_OF_USE.md）](./TERMS_OF_USE.md) をご確認ください。

---
© 2026 hidepon All rights reserved.
