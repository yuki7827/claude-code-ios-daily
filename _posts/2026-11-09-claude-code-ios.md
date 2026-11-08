---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-09"
date: 2026-11-09 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Apple 純正 Xcode プラグインが Claude Code 公式マーケットプレイス (claude-plugins-official) に登録（2026-11-06, PR #6338）](https://aicoder.com/news/news-20261106-apple-xcode-plugin-claude-code-marketplace-official) — `claude plugin install xcode@claude-plugins-official` の 1 コマンドで導入可能。機能はビルド・テスト・シミュレーター操作・実機ドライブ・SwiftUI プレビュー描画・ビルドログ読み取り・Apple 開発者ドキュメント検索。要件は最新 Xcode（Xcode 26.3 以降）と Xcode Intelligence 設定での外部エージェント許可。プラグイン本体はローカルの Xcode インストールにリンクされる形式で、マーケットプレイスのリポジトリにはソースを含まない。

## 🛠 GitHub の動き

- 該当なし（2026-11-09 時点で、直近 5 件に未掲載の新規 iOS / Xcode / Swift 関連 Issue・PR は確認できず）

## 📝 日本語コミュニティ

- [Claude Code にシミュレーターを渡したら、自分でタップ・スクリーンショットし始めた（Zenn / shimo4228、dev.to にクロスポスト）](https://dev.to/shimo4228/i-gave-claude-code-a-simulator-and-it-started-tapping-and-taking-screenshots-on-its-own-57bj) — XcodeBuildMCP を設定すると Claude Code がアプリの UI を自律的にタップしてスクリーンショットで動作確認するループを回し始めた体験レポート。ビルド → インストール → タップ → 確認のサイクルを人手を介さずに繰り返した際の挙動と注意点が実測値込みで整理されている。

## 🌐 海外コミュニティ / Tips

- [Letting Claude Code Drive Xcode: Setting Up the Xcode MCP Server（martiancraft.com）](https://martiancraft.com/blog/2026/11/xcode-mcp-server-setup-guide/) — Xcode 26.3 以降の Intelligence 設定（Settings → Intelligence → External Agents）から Claude Code にプロジェクトアクセスを許可するセットアップ手順の解説。`~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/.claude.json` の役割や、Xcode 外部から起動した Claude Code の PATH 制限への対処法も含まれる。

- [Apple's Xcode Plugin for Claude Code: What It Does（cellcog.ai）](https://cellcog.ai/blog/apple-xcode-plugin-claude-code/) — Apple 純正プラグインの仕組みを解説した記事。プラグインはマーケットプレイスに本体ファイルを持たず、`/plugin install` 時にローカルの Xcode インストール済みバイナリを参照する構成になっている。Xcode バージョンを上げるとプラグインの機能も追従する一方で、Xcode をインストールしていないマシン（CI コンテナなど）では利用不可という制限も整理されている。

## 💡 今日のおすすめ実践 Tip

**Apple 純正 Xcode プラグインの導入から有効化まで**

2026-11-06 に公式マーケットプレイスへ登録された Apple の Xcode プラグインを Claude Code で使えるようにするための手順：

```bash
# 公式マーケットプレイスが未登録の場合（初回のみ）
claude plugin marketplace add anthropics/claude-plugins-official

# Xcode プラグインをインストール
claude plugin install xcode@claude-plugins-official
```

インストール後、Xcode 側で外部エージェントへのアクセスを許可する必要がある：

1. Xcode を開き、**Settings → Intelligence → External Agents** を開く
2. Claude（または Anthropic）のエントリを **Allow** に設定
3. プロジェクトを開いた状態で Claude Code を起動

有効化後は Claude Code から `Build`, `RunTests`, `OpenSimulator`, `CaptureSwiftUIPreview` などのツールが呼べるようになる。**SwiftUI プレビューのキャプチャ**がエージェントループに組み込めるのが純正プラグインの大きな強みで、コード変更 → プレビュー確認 → 再修正の自律ループが MCP だけでなくプラグイン経由でも実現できる。ただし macOS 上の Xcode インストールが前提のため、ヘッドレス Linux CI では利用できない点に注意。
