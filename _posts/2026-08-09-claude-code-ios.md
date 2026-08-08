---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-09"
date: 2026-08-09 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.225（2026-08-08）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS / Remote Control 向けに実害を解消する複数の修正が含まれる: ① **コンパクション後の Remote Control セッション再開で会話履歴が壊れる問題を修正** — 長時間の XcodeBuildMCP セッション後に会話がコンパクションされ、iPhone の Code タブからセッションを再開すると履歴が欠損するケースが解消; ② **Claude アプリから添付した写真をディスクから読み込む代わりに直接表示する修正** — iOS 側でスクリーンショットや図を撮ってそのままチャットに添付したときの表示遅延・エラーが解消; ③ **クロスセッション SendMessage の改善** — Remote Control セッションを名前で検索して別セッションからメッセージを開始できるように（v2.1.224 で追加されたクロスセッション機能の追加修正）; ④ **MCP OAuth サーバーの macOS Keychain タイムアウト 401 エラーを修正** — XcodeBuildMCP などの MCP 接続で macOS Keychain 読み込みがタイムアウトするたびに 401 が発生し再認証を求められていた問題が解消。

- **v2.1.226（2026-08-08）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— バグ修正・安定性向上のみのリリース。詳細変更点は変更ログを参照。

## 🛠 GitHub の動き

- [Issue #85091 — Remote Control（iOS）から /feedback を送れない、エラー直後でも代替チャネルを案内しない（OPEN / 2026-08-08）](https://github.com/anthropics/claude-code/issues/85091) — iPhone の Code タブや `claude.ai/code` からセッションが API エラーで終了した直後に `/feedback` を打つと「`/feedback isn't available over Remote Control.`」とだけ返され、代替の報告先（URL 等）が一切案内されない問題。Remote Control はモバイル特有のバグ（セッション中断・接続エラー・モバイル描画問題）が最も発生しやすい場面であるにもかかわらず、その場でフィードバックを送れない構造的欠陥を指摘している。報告者は ① Remote Control でも `/feedback` を有効にする（デスクトップ到達不能時はキューイング）か、② 少なくとも拒否メッセージに別の報告先を明示することを要求。**回避策: エラーの Request ID をメモしておき、デスクトップ / ブラウザに切り替えてから `/feedback` または [github.com/anthropics/claude-code/issues](https://github.com/anthropics/claude-code/issues) に報告する。**

- [Issue #84488 — Remote Control 接続中は画面ロック状態でもプッシュ通知が届かない（OPEN / 2026-08-06、最終更新 2026-08-08）](https://github.com/anthropics/claude-code/issues/84488) — `agentPushNotifEnabled: true` かつ OS レベルの通知権限が有効でも、Claude iOS アプリが Remote Control に接続している間は PushNotification ツールが「`Not sent — this terminal is active`」を返し、画面ロック中・アプリをバックグラウンドに落とした状態でも通知が届かない問題。報告者は「active の判定が Remote Control の接続状態に紐付いており、実際のユーザー注目度（画面点灯・操作有無）と切り離されていない」と指摘。XcodeBuildMCP の長時間ビルド後に iPhone へ通知させる運用が根本的に機能していない。**回避策: Remote Control を切断してからビルド完了通知を受け取る、または外部 Webhook（Slack / ntfy.sh 等）でビルド完了を通知する（本日 Tip 参照）。**

## 📝 日本語コミュニティ

- [【第2弾】VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano / 2026-04-16）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — 第1弾の続編として、mcpbridge 経由で Xcode MCP に接続した VS Code + Claude Code を使い、PR レビューコメントを起点に Swift コードの自動修正 → Xcode ビルド確認 → XCTest テスト項目の自動生成まで一連のワークフローを解説。実際の diff 例と CLAUDE.md 設定が具体的で実務応用しやすい内容。直近 5 投稿での掲載なし。

## 🌐 海外コミュニティ / Tips

- 該当なし（本日時点で直近 5 投稿（2026-08-04〜08-08）と重複しない新規の iOS / Swift / Xcode 関連記事は海外コミュニティにおいて確認できなかった）

## 💡 今日のおすすめ実践 Tip

**「Remote Control 接続中のプッシュ通知 "Not sent" 問題の回避策と v2.1.225 修正の活用」**

Issue #84488 が示す通り、iPhone の Remote Control 接続中は Claude のプッシュ通知が「`Not sent — this terminal is active`」で完全にブロックされます。XcodeBuildMCP で iOS ビルドを走らせて「ビルド完了を iPhone で受け取ろう」と考えている場合、現状では以下の代替手段が有効です。

**代替手段 1: Stop フック + 外部通知サービス**

`.claude/settings.json` に Stop フックを追加して、セッション終了時に外部 Webhook や ntfy.sh（OSS のプッシュ通知サービス）に通知します。Claude の pure push 経路を使わないため Remote Control の接続状態に左右されません。

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "curl -s -X POST 'https://ntfy.sh/<your-topic>' -d 'Claude task finished'"
          }
        ]
      }
    ]
  }
}
```

ntfy.sh は iOS / Android アプリがあり、設定不要でトピック名だけで通知を受け取れます。Slack Webhook に置き換えてチームに通知する運用も同様に可能です。

**代替手段 2: Remote Control を切断してからビルド開始**

XcodeBuildMCP の長時間ビルド（archive / export など）は Remote Control 接続なしでバックグラウンド実行し、完了後に iPhone から接続し直すと純正プッシュが届きます。v2.1.225 の修正（コンパクション後のセッション再開で履歴が壊れる問題）により、再接続したときに「会話の続き」が正しく復元されるようになりました。「ビルドを仕掛けて離席 → 純正通知で復帰 → 履歴が途切れずに再確認」という運用がより安定します。

**CLAUDE.md への追記例**

```markdown
## Remote Control + XcodeBuildMCP 通知ポリシー

- 長時間ビルド中のプッシュ通知は外部 Webhook を使う（Stop フック参照）
- Remote Control から切断してビルドを開始する場合は
  セッション再開後に履歴が復元されることを前提として作業を引き継ぐ
```
