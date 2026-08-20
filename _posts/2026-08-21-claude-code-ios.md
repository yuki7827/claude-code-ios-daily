---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-21"
date: 2026-08-21 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.237（2026-08-20）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更点: ① **ビルトイン「Concise」出力スタイルを追加** — Claude が結果から先に書き、前置きを省略するスタイルが `/config` の Output style に追加。Xcode ビルドログや XCTest の失敗サマリを大量に扱うセッションで「エラー内容をまず一行で教えて、詳細は後で」というニーズに応える。② **LLM ゲートウェイ・カスタムベース URL 使用時のプロンプトキャッシュを修正** — 社内プロキシや Bedrock 経由で Claude Code を使っている環境でキャッシュが効かなかったバグが解消。長いビルドログをコンテキストに積む CI ワークフローでのキャッシュ効率が向上する見込み。

- **v2.1.236（2026-08-19）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更点: ① **`ANTHROPIC_DEFAULT_MODEL` 環境変数を追加** — 新規セッションのデフォルトモデルを環境変数で指定可能に。CI スクリプトや fastlane lane からモデルを固定したい場合に `/model` コマンドなしで制御できる（セッション内で `/model` で上書き可能）; ② **macOS サンドボックス強化: ワイルドカード read-deny ルールが許可領域内でも優先されるように** — `**/.env` や `**/Secrets.xcconfig` のような glob パターンの拒否ルールが、読み取り許可ディレクトリ内のファイルにも確実に適用されるようになった。`.xcconfig`・`GoogleService-Info.plist` 等の機密ファイルを含む iOS プロジェクトでのセキュリティが向上; ③ **`/goal`：アイドルセッションが 30 分後（次いで 1h、2h）に自動チェックイン** — 長時間の archive ビルドや TestFlight 配信タスクを `/goal` で設定しておくと、Claude が定期的に状態を確認するようになった。

## 🛠 GitHub の動き

- [Issue #80205 — iOS Simulator MCP: `control { action: "launch" }` が "disclaimer exited with code 143" で失敗 — sidecar サンドボックスがビルド出力の読み取りを拒否（OPEN / 2026-07-22 起票・2026-08-20 更新）](https://github.com/anthropics/claude-code/issues/80205) — `build` アクションが成功した直後に `launch` アクションを呼ぶと `disclaimer exited with code 143`（SIGTERM）で失敗する問題。根本原因は `build` が出力先として使う `~/Library/Application Support/Claude/simulator-builds/…` ディレクトリが `claude-ios-sim.sb` サンドボックスの `file-read-data` 許可リストに含まれていないため、sidecar が `.app` バイナリを読み取れずにウォッチドッグタイムアウトで SIGTERM されること。**現時点の回避策**: ① ビルド出力を許可領域（例: `~/Library/Developer/CoreSimulator/`）にコピーしてから `launch` する、または ② `build` を使わず XcodeBuildMCP の build + `xcrun simctl install/launch` を使う。

- [Issue #80612 — Intel Mac（Ice Lake iGPU）で iOS Simulator ライブパネルがクラッシュループ — `claude-ios-sim` が `AppleIntelICLGraphicsVADriver` で SIGSEGV（OPEN / 2026-07-23 起票・2026-08-20 更新）](https://github.com/anthropics/claude-code/issues/80612) — 2020 年製 MacBook Pro（Intel/Ice Lake）+ macOS 26.5.1 + Xcode 26.5 環境で、iOS Simulator ライブパネルが起動直後に黒画面またはクラッシュループになる問題。`claude-ios-sim` ヘルパーがハードウェア H.264 エンコーダパス（AppleGVA 経由）を呼び出した瞬間に Ice Lake の GPU ドライバ内で SIGSEGV が発生する。スクリーンショット（静止フレーム）は影響なく、コントロールプレーン（tap/text/swipe）も動作する。**提案**: Intel iGPU 環境またはエンコーダクラッシュ検出時にソフトウェアエンコーディングへフォールバックする。Intel Mac ユーザーはパネルが使えない間 XcodeBuildMCP のスクリーンショットツールを代替として使用可能。

## 📝 日本語コミュニティ

- [Claude Code × iOSアプリ開発 一気通貫ガイド（Qiita / tobaru-hideyasu）](https://qiita.com/tobaru-hideyasu/items/f9ed60b136d95960b1d0) — 企画・PRD 作成から App Store 申請まで Claude Code だけで完結させるためのステップバイステップガイド。XcodeBuildMCP セットアップ・CLAUDE.md の書き方・ビルドループ自動化・TestFlight 配信までをカバーした実践的な一気通貫資料。全投稿での初掲載。

## 🌐 海外コミュニティ / Tips

- [Building iOS Apps with AI Agents: The Practitioner's Guide（blakecrosley.com）](https://blakecrosley.com/guides/ios-agent-development) — XcodeBuildMCP を使った iOS エージェント開発の総合プラクティショナーガイド。同著者の「Two MCP Servers Made Claude Code an iOS Build System」（2026-08-20 掲載）のブログ記事をさらに掘り下げた長文ガイドで、プロジェクト構成・CLAUDE.md テンプレート・ビルドループの設計・デバッグ戦略・CI 統合（GitHub Actions + XcodeBuildMCP）まで包括的にカバー。2026-08-20 掲載のブログ記事とは別の URL・別コンテンツ。全投稿での初掲載。

- [Best iOS Simulator Skill for Claude Code（claudedirectory.org / August 2026）](https://www.claudedirectory.org/skills/ios-simulator) — Claude Directory による iOS Simulator スキルの 2026年8月版まとめ。現時点でコミュニティに公開されている iOS Simulator 操作スキルを機能・安定性・メンテナンス状況で比較・ランキング。`conorluddy/ios-simulator-skill`・Axiom の iOS スキル・XcodeBuildMCP 付属スキルの三つを軸に解説し、それぞれの強み（シンプル操作向け・監査 + 操作セット・フルビルド連携）を整理している。全投稿での初掲載。

## 💡 今日のおすすめ実践 Tip

**「v2.1.236 の macOS サンドボックス強化 — iOS プロジェクトの機密ファイルを `**/.env` ワイルドカードで確実に保護する」**

v2.1.236 で追加されたサンドボックス改善は iOS 開発者に特に重要です。これまで `**/.env` のようなワイルドカード拒否ルールは、許可された読み取りディレクトリ（例: プロジェクトルート）の内側に置かれたファイルには有効に働かないケースがありました。今回の変更でワイルドカード拒否ルールが許可領域内でも確実に優先されるようになりました。

**iOS プロジェクトで守りたい主な機密ファイル**

```
Secrets.xcconfig       # API キー・アクセストークン
GoogleService-Info.plist  # Firebase 設定
.env                   # ローカル環境変数
.env.local
**/*.p8                # App Store Connect API キー
**/AuthKey_*.p8
```

**`.claude/settings.json` への追記例（機密ファイルの read-deny）**

```json
{
  "permissions": {
    "deny": [
      "Read(**/.env)",
      "Read(**/.env.*)",
      "Read(**/Secrets.xcconfig)",
      "Read(**/GoogleService-Info.plist)",
      "Read(**/*.p8)"
    ]
  }
}
```

**なぜ重要か**

Claude Code はデフォルトでプロジェクトルート以下のファイルを読み取れます。`Secrets.xcconfig` に `STRIPE_SECRET_KEY` や `ALGOLIA_API_KEY` を書いているプロジェクトでは、エージェントがプロジェクト全体を把握しようとした際にこれらのキーを読み取ってしまう可能性があります。Public リポジトリに push する内容と同様に、Claude に渡す情報も意識的に絞ることが重要です。

v2.1.236 以降であれば、上記の `deny` ルールが読み取り許可ディレクトリ内に入れ子になった機密ファイルにも確実に効くようになっています。

```bash
# バージョン確認
claude --version
# 2.1.236 以上であれば対応済み

# 未満の場合はアップデート
claude update
```

**CI 環境での `ANTHROPIC_DEFAULT_MODEL` 活用（v2.1.236 追加）**

fastlane や GitHub Actions から Claude Code を呼び出す場合、新規追加の環境変数でモデルを固定できます:

```yaml
# .github/workflows/ios-review.yml
env:
  ANTHROPIC_DEFAULT_MODEL: claude-sonnet-5  # コストと速度のバランスで固定
```

```ruby
# Fastfile
lane :ai_review do
  ENV["ANTHROPIC_DEFAULT_MODEL"] = "claude-sonnet-5"
  sh "claude --print 'XCTest の失敗ログを確認して修正案を提示して'"
end
```
