---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-19"
date: 2026-10-19 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[Claude Haiku 5.5 が Claude Code のデフォルト Haiku モデルに（2026-10-07 〜）](https://code.claude.com/docs/en/changelog)** — 1M トークンコンテキスト・128K 出力・$0.10/M input（100K 超は 5 倍率）の Haiku 5.5 が Claude Code v2.1.293 以降でデフォルト Haiku として利用可能に。Haiku シリーズ初の effort control（low〜max）と thinking 常時 on を搭載。大規模 Xcode プロジェクトでも全ファイルをコンテキストに収められる点が iOS 開発で有用。サブエージェントモデルとして `--model claude-haiku-5-5` で起動できる（Pro/Max/Team/Enterprise または API キープランが必要）。

## 🛠 GitHub の動き

- 該当なし（2026-10-19 時点で iOS / Xcode / Swift 関連の新規 Issue・PR は確認できず）

## 📝 日本語コミュニティ

- **[Xcode MCP × Claude Code プラグインで、iOS ビルドを自動化する（zenn.dev/kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid)** — Xcode 26.3 付属の純正 MCP（xcrun mcpbridge）と Claude Code のプラグイン機構を組み合わせてビルド〜テスト〜SwiftUI プレビュー確認を一気通貫で自動化するワークフローを解説。プラグインのフックで `xcodebuild` 実行後に mcpbridge 経由でプレビューを自動取得するパターンが実践的。Claude Code v2.1 以降・macOS 26・Apple Silicon が前提条件として挙げられている。

- **[Xcode 26.3 の MCP とは何か — なぜ必要で、どう使うのか。7 つの Skills で実践する（zenn.dev/dsgarage）](https://zenn.dev/dsgarage/articles/xcode-263-mcp-skills)** — Xcode 26.3 の xcrun mcpbridge が提供する 7 つの主要機能（SwiftUI Preview キャプチャ・Swift Snippet 実行・Build Diagnostics・Apple Docs 検索・WWDC セマンティック検索・File Operations・Simulator UI）について、各機能に対応した Claude Code Skill の実装例を交えて解説。Skills は `~/.claude/skills/` に置いてプロジェクト横断で使えるため、毎回プロンプトに書かなくて済む。

## 🌐 海外コミュニティ / Tips

- **[Xcode 26.3 + Claude Agent: Model Swapping, MCP, Skills, and Adaptive Configuration（fatbobman.com）](https://fatbobman.com/en/posts/xcode-263-claude/)** — Swift/SwiftUI 開発者向けに人気のブログ Fatbobman が Xcode 26.3 × Claude Agent の実務設定を英語で解説。複数モデルの切り替え戦略（重い設計タスクには Opus、ビルドエラー修正には Haiku）、MCP サーバーの project スコープ設定、Xcode ビルドキャッシュと Claude のファイル読み取りを同期させる Adaptive Configuration（`CLAUDE.md` に derived data パスを明示する手法）の 3 点が実践的。MCP の project/user スコープ混在時の挙動についても触れている。

## 💡 今日のおすすめ実践 Tip

**Haiku 5.5 をサブエージェントに使って iOS ビルドエラー修正ループを高速化する**

Haiku 5.5 は 1M コンテキストで価格が Sonnet 比で大幅に安い。ビルドエラーの修正サイクルという繰り返しが多い作業を Haiku に委ねると、コスト・速度の両面で効果がある。

**基本的な使い方:**

```bash
# Haiku 5.5 でビルドエラーのみを修正させる（大きな設計変更はさせない）
claude --model claude-haiku-5-5 \
  "xcodebuild build -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 16' 2>&1 | \
   tail -50 を読んでエラーを修正してください。Swift ファイルのみ変更可。project.pbxproj は触らない。"
```

**CLAUDE.md に書いておくとさらに便利:**

```markdown
## モデル使い分け方針
- 設計・アーキテクチャ判断: デフォルトモデル（Sonnet/Opus）
- ビルドエラー修正・単純なリファクタリング: `claude --model claude-haiku-5-5`
- 長大なログ解析（10,000 行超の xcodebuild ログ等）: Haiku 5.5（1M コンテキストを活かす）

## ビルドコマンド（Haiku 用ショートカット）
SCHEME=MyApp
SIMULATOR="platform=iOS Simulator,name=iPhone 16,OS=latest"
BUILD_CMD=xcodebuild build -scheme $SCHEME -destination '$SIMULATOR' 2>&1
```

**注意点:**
- Haiku 5.5 は thinking が常時 on のため、単純な 1 行修正でも思考トークンが発生し思ったより遅いことがある。軽量タスクでは effort を `low` に下げると良い: `--effort low`
- 100K トークンを超えるリクエストは 5 倍の価格になるため、ログは `tail -50` で絞るか、`grep error: build.log` でエラー行だけ渡すようにするとコスト削減になる
- iOS の `.pbxproj` や `*.xcconfig` は Haiku に触らせないよう CLAUDE.md に明記しておくこと（設計意図が伝わりにくいファイルを変更されると壊れやすい）
