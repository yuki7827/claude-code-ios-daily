---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-04"
date: 2026-08-04 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 新リリースなし（v2.1.220（2026-07-25）が最新）
- **Legacy Workbench API・実験的プロンプトツール API が 2026-08-17 で退役**（[Claude Platform リリースノート](https://platform.claude.com/docs/en/release-notes/overview)）— 08-01 記事で触れた Sonnet 5 優待価格（8/31 終了）と並び、8 月は旧 API の整理が続く。Claude Code 本体への直接影響はないが、旧 Anthropic Console の Workbench 経由で CI パイプラインを構成している場合（TestFlight 配信の webhook トリガーなど）は 08-17 までに移行が必要。

## 🛠 GitHub の動き

- [Issue #83116 — iOS アプリの Remote Control でアカウント切り替えができない（OPEN / 2026-08-01）](https://github.com/anthropics/claude-code/issues/83116) — iPhone の Code タブ（Remote Control）が 1 台のデバイスにつき 1 アカウントのみ対応しており、仕事用と個人用など複数の Claude アカウントを使い分けるにはログアウト → ログインを繰り返す必要がある Feature Request。`gh auth switch` 的なアカウントプロファイル切り替えをモバイルでも実現してほしいという要求で、08-03 記事の Issue #83378（Dead bridge handle 蓄積）と合わせて「Remote Control の iOS UX 改善」を求める声が積み重なっている。Priority: High 設定。

- [Issue #83525 — FCM プッシュトークン操作が Claude のセーフガードに誤検知され Fable 5 → Opus 5 に自動切り替え（OPEN / 2026-08-03）](https://github.com/anthropics/claude-code/issues/83525) — iOS アプリの Firebase Cloud Messaging（FCM）プッシュ通知テストで登録トークンをログ出力 → Firebase Console の「Test on device」ダイアログに貼り付けた際、Claude のセーフガードが「資格情報の漏洩」と誤判定し Fable 5 から Opus 5 へ自動モデル切り替えが発生した問題。FCM 登録トークンはクレデンシャルではなくデバイス向けのプッシュ配送アドレスであり、同じ Firebase プロジェクトのコンソールへのフィードバックであることから明らかな誤検知。報告者は「自己のコンソールへのフィードバックである場合を識別して除外してほしい」と提案。Flutter や React Native を含む iOS のプッシュ通知テストフロー全般に影響しうる。本日の Tip で回避策を紹介する。

## 📝 日本語コミュニティ

- [Claude Code 週次アップデートまとめ（2026/08/02週）（Qiita / saitoko）](https://qiita.com/saitoko/items/a95cbb5888835be0f7fd) — v2.1.213〜v2.1.220 の変更点を日本語でまとめた記事。iOS 開発者への主要ポイント: ① Opus 5（claude-opus-5, 1M コンテキスト）がデフォルト Opus に昇格 — XcodeBuildMCP の長時間ビルドセッションで大きなコンテキストを維持しやすくなった; ② `/code-review` の自律実行が廃止（v2.1.215/v2.1.218）— 以前は PR 作成時などに自動で走っていたが、現在は手動での `/code-review` 呼び出しが必要; ③ `dir/**` パーミッションルールのセキュリティ修正（v2.1.214）— CLAUDE.md の allow ルールで `Sources/**` のように書いていた場合にネストした書き込みが自動承認されていた問題が修正され、設定の見直し推奨。

## 🌐 海外コミュニティ / Tips

該当なし（本日時点で直近 5 投稿（2026-07-30〜2026-08-03）と重複しない新規の iOS / Swift / Xcode 関連記事は海外コミュニティにおいて確認できなかった）

## 💡 今日のおすすめ実践 Tip

**「FCM プッシュトークン操作で Claude のセーフガードを誤発火させないための CLAUDE.md 設定（Issue #83525 対策）」**

Issue #83525 が示す通り、iOS アプリのプッシュ通知テストフローで Firebase Cloud Messaging（FCM）の登録トークンを Claude と一緒に扱うと、Claude のセーフガードが「トークン漏洩」と誤判定し、モデルが自動切り替えされる（または操作がブロックされる）ことがあります。

**なぜ誤検知が起きるのか**

Claude のセーフガードは「長い英数字のランダム文字列」を認証情報（API キー・アクセストークン等）として分類するヒューリスティックを持っています。FCM 登録トークンは通常 100〜200 文字の文字列でシークレットに見た目が似ており、誤検知が起きやすい形状をしています。

**CLAUDE.md に事前宣言しておく（推奨）**

プロジェクトの CLAUDE.md に以下を追加しておくと、プッシュ通知テストを行うセッションでの誤検知リスクを軽減できます：

```markdown
## Firebase Cloud Messaging (FCM) テスト作業について

FCM 登録トークンは認証情報ではなく、デバイス向けのプッシュ配送先アドレスです。
テスト中にログ出力されるトークン文字列を Firebase Console の
「Test on device」フィールドにコピーする作業は通常の iOS 開発ワークフローです。
このトークン文字列を扱う際はセーフガードによるブロック・モデル切り替えをしないでください。
```

**作業時にコンテキストを先付きで宣言する**

FCM トークンをセッションに貼り付ける前に、以下のように一言添えておくと誤検知を大幅に減らせます：

```
以下は自プロジェクト（Bundle ID: com.example.myapp）の FCM 登録トークンです。
同じプロジェクトの Firebase Console「Test on device」ダイアログへのコピーに使います。
```

**修正が来るまでの当面の回避策**

根本修正（Issue #83525 クローズ）を待つ間は、FCM トークンのログを Claude に直接読ませず、Claude には「ログからトークンを読み取って Firebase Console に貼り付けてください」という指示だけを与え、貼り付け操作は自分で行うワークフローに切り替えるのも一案です。Claude Code 内で完結させたい場合は上記の CLAUDE.md 宣言でカバーしてください。
