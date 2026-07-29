---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-30"
date: 2026-07-30 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 新リリースなし（v2.1.220（2026-07-25）が最新。2026-07-26 記事で取り上げ済み）
- [MCP 2026-07-28 仕様の Claude 対応が発表（Claude.com Blog）](https://claude.com/blog/bringing-mcp-2026-07-28-to-claude) — MCP がステートレスコアへ移行し、サーバーレス・エッジ環境へのデプロイが可能になった新仕様に Claude が対応。**iOS 開発への直接的影響**：XcodeBuildMCP（getsentry 管理）および Apple 公式 `xcrun mcpbridge` が新仕様の stateless core に対応すると、CI / GitHub Actions 上でのヘッドレス Xcode ビルドが軽量化される可能性がある。XcodeBuildMCP 側の対応状況は 2026-07-30 時点で未発表。MCP の OAuth / OIDC 強化（新仕様の柱の一つ）は Xcode MCP ツールの App Store Connect 連携にも将来的に関係する。

## 🛠 GitHub の動き

- [Issue #82374 — Mobile push notifications silently dropped ~50% of the time, with no client-side signal（OPEN / 2026-07-29）](https://github.com/anthropics/claude-code/issues/82374) — iOS アプリへのプッシュ通知（ツール承認要求・エージェント完了通知）が約 50% の確率で無信号のまま端末に届かない問題。`PushNotificationツール` は常に `"Mobile push requested."` を返すが、実際の配信には一定確率でサイレント失敗が発生。デスクトップ通知（ターミナル）は 100% 動作しており、**モバイル通知パスのみ**が問題。アイドル時間との相関が疑われており（アイドル後の最初のプッシュが失敗しやすい）、Remote Control 接続が健全でも通知が届かないケースが報告されている。関連 Issue は #50949・#60383・#57758・#60208 と複数あり、以前から未解決の問題。iOS で XcodeBuildMCP のビルド完了をモバイル通知で受け取るワークフローを使っている場合は要注意（本日の Tip 参照）。

- [Issue #81250 — Permission-decision prompt overlays keyboard on iPhone, causing accidental taps（OPEN / 2026-07-25 / 2026-07-29 更新）](https://github.com/anthropics/claude-code/issues/81250) — iPhone のキーボード表示中にツール実行許可プロンプト（承認 / 常に許可 / 拒否）が出ると、プロンプトがキーボード領域と重なって表示され、誤タップのリスクが高い iOS UI バグ。特に「常に許可」を誤選択するとセッション全体のパーミッション設定に影響するため危険度が高い。iOS 27.0 + iPhone 17 Pro で確認。**回避策**：入力完了後にキーボードを閉じてから（画面タップ等で折りたたんでから）エージェントのアクションを開始するのが現状最も安全。

## 📝 日本語コミュニティ

該当なし（本日時点で直近 5 投稿（2026-07-25〜29）と重複しない新規の iOS / Swift / Xcode 関連記事は Zenn・Qiita ともに確認できなかった）

## 🌐 海外コミュニティ / Tips

- [ApptitudeLabs/claude-code-ios-dev-setup（GitHub）](https://github.com/ApptitudeLabs/claude-code-ios-dev-setup) — keskinonur の iOS 向け Claude Code ガイドをベースに、**トークン最適化**を大幅に強化したコミュニティ製セットアップガイド（MIT ライセンス）。目玉は 3 つのトークン削減ツールの組み合わせ：**RTK**（ツール出力を 60〜90% 削減）、**Caveman**（出力圧縮で約 75% 削減）、**context-mode**（ツール出力を 98% 削減してコンテキスト長を抑える）。長時間のビルド・テストセッションでコンテキストが肥大化しやすい iOS 開発に直接的に効く設計で、XcodeBuildMCP のビルドログや LLDB ログがコンテキストを圧迫する問題への対処としても参考になる。また、**AgentMemory**（セマンティック検索付き永続メモリ）と **Memvid**（ビデオエンコードされた知識ベース）を使ったセッション間知識引き継ぎの設計も含む。50 以上の iOS Agent Skills（Axiom 経由）・Swift 6 厳格並行性チェック・SwiftLint 統合のフックスクリプト付き。

## 💡 今日のおすすめ実践 Tip

**「プッシュ通知不達（Issue #82374）が起きたときの確認フローと Routines での通知設計指針」**

Issue #82374 が示すように、Claude Code のモバイルプッシュ通知は現時点で配信保証がなく、約 50% の確率でサイレントに失われます。XcodeBuildMCP のビルドジョブや長時間 Routine を iPhone でモニタリングしている場合に備えて、以下の 2 点を取り入れることを推奨します。

**① 通知を複数チャネルで冗長化する**

CLAUDE.md に以下を追加しておくと、プッシュ通知が届かなかった場合でも最終メッセージから作業結果を把握できます（昨日の Issue #82087 対策とも兼用できます）：

```markdown
## 完了報告の形式

長時間タスク（ビルド・テスト・Routine）の最終メッセージには必ず以下を含める:
1. 結果サマリー（成功 / 失敗、変更ファイル数、テスト合格率）
2. 次のアクションが必要な場合はその内容を明示
3. 重要なエラーがある場合はエラーコードを先頭に記載
```

これにより、Code タブを手動で開いたときに最終メッセージだけで作業全体を把握できます。

**② アイドル明けのプッシュ通知を疑う**

Issue #82374 の分析では、**アイドル後最初のプッシュが特に失敗しやすい**（長い休止期間後の 1 本目が未到達になるパターン）ことが示唆されています。外出先から Remote Control セッションを再開した直後の通知は届かない可能性が高いため、重要なジョブ完了後は Code タブを手動で開いて最終メッセージを直接確認する習慣をつけてください。`agentPushNotifEnabled: true` は有効にしたままでよいですが、「通知が来ない = 完了していない」とは限らないことを念頭に置いてください。
