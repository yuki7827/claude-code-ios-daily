---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-07"
date: 2026-06-07 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.166（2026-06-06）](https://github.com/anthropics/claude-code/releases) — **`fallbackModel` 設定を追加**。最大 3 つのフォールバックモデルを優先順に指定でき、プライマリモデルが過負荷・利用不可のときに自動切替する（`--fallback-model` フラグもインタラクティブセッションに対応）。iOS CI/CD でビルドエージェントがモデル過負荷でサイレント停止するケースへの対策として有用。**MCP deny ルールのツール名位置にグロブパターンを追加**（`"*"` で全ツール拒否）し、XcodeBuildMCP などの MCP に対してより細粒度の許可制御が可能に。**クロスセッション SendMessage のセキュリティ強化**：他 Claude セッションから中継されたメッセージがユーザー権限を引き継がなくなり、マルチエージェントで組む iOS 自動化パイプラインの権限昇格リスクを低減。`MAX_THINKING_TOKENS=0` / `--thinking disabled` でデフォルト思考モデルの thinking を明示的に無効化可能に。バグ修正として JetBrains IDE ターミナル（IntelliJ/PyCharm/WebStorm 2026.1+）のちらつき修正、バックグラウンドエージェントのクラッシュループ修正、ボイスモード認証問題なども解消。

- [Claude Code v2.1.167（2026-06-06）](https://code.claude.com/docs/en/changelog) — バグ修正と信頼性向上のみのパッチリリース（ユーザー向け機能変更なし）。

## 🛠 GitHub の動き

- [anthropics/claude-code-security-review（★5k・公式）](https://github.com/anthropics/claude-code-security-review) — Claude が PR の差分を解析して脆弱性を自動コメントする公式 GitHub Action。240+ プロジェクトで採用済み。Swift コードにも対応し、インジェクション攻撃・認証の問題・データ露出・暗号化の欠陥・XSS を検出。`comment-pr: true` で PR の該当行に直接指摘が入る。iOS チームの CI パイプラインに `.github/workflows/security.yml` として 10 行程度追加するだけで導入できる。

- [**WWDC 2026 前夜：iOS 27 Siri Extensions（App Intents ベース）**](https://9to5mac.com/2026/05/05/ios-27-will-let-you-choose-between-gemini-claude-and-more-for-ai-features-report/) — 明日（6/8）の WWDC 2026 Keynote で正式発表が確実視されている「Siri Extensions」フレームワーク。Claude / Gemini / ChatGPT を Siri・Writing Tools・Image Playground から呼び出せる開発者 API で、App Intents を基盤とする。Keynote 後すぐに iOS 27 Developer Beta が公開される予定（日本時間 6/9 午前以降）。Claude コードを App Intents 経由で Siri 対応させる新たな実装機会に備え、既存 App Intents の整備を今日中に進めておくとよい。

## 📝 日本語コミュニティ

- [Claude CodeとXcodeBuildMCPで簡単なiOSアプリを作ってみました](https://zenn.dev/kobashuu/articles/42a551b609c06d)（Zenn / kobashuu）— `~/.claude.json` で XcodeBuildMCP を設定し、Claude Code にじゃんけんゲームアプリを一から作らせる実践レポート。ビルドエラーの自律修正→シミュレーター起動→動作確認まで Claude が完遂した過程を具体的に記録しており、XcodeBuildMCP 導入の入門として読みやすい。

- [Xcode 26.3エージェント型コーディング入門 — Claude・Codex・MCPで開発を自動化](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0)（Qiita / kai_kou）— Xcode 26.3 の `xcrun mcpbridge` を使って Claude Code・Codex・Cursor のいずれからも Xcode の BuildProject / RenderPreview / ExecuteSnippet などを呼び出す接続手順を解説。`agents.md`/`claude.md` でプロジェクトコンテキストを渡す方法も整理されており、WWDC 翌日に iOS 27 Beta を試す際の出発点として使える。

## 🌐 海外コミュニティ / Tips

- [iOS 27 Will Let You Pick Claude or Gemini Instead of ChatGPT for Apple Intelligence（MacRumors）](https://www.macrumors.com/2026/05/05/ios-27-third-party-chatbots-apple-intelligence/) — 明日の WWDC で発表される Siri Extensions の詳細をまとめた記事。Extensions は App Intents の上位レイヤーとして実装され、Claude アプリをインストールしているユーザーが Siri・Writing Tools・Image Playground で Claude をデフォルト AI として選択できる。**iOS アプリ開発者にとっては**、自社アプリの機能を App Intents でエクスポートするだけで Claude 経由の Siri 対応が実現できるという開発機会でもある。

- [Siri Extensions API in iOS 27: Integrate Your AI App（byteiota.com）](https://byteiota.com/siri-extensions-api-ios-27-integrate-ai-app/) — Extensions が App Intents ベースであることを確認し、自社アプリの既存 App Intents をそのまま Siri Extensions として登録する実装フローを解説した技術記事。Claude Code に App Intents のボイラープレートを自動生成させてから Extensions 対応に拡張するワークフローが Beta 期間の定番になりそう。

## 💡 今日のおすすめ実践 Tip

**`fallbackModel` を設定して iOS CI/CD のモデル過負荷サイレント停止を防ぐ**

v2.1.166 で追加された `fallbackModel` 設定は、iOS の CI/CD パイプライン（GitHub Actions / Xcode Cloud）で `claude -p` や Agent SDK を呼び出している場合に特に効果的です。夜間バッチや多並列ビルド時にプライマリモデルが過負荷になると、エラーログすら出ずにジョブが止まるケースがありましたが、以下のような設定で安定性を高められます。

```json
// .claude/settings.json
{
  "model": "claude-opus-4-8",
  "fallbackModel": [
    "claude-sonnet-4-6",
    "claude-haiku-4-5"
  ]
}
```

CI 側（GitHub Actions）での利用例：

```yaml
- name: Claude Code Review
  run: claude -p "このPRのSwiftコードを確認して問題があれば報告してください"
  env:
    ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

フォールバック順にモデルが試行されるため、`opus-4-8` が過負荷でも `sonnet-4-6` でコードレビューが続行されます。`haiku-4-5` を最後段に置くことでコスト上限を設けつつ、ビルドの完全停止は避けられます。

あわせて v2.1.166 の **クロスセッション SendMessage セキュリティ強化**も重要です。複数の Claude サブエージェントを並列で走らせる iOS 自動化スクリプトでは、`SendMessage` でセッション間連携を組んでいる場合、リレーメッセージが以前はユーザー権限を引き継いでいましたが、今後は引き継がなくなります。既存の並列エージェント設定を見直し、権限が必要なツール呼び出しはオーケストレーターセッションで直接行うよう修正しておきましょう。
