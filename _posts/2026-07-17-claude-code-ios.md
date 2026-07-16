---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-17"
date: 2026-07-17 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.211（2026-07-15）](https://code.claude.com/docs/en/changelog) — **権限プレビューの文字偽装攻撃に対するセキュリティ修正**と **Bedrock/Vertex の課金バグ修正**が主な柱。セキュリティ面では、ツール入力に含まれる双方向 Unicode 文字・ゼロ幅文字・類似クォート文字を権限確認ダイアログで無効化し、悪意あるプロンプトが承認ダイアログの見た目を改変できてしまう経路を塞いだ。コスト面では、Bedrock・Vertex・Mantle・Foundry で末尾のシステムコンテキストブロックが毎リクエスト新規入力トークンとして課金されていた回帰バグを修正（Bedrock 経由で XcodeBuildMCP を利用している企業 iOS チームに直接影響）。新機能として `--forward-subagent-text` フラグ（`CLAUDE_CODE_FORWARD_SUBAGENT_TEXT` 環境変数）を追加し、stream-json 出力にサブエージェントのテキスト・推論を含められるようになった。また、auto モードが PreToolUse フックの `ask` 判定を無視して自動承認してしまうバグ、ネストされた `.claude/rules/*.md` が読み込まれないバグも修正。`claude agents` で複数ビルドエージェントをバックグラウンド並走させている場合のフック制御精度が向上する。

## 🛠 GitHub の動き

- [Issue #78177 — Remote Control: TUI コンポーザーにメッセージが届くが Enter が飲まれて送信されない — tmux/ヘッドレス、断続的発生（OPEN / 2026-07-16）](https://github.com/anthropics/claude-code/issues/78177) — tmux やヘッドレス環境で Claude Code を動かしながら iOS アプリから Remote Control でメッセージを送信しようとすると、テキストは TUI の入力欄に届くものの Enter が飲まれて実際には送信されず iOS 側が「送信待ち」のまま固まる問題。XcodeBuildMCP のビルドループを tmux セッション内で走らせ、外出中に iPhone から追加指示を出そうとするときに踏みやすい。暫定回避として `ctrl+j`（改行コード送信）で Enter の代わりに送信できる場合があるとのコメントあり。

- [Issue #69655 — Remote Control: 終了したセッションが "Connected" のまま残りリスト汚染が蓄積（OPEN・👍1・2026-07-16 更新）](https://github.com/anthropics/claude-code/issues/69655) — iOS アプリの Remote Control セッション一覧に、すでに終了・切断されたセッションが "Connected" 状態のまま永続的に残り続け、手動削除もできない問題。1 日複数回 Mac を再起動しながら Claude Code を使う iOS 開発者でゾンビエントリが蓄積しやすい。2026-07-16 にコメントが再活発化しており、v2.1.211 の Remote Control 関連修正で改善が期待されるが未確認。

## 📝 日本語コミュニティ

- [iPhoneからClaude Codeを操作できるアプリ「NeriTerm」をリリースしました（Qiita / nerikeshi）](https://qiita.com/nerikeshi/items/6e50dc84a390feedbc1c) — Tailscale VPN（WireGuard 暗号化）＋自作 Node.js サーバー（Claude Remote Server）構成で iPhone/iPad から Mac 上の Claude Code をリモート操作する iOS アプリ。Tailscale でピアツーピア接続するためコード・認証情報がクラウドリレーを経由しない。公式 Remote Control で発生する "GitHub repository access check failed"（Issue #44805）を回避でき、Xcode プロジェクトルートから直接操作できる点が最大の差別化。通勤中にビルド結果を確認・修正指示を出したり、就寝前にビルドを走らせてベッドから完了確認したりといったユースケースを想定した設計。

- [Xcode の Claude Agent・Codex が settings.json・config.toml でセットアップできるように（Zenn / treastrain）](https://zenn.dev/treastrain/articles/bc9435a36fe498) — **Xcode 26.5 の Enterprise Configuration** を解説した記事。Xcode 26.5 以降、Claude Agent 用 `settings.json` と Codex 用 `config.toml` でアカウント設定ができるようになり、接続先を Claude.ai / ChatGPT の代わりに Amazon Bedrock・Google Vertex AI・Microsoft Foundry へ向けられる。設定ファイルは `~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/` に置いたグローバル設定が全 Xcode セッションに適用される。企業の iOS 開発チームが設定ファイルを Git リポジトリに含めることでチーム全員の Claude Agent バックエンドを一元管理できる点が実務上の大きな利点。

- [AIエージェントを使って実際にアプリ開発→リリースした経験・知見を共有する（Qiita / koher / Swift）](https://qiita.com/koher/items/503cd5a9811ab5683a85) — Claude Code を用いて iOS アプリを実際に App Store リリースした開発者が実務から得た知見を公開した記事。「AI に任せる部分と自分で判断する部分の境界線」「Swift Concurrency と Claude Code の相性」「コンテキスト管理のコツ」など、リリース済みアプリの開発を通じて蒸留した実践的ノウハウをまとめている。

## 🌐 海外コミュニティ / Tips

- [Xcode 26.3 + Claude Agent: Model Swapping, MCP, Skills, and Adaptive Configuration（fatbobman.com）](https://fatbobman.com/en/posts/xcode-263-claude/) — Xcode 26.3 の Claude Agent に対してモデルを `settings.json` で動的に切り替える方法（`claude-opus-4-8` / `claude-sonnet-5` 等）、MCP サーバーの追加設定先（`~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/`）、`/think` `/ultrathink` スキルの有効化を網羅した実践ガイド。「XcodeBuildMCP はグローバルの `~/.claude/settings.json` に書き、Xcode MCP は `ClaudeAgentConfig/` 配下に分離する」という 2 MCP 共存時の構成整理が特に参考になる。Adaptive Configuration（プロジェクト属性に応じて設定を自動調整）の使い方も解説。

- [Using Amazon Bedrock as Backend for Xcode 26 Coding Agent（stormacq.com / Part 1 & 2）](https://stormacq.com/2026/02/22/using-amazon-bedrock-as-backend-for-xcode-26-coding-agent-part-2/) — AWS のソリューションアーキテクトが Xcode 26 の Claude Agent の接続先を Amazon Bedrock に切り替える設定手順を 2 回連載で解説。`aws configure` で認証情報を設定後、`settings.json` の `awsProfile` / `awsRegion` を指定するだけで Bedrock 経由に切り替わる。CI/CD パイプラインで IAM ロールを使う場合の設定や、Bedrock のプロビジョンドスループット（PT）によるコスト最適化も言及。treastrain 記事（上記）で紹介した Xcode 26.5 Enterprise Configuration とセットで読むと体系的に理解できる。

## 💡 今日のおすすめ実践 Tip

**「NeriTerm + Tailscale で公式 Remote Control の制約を回避し、Xcode プロジェクトルートからも安定稼働させる」**

公式 `claude remote-control` は Xcode プロジェクトルート（git リポジトリ）から起動すると "GitHub repository access check failed"（Issue #44805）が発生し、iPhone から操作不能になるバグが残っています。nerikeshi 氏の NeriTerm は Tailscale VPN を使うことでこの問題をアーキテクチャ上解決しています。

**1. 公式 Remote Control との比較**

| 項目 | 公式 Remote Control | NeriTerm + Tailscale |
|---|---|---|
| git リポジトリ内起動 | ❌ Issue #44805 発生 | ✅ 問題なし |
| Desktop App 対応 | ❌ Issue #29006 未対応 | ✅ |
| 通信経路 | claude.ai 経由 | Tailscale P2P（クラウドなし） |
| 設定の手間 | 少ない | Tailscale + サーバー設定が必要 |

**2. セットアップ概要（詳細は Qiita 記事参照）**

1. Mac と iPhone 双方に [Tailscale](https://tailscale.com) をインストールし同一アカウントで接続
2. Mac 側で nerikeshi 公開の Claude Remote Server（Node.js）を起動
3. iPhone の NeriTerm アプリでサーバーの Tailscale IP アドレスを登録して接続

**3. `--forward-subagent-text` フラグ（v2.1.211 新機能）との組み合わせ**

NeriTerm で Mac の Claude Code に接続した状態で、サブエージェント（`claude agents`）のテキスト出力をリアルタイムに受け取りたい場合、Mac 側のセッション起動時に以下の環境変数を設定しておくと、iPhone 側でサブエージェントの思考も追えるようになります：

```bash
# Mac 側: サブエージェントのテキスト・推論を stream-json に含める
CLAUDE_CODE_FORWARD_SUBAGENT_TEXT=1 claude --output-format stream-json
```

外出先から XcodeBuildMCP のビルドサブエージェントが何をしているか逐次確認しながら、必要に応じて iPhone から追加指示を出す運用が実現できます。
