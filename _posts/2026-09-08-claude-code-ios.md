---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-08"
date: 2026-09-08 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-09-08 時点の最新版は v2.1.263 / 2026-09-06 リリース。昨日の記事参照）

## 🛠 GitHub の動き

- **[Issue #92442 — iOS Simulator ツール: macOS 27 beta で claude-ios-sim が Metal/CoreImage 初期化のたびに 100% クラッシュ（anthropics/claude-code / OPEN 2026-09-06）](https://github.com/anthropics/claude-code/issues/92442)** — これまで報告されていた iOS Simulator パネルの macOS 27 beta 互換性問題（Issue #80177・#90707 系）の最新報告。`-[_MTLDevice recordBinaryArchiveUsage:]` で nil 配列が渡され `claude-ios-sim` ヘルパーが 100% の再現率でクラッシュすることが改めて確認されている。詳細なスタックトレースが提供され、修正の優先度がさらに上がっている状況。macOS 27 beta 環境では引き続き XcodeBuildMCP の `get_sim_screenshot` / `launch_sim` などを代替手段として利用することを推奨。

- **[Issue #81864 — iOS Simulator パネルがホスト再起動後に黒画面になり復帰しない問題 → CLOSED（anthropics/claude-code / 2026-09-05 解決）](https://github.com/anthropics/claude-code/issues/81864)** — macOS を再起動した後 iOS Simulator パネルが黒画面のままになり、シミュレーター本体が正常でも復帰しなかった問題が修正済み（2026-09-05 クローズ）。最新版の Claude Code Desktop に更新することで解消する。

- **[Issue #453 — Xcode 27 beta: AXe が SimulatorKit を PrivateFrameworks 以下で探すため UI 自動化が失敗し続ける（getsentry/XcodeBuildMCP / OPEN）](https://github.com/getsentry/XcodeBuildMCP/issues/453)** — Xcode 27 で `SimulatorKit.framework` の配置場所が `Contents/Developer/Library/PrivateFrameworks/` から `Contents/SharedFrameworks/` に変更されたことで、XcodeBuildMCP にバンドルされた AXe がフレームワークを見つけられず、UI 自動化（`snapshot_ui` / 要素タップなど）が実行時にエラーとなる問題。Issue #446 の続報で、Xcode 27 正式リリース前の対応が急務となっている。暫定回避策は Xcode 26.x 系で UI 自動化を実行すること。

- **[Issue #446 — \[Bug\]: AXe が Xcode 27 で失敗する — SimulatorKit.framework が Contents/SharedFrameworks に移動（getsentry/XcodeBuildMCP / OPEN）](https://github.com/getsentry/XcodeBuildMCP/issues/446)** — 上記 #453 の原因を最初に報告した Issue。Xcode 27 でのフレームワークパス変更に起因しており、影響を受けるのはアクセシビリティ階層キャプチャを使う `ui-automation/*` ツール群全般。ビルド・テスト実行・スクリーンショット取得（AXe を使わない系）は引き続き正常に動作するため、UI 自動化機能のみ Xcode 26.x 環境へのフォールバックで対応可能。

## 📝 日本語コミュニティ

- [Claude Code から Xcode が提供する MCP に接続する方法（Zenn / pepabo）](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) — Apple の `xcrun mcpbridge`（Xcode 26.3 以降）を Claude Code から利用するセットアップ手順を解説した記事。ペパボ社エンジニアによる実践ガイドで、ビルド・テスト・SwiftUI Preview レンダリング・Apple ドキュメント検索を MCP 経由で Claude Code から操作する設定方法が解説されている。

- [Xcode MCP × Claude Code プラグインで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Xcode の公式 MCP と Claude Code プラグインを組み合わせて SwiftUI Preview レンダリング・Apple ドキュメント検索・各種ビルドタスクをハイブリッドで自動化する構成を解説した記事。Apple 公式 MCP（20 ツール）と XcodeBuildMCP の役割分担の考え方が整理されている。

- [Automating iOS App Testing with Claude Code and XcodeBuildMCP（Zenn / shimo4228）](https://zenn.dev/shimo4228/articles/xcodebuildmcp-ios-verification) — Claude Code と XcodeBuildMCP を使った iOS アプリテスト自動化を解説した英語記事（Zenn に掲載）。Apple 公式 MCP と XcodeBuildMCP の比較（IDE 統合の深さ vs ヘッドレス実行の柔軟性）が整理されており、CI 環境と開発機の使い分けの判断軸として参考になる。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP と Apple 公式 Xcode MCP の 2 サーバーを同時に Claude Code へ接続し、「ビルド・テスト実行（XcodeBuildMCP）」と「SwiftUI Preview 確認・Apple ドキュメント検索（Apple MCP）」を相互補完させる構成を紹介したブログ記事。「2 つのサーバーを同時に使うことで Claude Code が実質的な iOS フルスタックビルドシステムになった」という実体験がまとめられており、設定例も掲載されている。

## 💡 今日のおすすめ実践 Tip

**「Xcode 27 beta 環境での XcodeBuildMCP 使用時の注意点と分岐戦略」**

現在 Issue #446・#453 で報告されているとおり、Xcode 27 beta 環境では XcodeBuildMCP の **UI 自動化（`ui-automation/*` ツール群）が動作しない**状況が続いています。一方、ビルド・テスト・スクリーンショット（AXe 非依存）は正常です。

**環境別の推奨構成**

| 環境 | 推奨設定 |
|------|---------|
| Xcode 26.x（安定版） | XcodeBuildMCP フル機能（UI 自動化を含む） |
| Xcode 27 beta | XcodeBuildMCP でビルド・テストのみ使用、UI 自動化は Xcode 26.x にフォールバック |
| macOS 27 beta | Claude Code Desktop の iOS Simulator パネルは非推奨、XcodeBuildMCP の `get_sim_screenshot` を代替手段として使用 |

**CLAUDE.md に記載しておくと便利な注記**

```markdown
## Xcode バージョンに関する注意（2026-09 現在）

- ビルド・テスト実行: XcodeBuildMCP v2.7.0 を使用（xcodebuild ラッパー）
- UI 自動化（`ui-automation/tap` 等）: **Xcode 27 beta では AXe がクラッシュするため使用不可**
  - 対応するまでは Xcode 26.x 安定版の環境を使うか、
    Apple 公式 MCP（xcrun mcpbridge）の SwiftUI Preview で代替する
- macOS 27 beta では Claude Code Desktop の iOS Simulator パネルが動作しない場合がある
  → `get_sim_screenshot` または `launch_sim` ツールを CLI から呼ぶこと
```

Xcode 27 が正式リリースされれば XcodeBuildMCP 側も対応アップデートが見込まれますが、beta 期間中はこの分岐戦略で乗り切るのが現実的です。
