---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-05"
date: 2026-07-05 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.201（2026-07-03）— Sonnet 5 セッションでミッドコンバセーション System Role を廃止](https://code.claude.com/docs/en/changelog) — 単一変更のマイナーリリースだが、Sonnet 5 をデフォルトで使う iOS 開発者全員に影響する。これまで長い会話の途中でハーネスリマインダー（「ツール一覧を確認せよ」等の内部指示）をシステムロールで挿入していたが、今後はユーザーロール側に移動する。これにより会話履歴のキャッシュ効率が向上し、プロジェクト全体を渡して長時間作業するような SwiftUI リファクタリングや TCA 移行タスクでのコスト削減が見込まれる。Sonnet 5 を使わない場合（Opus 4.6 等を明示指定している場合）は変更なし。

## 🛠 GitHub の動き

- [ryaker/appstore-connect-mcp — iOS 対応 OAuth 付き App Store Connect MCP サーバー](https://github.com/ryaker/appstore-connect-mcp) — Apple の App Store Connect API を Claude AI から呼び出すための MCP サーバー。iOS 向けの OAuth フローに対応しており、アプリのメタデータ管理・スクリーンショット・レビュー・TestFlight ビルド確認などを Claude Code のセッション内から直接操作できる。App Store Connect MCP は「メタデータ・ビルドトラッキング・TestFlight 確認」を担い、fastlane は「コード署名・IPA パッケージング・バイナリアップロード」を担う分業構成が推奨されており、両者は競合ではなく補完関係。

- [Piebald-AI/tweakcc — Claude Code のシステムプロンプトとツールセットをカスタマイズする CLI](https://github.com/Piebald-AI/tweakcc) — Claude Code のシステムプロンプト差し替え・カスタムツールセット作成・入力パターンハイライト・テーマ変更・AGENTS.md サポートなど多数のカスタマイズを提供する CLI ツール。iOS 開発への応用として、「Swift / SwiftUI 専用モード」としてツールセットを絞り込んだり、XcodeBuildMCP に特化したカスタムシステムプロンプトをプロジェクトごとに切り替えたりする使い方が想定される。macOS / npm 両インストールに対応。

## 📝 日本語コミュニティ

- [Claude Code標準SkillをiOSアプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — VS Code × Claude Code × iOS シリーズの最新作。公式ドキュメントに記載されている標準 Skill（`/code-review`・`/verify`・`/simplify`・`/security-review` など）をそれぞれ iOS 開発のどのシーン（SwiftUI レビュー・テスト・リファクタリング・コード署名フロー確認）で有効に使えるか、根拠となる公式の仕様と合わせて整理した解説記事。Skill の仕様を正確に理解した上で iOS CI に組み込みたい開発者向け。

- [iOSアプリ開発未経験者がClaude Codeを使って1週間でリリースしたお話（Qiita / chatrate）](https://qiita.com/chatrate/items/bd2703d68a2bb8fffe08) — iOS 開発経験ゼロの著者が Claude Code だけを頼りに SwiftUI アプリを約 1 週間（実質コーディング 2 日程度）で App Store リリースするまでの実録。詰まった箇所と突破法・Claude Code が間違えやすい UI/UX パターン・「どこで人間の判断が必要か」のリアルな感触が率直に書かれており、初めて Claude Code で iOS 開発を始める人の参考になる。

## 🌐 海外コミュニティ / Tips

- [App Store Connect Expert — MCPmarket の Claude Code Skill](https://mcpmarket.com/tools/skills/app-store-connect-expert) — App Store Connect API と Fastlane を組み合わせて App Store 申請・メタデータ管理・スクリーンショット生成を自動化する公開 Skill。`/plugin marketplace add` で即導入でき、`ExportOptions.plist` の設定やローカライズされたスクリーンショット生成（5 デバイスファミリー × 28 言語）まで Claude に委任できる。同サイトには [Fastlane iOS Beta Release Skill](https://mcpmarket.com/tools/skills/fastlane-ios-beta-release)（Match でのコード署名自動化・Pilot での TestFlight アップロード）も別 Skill として公開されており、CI/CD の各フェーズを Skill 単位で差し替えられる設計が特徴。

- [Shipping iOS Apps with Claude: SwiftUI to App Store（vmobify.com）](https://vmobify.com/blog/shipping-ios-apps-claude-app-store/) — SwiftUI で実装した iOS アプリを Claude Code を使って App Store に提出するまでの全工程を解説したガイド。Fastlane（コード署名・IPA 生成・TestFlight アップロード）と App Store Connect MCP（メタデータ・スクリーンショット・審査ステータス確認）を役割分担させ、Claude Code が両者のオーケストレーションを担う構成。TestFlight ベータ配信の自動化から審査提出まで Claude Code セッション内で完結する実装例が示されている。

## 💡 今日のおすすめ実践 Tip

**「App Store Connect MCP + Fastlane Skill で TestFlight 配信を Claude Code から一気通貫自動化する」**

`ryaker/appstore-connect-mcp` と MCPmarket の Fastlane iOS Beta Release Skill を組み合わせると、「ビルド → 署名 → TestFlight アップロード → 内部テスター通知」を Claude Code セッション内で完結できます。

**設定手順**

```bash
# 1. App Store Connect MCP を追加（ユーザースコープ）
claude mcp add appstore-connect-mcp -s user -- \
  npx -y appstore-connect-mcp@latest mcp

# 2. Fastlane iOS Beta Release Skill をインストール
/plugin marketplace add fastlane-ios-beta-release

# 3. fastlane/Matchfile に証明書リポを設定しておく
# （初回のみ。以降は Skill が自動参照）
```

**Claude Code への指示例**

```
「TestFlight に最新ビルドを配信して。
手順：
1. Fastlane match で Development/Distribution 証明書を更新
2. xcodebuild archive で MyApp-iOS をアーカイブ（スキーム: MyApp-Release）
3. Fastlane pilot で TestFlight にアップロード
4. App Store Connect MCP で内部テスターグループ "Core Team" に通知

ビルドが失敗したら XcodeBuildMCP のエラー抽出ツールで原因を特定して
自律修正してから再ビルドして。完了したらビルド番号を教えて。」
```

**役割分担のポイント**

| ツール | 担当範囲 |
|--------|----------|
| XcodeBuildMCP | ビルド・テスト・エラー修正 |
| Fastlane Skill | コード署名・IPA 生成・TestFlight アップロード |
| App Store Connect MCP | メタデータ更新・テスター通知・審査ステータス確認 |

`dontAsk` モードを CI 環境で使いつつ、`Bash(git push:*)` を明示 deny しておくと、エージェントがブランチを汚染せずに配信フローだけを自律実行できます。ブランチ保護と組み合わせることで、CI トリガーから TestFlight 配信まで人手ゼロで回るパイプラインが組めます。
