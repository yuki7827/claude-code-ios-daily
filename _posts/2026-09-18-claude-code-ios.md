---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-18"
date: 2026-09-18 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **[v2.1.274（2026-09-17）リリース](https://code.claude.com/docs/en/changelog)** — 09-17 記事の「v2.1.273 が最新」との確認直後に公開されたバージョン。iOS 開発者に直接関係する主な変更: ① **`CLAUDE_CODE_MCP_STARTUP_WAIT_MS` 環境変数を追加** — 非インタラクティブターンが MCP サーバーの接続完了を待つ最大時間をミリ秒単位で指定可能（`0` = 待機なし）。XcodeBuildMCP や Xcode 26.3 MCP サーバーは起動に数秒かかる場合があり、iOS CI パイプラインでタイムアウトが発生していた環境で有効。② **HTTP+SSE レガシープロトコルの MCP サーバー接続失敗を修正** — XcodeBuildMCP の一部バージョンは SSE ベースのトランスポートを使用しており、接続が安定する。③ **Bedrock・Vertex・Foundry が MCP 2026-07-28 プロトコルをデフォルト使用** — AWS/GCP 経由で Claude Code を iOS CI に組み込んでいる環境でも最新 MCP ネゴシエーションが自動適用（オプトアウト: `MCP_SDK_GENERATION=v1`）。④ **クリティカルメモリ使用時に可視警告を表示** — 大規模 Xcode プロジェクトを長時間セッションで扱う場合、クラッシュ前に再起動を促す警告が出るようになった。⑤ **`unexpected tool_use_id` 400 エラーでセッションがループするバグを修正** — 長期 iOS 開発セッションで発生していたスタック問題が解消。

## 🛠 GitHub の動き

- 該当なし（2026-09-18 時点で anthropics/claude-code に新規の iOS / Swift / Xcode 関連 Issue は確認できず。09-17 記事掲載の Issue #94767・#94360 は引き続き OPEN）

## 📝 日本語コミュニティ

- 該当なし（2026-09-18 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- 該当なし（2026-09-18 時点で新規の注目記事・投稿は確認できず）

## 💡 今日のおすすめ実践 Tip

**「`CLAUDE_CODE_MCP_STARTUP_WAIT_MS` で XcodeBuildMCP の CI タイムアウト問題を解消する（v2.1.274 〜）」**

v2.1.274 で追加された `CLAUDE_CODE_MCP_STARTUP_WAIT_MS` は、XcodeBuildMCP を iOS CI で使う際の「MCP サーバーが起動し切る前に Claude Code が応答を返してしまう」問題に対処するための設定です。

**典型的な症状（v2.1.273 以前）**

```
[MCP] xcodebuildmcp: connecting...
Tool call failed: tool not found (xcodebuild_build)
```

xcodebuildmcp の起動に 2〜5 秒かかる環境で、Claude Code が接続完了を待たずに最初のターンを実行してしまい、ツール呼び出しが失敗する。

**解決方法: 環境変数を設定する**

```bash
# GitHub Actions ワークフロー例
- name: Run Claude Code with XcodeBuildMCP
  env:
    ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
    CLAUDE_CODE_MCP_STARTUP_WAIT_MS: 8000   # 最大 8 秒待機
  run: |
    claude --mcp-server xcodebuildmcp \
      "Build the MyApp scheme for iPhone 17 Pro simulator and report any build errors."
```

**ポイント**

- 値はミリ秒単位。手元の Mac では 3000（3 秒）程度で十分なことが多いが、CI ランナー（コールドスタート）では 8000〜10000 推奨。
- `0` を設定すると「待機なし」になり、以前の挙動に戻る。
- この設定は非インタラクティブターンにのみ効く（インタラクティブセッションでは MCP サーバーが接続されてから操作するため不要）。
- MCP 2026-07-28 プロトコル移行（③の変更）と組み合わせることで、Bedrock / Vertex 環境でも同じ設定が有効になった。

この 1 行の環境変数設定で、XcodeBuildMCP を iOS CI に組み込む際の最も多い初期化ミスを解消できます。
