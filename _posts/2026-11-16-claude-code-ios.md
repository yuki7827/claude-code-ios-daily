---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-16"
date: 2026-11-16 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.325 追補 — /loop・スケジュールタスクでスキル実行前の確認が必須に](https://code.claude.com/docs/en/changelog) — 11/13 リリースの v2.1.325 には昨日の記事で触れた zstd セキュリティ修正・コンテキスト長修正の他に iOS CI に影響する変更が含まれる。①スケジュールタスクや `/loop` が `allowed-tools` または hooks を宣言するスキルを実行する際にユーザー確認が必須になった（無人実行する場合は `permissions.allow` に `Skill(スキル名)` を追加する必要あり）。② sandbox モードの Bash が通常のパーミッションフローに従うようになった（旧動作に戻すには settings.json に `"sandbox": { "autoAllowBashIfSandboxed": true }` を追加）。iOS CI でスキルを含む無人ビルドをしているチームは今週中に設定確認を推奨。

## 🛠 GitHub の動き

- [ApptitudeLabs/claude-code-ios-dev-setup（GitHub）](https://github.com/ApptitudeLabs/claude-code-ios-dev-setup) — MobileBuildMCP・AXe/Baguette/idb によるシミュレーター自動操作・Axiom iOS スキルスイート・PRD 駆動ワークフロー・SwiftLint/swift-format 連携など iOS 開発に必要な Claude Code セットアップを一冊にまとめた設定ガイド（MIT）。既出の `keskinonur/claude-code-ios-dev-guide` よりシミュレーター操作ツール（AXe・idb）とトークン効率化の項目が充実している。

## 📝 日本語コミュニティ

- [iPad から Claude Code をスキル選択付きで操作するアーキテクチャ（Zenn / sika7）](https://zenn.dev/sika7/articles/54074477b83049) — VPS 上の Claude Code を iPad の SwiftUI アプリから無線操作するアーキテクチャ提案。スキルをタッチメニューで選び HTTPS → Laravel → `claude -p` と橋渡しし、実行結果を SSE でリアルタイム表示する構成。ターミナル不要で iPad のみで Claude Code を使える点が面白い。ヘッドレス CLI の制約（セッション継続性なし・パーミッション割り込み）の回避策も整理されている。

## 🌐 海外コミュニティ / Tips

- [Claude Agent in Xcode 27: Setup and Real-iPhone Testing（testmuai.com）](https://www.testmuai.com/blog/claude-agent-xcode-real-device-testing/) — Xcode 27 での `/plan` モード・Device Hub によるシミュレーター操作の紹介に加え、「エージェントが書いたコードをエージェント自身に採点させない」独立検証の重要性を指摘。解決策として **Kane CLI**（実機クラウドテスト CLI）を紹介。exit code `0=pass / 1=fail` を CI ゲートとして使い、`xcodebuild` でビルド → `.ipa` を Kane CLI に投入 → 実機マトリクスでテスト → 緑になってからマージ、という CI ループが具体的に解説されている。

- [Xcode 26.3 で MCP が追加され Claude Code から SwiftUI のレンダリングができるようになった（Zenn / zaico）](https://zenn.dev/zaico/articles/98a1528d07d195) — `RenderPreview` ツールが SwiftUI プレビューのスナップショットを一時ディレクトリに保存することを発見し、**PostToolUse フック**で自動的にプロジェクトの `Snapshots/` フォルダへビュー名でコピーするレシピを紹介。ビジュアルリグレッションの基準画像生成・PR への UI 証跡添付・デザインレビュー資料の自動作成に応用できる。macOS 26.2 以前では認証ダイアログが出ない不具合も言及されている。

## 💡 今日のおすすめ実践 Tip

**PostToolUse フックで SwiftUI プレビューのスナップショットを自動蓄積する**

Zenn zaico 記事が示したアプローチを整理すると、設定は `.claude/settings.json` の `hooks.PostToolUse` に `mcp__xcode__RenderPreview` を matcher として追加するだけ（詳細コードは上記記事参照）。これにより：

1. Claude に「各 View のプレビューを描画して」と指示するだけで `Snapshots/` フォルダに成果物が自動蓄積される
2. `Snapshots/` を Git 管理すれば PR の diff でデザイン変化を可視化できる
3. GitHub Actions の `upload-artifact` と組み合わせれば PR への UI スクリーンショット自動添付が実現する

前提として Xcode が起動中であり、`claude mcp add --transport stdio xcode -- xcrun mcpbridge` で mcpbridge が登録済みであること。macOS 26.2 未満では認証ダイアログが表示されない既知の不具合があるため、macOS のバージョンを先に確認しておくこと。
