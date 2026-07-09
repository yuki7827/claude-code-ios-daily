---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-10"
date: 2026-07-10 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.205（2026-07-08）— Auto モード安全強化・JSON Schema 修正・エージェントビュー改善](https://code.claude.com/docs/en/changelog) — v2.1.203/v2.1.204 に続く連続リリース。iOS 開発者に影響する主な変更点は 4 つ。① **Auto モードに「未解決変数への `rm -rf` は事前確認」ルールを追加** — `rm -rf $TMPDIR/*` のようなシェル展開前の変数を含むコマンドに Claude が自律実行をためらうようになり、XcodeBuildMCP 連携の自律ビルドループで誤ったディレクトリを削除するリスクが減少。② **`--max-turns` 上限でのメッセージ消失バグを修正** — 上限に達したターンで送信中だったメッセージがサイレントに失われる問題を解消。長時間の自律 iOS ビルドタスクで途中指示が無視されていた原因の一つ。③ **バックグラウンドエージェントのステータス表示改善** — エージェント一覧行に色付きステータス語と分類器が自動生成したヘッドライン（「Fixing Swift concurrency errors in ContentView.swift」等）が表示されるようになり、複数のビルドエージェントを並行実行中の状況把握が格段に楽になった。また PR の作成・マージ・push 操作をエージェントが実行した場合、セッション行にその PR へのリンクが表示される。④ **自動更新ダウンロードをディスクへのストリーム書き込みに変更** — ピーク時のメモリ使用量を約 400 MB 削減。CI コンテナで Claude Code を動かしている場合のメモリ逼迫緩和に効く。

## 🛠 GitHub の動き

- [twostraws/swift-agent-skills — Paul Hudson による Apple プラットフォーム向け Agent Skills のキュレーション集](https://github.com/twostraws/swift-agent-skills) — "Hacking with Swift" の Paul Hudson が公開した、Swift・Apple プラットフォーム開発向け Agent Skills のディレクトリリポジトリ。SwiftUI（4 スキル）・SwiftData（2）・Swift Concurrency（3）・Swift Testing（3）を軸に、アクセシビリティ・App Intents・App Store・アーキテクチャ・バックグラウンド実行・Core Data・パフォーマンス・セキュリティ・ウィジェット等、30+ カテゴリを網羅。個別スキルは `npx skills add https://github.com/twostraws/SwiftUI-Agent-Skill --skill swiftui-pro` のように claude code スキル CLI で取り込める。元の `SwiftUI-Agent-Skill`（SwiftUI Pro）は 4,300 以上のスターを集めており、本ディレクトリはその発展形。「LLM が実際に間違えやすい点」—— VoiceOver に不可視な Button・非推奨 API の使用・パフォーマンス問題を引き起こすコード ——を集中的に矯正する設計が特徴。`dpearson2699/swift-ios-skills`（07-06 紹介）が iOS 26+API 全般の 84 スキルをカバーするのに対し、本ディレクトリは Swift コア言語・フレームワーク別に細粒度で整理されており用途に応じて選択できる。

- [superagents-lab/xcode27-skills — Xcode 27 に同梱の Apple 公式 Agent Skills をエクスポート・再配布](https://github.com/superagents-lab/xcode27-skills) — Xcode 27 developer beta（WWDC 2026 発表）には `xcrun agent skills export` で取り出せる Apple 製 Agent Skills が 7 本同梱されており、それを Claude Code・Codex・Cursor など任意の AI コーディングエージェントで使えるように再配布したリポジトリ。内訳：**`swiftui-specialist`**（SwiftUI のベストプラクティス全般）・**`swiftui-whats-new-27`**（iOS 27 新 SwiftUI API）・**`uikit-app-modernization`**（UIKit → SwiftUI / 新 API 移行）・**`test-modernizer`**（XCTest → Swift Testing 移行）・**`c-bounds-safety`**（C コード境界チェック）・**`audit-xcode-security-settings`**（Xcode セキュリティ設定監査）・**`device-interaction`**（実機テスト自動化）の 7 スキル。`npx skills add superagents-lab/xcode27-skills` で一括インストールでき、Claude Code 起動時に自動ロードされる。なお Xcode 27 の developer beta は Apple Silicon Mac + macOS Tahoe 26.4 以上の環境が必要で、正式リリースは 2026 年 9 月予定。

## 📝 日本語コミュニティ

- [Claude Code で 5 日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか（Zenn / seeda_yuto）](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days) — Swift 経験ゼロの著者が全コードを Claude Code に任せ、5 日間で iOS アプリの App Store 審査を通過した実録。「何を Claude に任せたか」「何は人間が判断しなければならなかったか」をタスク単位で整理した構成が実務的で参考になる。XcodeBuildMCP 連携のビルドループ・SwiftUI ビュー実装・クラッシュログ解析は高精度で自律実行できた一方、プロビジョニングプロファイルの初期設定・App Store スクリーンショットのデバイス別調整・審査対応文書の作成は「Claude が間違いやすいため人間が最終確認すべき」と結論付けている。

- [iPhone だけで iOS アプリ開発するワークフロー（Zenn / oikon）](https://zenn.dev/oikon/articles/dev-from-mobile) — Mac なし・iPhone のみで iOS アプリを開発して App Store に公開するユニークなワークフローを解説した記事。VPS 上に Claude Code CLI をヘッドレスホストし、iPhone の SSH クライアント（またはモバイルウェブ）から操作する構成。コード編集・`xcodebuild` ビルドも VPS 経由で実行し、アーカイブと App Store 提出には App Store Connect MCP を使う。「Mac を持っていない・もしくはカフェで開発したい」という状況を想定した実験的なフローで、Claude Code のバックグラウンドエージェント機能と組み合わせることで「指示を投げてから iPhone で他の作業をして後で確認する」スタイルが実現できることを示している。

## 🌐 海外コミュニティ / Tips

- [Using Xcode 27's Agent Skills in Claude, Codex, and Cursor（SwiftLee / Antoine van der Lee）](https://www.avanderlee.com/ai-development/using-xcode-27s-agent-skills-in-claude-codex-and-cursor/) — iOS/Swift の著名ブログ SwiftLee が Xcode 27 の Agent Skills を Claude Code・Codex・Cursor に取り込む方法を丁寧に解説した記事。`xcrun agent skills export` で Xcode 27 のスキルを .claude/skills/ にエクスポートする手順と、`/clear` または Claude Code の再起動でスキルを認識させるステップを図解。Apple が自社の iOS 開発ベストプラクティスを Agent Skills として正式に提供したことで、「Apple エンジニアが書いた SwiftUI のベストプラクティスと同じ知識を Claude が参照しながらコードを生成できる」という点に意義があるとしている。Xcode 27 developer beta ユーザーへの推奨アクションとしてまとまっており、実際にスキル取り込み後の `swiftui-specialist` スキルが旧来の `@StateObject` / `ObservableObject` 使用を自律的に `@Observable` に置き換えた例を示している。

- [WWDC 2026 — Xcode 27 Ships With Apple's Own Agent Skills（DEV.to / arshtechpro）](https://dev.to/arshtechpro/wwdc-2026-xcode-27-ships-with-apples-own-agent-skills-what-they-are-and-how-to-use-them-3g2) — 7 スキルの内容と背景を詳述した英語解説記事。`test-modernizer` は XCTest → Swift Testing 移行を、`uikit-app-modernization` は UIKit の古い API を SwiftUI・最新 Foundation API へ自動移行するスキルと紹介。`audit-xcode-security-settings` では Xcode プロジェクトの `ENABLE_BITCODE`・App Sandbox 設定・コード署名ポリシーを Claude が確認して修正提案するユースケースを解説しており、App Store 審査前のセキュリティチェックリストとして活用できる。

## 💡 今日のおすすめ実践 Tip

**「`xcrun agent skills export` で Xcode 27 の Apple 公式スキルを Claude Code に組み込む」**

Xcode 27 developer beta では、Apple が自社開発した 7 本の Agent Skills がツールチェーンに同梱されています。1 コマンドで Claude Code に取り込める手順を示します。

**1. Xcode 27 developer beta の前提確認**

```bash
# macOS Tahoe 26.4 以上 + Apple Silicon が必要
sw_vers
# → ProductVersion: 26.4 以上を確認

xcodebuild -version
# → Xcode 27.x ... であること
```

**2. スキルのエクスポート（プロジェクトローカル）**

```bash
# プロジェクトルートで実行
cd /path/to/MyApp
xcrun agent skills export
# → .claude/skills/ 以下に 7 つのスキルフォルダが生成される
```

**3. Claude Code を再起動してスキルをロード**

```bash
# 既存のセッションがある場合は /clear または再起動
claude
# セッション内で確認
/skills list
# swiftui-specialist, swiftui-whats-new-27 などが表示されれば OK
```

**4. superagents-lab 経由で Xcode 27 スキルを先取りする（beta 環境がない場合）**

```bash
# Xcode 27 beta 環境がなくても GitHub から取り込み可能
npx skills add superagents-lab/xcode27-skills

# または個別スキルだけ
npx skills add superagents-lab/xcode27-skills --skill swiftui-specialist
npx skills add superagents-lab/xcode27-skills --skill test-modernizer
```

**各スキルの活用シーン**

| スキル | おすすめ用途 |
|--------|------------|
| `swiftui-specialist` | SwiftUI コードレビュー・新機能実装時の標準化 |
| `swiftui-whats-new-27` | iOS 27 対応コードを書くとき（新 API の正しい使い方） |
| `uikit-app-modernization` | 既存 UIKit プロジェクトの段階的 SwiftUI 移行 |
| `test-modernizer` | XCTest コードを Swift Testing に一括変換 |
| `audit-xcode-security-settings` | App Store 提出前のセキュリティ設定確認 |
| `device-interaction` | 実機 UI テスト自動化スクリプトの生成 |
| `c-bounds-safety` | Objective-C / C レイヤーの境界チェック追加 |

`swiftui-specialist` + `test-modernizer` の 2 本を先に取り込み、`/code-review` スキルと組み合わせると「Apple が推奨する書き方かつ Swift Testing 対応済みかどうか」を Claude が自律チェックするコードレビューフローが構築できます。
