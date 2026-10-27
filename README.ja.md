<div align="center">

# TokoToko (トコトコ) 🐾

**ピクセルの隙間に広がる電脳空間から、あなたのデスクトップへ。**  
開発環境やAIアシスタントのステータスに寄り添う、超軽量な自律型デスクトップマスコット。

[English](README.md) | **日本語**

https://github.com/user-attachments/assets/ad3d3bed-1db9-4a9a-b4e1-814875adcbbf

</div>

---

## 🌿 TokoTokoとは？

> *「コードを書くとき、ピクセルの隙間に小さな庭があるのを知っていますか？*  
> *打鍵音を音楽のように聴き、コンパイルの熱を太陽のように浴びて暮らす住民たちの世界――ビット・ガーデン。」*

**TokoToko** は、画面の端っこをとことこ歩き回る超軽量なデスクトップマスコットです。  
最初の住人「Toko」をはじめとするキャラクターたちが、あなたの作業の邪魔をすることなく、タイピングやターミナルのビルド、AIアシスタントの思考にそっと寄り添います。

作業や思考の邪魔をしないよう、**「完全静音（音声なし）」** かつ **「画面端を水平に歩くだけの控えめな設計」** を採用しており、Web会議中や深夜の開発、音楽鑑賞中でも安心して常駐させられます。

---

## ✨ 主な機能

* 🪶 **超軽量・極小フットプリント**  
  Tauri v2 と Rust で構築され、常駐時のメモリ消費やCPU負荷を極限まで抑制。ノートPCでのバッテリー駆動時でも安心して常駐させられます。
* 🛡️ **作業集中モード (Focus Mode)**  
  マスコットのクリック判定を背後のウィンドウへ透過させるモードを搭載。画面端のウィンドウ操作やテキスト選択を一切妨げません。
* 💬 **すりガラス通知バー**  
  頭上に現れるスマートな通知バー。送信元（ターミナル、AI、メール等）に合わせてアイコンやアクセントカラーが美しく切り替わります。
* 🖼️ **Character Slots (最大5スロット) & キズナ度**  
  ビルトインの「Toko」に加え、最大4体の自作キャラクターや追加キャラクター（`.png` / `.webp` / `.toko` / `.zip` / `.tokochar`）を登録可能。一緒に過ごした時間に応じてキズナランクが成長します。
* 🌿 **Bit Garden (🔒 近日公開予定)**  
  キャラクターたちが暮らす電脳庭園を覗く専用ウィンドウ。今後のアップデートで順次公開予定です（※入場用 Bit Garden Passport は BOOTH 等の外部ショップにて提供予定）。
* 🧩 **外部ツール・AI連携**  
  Google Chrome拡張機能との連動や、ローカルソケット（Unix Domain Socket / Windows Named Pipe）経由でのワンライナー連携に対応しています。

---

## 📦 インストール方法

お使いのOSに合わせて、以下の方法で簡単にインストールできます。

### 🪟 Windows
* **Microsoft Store** よりワンクリックでインストール可能です。  
  <!-- [Microsoft Store バッジリンク] -->
* または [GitHub Releases](https://github.com/hidepon-dev/tokotoko-releases/releases) からインストーラー（`.msi` / `.exe`）をダウンロードしてください。

### 🍏 macOS
* **Homebrew Cask** に対応しています：
  ```bash
  brew install --cask tokotoko
  ```
* または [GitHub Releases](https://github.com/hidepon-dev/tokotoko-releases/releases) から `.dmg` をダウンロードし、アプリケーションフォルダへ配置してください。

---

## 🚀 基本的な使い方

1. **起動と終了**  
   アプリを起動すると、デスクトップ上に Toko が現れます。マスコット（またはトレイアイコン）を右クリックしてコンテキストメニューを開き、「終了」を選択すると終了します。
2. **作業集中モード (Focus Mode)**  
   メニューから「作業集中モード」をオンにすると、クリック透過状態になります。**解除するときは、メニューバー（macOS）またはタスクトレイ（Windows）の TokoToko アイコンからチェックを外してください。**
3. **設定画面 (`⚙️ System Core...`)**  
   メニューから `⚙️ System Core...` を開くと、マスコットの移動速度、表示スケール（50%〜120%）、キャラクタースロット、通知バーのデザインなどを自由にカスタマイズできます。

👉 詳しい操作方法・ターミナルやChrome拡張機能の導入手順・FAQは **[TokoToko 利用者ガイド](docs/USER_GUIDE.ja.md)** をご覧ください。

---

## 🛠️ 自作キャラクター & 外部連携を作ろう！

TokoToko は、クリエイターやギークが自由にカスタマイズできるよう、オープンな連携口を用意しています。

### 🎨 自作キャラクターの追加・パッケージ配布
自分で描いたキャラクター画像（`.png` / `.webp`）や、名前・セリフをまとめたパッケージ（`.toko` / `.zip`）をドラッグ＆ドロップするだけで、デスクトップを歩かせることができます。  
* 👉 **[自作キャラクター制作・スプライトシート仕様ガイド](docs/SPRITE_SPEC.ja.md)**
* 👉 **[キャラクターパッケージ（`.toko` / `.zip`）制作・配布ガイド](docs/CHARACTER_PACKAGE.ja.md)**

### ⚡ ターミナル・外部アプリからの連携 (IPC)
TokoToko はローカルソケット（macOS: Unix Domain Socket / Windows: Named Pipe）を開いており、JSONを1行流し込むだけで、好きなタイミングでマスコットをジャンプさせたりメッセージを喋らせたりできます。

* **macOS (zsh / fish / bash) のワンライナー例:**
  ```bash
  echo '{"source":"terminal","action":"jumping","duration":3000,"text":"ビルドが完了したよ！"}' | nc -U /tmp/tokotoko_mascot.sock
  ```

* **Windows (PowerShell) のワンライナー例:**
  ```powershell
  $pipe = New-Object System.IO.Pipes.NamedPipeClientStream('.', 'tokotoko_mascot.sock', [System.IO.Pipes.PipeDirection]::Out); $pipe.Connect(500); $w = New-Object System.IO.StreamWriter($pipe); $w.WriteLine('{"source":"terminal","action":"jumping","duration":3000,"text":"タスク完了！"}'); $w.Flush(); $w.Close(); $pipe.Close()
  ```

自作スクリプト、CI/CD完了フック、エディタ拡張などから自由にTokoTokoを動かせます。  
* 👉 **[ローカルIPC通信プロトコル仕様書](docs/IPC_SPEC.ja.md)**

---

## 📄 利用規約・プライバシーポリシー

本ソフトウェアは無料でお使いいただけるフリーウェア（プロプライエタリ）です。ソースコードの開示や無断再配布は行っておりません。

* **個人利用・法人利用**:  
  自身の端末（私物PC・業務用PC）上での通常利用は、個人・法人を問わず無償です。
* **商用・営利目的での利用**:  
  本ソフトウェアを自社製品や広告等へ組み込むこと、および再販売・再配布することは禁止しています。  
  *(※個人の動画配信や画面共有においてデスクトップ上のTokoTokoが映り込むことは大歓迎です)*

詳細な利用条件、禁止事項、クレジット表記、および免責事項については、以下のドキュメントをご確認ください。
* 📜 **[ソフトウェア利用規約 (TERMS_OF_USE.md)](TERMS_OF_USE.md)**
* 🔒 **[プライバシーポリシー (PRIVACY_POLICY.md)](docs/privacy/PRIVACY_POLICY.md)**

---

<div align="center">
  <sub>Designed & Developed with ❤️ by hidepon & Alice</sub><br>
  <sub>Copyright © 2026 TokoToko Project 🐾. All rights reserved.</sub>
</div>
