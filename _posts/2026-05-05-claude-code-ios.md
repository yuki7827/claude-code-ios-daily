---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-05"
date: 2026-05-05 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート
- 該当なし（XcodeBuildMCP v2.5.0-beta.1 は昨日掲載済み。次の安定版待ち）

## 🛠 GitHub の動き
- [greenstevester/fastlane-skill](https://github.com/greenstevester/fastlane-skill) (⭐18) — Claude Code から Fastlane を自然言語で操作するスキル。「Set up Fastlane for my iOS app」「Upload this build to TestFlight」のような指示だけで、Appfile/Fastfile 生成・Match 証明書設定・スクリーンショット自動化・TestFlight アップロード・App Store 申請の 5 工程を自動化。MIT ライセンス。
- [iOS Project Claude Code Setup Prompt（Gist: joelklabo）](https://gist.github.com/joelklabo/6df9fa603bec3478dec7efc17ea44596) — XcodeBuildMCP 前提の 8 フェーズ初期設定プロンプト。`.claude/` ディレクトリに `/build` `/test` `/run` などのスラッシュコマンドを自動生成し、セッション開始フックや CLAUDE.md を含む「コンテキスト自己維持」環境を一気に構築できる。

## 📝 日本語コミュニティ
- [Claude Code で5時間で2つのミニアプリを作った話](https://zenn.dev/hololab/articles/9b8eb0570975ac) — 実務経験者が Claude Code を使い 5 時間で iOS ミニアプリを 2 本完成させた実録。スピード感とプロンプト設計のコツを紹介。
- [Copilot VisionとClaude CodeでiOSアプリを新規作成して比較](https://zenn.dev/yamazaking/articles/copilot-vision-claude-code-ios) — 同一仕様で Copilot Vision と Claude Code を比較。Claude Code はデータ永続化（UserDefaults）を自律的に実装した一方、Copilot Vision はセッション揮発の問題が出た点など差異を整理。
- [Claude CodeからXcodeが提供するMCPに接続する方法](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) — Xcode 26.3 の純正 MCP サーバーに Claude Code CLI から接続する具体的な手順。BuildProject・RunAllTests・RenderPreview を CLI から呼び出す設定を解説。

## 🌐 海外コミュニティ / Tips
- [Claude Code × Xcode iOS Development Guide — Practical Techniques](https://claudelab.net/en/articles/claude-code/claude-code-xcode-ios-swift-development-guide) — SwiftUI コンポーネント生成・テスト自動化・Core Data スキーマ設計をターミナルから加速する実践ガイド。プロジェクト初期設定から CLAUDE.md の書き方まで網羅。
- [An Architecture for Operating Claude Code from iPad with Skill Selection](https://zenn.dev/sika7/articles/54074477b83049?locale=en) — iPad のタッチ UI からスキルを選択して Claude Code を操作するアーキテクチャ設計。ローカル Mac をサーバーにして iPad をフロントエンドとして活用するモバイルファーストな構成。

## 💡 今日のおすすめ実践 Tip

**fastlane-skill で TestFlight アップロードを自然言語 1 行に**

[fastlane-skill](https://github.com/greenstevester/fastlane-skill) を `claude skills add greenstevester/fastlane-skill` でインストールすると、Claude Code のセッション内で `Upload this build to TestFlight` と打つだけで Fastlane の beta レーンが走り、証明書確認・ビルド・アップロードが一気通貫で完了します。従来は Fastfile の書き方を調べながら手動設定が必要でしたが、このスキルは Claude が Fastlane ドキュメントを内包しているため、初期設定も含めてゼロ知識から始められます。CI に組み込む前の手動アップロードフローを劇的に短縮できます。
