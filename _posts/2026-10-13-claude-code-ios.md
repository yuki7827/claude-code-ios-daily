---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-13"
date: 2026-10-13 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-13 時点で v2.1.296 が最新。直近リリースは 2026-10-11 キャッチアップ済み）

## 🛠 GitHub の動き

- 該当なし（2026-10-13 時点で新規 iOS / Xcode / Swift 関連 Issue の追加・クローズは確認できず）

## 📝 日本語コミュニティ

- **[Xcode 27 の公式スキルを Claude Code から使ってみた（dev.classmethod.jp）](https://dev.classmethod.jp/articles/xcode-agent-skills-claude-code/)** — Xcode 27 が公式に「エージェントスキル」のエクスポート機能を追加したことを受け、そのスキルを Claude Code プラグインとして読み込む手順を検証した記事。`xcodebuild`・iOS Simulator・Instruments との連携ツールが Claude Code から直接呼び出せるようになり、プロジェクトルートに配置した `.mcp.json` を共有するだけでチームメンバー全員が同じ設定を使える点が実践的。ヘッドレス MCP サーバー（`xcrun mcp-server`）との組み合わせにも触れており、CI/CD ランナーでの応用が見込める。

## 🌐 海外コミュニティ / Tips

- **[Xcode 27 の MCP サーバーをヘッドレスで Claude Code から使う方法（mehmetbaykar.com）](https://mehmetbaykar.com/posts/how-to-run-xcode-27-mcp-server-headless-with-claude-code/)** — Xcode 27.0 beta 5 で追加されたヘッドレス `xcrun mcp-server` を Claude Code に接続する手順を解説。従来の `xcrun mcpbridge` が Xcode ウィンドウを開いた状態でないと動作しなかったのに対し、`xcrun mcp-server` は「ウィンドウレス Xcode エンジン」として常駐するためターミナルのみの環境でも利用できる。GitHub Actions や Xcode Cloud の CI ランナーで Claude Code エージェントが `xcodebuild`・シミュレーター・Instruments に触れるようになるアーキテクチャを具体的なコマンドで示している。

- **[Apple Developer Forums — "Really happy with Xcode 27 Beta 1 + Claude Agent results"（developer.apple.com/forums/thread/834577）](https://developer.apple.com/forums/thread/834577)** — Xcode 27 beta 1 のエージェントモードで Claude を使った開発者のフィードバックスレッド。SwiftUI のコンポーネント生成や UI バグ修正の結果は「想定より優秀」との声がある一方、トークン消費量が非常に多くコスト管理が課題だという指摘も。Instruments を使ったパフォーマンスボトルネックの自動検出（Agent Mode の目玉機能）については beta 段階で動作が安定しないとの報告も含む。

## 💡 今日のおすすめ実践 Tip

**Xcode 27 のヘッドレス MCP サーバーを Claude Code CI に組み込む**

Xcode 27.0 beta 5 以降で追加された `xcrun mcp-server` を使うと、Xcode の GUI を開かない CI 環境でも Claude Code エージェントが Xcode ツールチェーン全体にアクセスできるようになる。

```bash
# 1. ヘッドレスサービスを有効化（管理者権限が必要）
sudo xcrun mcp-server enable

# 2. サービスを起動・状態確認
xcrun mcp-server start
xcrun mcp-server status
```

Claude Code への接続はプロジェクトスコープで登録しておくと `.mcp.json` に書き出され、チーム共有できる。

```bash
claude mcp add --scope project xcode-headless \
  --env DEVELOPER_DIR=/Applications/Xcode-beta.app/Contents/Developer \
  -- xcrun mcpbridge
```

CI/CD で使う場合の注意点:
- `DEVELOPER_DIR` は Xcode のフルパスで明示しないと複数バージョンが混在する環境で誤ったツールを呼ぶ
- beta 版はコマンドが変わる可能性があるため、stable リリース後に再確認すること
- ヘッドレスモードでの iOS Simulator 起動は `xcrun simctl boot` と組み合わせて使う

この構成が安定すれば、GitHub Actions 上で「Claude Code がビルド → テスト → スクリーンショット比較 → PR コメント」まで一気通貫で自動化できるようになる。
