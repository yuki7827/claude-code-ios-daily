---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-23"
date: 2026-07-23 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code Desktop に iOS シミュレーターパネルが搭載（公開ベータ、2026-07-21）](https://www.macrumors.com/2026/07/21/claude-code-ios-simulator/) — Claude Desktop v1.24012.0 以降で、会話ウィンドウの横に iOS シミュレーターパネルが追加された。「ビルドして動かして確認して」とひとこと伝えるだけで、Claude がシミュレーターを起動・操作・スクリーンショット検証・コード修正のループを回す。**最大のポイントは「computer use 不要」**：macOS アクセシビリティ許可・画面録画許可を求めずシミュレーターを直接制御するため、プロダクションマシンにセンシティブな権限を与えずに済む。ただしローカルセッション限定（クラウドリモートセッション・iPhone からの Remote Control セッションには非対応）、かつ Xcode + iOS プラットフォームのインストールが必要（詳細は Tips セクション参照）。

## 🛠 GitHub の動き

- [Issue #80295 — Claude iOS アプリがバックグラウンド時に MCP 承認プロンプトをサイレントドロップ（OPEN / 2026-07-22）](https://github.com/anthropics/claude-code/issues/80295) — カスタムリモート MCP コネクター（HTTP transport、OAuth 認証）を設定した環境で、アプリがバックグラウンドに移った後にモデルがツール呼び出しの承認を求めると、iOS プッシュ通知は届くが、タップしてアプリに戻っても承認ダイアログが表示されないまま処理が止まるバグ。前日の Issue #79711（Mobile Safari での `-32003` エラー）とは別の問題で、「承認ダイアログが来ない＝見えないうちに承認が落ちている」という挙動。XcodeBuildMCP の長時間ビルドを iPhone の Remote Control でモニタリングし、途中で承認操作を行うワークフローに直接影響する。暫定回避：承認が必要なツールは Mac のデスクトップアプリを前景にしたまま実行する。

- [Issue #80288 — iOS シミュレーターパネルが Xcode 27 / macOS 27 環境で完全に動作不能（2 バグ）（OPEN / 2026-07-22）](https://github.com/anthropics/claude-code/issues/80288) — 今回追加された組み込みシミュレーターパネルが、Xcode 27 + macOS 27 環境では 2 つの独立したバグにより使用不能。**Bug 1（workaround あり）**: `attach` が即時失敗するフレームワークパスのハードコード問題（Xcode 26 以前のパスを参照しており、`xcode-select -s /path/to/Xcode27.app` か `XCODE_DEVELOPER_DIR` 環境変数で回避可能）。**Bug 2（workaround なし）**: `claude-ios-sim` ヘルパーが Metal 4 / CoreImage 初期化時に SIGABRT でクラッシュし、パネルが「Attach a simulator」プレースホルダーから先に進まない。M4 Pro + Metal 4 の組み合わせで再現が確認されており、修正を Anthropic に待つしかない状態。Xcode 26 + macOS 26 環境では問題ないため、Xcode 27 beta への移行は公式修正まで待つことを推奨。

## 📝 日本語コミュニティ

- [Claude Code に iOS アプリの E2E テストまでやらせるようにした（iOSシミュレータ + AXe）（Qiita / peka2）](https://qiita.com/peka2/items/9ce150b3b480516fc16e) — 組み込みシミュレーターパネルが発表される前から XcodeBuildMCP + AXe CLI（Accessibility Inspector CLI）を使って Claude Code に E2E テストを自走させていた実践レポート。`xcrun simctl` でビルド・インストール・起動、AXe でアクセシビリティツリーからボタン特定・タップ、スクリーンショットで期待 UI を確認、という一連のフローを CLAUDE.md に記述して Claude Code に丸投げする構成。アクセシビリティ属性がない View への対処法や、tap 座標のキャリブレーション方法など実務寄りの Tips が充実。

- [Claude Code にシミュレータを渡したら AI が自分でタップしてスクショで検証し始めた（Qiita / shimo4228）](https://qiita.com/shimo4228/items/3c6106d5407deea0cbf4) — XcodeBuildMCP の `ui_tap` / `screenshot` ツールを使い、Claude Code がシミュレーター上でボタンをタップ → スクリーンショット → 意図した画面に遷移したか自己判定 → 問題があればコード修正、のループを自律実行する様子をスクリーンキャプチャ付きで解説。「コードを書かせるだけでなく、動作確認まで AI に任せる」という発想の転換を実証した記事として、組み込みシミュレーターパネル登場の前夜に公開されたことで注目を集めた（Zenn 英語版: [Letting Claude Code Drive the Simulator](https://zenn.dev/shimo4228/articles/xcodebuildmcp-ios-verification)）。

## 🌐 海外コミュニティ / Tips

- [Claude Code brings live iOS app testing into its Mac app（9to5Mac / 2026-07-21）](https://9to5mac.com/2026/07/21/claude-code-brings-live-ios-app-testing-into-its-mac-app/) — 9to5Mac による iOS シミュレーターパネル搭載の速報。「ビルド・実行・インタラクション・イテレーションをシミュレーターで繰り返すフルループが、Mac から離れることなく完結する」という開発者体験の変化を重点的にカバー。`Claude Code Desktop v1.24012.0` 以降でアップデートを確認する手順も掲載。

- [Claude Code Adds iOS Simulator Support on Mac（MacObserver / 2026-07-21）](https://www.macobserver.com/news/claude-code-adds-ios-simulator-support-on-mac/) — MacObserver の記事では「computer use を使わない制御（no Accessibility・no Screen Recording）」という設計判断の意義を特に強調。「従来の computer use ベースの自動化では macOS が要求するスクリーン録画許可を CI 環境やプロダクションマシンに付与する必要があり、企業のセキュリティポリシーにひっかかりやすかった。今回の方式はその問題を回避する」という文脈で、エンタープライズ iOS 開発チームへの影響を分析している。

## 💡 今日のおすすめ実践 Tip

**「組み込みシミュレーターパネル vs XcodeBuildMCP — 使い分けの基準と共存設定」**

Claude Code Desktop v1.24012.0 で組み込みの iOS シミュレーターパネルが使えるようになりましたが、既存の XcodeBuildMCP 環境との関係を整理しておくと迷わずに済みます。

**機能比較**

| 機能 | 組み込みパネル | XcodeBuildMCP |
|---|---|---|
| 動作環境 | ローカルセッション限定 | ローカル・リモート両対応 |
| 必要な権限 | アクセシビリティ/録画許可 **不要** | なし（`xcodebuild` 権限のみ） |
| ビルド実行 | Xcode / `xcodebuild` を呼び出し | 専用ツール（`BuildProject` 等）で制御 |
| UI 操作 | 組み込みツールでシミュレーター直接制御 | `ui_tap` / `ui_swipe` / `screenshot` |
| Xcode 27 対応 | Issue #80288 で不具合あり（修正待ち） | 問題なし（`xcodebuild` 直接呼び出し） |
| Remote Control（iPhone）| 非対応（ローカルのみ） | 対応 |

**推奨使い分け**

```
ローカル Mac 開発（Xcode 26 環境）
  → 組み込みパネル を試す。権限設定が不要で最もシンプル。

Xcode 27 / macOS 27 beta 環境
  → Issue #80288 修正まで XcodeBuildMCP を継続使用。

iPhone（Remote Control）からビルドをモニタリングしたい
  → XcodeBuildMCP 一択。組み込みパネルはリモートセッション不可。

CI 相当の自動ビルド・テスト（バックグラウンドエージェント）
  → XcodeBuildMCP（claude agents でバックグラウンド化可能）。
     組み込みパネルは対話型セッション向け。
```

**両方を共存させる `.claude/settings.json` 例**

```json
{
  "mcpServers": {
    "XcodeBuildMCP": {
      "command": "npx",
      "args": ["-y", "xcodebuildmcp@latest"]
    }
  },
  "sandbox": {
    "filesystem": {
      "disabled": true
    }
  }
}
```

XcodeBuildMCP を MCP として維持しつつ、組み込みパネルは Claude Desktop が自動的に有効化するため、追加設定なしで両方を使い分けられます。セッション開始時に「今日はローカルで UI 確認したい → 組み込みパネル」「CI 的に動かしたい → XcodeBuildMCP」と使うツールを指示するだけで切り替えられます。
