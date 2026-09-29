---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-30"
date: 2026-09-30 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.285（2026-09-29）リリース](https://github.com/anthropics/claude-code/releases/tag/v2.1.285)** — 大型バグ修正リリース。iOS/Remote Control に直接影響する修正を含む。**Remote Control でメッセージが早期に「既読」マークされる問題を修正**（iOS アプリでセッション状態の表示が不正確になっていたケースに対応）。`claude --desktop` コマンドを新規追加（現在のディレクトリで Claude Desktop を開くか、進行中のセッションを再開する）。`allowedProviders` managed 設定でアクセス可能な API プロバイダーを Anthropic / Bedrock / Vertex AI などに限定できるように。バックグラウンドシェルコマンドがタイムアウト後に強制停止するよう変更（デフォルト 30 分・最大 2 時間）。バックグラウンドサブエージェントがパーミッションモードを引き継がないバグも修正。

## 🛠 GitHub の動き

- **[Issue #36151（2026-03-19 作成 / OPEN）— Claude Mobile アプリで異なるメールアドレスのマルチアカウント切り替えができない](https://github.com/anthropics/claude-code/issues/36151)** — 2026-09-29 に複数コメントが追加されアクティブに議論中。個人（Pro/Max）と会社（Team/Enterprise）が別メールアドレスの場合、切り替えのたびにサインアウト/サインインが必要。Feature Request: プロフィールメニューからワンタップで切り替えられるネイティブマルチアカウント対応。デスクトップ版 Issue #18435 と同趣旨。iOS から複数プロジェクト・組織をまたいで作業する開発者に直接影響する。

- **[Issue #98220（2026-09-29 作成 / OPEN）— `skillOverrides` でプラグインの個別スキルを無効化できない](https://github.com/anthropics/claude-code/issues/98220)** — `aws-core` など公式プラグインを使用している場合、`skillOverrides` でプラグイン内の不要なスキルを `"off"` にしても無視される。スキルを絞るにはプラグインごと無効化するしかなく、MCP サーバーや必要なスキルも同時に消えてしまう。複数プラグインを組み合わせて iOS ワークフローを構築している開発者に影響。

## 📝 日本語コミュニティ

- 該当なし（2026-09-30 時点で Zenn・Qiita の新規 Claude Code × iOS / Swift / Xcode 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Xcode 27.1 beta リリース — iPhone Duo シミュレーター対応（Apple Developer / 2026-09-18）](https://developer.apple.com/news/?id=rfb1rooi)** — Apple が Xcode 27.1 beta を公開。iPhone Duo（折りたたみスマートフォン）向け iOS 27.1 SDK とシミュレーターが追加され、折りたたみ・展開・回転といった各ポーズを再現できる。**MobileBuildMCP は v2.7.0（2026-07-23）で Xcode 27 Device Hub 対応済みだが、iPhone Duo の折りたたみ状態の追従は Issue #96941 で未対応**（2026-09-29 時点）。10 月 23 日出荷予定の iPhone Duo に向けたアプリ対応を今から着手できる状況になった。

- **[9to5Mac — 2 回目の iOS 27.2 デベロッパーベータ公開（2026-09-21）](https://9to5mac.com/2026/09/21/apple-releases-second-ios-27-2-developer-beta-for-iphone/)** — iOS 27.2 の 2 回目デベロッパーベータが 9 月 21 日に公開。Claude Code + MobileBuildMCP でテスト自動化している開発者は、新 beta への差分テストに活用できる。

## 💡 今日のおすすめ実践 Tip

**「`claude --desktop` コマンドで Xcode ↔ iOS Remote Control の切り替えをスムーズにする（v2.1.285 新機能）」**

v2.1.285 で追加された `claude --desktop` を使うと、ターミナルから現在のディレクトリで Claude Desktop を直接開いたり、進行中のセッションを再開したりできます。

```bash
# Xcode プロジェクトのルートで実行
cd ~/Developer/MyApp
claude --desktop
# → Claude Desktop がそのディレクトリでセッションを開く（または再開する）
```

iOS アプリ側から Remote Control で作業を始め、「やはりデスクトップの大きな画面で続けたい」という場面で役立ちます。プロジェクトを毎回選び直す手間がなくなります。

**`CLAUDE_CODE_DISABLE_WEB_FETCH` の使いどころ**

CI / CD やオフライン環境で Claude が外部サイトへアクセスしないよう完全に遮断したい場合:

```bash
# .envrc や CI 設定に追加
export CLAUDE_CODE_DISABLE_WEB_FETCH=1
```

Xcode の自動ビルドパイプラインなど、ネットワーク制限のある環境で Claude Code を使う際に有効です。WebFetch ツールが無効になるため、Claude が意図せず外部ドキュメントを参照しに行くのを防げます。
