---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-16"
date: 2026-09-16 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.271（2026-09-14）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発者に関係する主な変更が複数。① **Remote セッションでの fast mode 対応** — クラウド・セルフホスト両ランナーで fast mode が有効になり、GitHub Actions 等の iOS CI パイプライン上で Claude Code を動かす際の応答速度が向上。② **エージェント frontmatter に `omitClaudeMd`** — サブエージェント定義の frontmatter に `omitClaudeMd: true` を指定すると、ユーザー・プロジェクト・ローカルの CLAUDE.md を一切読み込まずに動くエージェントを作れるようになった。iOS ビルドやテスト専用の single-purpose サブエージェントをプロジェクト設定に縛られない形で定義する際に便利。③ **Bash の `allowed_domains` がコマンドレベルで指定可能に** — オートモードのサンドボックス環境で Bash・PowerShell・Monitor ツールのコマンドごとに許可ドメインを絞れるようになった。Apple Developer API（api.appstoreconnect.apple.com 等）への通信だけを許す設定が書きやすくなる。④ **`--accept-command <sha256>` フラグ** — プラグインインストール時に前回実行したコマンドのハッシュで自動承認できるようになった。CI 上での XcodeBuildMCP 等のインストールを非対話的に実行する際に役立つ。

- **v2.1.272 / v2.1.273（2026-09-15）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— v2.1.272 はバグ修正のみ。v2.1.273 の主な変更: ① **LLM ゲートウェイへのリクエストヒントヘッダー追加**（`CLAUDE_CODE_GATEWAY_HINT_HEADERS=1` でオプトイン） — `x-claude-code-agent-type` 等のヘッダーをゲートウェイに送信することでルーティング・モニタリングが容易になる。企業内ゲートウェイ経由で Claude Code を iOS CI に組み込む際に役立つ。② **Remote Control で `--remote-control` セッションのフォーク** — `--remote-control` で起動したセッションをバックグラウンドセッションとしてフォーク実行できるようになった。iPhone から長時間の iOS ビルドジョブをキックしてバックグラウンドで動かし続ける用途に対応。

## 🛠 GitHub の動き

- **[Issue #94433（2026-09-15 作成 / OPEN）— iOS Simulator パネル: macOS 27.0 で起動直後に SIGABRT クラッシュ（CoreImage/Metal 初期化）](https://github.com/anthropics/claude-code/issues/94433)** — macOS 27.0 (26A428) + Xcode 27.0 (27A266a) 環境で `claude-ios-sim` ヘルパーがパネル起動のたびに CoreImage の Metal コンテキスト初期化中（`CI::MetalContext::init()` → `_MTLDevice recordBinaryArchiveUsage:`）に SIGABRT でクラッシュ。10 分間で 21 件のクラッシュレポートが生成されるほど再現性が高く、iOS 26.1〜27.0 のすべてのランタイムで発生する。#92442（macOS 27 beta Metal/CoreImage クラッシュ）と根本的には同系統の問題だが、GA ビルドで改めて報告されたもの。Xcode 27 では `Simulator.app` が廃止されているため、Claude パネルが唯一のライブビューであり影響が大きい。**暫定回避策**: `xcrun simctl io <udid> screenshot` / `xcrun simctl recordVideo` で撮影し、MCP の `control` ツール（tap / swipe / launch）は引き続き動作することを活用する。

## 📝 日本語コミュニティ

- 該当なし（2026-09-16 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[conorluddy/ios-simulator-skill — Claude Code 向け iOS Simulator スキル（xclaude-plugin とは別の専用スキルリポ）（github.com/conorluddy）](https://github.com/conorluddy/ios-simulator-skill)** — xclaude-plugin（2026-09-12 既報）の作者 conorluddy による別リポジトリ。こちらは「スキル」として切り出した 29 スクリプト構成で、`xcodebuild` によるビルド・テスト（段階的エラー開示でデフォルト 3〜5 行に圧縮）と xcrun simctl / idb によるシミュレーター操作（WCAG アクセシビリティ監査・Core Data/SwiftData モデル検査・GPS シミュレーション・権限管理 15 サービス）を 1 スキルで賄う。2026-09-11 にマージされた PR #99 で Xcode 27 の simctl プライバシーサービス名（`media-library`・`contacts-limited` 等）に対応済み。インストールは `/plugin marketplace add conorluddy/ios-simulator-skill` → `/plugin install ios-simulator-skill@conorluddy` の 2 コマンド。macOS 26.x + Xcode 26.x 環境で iOS Simulator ライブパネルが使えない場合の代替として有力。

## 💡 今日のおすすめ実践 Tip

**「`omitClaudeMd: true` で iOS CI 専用のクリーンなサブエージェントを定義する（v2.1.271 〜）」**

v2.1.271 で追加されたエージェント frontmatter の `omitClaudeMd: true` を使うと、プロジェクト CLAUDE.md・ローカル設定を一切継承しない純粋な CI サブエージェントを作れます。iOS ビルドやテスト自動化では「CLAUDE.md に書いたシミュレーター制約や Xcode 27 回避策」が CI 環境では邪魔になることがあるため、この設定が役立ちます。

**ステップ 1: CI 専用エージェント定義ファイルを作成**

```markdown
---
omitClaudeMd: true
description: "iOS CI agent — builds, tests, reports. Reads no user/project CLAUDE.md."
tools:
  - Bash
  - Read
  - Write
---

You are a CI build agent for an iOS project.
Always use `xcodebuild -scheme MyApp -destination 'platform=iOS Simulator,...'`.
Report results as JSON to stdout.
```

**ステップ 2: GitHub Actions ワークフローから呼び出す**

```yaml
- name: Run Claude CI agent
  run: |
    claude --agent .claude/agents/ios-ci-agent.md \
      "Build the app for iPhone 18 Pro Max simulator and output test results as JSON."
  env:
    ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

**ポイント**

- `omitClaudeMd: true` があるため、CI ランナー上に CLAUDE.md が存在しても読み込まれない。プロジェクト開発者向けの「macOS 27 制約」や「XcodeBuildMCP 使用指示」が CI エージェントを混乱させる心配がなくなる。
- v2.1.271 の `allowed_domains` と組み合わせると、Apple Developer API への通信のみを許可するサンドボックス設定を Bash コマンドレベルで制御できる。
- fast mode（Remote セッション）を有効にすると、同じ CI ジョブの壁掛け時間をさらに短縮できる。

CI サブエージェントと開発者向けエージェントを明確に分離することで、「CI では動くのに手元では動かない」「手元設定が CI を壊す」という典型的な問題を構造的に防げます。
