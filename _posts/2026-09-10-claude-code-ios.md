---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-10"
date: 2026-09-10 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.267（2026-09-09）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発者に影響する変更が複数。① **`maxEffortLevel` 設定を追加** — Bedrock・Vertex・Foundry 全プロバイダーでエフォートレベルの上限をグローバルに設定できるようになった（CI 環境でコスト上限を設ける際に便利）。② **Remote Control クライアントが古い権限モードを表示するバグを修正** — iPhone の Remote Control 画面で表示される権限状態が実態と乖離していた問題が解消。③ **`claude remote-control` サーバーの認証情報が約30日後に期限切れになるバグを修正** — 長期間 iPhone RC セッションを使い続けていると突然「認証エラー」で切れていた問題が修正済み。④ **大規模セッション再開（>5 MB）で並列ツール呼び出しが落ちるバグを修正** — XcodeBuildMCP でビルドとテストを並列実行するセッションが長期化した際に再開時に落ちていたケースに対応。⑤ **Cowork スケジュールタスクがマネージドサンドボックス要件で起動失敗するバグを修正**。

- **v2.1.266（2026-09-08）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— ホットフィックス。**`CLAUDE_CODE_USE_GATEWAY` 環境変数が API キーと組み合わせてクラウドゲートウェイサインインを強制してしまうリグレッションを修正** — この変数単体では再び無視されるようになった（設定変更不要）。AWS Bedrock や GCP Vertex 経由で Claude Code を iOS CI に組み込んでいる環境で「Not signed in」エラーが出ていた場合は最新版へのアップデートで解消する。

## 🛠 GitHub の動き

- **[Issue #93059 — \[BUG\] iOS Claude モバイルアプリでリポジトリピッカーが「No repositories」を表示する（anthropics/claude-code / OPEN・2026-09-09 作成）](https://github.com/anthropics/claude-code/issues/93059)** — Claude GitHub App をインストールして「All repositories」アクセスを付与しているにもかかわらず、Claude モバイルアプリ（iOS）の「Add repository」ピッカーに「No repositories」が表示されて先に進めない問題。インストール・再インストール後も再現するため、バックエンドのリポジトリインデックス問題（#61019・#34523・#57161 系）の可能性が指摘されている。iOS アプリから直接ワークスペースのリポジトリを追加したい場合に影響が大きい。暫定回避策は Web ブラウザ（claude.ai/code）から追加してモバイルアプリで確認すること。

- **[Issue #76841 — \[BUG\] Routines: 通知を消したらモバイルアプリ内でルーティンのセッションを再度開く方法がない（anthropics/claude-code / OPEN・2026-09-09 更新）](https://github.com/anthropics/claude-code/issues/76841)** — スケジュールルーティンの実行結果がプッシュ通知経由でしか確認できず、通知を見逃した・消してしまったら claude.ai/code/routines をブラウザで開くしかない問題。iOS アプリにルーティン一覧ビュー・実行履歴・通知からのディープリンクの追加を求めるフィーチャーリクエストで、コメントが増えており優先度が上がっている状況。

- **[Issue #32982 — \[BUG\] Remote Control sessions die after ~20 min idle — server TTL ignores keepalives（anthropics/claude-code / OPEN・2026-09-09 更新）](https://github.com/anthropics/claude-code/issues/32982)** — iPhone の Remote Control で20分ほどアイドルするとセッションが死ぬ問題の詳細な根本原因分析（7セッションを実機テストして再現率100%）が投稿された。バグは2つ: ①サーバー側の TTL が keepalive メッセージをセッション活動としてカウントしない ②`CLAUDE_CODE_REMOTE_SEND_KEEPALIVES` フラグの keepalive がモデル処理中にしか動かないため（refcount で制御）アイドル中は止まる。一時的な回避策は「15分ごとに端末から任意のメッセージを送る」でセッションの TTL をリセットする方法のみ。v2.1.267 で一部の RC 修正が入ったため改善している可能性もある（要検証）。

## 📝 日本語コミュニティ

- 該当なし（2026-09-10 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[Claude Code skill for automated Fastlane setup（fastlane/fastlane GitHub Discussions #29838）](https://github.com/fastlane/fastlane/discussions/29838)** — Claude Code スキルとして Fastlane のセットアップを自動化するオープンソース実装の紹介ディスカッション。「Set up Fastlane for my iOS app」「Upload this build to TestFlight」のような自然言語コマンドで、Appfile・Fastfile の生成・Xcode プロジェクトからのバンドル ID 自動抽出・Match（コード署名）・Snapshot（スクショ）・Beta（TestFlight 配布）・Release（App Store 提出）の5スキルを一括セットアップできる。Homebrew 経由インストールを前提とし、Fastlane の標準ディレクトリ構成に従っている。既存のプロジェクトに組み込みやすい設計で、スキルのリポジトリも公開されており iOS チームで応用しやすい。

## 💡 今日のおすすめ実践 Tip

**「iPhone Remote Control のアイドルタイムアウトを回避する実践的な設定」**

Issue #32982 の詳細分析により、iPhone から Claude Code を Remote Control する際にセッションが約20分でタイムアウトする原因がほぼ特定されました。v2.1.267 で RC 関連バグの一部が修正されましたが、根本的なサーバー側 TTL の問題は修正中です。現時点でアイドルタイムアウトを緩和する実践的な対策を紹介します。

**セッションを継続的に「生かす」設定（Claude Code を起動するマシン側）**

```bash
# .zshrc / .bashrc に追記
# Remote Control 用: 15分ごとにダミーメッセージで TTL をリセット
export CLAUDE_CODE_REMOTE_SEND_KEEPALIVES=1
```

ただし Issue #32982 の分析によると `SEND_KEEPALIVES=1` だけでは不十分（refcount バグで アイドル中に keepalive が止まる）。より確実な回避策として：

**tmux と watch コマンドを組み合わせてセッションを維持する**

```bash
# Claude Code を tmux セッションで起動した後
# 別のペインで10分ごとに silent なヘルスチェックを送る
# （実際にはメッセージは送らず、ウィンドウのアクティビティで TTL をリセット）
watch -n 600 "echo 'keepalive ping' > /tmp/cc_ping && date >> /tmp/cc_ping_log"
```

**v2.1.267 の RC 修正との組み合わせ**

今リリースで「Remote Control クライアントが古い権限モードを表示するバグ」と「`claude remote-control` サーバーの認証情報が約30日後に期限切れになるバグ」が修正されました。これらの修正でセッションの安定性が上がっている可能性があるため、まず最新版にアップデートして状況を確認してから上記の回避策を組み合わせてください。

```bash
# 最新版へのアップデート
npm install -g @anthropic-ai/claude-code
claude --version  # v2.1.267 以上を確認
```

iPhone から Claude Code で iOS ビルドを監視するような長時間セッション（夜間 CI 監視など）では、この問題の影響を受けやすいので注意してください。
