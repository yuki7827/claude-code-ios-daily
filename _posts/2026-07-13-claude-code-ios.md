---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-13"
date: 2026-07-13 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code changelog（最新: v2.1.207 / 2026-07-10）](https://code.claude.com/docs/en/changelog) — 本日時点で v2.1.207 より新しいリリースはなし。昨日（07-12）に紹介した Auto モード Bedrock/Vertex 対応・ターミナルフリーズ修正が引き続き最新。**Fable 5 週間上限 50% 増プロモーションは明日（日本時間 7/14 10AM JST）で終了予定** — 大型 iOS ビルドタスクがあれば今日中の投入を。

## 🛠 GitHub の動き

- [Issue #44805 — Remote Control: git リポジトリで iPhone アプリからメッセージ送信が「GitHub repository access check failed」で失敗する（open・👍29件）](https://github.com/anthropics/claude-code/issues/44805) — `cd` で GitHub リモートを持つ git リポジトリに入った状態で `claude remote-control` を起動すると、環境登録自体は HTTP 200 で成功するが、Claude iOS アプリからメッセージ送信時に「GitHub repository access check failed — re-authorize GitHub in settings」エラーが発生して操作不能になるバグ。非 git ディレクトリでは正常動作する。根本原因は環境登録リクエストに `git_repo_url` が含まれると iOS アプリが GitHub リポジトリアクセスチェックを実行する設計で、Remote Control はローカル実行なのにクラウド側の GitHub 認証が要求されてしまう点。iOS 開発者が Xcode プロジェクトのルートから Remote Control を使うと必ず遭遇するパターンで、2026-07-12 にもコメントが追加されている。暫定回避策は非 git ディレクトリ（例：`~`）から `claude remote-control` を起動し、CLAUDE.md でプロジェクトパスを指示する方法。

- [Issue #29006 — Claude Desktop App のセッションを iPhone / スマホから Remote Control できるようにしてほしい（open・👍143件）](https://github.com/anthropics/claude-code/issues/29006) — CLI 版の `claude remote-control` は Claude iOS/Android アプリや claude.ai/code から接続できるが、Claude Desktop App 経由のセッションは Remote Control に対応しておらず、iPhone からつなぐ手段がない。Desktop App のセッションも同様に「ファイルシステム・MCP サーバー・ツール設定はすべてローカル保持」なので技術的に遠隔操作は可能なはずという要望。143 upvotes は Direct Remote Control 関連の feature request 中で最多クラス。2026-07-12 にコメントが活発化しており、今後対応が期待される。Desktop App でエージェントを走らせ iPhone から監視・指示したい iOS 開発者に特に関係する。

- [TacticSpaceTech/TacticRemote v1.8.2 — iPhone/iPad から Claude Code をリモート操作する iOS アプリ](https://github.com/TacticSpaceTech/TacticRemote) — Claude Code・OpenAI Codex・Sourcegraph Amp の 3 エージェントに対応したモバイルコントロール iOS アプリ（App Store: [Tactic Remote: AI Coding](https://apps.apple.com/us/app/tactic-remote-ai-coding/id6758008464)）。**v1.8.2 の主要追加機能**はチャット履歴の永続化（再接続後に会話履歴が自動復元）と長いストリーミング応答中の画面フリーズ解消。iOS 固有の機能としてロック画面・Dynamic Island・ホーム画面でのリアルタイムステータスウィジェット、FaceID/TouchID アプリロック、ハードウェアキーボード対応の iPad 分割ビュー、25 言語クラウド音声入力を備える。Git diff 確認・ファイルブラウジング・ターミナルストリーミング（ANSI カラー対応）も搭載で、Mac に張り付かずに iOS プロジェクトのビルドステータスを iPhone で確認しながら別作業できる。上記 #29006 の問題（Desktop App 非対応）がある公式 Remote Control の代替として、tmux ＋ Node.js WebSocket サーバー経由で接続する構成を取るため Desktop App のセッションにも対応。

## 📝 日本語コミュニティ

- [【2026年7月最新】Claude Code を外出先から使う 3 つの方法｜スマホで AI エージェントをリモート操作する全手順（AI鬼管理）](https://genai-ai.co.jp/ai-kanri/blog/cc-yt-remote-access-27/) — 2026 年 7 月版として、①公式 Remote Control（`claude remote-control` コマンド）、②Tactic Remote などサードパーティアプリ、③VPS にヘッドレスホストして SSH 接続する方法の 3 通りを比較した解説記事。各方法の対応プラン・セキュリティモデル・iOS 開発用途での向き不向きを整理しており、「公式 RC だけでは Desktop App 非対応・git リポジトリでバグあり」という現状の制約も記載している（要 URL アクセスで最新内容を確認）。

- [Claude Code をスマホで操作する 2 つの方法｜リモートコントロールと Web 版の違い【実際にハマった話】（AI Crew）](https://www.ai-crew-school.jp/blog/claude-code-remote-control/) — Remote Control（CLI セッションをスマホ継続）と claude.ai/code のモバイルブラウザ版の違いをユーザー体験ベースで整理した記事。「ハマった話」とあるように、上記 Issue #44805 の git リポジトリ問題や iOS アプリの接続が不安定になるケースを実体験から解説している模様。Remote Control を初めて試す iOS 開発者への注意喚起として参考になる（要 URL アクセスで最新内容を確認）。

## 🌐 海外コミュニティ / Tips

- [Run Claude Code on a Cloud VM or Headless Linux Server and Control It from Your iPhone（Tactic Remote Blog）](https://tacticremote.com/blog/2026-05-16-run-claude-code-on-remote-server-control-from-phone/) — Tactic Remote 公式ブログが解説する「VPS 上の Claude Code を iPhone で操作する」構成ガイド。iOS 開発のビルドサーバーを AWS/GCP/Hetzner 上に置き、`xcodebuild`・XcodeBuildMCP・Fastlane を VPS で実行しながら Tactic Remote で iPhone からビルドログ確認・修正指示・PR 確認を行うワークフローを示している。自宅 Mac を起動したままにしたくない場合や、CI として常時稼働させたい場合の選択肢として有用。Cloudflare Tunnel 経由で TLS 保護された接続を確立するため、コード・認証情報が外部サーバーを経由しない点も明記されている。

- [iPad as Your AI Coding Cockpit: Running Claude Code, Codex, and Amp Remotely（Tactic Remote Blog）](https://tacticremote.com/blog/2026-05-26-claude-code-on-ipad-ai-coding-cockpit/) — iPad Pro を「AI コーディングコックピット」として使う事例紹介。Magic Keyboard 装着 iPad から Tactic Remote の分割ビューで Claude Code のチャットログ・ターミナル出力を並べて確認しつつ、Mac 側の XcodeBuildMCP が iOS シミュレータでビルド・テストを走らせるという構成。iOS 27 beta 対応コードを書きながらカフェで作業するユースケースを想定した記事で、Dynamic Island にビルドステータスをリアルタイム表示する設定手順も含む。

## 💡 今日のおすすめ実践 Tip

**「`claude remote-control` を git リポジトリで安全に使うための回避策と Tactic Remote 導入フロー」**

Issue #44805 の git リポジトリ問題（iPhone から "GitHub repository access check failed" エラーが出て操作できない）は現在も open のため、iOS プロジェクトを操作したい場合は以下の回避策を組み合わせて使います。

**【回避策 A】非 git ディレクトリから起動して CLAUDE.md でパスを指示**

```bash
# ホームディレクトリ（非 git）から Remote Control を開始
cd ~
claude remote-control --name "iOS Build Session"

# Claude への最初のメッセージで作業ディレクトリを指定
# "cd ~/Projects/MyApp && xcodebuild -scheme MyApp -sdk iphonesimulator build"
```

**【回避策 B】Tactic Remote を使う（Desktop App にも対応）**

公式 Remote Control とは異なり、Tactic Remote は tmux + Node.js WebSocket サーバーで動作するため、git リポジトリのチェックは発生しない。また Claude Desktop App のセッションにも接続可能。

```bash
# Mac 側: Tactic Remote サーバーのインストール（npm）
npm install -g @tacticspacetech/tactic-remote-server

# サーバー起動（iOS プロジェクトのルートで）
cd ~/Projects/MyApp
tactic-remote start

# iPhone 側: App Store から "Tactic Remote: AI Coding" をインストール
# → 同一 LAN または Cloudflare Tunnel 経由で接続
```

**iPhone から iOS ビルドを監視する推奨フロー**

1. **Mac で Claude Code を起動** → XcodeBuildMCP を有効にして `claude` セッション開始
2. **Tactic Remote サーバーを起動** → iPhone と接続
3. **iPhone の Tactic Remote でセッションを確認** → Dynamic Island にビルドステータス表示
4. **ビルドエラーが出たら iPhone からテキストで修正指示** → Claude が自律修正
5. **Git diff を iPhone から確認** → 問題なければ `git push` を指示

長時間の iOS テスト自動化（Simulator UI テスト・全機能テストスイート）を Mac に任せたまま外出するシナリオで特に有効です。Tactic Remote v1.8.2 で追加された「会話履歴の永続化」により、再接続時に前回のビルドコンテキストがそのまま引き継がれ、中断した作業をすぐ再開できます。

公式 Remote Control の Issue #44805 が修正されれば、`claude remote-control` でシームレスに同じフローが実現できるため、Issue にサムアップして修正を促すのも効果的です。
