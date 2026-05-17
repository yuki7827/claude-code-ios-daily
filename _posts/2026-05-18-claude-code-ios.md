---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-18"
date: 2026-05-18 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code 週次制限 +50%（5月13日〜7月13日）](https://x.com/ClaudeDevs/status/2054639777685934564) — Pro・Max・Team・Enterprise の週次クォータが 5 月 13 日から 50% 増量（7 月 13 日 PDT まで）。5 月 6 日に実施済みの「5 時間制限の 2 倍化＋ピークアワーペナルティ廃止」に重ねて適用される。さらに 5 月 15 日には Anthropic が 5 時間・週次カウンターを全ユーザー向けに手動リセット。CLI・IDE 拡張・デスクトップ・Web のいずれでも同じクォータが加算されるため、XcodeBuildMCP を使った長時間ビルド・テストセッションや Xcode Intelligence の利用（どちらも同一クォートから消費）で頻繁に制限に当たっていた iOS 開発者に直接恩恵がある。

## 🛠 GitHub の動き

- [kylehughes/apple-platform-build-tools-claude-code-plugin v2.0.0 (Mar 8, 2026)](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin) — Apple プラットフォーム向けビルド自動化プラグイン。**Agent Skill**（`building-apple-platform-products`）は `xcodebuild`・SPM・`simctl`・`devicectl`・コード署名・プロファイリング・バイナリ解析ツールのクイックリファレンス表を提供し、Claude がビルドコマンドを正確に組み立てられるようにする。**Subagent**（`builder`）はプロジェクト構造の自動探索→ビルド/テスト実行→シミュレータ/実機管理→アーカイブ・配布まで一気通貫で担う。iOS・iPadOS・macOS・tvOS・watchOS・visionOS すべてに対応。XcodeBuildMCP や xcrun mcpbridge と組み合わせて使うとコンテキスト効率が上がる。
- [rohitg00/tailclaude](https://github.com/rohitg00/tailclaude) — Tailscale tailnet 上に Claude Code の Web インターフェースを公開するツール。iOS 端末から QR コードをスキャンするだけでタッチ最適化の Claude Code セッションに接続できる。Mac でエージェントを走らせたまま iPhone から進捗確認・指示変更を行う「遠隔監視」構成を最小設定で実現。iOS 開発者が Xcode ビルドを流しっぱなしにしたまま外出・移動する際に有用。

## 📝 日本語コミュニティ

- [iPhone から Claude Code を操作する — Coder + Tailscale + Moshi で実現するモバイル開発環境](https://zenn.dev/toshipon/articles/d69e546655d06b) (toshipon, Zenn) — Tailscale で Mac の tailnet に iPhone を参加させ、2026 年 1 月リリースの iOS ターミナルアプリ **Moshi**（Mosh プロトコル対応、UDP ベースで Wi-Fi 切替・スリープ後も接続が維持される）で Claude Code を遠隔操作する環境構築手順。tmux と組み合わせることでアプリを閉じても Claude のセッションが継続し、再接続時に作業状態が復元される。iOS ビルドを夜間バックグラウンドで流しながら翌朝 iPhone で結果確認する使い方に向いている。
- [iOSアプリ開発を行う際に、ビルドやファイル追加で失敗しない方法 【Claude Code編】](https://engineering.nifty.co.jp/blog/37202) (NIFTY engineering, Feb 26, 2026) — Claude Code が「ビルドターゲットへの登録なしでファイルを生成してしまう」問題を `.claude/scripts/` パターンで解決する実践レポート。`on_build.sh`（ビルド前確認）と `on_new_file.sh`（新ファイルを Xcode プロジェクトに登録するスクリプト）をリポジトリに置き、CLAUDE.md や SKILLS.md から参照する設計。Claude の指示プロンプトをシンプルに保ちつつ、プロジェクト固有の処理を一箇所に集約できる点が利点。

## 🌐 海外コミュニティ / Tips

- [Building iOS Apps with AI Agents: The Practitioner's Guide (Updated Apr 28, 2026)](https://blakecrosley.com/guides/ios-agent-development) (Blake Crosley) — 現在 iOS 向けにコードを書ける 3 つのエージェントランタイム（Claude Code CLI + MCP、Codex CLI + MCP、Xcode 26.3 ネイティブ Intelligence エージェント）を体系的に比較し、用途別の使い分けを解説。XcodeBuildMCP（59 ツール）と Apple 純正 `xcrun mcpbridge`（20 ツール）の 2 MCP サーバー構成での責務分担（headless ビルド/テスト/LLDB は XcodeBuildMCP、SwiftUI プレビュー/ドキュメント検索は mcpbridge）を詳述。4 月 28 日更新版で Xcode 26.3 GA 時点の情報を反映。
- [Claude Code skill for automated Fastlane setup (Discussion #29838)](https://github.com/fastlane/fastlane/discussions/29838) (fastlane/fastlane GitHub, Jan 3, 2026) — Fastlane の公式ディスカッションに掲載された Claude Code スキル。「Set up Fastlane for my iOS app」「Upload this build to TestFlight」のような自然言語指示で Fastlane の初期セットアップ・Match コード署名・App Store スクリーンショット生成・TestFlight アップロード・App Store 申請の 5 フローを自動化。Xcode プロジェクトファイルからバンドル ID とチーム情報を自動検出して設定ファイルを生成する。

## 💡 今日のおすすめ実践 Tip

**Moshi + Tailscale + tmux で iPhone から iOS ビルドを監視・制御する**

iOS 開発では長時間ビルドや夜間テストランを Mac に任せたまま外出するケースが多い。Moshi（2026 年 1 月リリースの iOS ターミナルアプリ）を使うと、iPhone が Wi-Fi とモバイルデータを切り替えたりスリープしたりしても Mosh プロトコルの UDP セッションが維持され、再起動後も同じターミナルセッションに自動再接続できる。

構築ステップの概要:

```bash
# Mac 側: Homebrew で mosh をインストール
brew install mosh

# Tailscale を Mac と iPhone の両方でセットアップし同一 tailnet に参加
# → tailscale status で iPhone の Tailscale IP を確認

# Mac 側: tmux で Claude Code セッションを常駐させる
tmux new-session -s claude-ios
claude --bg "/goal MyApp の Swift Testing テストをすべて GREEN にしてください"
# Ctrl-B D でデタッチ
```

iPhone の Moshi アプリで Mac の Tailscale IP に接続し、`tmux attach -t claude-ios` でアタッチすると Claude Code のリアルタイム出力を確認・指示変更できる。

`tailclaude`（`rohitg00/tailclaude`）を使えば Moshi 不要でブラウザの Web UI を iPhone から開ける。touch-optimized なインターフェースなので外出先からのフォローアップ指示に向いている。May 13 の週次制限 +50% と組み合わせると、1 日あたりの「ながら開発」枠が事実上拡大したことになる。
