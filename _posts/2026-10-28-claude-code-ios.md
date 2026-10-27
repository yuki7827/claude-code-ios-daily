---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-28"
date: 2026-10-28 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.312（2026-10-27）— CLAUDE.md 大ファイルの部分読み取り上書きバグを修正](https://code.claude.com/docs/en/changelog) — 大きな CLAUDE.md を読み途中で打ち切って上書きする問題が修正された（22 件のバグ修正に含む）。iOS プロジェクトで CLAUDE.md に詳細な設計・ルールを記述している場合に有効な修正。フルスクリーンでキューイングしたメッセージをクリックで即送信できる機能、Cloud sessions のルーティン用モデル effort 設定と Bash エディタも追加。
- [v2.1.311（2026-10-26）— `ultrathink` が Anthropic API 経由で xhigh effort として動作するよう変更](https://code.claude.com/docs/en/changelog) — `ultrathink` キーワードを付けたターンが Opus 5.5・Sonnet 5.5・Haiku 5.5・Fable 5.1 に対して xhigh effort で実行されるよう変更（Anthropic API 経由のみ）。複雑な iOS アーキテクチャ設計や難解なバグ分析を依頼する際に `ultrathink` を付けると、より深い推論が期待できる。CLAUDE.md の長いリスト項目で発生していた長時間フリーズも修正（56 件のバグ修正含む）。

## 🛠 GitHub の動き

- [#105373（2026-10-27 open）デスクトップ Code セッションから送った音声ファイルが iOS アプリで「File preview unavailable」になる](https://github.com/anthropics/claude-code/issues/105373) — Claude Code デスクトップセッションで録音・添付した音声ファイルを Claude iOS アプリ側で開こうとすると「File preview unavailable」が表示される問題。先週は正常動作していたとの報告で、iOS アプリの更新後に発生した可能性が高い。Claude iOS アプリから Code セッションの音声添付を確認しているチームは要注意。
- [#104222（2026-10-22 open）iOS アプリが IP 変更後のログイン再認証でサイレントハングする](https://github.com/anthropics/claude-code/issues/104222) — VPN 切り替えやモバイル〜Wi-Fi 切り替え等で IP が変わり claude.ai がログイン再認証を求めた場合に、iOS の Code タブがフリーズして無応答になる問題。エラーメッセージが表示されないため原因に気づきにくい。カフェや移動中に iPhone × Remote Control で Claude Code を操作している場合は同症状に注意。
- [#104094（2026-10-21 open）[FEATURE] Projects ベータ（iOS）：アプリ内でフルのプロジェクト設定を表示したい](https://github.com/anthropics/claude-code/issues/104094) — Claude iOS アプリの Code タブから Projects ベータの全設定（モデル、CLAUDE.md、ツール権限など）を変更できるようにしてほしいというフィーチャーリクエスト。現状は制限されており、PC ブラウザで設定してから iOS アプリで使う必要がある。iOS 開発者に多い「移動中に設定調整したい」というニーズを反映している。

## 📝 日本語コミュニティ

- [AI に iOS アプリを実装させるとき、失敗を種類で分ける（Qiita / yasu-ya）](https://qiita.com/yasu-ya/items/989a098d54e9979b7588) — 前回（10/27 キャッチアップ）紹介した「触らせてはいけないファイル」同記事の著者による姉妹記事（2026-10-06 公開）。Claude Code に iOS 実装を任せたときの失敗パターンを「ファイル操作系」「ビルド設定系」「UI 挙動系」「コードロジック系」の 4 種類に分類し、それぞれの原因と CLAUDE.md・フック・パーミッション設定による予防策を体系的に解説。
- [【Swift未経験】Claude CodeでiPhoneアプリを作って審査に出すまで（Qiita / masa1124）](https://qiita.com/masa1124/items/6b66b5f27158e7067ff9) — Swift 未経験者が Claude Code だけを頼りにゼロから iOS アプリを作り App Store 審査に提出した実録記事（2026-10-16 公開）。「Swift コードより Xcode の設定の方が難しかった」という所感が印象的で、コード署名・スキーム設定・プロビジョニングプロファイルは人間が直接触る必要があるという結論に至っている。

## 🌐 海外コミュニティ / Tips

- [Building an iOS App with Claude Code（John Rodrigues / Substack）](https://johnrodrigues.substack.com/p/building-an-ios-app-with-claude-code) — SwiftUI アプリを Claude Code で段階的に作り上げた実践レポート。CLAUDE.md にコーディング規約・依存ライブラリ方針・コードパターンを明文化してから実装させると、リバート頻度が大幅に下がったと報告。Xcode プロジェクトファイル（.pbxproj）の変更は人間がレビューすべきという知見も共有。
- [AI-Assisted Development for iOS（Kodeco コース）](https://www.kodeco.com/ios/programs/ai-assisted-ios-development/ai-assisted-ios-dev/48114023-ai-assisted-development-for-ios/04-getting-started-with-claude-code/02) — iOS 開発者向け学習プラットフォーム Kodeco が「AI Assisted iOS Development」コースを開講。Claude Code の基礎から XcodeBuildMCP 連携・SwiftUI Preview 取得・TCA プロジェクトへの適用まで体系的に学べる内容。Claude Code 入門を探している iOS 開発者の参考になる。

## 💡 今日のおすすめ実践 Tip

**v2.1.312 の CLAUDE.md 上書きバグ修正に合わせて「重要設定の最上部集中」を見直す**

v2.1.312 で修正された「大きな CLAUDE.md を読み途中で打ち切って上書きする」バグは、ファイルが長い場合に後半部分が読まれないまま保存されてしまうというものだった。既に v2.1.312 で修正済みだが、このバグは「CLAUDE.md の後半に重要なルールを書いていたプロジェクトで、Claude が前半しか読まずにルールを無視するように見える」という症状として現れていた可能性がある。

この機会に CLAUDE.md の構造を見直し、**重要な制約（触らせてはいけないファイル・コード署名設定・ビルドスキーム変更禁止等）を上部に集中させる**のを推奨する。

**iOS プロジェクト向けの CLAUDE.md 推奨構成:**

```markdown
# 最重要制約（冒頭に必ず記述）
- .pbxproj / .xcconfig / .xcscheme は直接編集しない
- Signing & Capabilities の変更は行わない
- Podfile.lock / Package.resolved の手動書き換えは不可

# ビルドコマンド
xcodebuild -scheme MyApp -destination 'platform=iOS Simulator,name=iPhone 16 Pro' build

# アーキテクチャ方針
（以降に詳細を書く）
```

ルールが長くなる場合は `@import rules/ios-constraints.md` のようにファイルを分割して include することで、主ファイルの肥大化を防げる（v2.1.200 以降の機能）。
