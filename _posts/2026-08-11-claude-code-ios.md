---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-11"
date: 2026-08-11 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.226 が引き続き最新（2026-08-11 時点）**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— 2026-08-08 リリースの v2.1.226 から新バージョンは出ていない。前日（08-10）の記事で詳細を既報。iOS 関連では Self-hosted environments（v2.1.224）、Remote Control / MCP OAuth 修正（v2.1.225）、長時間セッションのフィーチャーフラグ修正（v2.1.226）が最近の主な変更点。

## 🛠 GitHub の動き

- [Issue #84943 — iOS Simulator パネルが「Xcode is installed but not selected」を返す — Xcode 26.6 / macOS 26.5 上で xcode-select は正しく設定済みなのに（OPEN / 2026-08-07）](https://github.com/anthropics/claude-code/issues/84943) — Claude Desktop の iOS Simulator パネルが `attach` 時に「`Xcode is installed but not selected. Run sudo xcode-select -s /Applications/Xcode.app/Contents/Developer`」を返し続ける問題。**macOS 27 beta ではなく、正式リリースの Xcode 26.6 / macOS 26.5.2 環境でも再現する**点が新しい。`xcode-select -p`・`env -i /usr/bin/xcode-select -p`・`xcrun --find simctl` はすべて正常値を返し、`xcodebuild` でのビルド自体も MCP の `build` アクション経由も成功する。報告者は、macOS 26.x では `/var/db/xcode_select_link` が存在しないため、パネルのプリフライトチェックがこのレガシーパスを確認して誤検知している可能性を指摘。XcodeBuildMCP など外部 MCP でのビルドは引き続き動作するため、**回避策は「iOS Simulator パネルの代わりに XcodeBuildMCP の build / launch / screenshot ツールを使う」**こと。

- [Issue #84905 — Remote Control: Agent Teams のサブエージェント権限プロンプトがモバイル（iOS）に転送されない — #24694 から未修正（OPEN / 2026-08-07）](https://github.com/anthropics/claude-code/issues/84905) — `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` で Agent Teams を起動した状態で Remote Control に iPhone から接続すると、**チームリード（メインペイン）の権限プロンプトは iOS 側で承認・拒否できるが、サブエージェント（チームメイト）の権限プロンプトはローカル端末側にのみ表示されて iOS からは操作不能**になる問題。報告者は 2026-03 に #30012 として初報、#24694 の重複としてクローズされたが #24694 自体も修正なくクローズされており、v2.1.223（2026-08-05）でも再現すると再提起。Agent Teams を組んで「Mac を離席中に iPhone から進捗管理」する運用が現状では機能しない構造的欠陥。**回避策: 権限が必要なステップだけ手元のターミナルで処理するか、`.claude/settings.json` に `bypassPermissions` を設定して特定ツールの承認を省略する（リスク承知の上で）。**

## 📝 日本語コミュニティ

- [Claude Code から Xcode が提供する MCP に接続する方法（Zenn / pepabo / 2026-02-06）](https://zenn.dev/pepabo/articles/4c0d1019ac1f7d) — `xcrun mcpbridge` 経由で Xcode 26.3 公式 MCP サーバーに Claude Code から接続する手順を解説した記事。SwiftUI Preview のスクリーンショット取得・ドキュメント検索・ビルドの各 MCP ツールの設定方法と、XcodeBuildMCP との使い分け指針が書かれている。Xcode MCP を使い始める際の最初の一歩として参照しやすい内容。直近 5 投稿での掲載なし。

- [Xcode MCP × Claude Code プラグインで、iOS ビルドを自動化する（Zenn / kyoichi / 2026-02-11）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Xcode 26.3 RC が提供する公式 MCP サーバーと Claude Code の Plugin 機能を組み合わせ、ビルド・テスト・SwiftUI Preview レンダリングを一連のワークフローとして自動化する手順を解説。`claude plugin add xcode-mcp` によるインストールと、CLAUDE.md への組み込み例が具体的で実践しやすい。直近 5 投稿での掲載なし。

## 🌐 海外コミュニティ / Tips

- [The Complete Guide to Building an iOS App with Claude Code (No Xcode Required)（dev.to / marypiakovski）](https://dev.to/marypiakovski/the-complete-guide-to-building-an-ios-app-with-claude-code-no-xcode-required-o5b) — React Native を使い Claude Code でクラウドビルドパイプラインを組むことで、ローカルに Xcode をインストールせずに iOS アプリをビルド・提出する手法を解説。XcodeBuildMCP が必要とする macOS 環境の用意が難しい場合のオルタナティブとして参考になる。クロスプラットフォーム開発者や CI 中心のチームに向いたアプローチ。直近 5 投稿での掲載なし。

## 💡 今日のおすすめ実践 Tip

**「Issue #84943 対策: iOS Simulator パネルが "Xcode is installed but not selected" になったとき — Xcode 26.6 安定版ユーザー向けの回避フロー」**

macOS 27 beta だけでなく、**正式リリースの Xcode 26.6 / macOS 26.5 環境でも** iOS Simulator パネルが `attach` を拒否するケースが Issue #84943 として報告されています。`xcode-select -p` や `xcrun simctl` が正常でも発生するため、「コマンドを打ったのに解消しない」状況になりやすいバグです。

**診断フロー（ターミナルで順に確認）**

```bash
# 1. クリーン環境での xcode-select の結果を確認
env -i /usr/bin/xcode-select -p
# → /Applications/Xcode.app/Contents/Developer なら OS 側は正常

# 2. レガシーシンボリックリンクの存在を確認
ls /var/db/xcode_select_link 2>&1
# → "No such file or directory" なら macOS 26.x の仕様。パネルの誤検知が原因の可能性大

# 3. MCP の build アクションが正常動作するか確認
xcrun simctl list devices booted
# → デバイスが見えていれば Xcode / simctl 自体は正常
```

**回避策: iOS Simulator パネルを使わず XcodeBuildMCP に切り替える**

Issue が修正されるまでの間、以下の XcodeBuildMCP ツールがパネルの代替機能をすべてカバーします。

| パネルの機能 | XcodeBuildMCP ツール |
|---|---|
| スクリーンショット取得 | `screenshot` |
| UI タップ操作 | `tap`（AXe エンジン） |
| アプリ起動 | `launch_app` |

`.claude/settings.json` に XcodeBuildMCP を登録済みであれば、パネルが使えない環境でも Claude Code からシームレスにシミュレーター操作が可能です。Issue がクローズされたら `attach` アクションに戻してください。

**CLAUDE.md への追記例**

```markdown
## iOS Simulator

- Claude Desktop の iOS Simulator パネルは使用不可（Issue #84943）
- スクリーンショット・タップは XcodeBuildMCP を経由する
  - screenshot: xcrun simctl IO screenshot の代替
  - tap: AXe エンジンでアクセシビリティ識別子を指定
```
