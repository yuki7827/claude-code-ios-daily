---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-09"
date: 2026-05-09 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.133 (May 7)](https://code.claude.com/docs/en/changelog) — `worktree.baseRef` 設定（`fresh` | `head`）を追加。デフォルト `fresh` は `origin/<default>` からブランチを切るが、`head` にするとローカルの未プッシュコミットを新しいワーキングツリーに引き継げる。iOS マルチ機能並行開発時に「別ブランチに切り替えたら WIP が消えた」問題を解消。`$CLAUDE_EFFORT` 環境変数と `effort.level` JSON フィールドがフックに渡されるようになり、effort レベルに応じてビルドログの詳細度を切り替えるフックが書けるように。サブエージェントがスキルを発見できないバグも修正。
- [Claude Code v2.1.136 (May 8)](https://code.claude.com/docs/en/changelog) — `settings.autoMode.hard_deny` を追加。`soft_deny` が AI の再判断を許すのに対し `hard_deny` は無条件でブロック。iOS CI で `git push --force` や `rm -rf DerivedData` を絶対に通したくない操作をルール化できる。VS Code / JetBrains / Agent SDK で `/clear` 後に MCP サーバーが消える不具合を修正。Extended Thinking がツール呼び出し後に redacted thinking ブロックを生成するとき 400 エラーが出るバグも解消。

## 🛠 GitHub の動き

- [XcodeBuildMCP v2.5.0 stable (May 7)](https://github.com/getsentry/XcodeBuildMCP/releases) — 05-04 掲載のベータから正式リリース。**Breaking Change**: レガシー単体ログキャプチャツールを削除、`extraArgs` → `launchArgs` にリネーム。Structured Outputs による JSON 化・プラットフォーム自動検出ウィザード・キーボード操作ツール・tvOS/watchOS/visionOS デバイス対応が安定版として提供開始。
- [XcodeBuildMCP v2.5.1 (May 8)](https://github.com/getsentry/XcodeBuildMCP/releases) — Homebrew / ポータブル macOS インストールで Structured Output スキーマが欠落し MCP クライアントがツールをロードできない致命的バグを修正。`brew upgrade xcodebuildmcp` での更新を推奨。
- [conorluddy/xclaude-plugin](https://github.com/conorluddy/xclaude-plugin) — 「トークンを節約しながら iOS 開発」を主眼に置いた Claude Code プラグイン。Xcode・Simulator・IDB ツールを 8 本のモジュール型 MCP（xc-build / xc-launch / xc-interact / xc-ai-assist 等）に分割し、**一度に 1 本だけ有効化する**ことでコンテキスト消費を最小化。ビルドログ・テスト結果を構造化 JSON にラップするため Claude への入力トークンを大幅に削減できる。`/plugin marketplace add conorluddy/xclaude-plugin` で導入可能。

## 📝 日本語コミュニティ

- [XcodeBuildMCPとClaude CodeでiOSアプリのテストを自動化する ― UI操作とスクリーンショット検証まで](https://zenn.dev/shimo4228/articles/xcodebuildmcp-ios-verification) (shimo4228, Zenn) — ビルド→テスト→シミュレーター起動→UI 操作→スクリーンショット検証をすべて Claude Code が自律実行した実録。608 テストが通過したコード（Cram Mode 実装 418 行）を Claude 自身がスクリーン読み取りで確認。XCUITest が「シナリオ通りに動くか」を検証するのに対し、XcodeBuildMCP 方式は「画面が期待通りの見た目か」を AI が判断する探索的テストとして機能する点が示唆的。
- [Xcode MCP×Claude Codeプラグインで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) / [XcodeBuildMCP×Claude Codeスキルシステムで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) (kyoichi, Zenn) — 2 本連続投稿。前者は Apple 純正 Xcode MCP（xcrun mcpbridge）+ CLI フォールバックのハイブリッド戦略を紹介し、後者はスキームを変更できない・Xcode が起動必須など純正 MCP の制約を踏まえ XcodeBuildMCP をメインに据えた再設計を報告。ビルドログをサブエージェント内に閉じ込めてメインコンテキストを汚さない構成がポイント。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System](https://blakecrosley.com/blog/xcode-mcp-claude-code) (Blake Crosley) — XcodeBuildMCP（82 ツール：ビルド・テスト・シミュレーター・デバイス・LLDB）と Apple 純正 Xcode MCP（20 ツール：ファイル操作・リアルタイム診断・SwiftUI Preview）を組み合わせる構成を解説。前者は Xcode 不要で CI にも使えるビルド系、後者は起動中の Xcode に橋渡しするプレビュー系と役割を明確に分離し、生の build log 解析からネイティブな構造化 JSON へ移行する手順を紹介。
- [UIKit to SwiftUI: 60% Faster Migration with Claude Code](https://medium.com/reversebits/we-migrated-uikit-to-swiftui-60-faster-using-claude-code-heres-how-2026-guide-05f9a995e583) (reverseBits, Medium) — UIKit → SwiftUI 移行を Claude Code で自動化し 60% の工数削減を達成した実践ガイド。ViewController を SwiftUI View に変換しながら既存テストを維持するワークフローと、Claude が理解しやすい CLAUDE.md の UIKit 注釈の書き方を解説。

## 💡 今日のおすすめ実践 Tip

**`autoMode.hard_deny` で iOS CI の誤爆をゼロにする**

v2.1.136 で追加された `hard_deny` は、`soft_deny`（AI が文脈で上書き可能）と異なり **分類スコアに関わらず必ず拒否**されます。iOS 開発の CI パイプラインでは以下のような設定が有効です。

```json
// .claude/settings.json
{
  "autoMode": {
    "hard_deny": [
      "Bash(git push*)",
      "Bash(rm -rf*)",
      "Bash(xcodebuild -destination 'platform=iOS*' archive*)"
    ]
  }
}
```

本番リリース用アーカイブ・TestFlight アップロード・強制プッシュなど「人間が明示的に承認すべき操作」を `hard_deny` に登録しておくと、`--auto-accept` で CI を回してもこれらの操作だけは必ず停止します。チームのローカル開発では `~/.claude/settings.json` に `worktree.baseRef: "head"` も追加しておくと、iOS の機能ブランチをワーキングツリーで分岐させたときに未プッシュの WIP コミットが引き継がれ、別フィーチャーと並行作業しやすくなります。
