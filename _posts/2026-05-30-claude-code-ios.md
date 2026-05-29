---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-30"
date: 2026-05-30 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.157（2026-05-29）](https://github.com/anthropics/claude-code/releases) — **`.claude/skills` ディレクトリのプラグインがマーケットプレイス登録なしに自動ロード**されるようになった（v2.1.157 の最大のポイント）。チーム内スキルをリポジトリに置くだけで全員に自動配布できる。`claude plugin init <name>` コマンドでスキルのスキャフォールディングも追加。`EnterWorktree` がセッション中に Claude 管理の worktree 間をシームレスに切り替え可能になり、マルチブランチ iOS 開発での並列作業が柔軟に。`/terminal-setup` が VS Code / Cursor / Windsurf で GPU アクセラレーションを自動無効化し文字化けを解消。シミュレータスクリーンショット等で非対応画像フォーマットをペーストした際にリクエストがクラッシュする問題も修正。

- [Claude Code v2.1.156（2026-05-29）](https://github.com/anthropics/claude-code/releases) — Opus 4.8 で thinking blocks が変更されて API エラーになるホットフィックス。`/effort xhigh` で拡張思考を多用している iOS チームは早めのアップデートを推奨。

## 🛠 GitHub の動き

- [XcodeBuildMCP Issue #220: Claude Code silently drops all tools due to MCP SDK 1.26.0 fields](https://github.com/getsentry/XcodeBuildMCP/issues/220) — MCP SDK v1.26.0 が追加した `instructions` / `execution` / `annotations` フィールドを Claude Code のクライアントが適切に処理できず、XcodeBuildMCP の全ツールが無言でドロップされる問題が報告中。サーバーが "Connected" と表示されるのにツール数が 0 になる症状で iOS 開発者には致命的。回避策は `instructions` や `$schema` フィールドを除去する Node.js プロキシスクリプトを `.mcp.json` に挟む方法（Issue 内に実装例あり）。

- [mintuz/claude-plugins](https://github.com/mintuz/claude-plugins) — Web と iOS 双方をカバーする Claude Code エージェント・スキル個人コレクション。v2.1.157 の `.claude/skills` 自動ロード機能に対応した配置構造になっており、`.mcp.json` の追加設定なしでチーム展開の参考になる構成。

## 📝 日本語コミュニティ

- 該当なし — 本日時点で新規の iOS × Claude Code 日本語記事は確認できず。

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 Using Claude Agent for Swift & SwiftUI — Part 3 (iOS 18 or Later)](https://medium.com/@chinthaka01/agentic-coding-in-xcode-26-3-8b62c1e46bf2) (Chinthaka Parana, Medium) — Xcode 26.3 Claude Agent 連載の第 3 弾。iOS 18+ を deployment target とするプロジェクトへの適用に特化し、Observation フレームワーク・SwiftData・iOS 18 Animation API とエージェントコーディングの組み合わせを解説。前 2 回で構築したワークフローを iOS 18 固有 API に対応させる応用編。

- [Claude Code Plugins for iOS Teams — Automation, Agents, and Guardrails](https://medium.com/@wesleymatlock/claude-code-plugins-for-ios-teams-automation-agents-and-guardrails-221a68eb57d5) (Wesley Matlock, Medium) — iOS チームで Claude Code プラグインを設計・運用する際の指針。`disallowed-tools` によるロールベース制限・`SessionStart` フックによる環境自動設定・サブエージェントへのビルドログ分離の 3 本柱で「ガードレール付きプラグイン設計」を構成する方針を解説。チームへの段階導入時の参考になる。

## 💡 今日のおすすめ実践 Tip

**v2.1.157 の `.claude/skills` 自動ロードで iOS チームスキルをリポジトリ管理する**

v2.1.157 からプロジェクト直下の `.claude/skills/` に置いたスキルファイルが、マーケットプレイス登録なしに自動ロードされます。チーム専用のビルド・テスト・リリーススキルをリポジトリで一元管理・配布できます。

```bash
# スキャフォールドで新スキルを作成
claude plugin init ios-build-check

# 作成されるファイル構造
.claude/
  skills/
    ios-build-check.md
```

スキルファイルの最小構成例：

```markdown
---
description: "iOS シミュレータビルド確認スキル"
disallowed-tools:
  - Write
  - Edit
---

XcodeBuildMCP の build_sim ツールだけを使って
iOS アプリをシミュレータビルドし、エラーを JSON で報告してください。
スキーム: ${SCHEME:-MyApp}
シミュレータ: ${SIMULATOR:-iPhone 17}
```

`SessionStart` フックで `reloadSkills: true` を返すよう設定しておくと、`git clone` 後に `claude` を起動するだけでチームのスキルセットが全員に揃います。CI 環境でも同じスキルが自動適用されるため、ローカルと CI でのエージェント挙動の差異を最小化できます。
