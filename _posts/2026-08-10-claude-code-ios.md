---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-10"
date: 2026-08-10 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.226 詳細（2026-08-08）**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— 前日記事では「バグ修正・安定性向上のみ」と記載したが、iOS / XcodeBuildMCP ワークフローに関係する修正の具体的内容が判明: ① **`--settings` CLI フラグ経由でプラグインが読み込まれない問題を修正** — `.claude/settings.json` を `--settings` オプションで明示指定しても XcodeBuildMCP などのプラグインが無視されていた問題が解消; ② **長時間セッションでフィーチャーフラグが古い状態に固まる問題を修正** — XcodeBuildMCP の長時間ビルドセッション中にフィーチャーフラグがキャッシュされたまま更新されないバグが解消; ③ **`claude update` および `claude doctor` がサイレントハングするケースを修正** — CI や自動化スクリプト内で `claude doctor` を実行しても応答がなくなる問題を解消。
- 本日（2026-08-10）時点での最新版は v2.1.226 のまま。新規リリースなし。

## 🛠 GitHub の動き

- [Issue #85240 — Remote Control: ブラウザで回答が手動リロードまで表示されない（iPad Safari/Chrome・macOS Safari）（OPEN / 2026-08-09）](https://github.com/anthropics/claude-code/issues/85240) — `claude.ai/code` ブラウザ版 Remote Control で、アシスタントの回答がリアルタイムにストリーム表示されず、ページを手動リロード（Cmd-R）してはじめて完全な回答が出現する問題。iPad Safari・iPad Chrome・macOS Safari いずれでも再現確認済み。サーバー側の生成・保存は正常だが SSE（Server-Sent Events）のストリーミングがクライアントに届いていないと推定される。iPhone / iPad から XcodeBuildMCP ビルド状況をリアルタイムに追う運用が事実上機能しない。**回避策: 確認したいタイミングで手動リロード、またはデスクトップアプリへ切り替え。**

- [Issue #85285 — [BUG] macOS Desktop のライブセッションが iOS アプリと Dispatch list_sessions API から消失（OPEN / 2026-08-09）](https://github.com/anthropics/claude-code/issues/85285) — Mac 上の Claude Desktop で進行中のセッションが、同一アカウントの iOS アプリ（Dispatch タブ）および Dispatch オーケストレーター `list_sessions` API から完全に見えなくなる問題。アーカイブ済みとして表示されるか、API レスポンスから静かに除外される断続的なバグ。UI フィルタリング層でなく API レベルの問題であることが確認されており、iPhone から Mac の XcodeBuildMCP セッションをリモート操作する構成や Dispatch で複数セッションを束ねる構成に直撃する。**回避策: デスクトップ側でセッション URL を直接開き直す。**

- [Issue #85291 — Remote Control ブリッジセッションが選択モデルを無視し常に Fable デフォルトで実行される（OPEN / 2026-08-09）](https://github.com/anthropics/claude-code/issues/85291) — iOS アプリの UI ピッカー・`claude.ai` のモデルセレクタ・Dispatch トリガー API の `session_context.model` いずれで指定しても、`claude --remote` ブリッジ経由のセッションが無条件に Fable 5（アカウントデフォルト）で起動する。**Fable のクレジット上限に達したアカウントでは「You're out of usage credits」エラーが出てセッションが即失敗し、毎朝実行するスケジュール実行ルーティンが連続して停止する**実害が Max プランで報告済み（v2.1.224・v2.1.226 両方で再現）。**回避策: `.claude/settings.json` の `model` キー、または `CLAUDE_MODEL` 環境変数でデーモン起動時のモデルを固定する（本日 Tip 参照）。**

## 📝 日本語コミュニティ

- [Claude Code にシミュレータを渡したら自分でタップしてスクショで検証し始めた（Qiita / shimo4228 / 2026-03-03）](https://qiita.com/shimo4228/items/3c6106d5407deea0cbf4) — XcodeBuildMCP の `snapshot_ui` ツール（AXe エンジン）と `tap` ツールを使い、Claude Code が自律的に iOS Simulator をタップして画面をスクリーンショットで確認し、問題があれば自動修正する実験記録。アクセシビリティ識別子を使うことで座標依存の脆いタップから抜け出せる点が実務上のポイント。直近 5 投稿での掲載なし。

- [Xcode 26.3 の MCP とは何か — なぜ必要で、どう使うのか。7 つの Skills で実践する（Qiita / dsgarage / 2026-03-05）](https://qiita.com/dsgarage/items/7c4b13de4dd4b2ec2370) — Xcode 26.3 で公式追加された `xcrun mcpbridge` の概念を丁寧に解説し、ドキュメント検索・SwiftUI Preview レンダリング・ビルド・テスト・シミュレータ操作など 7 つの Skills に落とし込んだ実践記事。Claude Code から Xcode 公式 MCP サーバーに接続する手順と、XcodeBuildMCP との使い分け方針も示されている。直近 5 投稿での掲載なし。

## 🌐 海外コミュニティ / Tips

- [Letting Claude Code Drive the Simulator: Automated UI Verification via Screenshots（Zenn / shimo4228 / 英語版）](https://zenn.dev/shimo4228/articles/xcodebuildmcp-ios-verification?locale=en) — 上記 Qiita 記事の英語版として Zenn に掲載。XcodeBuildMCP で Claude Code が iOS Simulator を自律運転するワークフローを英語圏向けに解説しており、アクセシビリティ識別子の付与方針・AXe による要素ターゲティング・スクリーンショット検証ループの実装が具体例付きで紹介されている。国際的な開発者との知識共有にも参照しやすい。直近 5 投稿での掲載なし。

## 💡 今日のおすすめ実践 Tip

**「Issue #85291 対策: Remote Control ブリッジセッションで Fable に強制される問題をデーモン設定で回避する」**

Issue #85291 が示す通り、`claude --remote` ブリッジ経由のセッションは iOS アプリや Dispatch API でモデルを指定しても Fable 5 で起動してしまいます。毎朝実行するスケジュールルーティンや XcodeBuildMCP 自動ビルドジョブが Fable クレジット切れで連続失敗するリスクがある場合、以下の回避策が有効です。

**回避策 1: `.claude/settings.json` の `model` キーでデーモン起動時のモデルを固定**

Remote Control デーモンを起動するプロジェクトのグローバル設定（`~/.claude/settings.json`）または `.claude/settings.json` に追記します:

```json
{
  "model": "claude-sonnet-5"
}
```

`settings.json` の `model` キーはデーモン起動時に読み込まれ、Dispatch 側の `session_context.model` 指定より先に適用されるため、Issue #85291 の条件下でも確実にモデルが固定されます。

**回避策 2: `CLAUDE_MODEL` 環境変数でデーモンを起動**

```bash
CLAUDE_MODEL=claude-sonnet-5 claude --remote-control
```

デーモンプロセス起動時に環境変数を付与することで、生成される子セッションが値を引き継ぎます。systemd / launchd でデーモンを管理している場合は `Environment=CLAUDE_MODEL=claude-sonnet-5` をサービス定義に追加してください。

**スケジュール実行ルーティンへの適用例**

```json
// ~/.claude/settings.json
{
  "model": "claude-sonnet-5",
  "hooks": {
    "Stop": [...]
  }
}
```

Fable クレジットの消費が多い環境では Sonnet 5 への切り替えでコスト・安定性の両面が改善します。Issue がクローズされ公式修正が入った段階で `model` キーを削除し、Dispatch 側のモデル指定に戻してください。
