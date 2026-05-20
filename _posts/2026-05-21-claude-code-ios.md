---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-21"
date: 2026-05-21 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.145 (May 19)](https://github.com/anthropics/claude-code/releases) — v2.1.144 に続くマイナーリリース。**`claude agents --json`** でライブセッション一覧を JSON として出力できるようになり、tmux-resurrect・ステータスバー・セッションピッカーなど外部スクリプトとの連携が容易になった。ステータスライン JSON に GitHub リポジトリ・PR 情報が追加され、iOS ビルドエージェントのセッション状態を外部ダッシュボードや通知スクリプトから参照できる。`/plugin` 画面がプラグインのコマンド・エージェント・スキル・フック・MCP/LSP サーバーを一覧表示するようになり、Xcode MCP 関連プラグインの設定確認が容易になった。端末リサイズ後のスピナーフリーズ・タスクリストのランダム表示順バグも修正。

## 🛠 GitHub の動き

- [zelentsov-dev/asc-mcp v2.5.0 (May 20)](https://github.com/zelentsov-dev/asc-mcp) — App Store Connect API を Claude Code から自然言語で操作できる Swift 製 MCP サーバー。36 ドメイン・**348 ツール**でリリースパイプライン全体（バージョン作成→ビルド添付→審査提出→段階的ロールアウト）・TestFlight ベータ配信・カスタマーレビュー返信・サブスクリプション管理・スクリーンショット/A/B テスト・アナリティクスまでカバー。`brew install mint && mint install zelentsov-dev/asc-mcp@v2.5.0` でインストールし、`claude mcp add asc-mcp -- ~/.mint/bin/asc-mcp` の 1 コマンドで Claude Code に追加可能。macOS 14+ / Swift 6.2+ / Xcode 26+ 必須。

- [gotoplanb/Hermit](https://github.com/gotoplanb/Hermit) — tmux + Claude Code ワークフロー専用 iOS SSH クライアント（Swift/SwiftUI 製、iOS 17+）。一般的なターミナルエミュレーターとは異なり、named tmux セッションへのアタッチに特化して設計。Citadel（SwiftNIO ベース）で SSH 接続・xterm.js でレンダリング・Super Whisper 連携のボイス入力・iCloud Drive によるセッション設定同期を装備。iPhone で `tmux attach -t claude-ios` するだけで Mac 上の Claude Code セッションに合流できる。Fastlane 経由の TestFlight 配布にも対応した Xcode プロジェクトとして公開されており、セルフビルドして TestFlight から実機インストールする構成。

## 📝 日本語コミュニティ

- [Claude Code「使い放題」は終わるのか？6月改定の全容と開発者がやるべきこと](https://zenn.dev/sanpi34/articles/claude-code-pricing-shift-2026) (sanpi34, Zenn) — 2026年6月15日から Claude Agent SDK / `claude -p` 経由のプログラム利用が通常サブスク枠とは別の Credit Pool（プラン別 $20〜$200/月）で従量課金に移行することを解説。「人が対話で使う Claude」と「エージェントが自律実行する Claude」がコスト上も分離されることが骨子。iOS 開発者への直接影響は Fastlane や CI スクリプトで `claude -p` を呼び出しているケースで、その用途は Credit Pool 消費となる。Anthropic Console の API キー（pay-as-you-go）を使う場合は対象外で従来どおり。6月以降のコスト最適化を検討している iOS 開発者向けの整理として参照価値が高い。

- [Claude Code で5日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days) (seeda_yuto, Zenn, Mar 31, 2026) — Swift 経験ゼロの著者が Claude Code（Opus）に全コードを書かせ、5 日間・55 コミット・SwiftUI + CryptoKit + MapKit 約 4,000 行の iOS アプリ「VoltKeep」（Tesla 走行ログレコーダー）を App Store リリースした実録。`.pbxproj` を直接編集させずに Xcode MCP 経由で登録を通す・300,000 件のデータ消失を引き起こした失敗など「任せてよかったこと・任せてはいけないこと」の判断基準が具体的にまとめられており、初めて Claude Code で iOS アプリを仕上げる際のリスク把握に有用。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System](https://blakecrosley.com/blog/xcode-mcp-claude-code) (Blake Crosley) — XcodeBuildMCP（ビルド・テスト・シミュレータ・LLDB、Xcode プロセス不要）と Apple 純正 xcrun mcpbridge（ファイル操作・リアルタイム診断・SwiftUI プレビュー・ドキュメント検索）の 2 本の MCP サーバーを組み合わせて Claude Code を iOS ビルドシステムとして機能させるセットアップ手順を解説。ログ文字列を解析する代わりに構造化 JSON で結果を受け取り、シェルコマンド代わりのツールコールでビルドを制御できる点を強調。`claude mcp add` 2 コマンドで完結する最小構成から説明しており、MCP 初めての iOS 開発者向けの導入記事として読みやすい。（前作 *Practitioner's Guide* とは別記事）

## 💡 今日のおすすめ実践 Tip

**asc-mcp で TestFlight 配信を自然言語から実行する**

`zelentsov-dev/asc-mcp` を Claude Code に追加すると、TestFlight へのビルド配信・ベータグループ管理・リリースノート編集などを自然言語で依頼できる。XcodeBuildMCP でアーカイブビルドを作成した直後に asc-mcp でアップロードまで流すと、「ビルド→配信」を Claude Code だけで完結させられる。

```bash
# 1. Mint でインストール
brew install mint
mint install zelentsov-dev/asc-mcp@v2.5.0

# 2. Claude Code に MCP サーバーとして追加
claude mcp add asc-mcp -- ~/.mint/bin/asc-mcp

# 3. App Store Connect API キーを環境変数で設定
export ASC_KEY_ID="XXXXXXXXXX"
export ASC_ISSUER_ID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
export ASC_PRIVATE_KEY_PATH="$HOME/.private_keys/AuthKey_XXXXXXXXXX.p8"
```

設定後、Claude Code に「ステージングビルドを TestFlight のベータグループ "Internal QA" に配信して、リリースノートに『v1.2.3 — ログイン画面のバグ修正』を記入してください」と依頼するだけで処理が走る。ツール数が多い（348 本）ため `--workers testflight,builds` のように使うドメインを絞ると Windsurf などツール数制限のある MCP クライアントとも共存できる。6 月 15 日以降の Credit Pool 移行後も、CI 上で `claude -p` 経由で呼び出す場合はコスト設計を見直すタイミング。
