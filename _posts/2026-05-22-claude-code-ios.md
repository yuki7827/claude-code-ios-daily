---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-22"
date: 2026-05-22 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.147 (May 21)](https://code.claude.com/docs/en/changelog) — 前日の v2.1.145 から 2 バージョン進んだメジャーパッチ。iOS 開発者に直接影響する変更が複数含まれる。**`/simplify` が `/code-review` にリネーム**され、`/code-review high` のように effort level を指定して正確性バグの検出精度を調整できるようになった。`--comment` フラグを付けると GitHub PR にインラインコメントとして自動投稿するため、XcodeBuildMCP でビルドを通した直後に `/code-review --comment` を走らせると PR レビューを自律完了させられる。**`Workflow` ツール**（`CLAUDE_CODE_WORKFLOWS=1` で有効化）が追加され、複数エージェントを決定論的に順序制御するオーケストレーションが可能に。`claude agents` 画面でのピン留めバックグラウンドセッションがアイドル中も継続し、Claude Code アップデート時に自動再起動するよう改善。REPL と Workflow サンドボックスをプロトタイプ汚染・thenable ベースのエスケープに対して強化するセキュリティ修正も含む（50+ バグ修正）。

## 🛠 GitHub の動き

- [SzakacsCo/sourcekit-lsp-marketplace](https://github.com/SzakacsCo/sourcekit-lsp-marketplace) — Apple SourceKit-LSP を Claude Code マーケットプレイスプラグインとして提供するリポ（新規）。Swift・Objective-C・Objective-C++・ヘッダファイルに対応し、定義ジャンプ・参照検索・インライン診断・コード補完を Claude Code のツールとして公開する。インストールは `/plugin` → Marketplaces タブで `SzakacsCo/sourcekit-lsp-marketplace` を追加 → Discover タブから `sourcekit-lsp` をインストール。SourceKit-LSP は Swift ツールチェーンに同梱されており追加インストール不要。

- [Issue #21335 — swift-lsp plugin installed but LSP tools not exposed to Claude（Closed）](https://github.com/anthropics/claude-code/issues/21335) — 公式 `swift-lsp` プラグインが正常にインストールされているにも関わらず Claude Code 側に LSP ツールが公開されない問題が Closed（解決）に。v2.1.147 の修正に含まれるとみられる。回避策として残る場合は `claude plugin enable swift-lsp` 後に再起動し、それでも解決しない場合は `export ENABLE_LSP_TOOL=1` をシェルプロファイルに追加すると有効。

## 📝 日本語コミュニティ

- [Xcode 26.3エージェント型コーディング入門 — Claude・Codex・MCPで開発を自動化](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) (kai_kou, Qiita) — Xcode 26.3 のエージェント型コーディング機能を体系的に解説した入門記事。Claude Agent と Codex の組み込みエージェント、Apple 純正 MCP ブリッジ（`xcrun mcpbridge`）経由のツール呼び出し（`BuildProject`・`RenderPreview`・`DocumentationSearch`）、外部 MCP サーバー（XcodeBuildMCP）の組み合わせ方を手順付きで紹介。

- [【第2弾】VS CodeのClaude Codeで「iOSコードレビュー → 修正 → Xcodeビルド確認 → テスト項目作成」を自動化する](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) (4q_sano, Qiita) — VS Code 拡張経由の Claude Code で iOS のコードレビュー→自動修正→Xcode ビルド→テスト項目生成を一気通貫に自動化するフロー解説の第 2 弾。第 1 弾（コードレビュー→修正→ビルド確認）にテスト項目作成ステップを追加した構成。

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 — Part 3: Using Claude Agent Targeting iOS 18 or Later](https://medium.com/@chinthaka01/agentic-coding-in-xcode-26-3-8b62c1e46bf2) (Chinthaka, Medium) — Xcode 26.3 内 Claude Agent を iOS 18+ 専用にターゲットを絞って運用する実践シリーズの第 3 回。iOS 26 SDK API へのアダプティブ構成・新しい Liquid Glass コンポーネント生成・SwiftUI Preview キャプチャによる反復修正のループを iOS 18 以上限定プロジェクトに特化した設定で試した実録。

## 💡 今日のおすすめ実践 Tip

**`/code-review --comment` で iOS PR のインラインレビューを Claude Code に自動投稿させる**

v2.1.147 で `/simplify` が `/code-review` にリネームされ、GitHub PR へのインラインコメント投稿がワンフラグで完結するようになりました。XcodeBuildMCP でビルド・テストをパスさせた後、以下の手順だけで Claude Code が PR レビューを書いてコメントとして投稿します。

```bash
# 1. PR を作成済みの状態で Claude Code に依頼
# （GitHub MCP が設定されていることが前提）

/code-review --comment
# → 複数のサブエージェントが並列でコードを解析し、
#    信頼度 80% 以上の指摘のみ GitHub PR にインラインコメントとして投稿

# effort level を指定（high にするほど厳密・時間増）
/code-review high --comment
```

Swift コードのレビューでは以下の観点が特に有効です：
- `async/await` の誤用（`Task {}` 内で `await` なしで非同期関数を呼ぶ等）
- `@MainActor` 境界をまたぐ `Sendable` 違反
- `@Observable` マクロとの組み合わせで起きる購読漏れ

`SzakacsCo/sourcekit-lsp-marketplace` で SourceKit-LSP を有効化した状態で `/code-review` を実行すると、LSP の型情報を参照した精度の高いレビューが得られます。PR ごとに手動レビューをゼロから書く手間が大幅に削減できます。
