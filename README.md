<div align="center">

  <img src="ui/tokotoko-badge.svg" alt="TokoToko Logo" width="220">

  # TokoToko (トコトコ)

  **デスクトップでちょこちょこ動く、Gemini連携デスクトップマスコット**

  [![Release](https://img.shields.io/github/v/release/<GitHubユーザー名>/tokotoko?style=flat-square)](https://github.com/<GitHubユーザー名>/tokotoko/releases)
  [![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Windows-blue?style=flat-square)](#)
  [![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](#)

</div>

---

## 概要 (Overview)

**TokoToko** は、Tauri (Rust + Web) で構築された軽量なデスクトップマスコットアプリです。  
ブラウザでの作業や Gemini との対話に合わせて、画面の隅でキャラクターが愛らしくアニメーション（歩く・待機・リアクション）します。

- **省リソース & 軽量:** Rust 製バックエンド（Tauri）による高速・低負荷な動作。
- **クロスプラットフォーム:** macOS (Apple Silicon / Intel) および Windows に両対応。
- **ブラウザ連携:** 専用 Chrome 拡張機能やローカル通信を介して、Gemini の状態に応じたリアクションを実現。
- **スプライトシート差し替え:** お好みのキャラクター画像（スプライトシート）に入れ替えて楽しむことが可能。

---

## ダウンロード (Download)

最新版のインストーラーおよび実行ファイルは [Releases ページ](https://github.com/<GitHubユーザー名>/tokotoko/releases) からダウンロードしてください。

| OS | 配布フォーマット | 備考 |
| :--- | :--- | :--- |
| **macOS** | `.dmg` / `TokoToko.app` | 初回起動時に Gatekeeper の警告が出る場合は「右クリック ➔ 開く」を実行してください |
| **Windows** | `.msi` / `.exe` | SmartScreen が表示された場合は「詳細情報 ➔ 実行」を選択してください |

---

## クイックスタート (Quick Start)

### 1. アプリの起動
ダウンロードしたインストーラーを実行するか、アプリケーションフォルダに配置して起動します。  
画面右下にマスコットが表示され、待機モーションを開始します。

### 2. ブラウザ / Gemini 連携 (設定)
1. タスクトレイ（またはアプリ設定）から **Settings** を開きます。
2. 連携用のローカルポート（デフォルト設定）を確認します。
3. Chrome 拡張機能をブラウザに導入し、Gemini 画面を開くことで自動的にマスコットへ状態が送信されます。

---

## 開発者向けガイド (For Developers)

### 前提条件
- [Rust](https://www.rust-lang.org/) (最新の stable)
- Node.js (v18+) または関連ツールチェーン
- OS ごとのビルド依存関係 ([Tauri 公式前提条件](https://tauri.app/v1/guides/getting-started/prerequisites) を参照)

### セットアップ & 開発実行
```bash
# リポジトリのクローン
git clone [https://github.com/](https://github.com/)<GitHubユーザー名>/tokotoko.git
cd tokotoko

# 開発サーバー起動
cargo tauri dev
