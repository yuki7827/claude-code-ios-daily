---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-16"
date: 2026-05-16 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.142 (May 14)](https://github.com/anthropics/claude-code/releases/tag/v2.1.142) — **Fast mode が Opus 4.7 にアップグレード**（旧 Opus 4.6。`CLAUDE_CODE_OPUS_4_6_FAST_MODE_OVERRIDE=1` で旧挙動に固定可能）。`claude agents` に複数の新フラグを追加：バックグラウンドセッションに対して `--add-dir`（スコープ追加）・`--settings`（設定ファイル指定）・`--mcp-config`（MCP 設定）・`--plugin-dir`（プラグインディレクトリ）・`--permission-mode`・`--model`・`--effort`・`--dangerously-skip-permissions` が設定可能に。iOS ビルド専用の MCP 構成（XcodeBuildMCP のみ）を持つバックグラウンドセッションを起動コマンド一発で構成できるようになった。root-level の `SKILL.md` のみ持つプラグイン（`skills/` サブディレクトリなし）もスキルとして認識されるように改善。`/plugin details` と `claude plugin details` で LSP サーバー提供状況も表示。バックグラウンドセッションが既存 git worktree を認識しないバグ・macOS スリープ/ウェイク後のデーモンクラッシュ・Claude-in-Chrome 拡張でのクラッシュループを修正。

## 🛠 GitHub の動き

- [jamesrochabrun/ClaudeCodeSDK](https://github.com/jamesrochabrun/ClaudeCodeSDK) (Swift, v1.2.4) — macOS アプリから Claude Code を呼び出す Swift フレームワーク。ヘッドレスモード・Agent SDK バックエンドの 2 系統をサポートし、MCP ツール統合・マルチターン会話・レート制限などを提供。**注意**: iOS サンドボックスでは外部プロセス実行が禁止されるため、対応は macOS 13+ のみ。iOS 開発ツール（CI スクリプト・開発用 Mac アプリ）向けに活用できる。

## 📝 日本語コミュニティ

- [Claude Code 5月アップデート総括 — skills検索 / async hooks / HTTP hooks を個人開発パイプラインへ組み込む](https://qiita.com/creolab_dev/items/5f058d93b1f88c43f339) (creolab_dev, Qiita) — 5月の「静かだが構造的に重要な」3点のアップデートを総括。①スキル名・説明文からファジー検索が可能になりスキルが 100 本超えても `/` メニューから即座に呼び出せる、②`async: true` フラグで Discord 通知・画像生成などのフック処理をバックグラウンド実行して本流をブロックしない（実測 30〜60 秒の短縮）、③ HTTP hooks で外部 Web サーバーに POST できる（内部 CI 起動・Datadog メトリクス送信等）。個人開発から小チーム規模へのスケールを念頭に置いた実践的まとめ。

- [SwiftData × Claude Code で永続化層を設計する — @Model設計からマイグレーションまで実務で詰まらないための完全ガイド](https://qiita.com/kotaro_ai_lab/items/119901d60f34e07da801) (kotaro_ai_lab, Qiita) — SwiftData で躓きやすいポイントを Claude Code と協働する前提で整理した実践ガイド。CLAUDE.md に規約（`@Model` クラスはデフォルト値必須・リレーションシップは `@Relationship(deleteRule: .cascade)`・スキーマバージョニングは `VersionedSchema` 使用・ViewModel は `@Observable @MainActor final class` など）を書き込んでおくことで Claude Code が一貫したコードを生成。軽量マイグレーションは新プロパティにデフォルト値がないと自動移行が失敗する点（Apple 公式でも明記）など、プロジェクト開始前に抑えるべき落とし穴を網羅。

- [Xcode 26.3 で iOS 開発の AI 活用はどう変わるのか ── タップルでの導入方針とあわせて](https://developers.cyberagent.co.jp/blog/archives/62551/) (CyberAgent Developers Blog) — CyberAgent の恋愛マッチングアプリ「タップル」チームによる Xcode 26.3 × Claude Code 導入方針。ファイル追加・削除時は Claude Code の標準 Write ではなく Xcode MCP の `XcodeWrite`/`XcodeRM` を使うことでプロジェクトへの自動登録とビルドターゲット認識を保証。現状の Xcode MCP は `tabIdentifier` しか指定できず複数スキームを持つプロジェクトでのスキーム指定ビルドは不可のため段階的導入を採用。実プロジェクトでの制約と対処法がリアルに記述されており、チーム導入の参考になる。

## 🌐 海外コミュニティ / Tips

- [Claude Code Plugins for iOS Teams — Automation, Agents, and Guardrails](https://medium.com/@wesleymatlock/claude-code-plugins-for-ios-teams-automation-agents-and-guardrails-221a68eb57d5) (Wesley Matlock, Medium) — iOS チーム向けのプラグイン設計論。プラグインはスキルの集合ではなく「チームの暗黙のルール（アーキテクチャ・命名・リリース儀式・CI 前提）を埋め込んだ振る舞いのバンドル」という視点で整理。最も効果的なガードレールは早期に決めた人間側のポリシー（デフォルト保守的・明示的呼び出し・意図の可視化・安全でなくなった自動化を削除する意思）であり、ガバナンスはツールに丸投げできないと主張。Conservative defaults の具体例（`hard_deny` で force push と `rm -rf DerivedData` を絶対ブロック等）も掲載。

## 💡 今日のおすすめ実践 Tip

**v2.1.142 の `claude agents --mcp-config` で iOS ビルド専用バックグラウンドセッションを立ち上げる**

v2.1.142 で `claude agents` に追加された `--mcp-config` と `--add-dir` を使うと、メインセッションとは独立した MCP 構成のバックグラウンドセッションを起動できます。XcodeBuildMCP だけを有効化した「ビルド専用エージェント」を走らせながら、フォアグラウンドでは別の機能実装を進める構成が手軽に組めます。

```bash
# XcodeBuildMCP 専用の MCP 設定ファイルを用意
cat > /tmp/xcode-mcp.json << 'EOF'
{
  "mcpServers": {
    "xcodebuild": {
      "command": "npx",
      "args": ["-y", "xcodebuildmcp@latest", "mcp"]
    }
  }
}
EOF

# バックグラウンドセッションを XcodeBuildMCP 専用設定で起動
claude --bg \
  --mcp-config /tmp/xcode-mcp.json \
  --add-dir ~/Projects/MyApp \
  "TaskListView の Swift Testing テストがすべて GREEN になるまでビルドと修正を繰り返してください。10回失敗したら停止して報告してください"
```

`claude agents` ビューで進捗を確認しつつ、別ウィンドウでは通常のメインセッションを使えます。メインセッションには XcodeBuildMCP を入れず、ビルドセッションにだけ有効化することでコンテキスト消費を最小化できるのがポイントです。`--permission-mode` に `default` または `auto` を渡してセッションの自律度も制御できます。
