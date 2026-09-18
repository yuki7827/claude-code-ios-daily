---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-19"
date: 2026-09-19 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.277（2026-09-18）リリース](https://code.claude.com/docs/en/changelog)** — **AGENTS.md のサポートを追加**（CLAUDE.md の代替として読み込み可）。Claude Code と OpenAI Codex CLI を併用している iOS チームは、これまで同内容の CLAUDE.md と AGENTS.md を 2 ファイル管理していたが、AGENTS.md 一本に集約できるようになった。あわせて `CLAUDE_GATEWAY_PROXY_IS_EGRESS_BOUNDARY=1` 環境変数が追加され、企業ゲートウェイ経由の iOS CI 環境で proxy 設定をより明示的に指定できる。VSCode 拡張の改善（バックグラウンドシェルのエージェントマップ表示・レスポンスコピーボタン・セッションコスト/トークン使用量表示）も含む。

- **[v2.1.276（2026-09-18）リリース](https://code.claude.com/docs/en/changelog)** — クリティカル修正: `ANTHROPIC_BASE_URL` をプロキシまたはゲートウェイに向けているとすべてのリクエストが `400 … Input tag 'advisor_20260301'` で失敗するリグレッション（v2.1.275 で混入）を修正。企業内ゲートウェイ経由で iOS CI に Claude Code を組み込んでいる環境は早急にアップデート推奨。

- **[v2.1.275（2026-09-17）リリース](https://code.claude.com/docs/en/changelog)** — 送信即時キー（Ctrl+Enter）の追加に加え、**claude.ai アカウントにサインインしたターミナルセッションへのスキル・プラグインの自動同期**が実装された。conorluddy/ios-simulator-skill 等の iOS 関連プラグインを claude.ai 側に登録しておくと、複数端末のターミナルセッションで自動的に有効になる。オプトアウトは `syncClaudeAiSkills: false` / `syncClaudeAiPlugins: false`。

## 🛠 GitHub の動き

- 該当なし（2026-09-19 時点で anthropics/claude-code に新規の iOS / Swift / Xcode 関連 Issue は確認できず。既報 Issue #94767・#94360 は引き続き OPEN）

## 📝 日本語コミュニティ

- **[Xcode 26.3エージェント型コーディング入門 — Claude・Codex・MCPで開発を自動化（Qiita / kai_kou 氏）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0)** — Xcode 26.3 のエージェント型コーディング機能（Claude Agent SDK / MCP 統合）のハンズオン入門記事。Claude Code の `omitClaudeMd: true` サブエージェントと Xcode 内蔵エージェントの使い分け、XcodeBuildMCP との連携設定、`.mcp.json` の書き方を日本語でまとめている。エージェント型ワークフローへの第一歩として読むのに適した内容。

## 🌐 海外コミュニティ / Tips

- **[ClaudeForFoundationModels、iOS 27 GA 以降プロダクション対応（anthropics/ClaudeForFoundationModels）](https://github.com/anthropics/ClaudeForFoundationModels)** — 09-17 既報の v0.2.0（Xcode 27 beta 5 採用）に続き、2026-09-14 の iOS 27 GA 公開を経て `LanguageModelSession` 経由の Claude 呼び出しが App Store 提出アプリで実運用可能になった。`claude-fable-5-1` を on-device モデルの透過的フォールバックとして組み込む iOS アプリが今後増えると予想される。iOS 27 未満のデプロイターゲットを維持する場合は `if #available(iOS 27, *)` での分岐が必須。

## 💡 今日のおすすめ実践 Tip

**「v2.1.277 の AGENTS.md サポートで iOS チームのプロジェクト設定ファイルを一元化する」**

v2.1.277 から Claude Code が `AGENTS.md` を `CLAUDE.md` の代替として読み込めるようになりました。iOS 開発チームで Claude Code と Codex CLI を併用している場合、同内容の 2 ファイルを廃止して `AGENTS.md` に一本化できます。

**移行前（2 ファイル管理）**

```
myproject/
├── AGENTS.md    # Codex CLI 向け
└── CLAUDE.md    # Claude Code 向け（内容がほぼ同じ）
```

**移行後（AGENTS.md 一本化）**

```
myproject/
└── AGENTS.md    # Claude Code も Codex も両方読み込む
```

Claude Code 固有設定（フック定義・Memory 等）だけを 1 行インポートで追記する方法もある:

```markdown
<!-- CLAUDE.md -->
@AGENTS.md

## Hooks
...
```

**iOS プロジェクト向け AGENTS.md テンプレート（Claude Code & Codex 共用）**

```markdown
# プロジェクト概要
MyApp / Xcode 27 / Swift 6.4 / iOS 18+ ターゲット

## ビルド
xcodebuild -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 17 Pro'

## テスト
xcodebuild test -scheme MyApp \
  -destination 'platform=iOS Simulator,name=iPhone 17 Pro'

## 制約
- Xcode ファイル追加は xcodegen（project.yml 管理）を使う
- Swift Concurrency: strict モード有効（Swift 6 準拠）
- スクリーンショット: xcrun simctl io <UDID> screenshot /tmp/sim.png
  （パネル経由は macOS 27 で不安定な Issue #94767 が未解消）
```

1 ファイルに集約することでどのエージェントからも同じ前提でコードを生成・修正できるようになります。
