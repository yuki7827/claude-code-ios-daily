---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-01"
date: 2026-08-01 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 新リリースなし（v2.1.220（2026-07-25）が最新。2026-07-26 記事で取り上げ済み）
- **Claude Code 週次使用量ブーストが 2026-08-19 まで延長**（[AI Builder Club on X](https://x.com/aibuilderclub_/status/2078666540740759587)）— 当初 2026-07-19 に終了予定だったブーストが 2026-08-19 まで継続。Pro / Max / Team / シートベース Enterprise が対象（Free プランと消費型 Enterprise は対象外）。Xcode 連携のビルドセッションや長時間の XcodeBuildMCP タスクなど、Claude Code をヘビーに使う iOS 開発者にとって引き続き実質的な恩恵。
- **Claude Sonnet 5 優待価格が 2026-08-31 で終了**（[releasebot.io](https://releasebot.io/updates/anthropic/claude-developer-platform)）— 現行の優待価格（入力 $2 / 出力 $10 per MTok）は 8 月末で終了し、9 月 1 日から通常価格（$3 / $15 per MTok）に移行。Claude Code のデフォルトモデルを Sonnet 5 に設定して iOS プロトタイプを回している場合、予算の見直しを 8 月中に済ませておくと安心。

## 🛠 GitHub の動き

- [Issue #82864 — iOS Simulator の MCP ツールが Metal スクリーンキャプチャパスで SIGABRT クラッシュループ（OPEN / 2026-07-31）](https://github.com/anthropics/claude-code/issues/82864) — `mcp__Claude_Code_iOS_Simulator__control` ツールの `screenshot` / `attach` 呼び出しが「Claude Code iOS Simulator is restarting after a crash.」を連続して返してリカバリーしないバグ。5〜10 秒待機してのリトライでも回復せず、`attach` が誤って別デバイスを報告するケースも確認されている。07-28 記事で取り上げた Issue #81520（macOS 27 beta + Xcode 27 beta 4 でのフレームワーク移動に起因するクラッシュ）とは別の問題で、Claude Desktop の内蔵 MCP ツールレベルでのクラッシュループであるのが特徴。`build` や `launch` アクション単体は正常動作するが、ライブ映像キャプチャ（画面確認ループ）が使えなくなる点で UI 検証の自動化に影響が出る。本日の Tip で回避策を紹介する。

- [Issue #71616 — 新規作成 Code セッションが iOS アプリ上で数分以内に自動アーカイブされ利用不能になる（OPEN / 最終更新 2026-07-31）](https://github.com/anthropics/claude-code/issues/71616) — セッション作成直後から iPhone の Claude アプリ「Archived」タブに移動し、アクティブリストから消えてしまう問題。07-27 記事の Issue #81466（Desktop 起動セッションの終了時自動アーカイブ）が特定条件下での発生だったのに対し、こちらはすべての新規セッションに一律に適用される。2026-06-26 に初報されてから約 1 ヶ月以上未解決のまま継続しており、**回避策として `claude remote-control` デーモン経由のセッション**を使う（`environment_id` が付与されたセッションはアーカイブトリガーを回避しやすい）のが現状のベストプラクティス。

- [Issue #62458 — iOS プッシュ通知に許可 / 拒否の操作ボタンを追加してほしい Feature Request（OPEN / 最終更新 2026-07-31）](https://github.com/anthropics/claude-code/issues/62458) — Claude Code がリスクのある Bash コマンドなどでブロックした際、Remote Control は iPhone にプッシュ通知を送るが、通知バナーにはアクション可能なボタンがなく、承認や拒否のためにデスクトップに戻る必要がある。Feature Request の内容は「通知バナー上の Approve / Deny ボタン（iOS Interactive Notification）でセッションをアンブロックできるようにしてほしい」というもの。07-30 記事で取り上げた Issue #82374（通知そのものの配信不達）とは別の問題で、こちらは「通知は届くが操作できない」UX の課題。XcodeBuildMCP のビルドジョブが許可待ちで止まっている間、iPhone だけでそのまま操作を完結できるようになると外出中のワークフローが大幅に改善される。

## 📝 日本語コミュニティ

該当なし（本日時点で直近 5 投稿（2026-07-27〜31）と重複しない新規の iOS / Swift / Xcode 関連記事は Zenn・Qiita ともに確認できなかった）

## 🌐 海外コミュニティ / Tips

- [Building iOS Apps with AI Agents: The Practitioner's Guide（blakecrosley.com）](https://blakecrosley.com/guides/ios-agent-development) — Claude Code と XcodeBuildMCP を中心に、AI エージェントに iOS 開発を任せる際の実践的なプラクティスをまとめたガイド。「エージェントに任せる範囲」と「人間が監視する範囲」の分界点（アーキテクチャ設計・App Store コンプライアンス・エッジケースの動作確認）を明確にしており、Claude Code を初めてチーム導入するときの役割定義の参考になる。Claude Code が生成する Swift コードのアーキテクチャ一貫性（プロジェクト固有の命名規約を 89% のファイルで維持）についても言及している。

## 💡 今日のおすすめ実践 Tip

**「iOS Simulator MCP ツールがクラッシュループしたときの XcodeBuildMCP フォールバック（Issue #82864 対策）」**

Issue #82864 が示すように、Claude Desktop の内蔵 `mcp__Claude_Code_iOS_Simulator__control` ツールは Metal スクリーンキャプチャのパスで SIGABRT クラッシュループに入ることがあります。`screenshot` / `attach` が "restarting after a crash." を連続して返す状態では、UI 確認ループが回らなくなります。

**即効の回避策: XcodeBuildMCP の `simulator` ツール群に切り替える**

XcodeBuildMCP（`npx -y xcodebuildmcp@latest mcp` でセットアップ済みの場合）は内蔵の Simulator ツールとは独立したスクリーンキャプチャ経路を持っています。CLAUDE.md に以下を追記しておくと、内蔵ツールが応答しない場合に自動的にフォールバックさせられます：

```markdown
## iOS Simulator スクリーンショット取得の優先順位

1. `mcp__Claude_Code_iOS_Simulator__control` の `screenshot` を試みる
2. "restarting after a crash" エラーが返ったら、そのまま再試行せず XcodeBuildMCP の
   `simulator_screenshot` ツールに切り替える
3. XcodeBuildMCP でも失敗する場合は `xcrun simctl io booted screenshot /tmp/sim.png` を
   Bash で直接呼ぶ
```

**クラッシュループのリセット手順**

`mcp__Claude_Code_iOS_Simulator__control` の再起動は Claude Desktop を再起動するだけでは回復しないことがあります。以下の順序を試してください：

```bash
# 1. ビジー状態のシミュレータープロセスを確認・終了
xcrun simctl list devices | grep Booted
xcrun simctl shutdown all

# 2. Claude Desktop を完全終了（Cmd+Q）してから再起動
# 3. シミュレーターを手動で一度起動してから Simulator パネルを開く
open -a Simulator
```

07-28 記事の Issue #81520（macOS 27 beta 環境の別バグ）の際と同様に、`build` / `launch` アクション自体は正常動作するため、**ビルド → 起動 → スクリーンショット（XcodeBuildMCP 経由）→ 修正 → 再ビルド**のループは維持できます。
