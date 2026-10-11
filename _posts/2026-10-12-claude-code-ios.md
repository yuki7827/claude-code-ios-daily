---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-12"
date: 2026-10-12 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-12 時点で v2.1.296 が最新。10-11 キャッチアップ済み）

## 🛠 GitHub の動き

- **[Issue #95191（2026-09-17 作成 / 2026-10-11 CLOSED）— iOS Simulator 入力アクションがサンドボックスエラー "Failed to initialize IO ports for device IO client" で失敗](https://github.com/anthropics/claude-code/issues/95191)** — Claude Code デスクトップアプリの iOS Simulator パネルから tap / swipe / type 等の入力アクションを送ると、サンドボックスの IO ポート初期化に失敗して操作全体がブロックされていた問題が 2026-10-11 にクローズ（修正済み）。Device Hub（`com.apple.dt.Devices`）経由でシミュレーターを操作する UI 自動化フローに影響していた。最新版へのアップデートで解消する。ラベル: `bug`, `has repro`, `platform:macos`, `area:desktop`, `area:sandbox`。

- **[Issue #96941（2026-09-25 作成 / OPEN）— iOS Simulator パネル・ツールが iPhone Duo の折り畳み状態・向きを無視（座標が古い 466×678 に固定、screenshot captureFailed）](https://github.com/anthropics/claude-code/issues/96941)** — Apple の折り畳みスマートフォン「iPhone Duo」を使っている場合、折り畳み状態（fold / unfold）や画面向きの変更が Claude Code の iOS Simulator コントロールツールに反映されず、旧解像度のままで座標がずれて tap が外れたり、スクリーンショット取得が `captureFailed` になるバグ。iPhone Duo 向けアダプティブレイアウト（`UITraitCollection` の横幅変化やマルチウィンドウ対応）の UI テストを Claude Code で自動化しているケースに影響する。現在 OPEN でトリアージ中。ラベル: `bug`, `platform:macos`, `area:tools`。

## 📝 日本語コミュニティ

- **[AIにiOSアプリを実装させるとき、触らせてはいけないファイルと守り方（Qiita: yasu-ya、2026-10-02）](https://qiita.com/yasu-ya/items/c5b28fe4781d96723b20)** — GitHub Issue → Claude Code 実装 → ビルド/テスト/E2E 検証 → PR 作成の自動ループ実践シリーズ第 3 弾。`.pbxproj`・`Package.resolved`・コードサイニング関連ファイル（`*.xcconfig`、`ExportOptions.plist`）・`GoogleService-Info.plist` 等を Claude Code から守る方法を具体的に整理している。`CLAUDE.md` の `do not modify` 指定と `.claude/settings.json` の `deny` リストを組み合わせる二重防護の設計が実践的。Xcode 26.6 / GitHub Actions / Claude Code で確認済み。

- **[Xcode MCP × Claude Code プラグインで、iOS ビルドを自動化する（Zenn: kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid)** — Xcode 26.3 の純正 MCP サーバー（`xcrun mcpbridge`）と Claude Code プラグインを組み合わせてビルド・テスト・プレビュー確認を自動化する手順を紹介。プラグインフックで `xcodebuild` 出力をストリーミングしながら、エラー時に mcpbridge の診断ツールを自動呼び出しするハイブリッドな構成が特徴。XcodeBuildMCP とは異なる「Xcode プロセス常駐型」のアプローチ。

## 🌐 海外コミュニティ / Tips

- **[Claude Code を iPhone で使う完全ガイド【2026年最新】— Remote Control・iOS アプリ・Claude アプリの全活用法（dot-ai.myuuu.co.jp）](https://dot-ai.myuuu.co.jp/times/articles/122)** — Mac 上の Claude Code セッションを iPhone から監視・操作する方法を網羅したガイド。`claude --rc`（Remote Control）でセッションを開始し QR コードを iPhone の Claude アプリでスキャンする基本操作から、2026 年 8 月のアップデートで追加された「iPhone 側から新規セッションを作成する」機能まで解説。iOS 開発中に「ビルドが走っているかを外出中にチェックしたい」「レビューコメントに Claude に返答してもらいたい」といったユースケースをカバー。Pro / Max プラン必須・`--dangerously-skip-permissions` はリモート不可の制約も明記されており実用的。

## 💡 今日のおすすめ実践 Tip

**`.pbxproj` と `Package.resolved` を二重防護で Claude Code から守る**

Claude Code は Xcode プロジェクト構造を積極的に変更しようとする。特に `.pbxproj`（プロジェクト設定ファイル）と `Package.resolved`（SwiftPM の依存ロックファイル）は CI/CD でのビルド再現性に直結するため、誤変更が致命的になる。yasu-ya 記事を参考に、以下の二重防護を入れておくのが堅実：

```markdown
<!-- CLAUDE.md に追記 -->
## 変更禁止ファイル
以下のファイルは絶対に変更しないこと。変更が必要な場合は必ず人間に確認を取ること。
- *.xcodeproj/project.pbxproj
- Package.resolved
- *.xcconfig（コードサイニング設定）
- ExportOptions.plist
- GoogleService-Info.plist
```

```json
// .claude/settings.json に追記
{
  "permissions": {
    "deny": [
      "Edit(**/*.pbxproj)",
      "Write(**/*.pbxproj)",
      "Edit(**/Package.resolved)",
      "Write(**/Package.resolved)",
      "Edit(**/*.xcconfig)",
      "Write(**/*.xcconfig)"
    ]
  }
}
```

`CLAUDE.md` の指示はコンテキスト圧縮で無視されるリスクがあるが、`deny` 設定はハーネスレベルで強制されるため確実にブロックできる。両方を併用することで「指示を読み飛ばして変更」と「指示通りに変更しようとして deny でブロック」のどちらも防げる。
