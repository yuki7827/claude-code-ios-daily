---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-27"
date: 2026-08-27 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.246（2026-08-25）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— v2.1.243・v2.1.245 と同日リリースだが前日キャッチアップでは未収録。iOS 開発観点での主な変更点: ① **`/permissions` に Auto mode タブを追加** — Auto mode がどのツール呼び出しを自動承認するかのルール一覧を確認・編集できるタブが新設された。CI 環境でのツール許可設定を視覚的に把握・調整しやすくなる; ② **Bash allow ルールにワイルドカードが含まれる場合の起動警告を追加** — `"Bash(*)"` のような過度に広い許可ルールに対して起動時に警告が出るようになった。iOS プロジェクトの CLAUDE.md や settings.json で権限を設定している場合に確認を促す安全策; ③ **非常に長い 1 行（base64 文字列など）でのトランスクリプト描画遅延を修正** — Xcode のビルドログに含まれる base64 エンコードされた署名データや Provisioning Profile のダンプなどで発生していた重大なパフォーマンス問題が解消; ④ **フルスクリーン時のスクロールと jump-to-bottom のちらつきを修正** — ビルドログを眺める際の UI が安定; ⑤ **開始ディレクトリが削除済みのときにバックグラウンドセッションが 45 秒後に失敗するバグを修正** — iOS CI ワークフローでの一時ビルドディレクトリ削除後のセッション継続性が向上; ⑥ **`/stats` アクティビティヒートマップの UTC 東タイムゾーンでのズレを修正** — JST（UTC+9）ユーザーに影響していたセル表示ずれが解消

## 🛠 GitHub の動き

- [Issue #89826 — iOS Simulator 制御ツールが同一 Mac 上で複数セッション同時実行時に指定 UDID を無視する（OPEN / 2026-08-26 起票）](https://github.com/anthropics/claude-code/issues/89826) — 7 つ以上の Claude Code セッションが同時起動し 3 台のシミュレータが並列動作する環境で、あるセッションが特定の UDID を指定して `attach` しても、直後の `screenshot`・`tap`・`swipe` コマンドが**別のセッションが使用中のシミュレータ**に向いてしまう問題。根本原因はシミュレータの追跡ステートがセッションごとではなくグローバル（プロセス共有ないし OS レベルのウィンドウフォーカス）になっているため。環境: macOS 26.5.2 + Xcode 26.0.1。**現時点で有効な回避策なし**。並列 iOS UI テストを Claude Code に委任しているチームで視覚的 QA が完全にブロックされる深刻な問題。セッション ID ベースのスコープ管理が提案されている。

- **Xcode 27 (DeviceHub) 関連 Issue を複数クローズ** — [#79991（Xcode 27 の DeviceHub で Desktop アプリの iOS Simulator が動作しない）](https://github.com/anthropics/claude-code/issues/79991)・[#80425（Xcode beta で "No booted simulator found" — DeviceHub が Simulator.app を置き換えたことによる attach 失敗）](https://github.com/anthropics/claude-code/issues/80425)・[#80180（Xcode 27 で SimulatorKit.framework が移動したためデバイス探索が壊れ "No booted simulator found" が表示される）](https://github.com/anthropics/claude-code/issues/80180) が 2026-08-25 にクローズ。Xcode 27 の DeviceHub アーキテクチャ変更への対応が v2.1.245 前後で一段落したことが伺える。

## 📝 日本語コミュニティ

- [SwiftData × Claude Code で永続化層を設計する — @Model設計からマイグレーションまで実務で詰まらないための完全ガイド（Qiita / kotaro_ai_lab）](https://qiita.com/kotaro_ai_lab/items/119901d60f34e07da801) — SwiftData の `@Model` 定義から `ModelConfiguration`・`ModelContainer` の構築、スキーマバージョン管理と `SchemaMigrationPlan` によるマイグレーション戦略まで Claude Code と組み合わせた実務的な設計手法を網羅。「永続化層の初期設計が最も重要」という観点から、Claude Code に設計を委ねる際のプロンプトパターンも収録。

- [Xcode 26.3 エージェント型コーディング入門 — Claude・Codex・MCP で開発を自動化（Qiita / kai_kou）](https://qiita.com/kai_kou/items/1b24ad62dde4c02ae4f0) — Xcode 26.3 に統合された Claude Agent SDK・OpenAI Codex・MCP を組み合わせてエージェント型コーディングを始める入門記事。Claude Agent SDK の組み込みエージェントを Xcode から直接起動する設定手順と、MCP 経由でビルド・テストを自動化するワークフローを解説。

## 🌐 海外コミュニティ / Tips

- [Auto mode is now Claude Code's default: what the classifier approves, and how to switch back（DEV Community / rulestack）](https://dev.to/rulestack/auto-mode-is-now-claude-codes-default-what-the-classifier-approves-and-how-to-switch-back-4j2j) — 2026-08-14 以降 Pro・Max・Team プランの新規セッションで Auto mode がデフォルトになったことを受け、Auto mode の分類ロジック（どのツール呼び出しが自動承認されるか）と、必要に応じて従来の Manual/Bypasss mode に切り替える方法を詳解。iOS CI 環境で意図しないツール呼び出しが自動承認されないよう `/permissions` の Auto mode タブ（v2.1.246 で追加）で設定を確認することを推奨している。

- [Claude Code v2.1.246 Major Updates — Bash Permission Rules Warning and Auto Mode Permissions Tab（DevelopersIO / classmethod）](https://dev.classmethod.jp/en/articles/20260826-cc-updates-v2-1-246/) — v2.1.246 の変更点を日本語・英語バイリンガルで解説したクラスメソッド社のブログ記事。Bash allow ルールのワイルドカード警告と `/permissions` Auto mode タブに特化した実務解説。settings.json での権限設定例が具体的でわかりやすい。

## 💡 今日のおすすめ実践 Tip

**「Issue #89826 の回避策がない今、並列 iOS シミュレータ操作は 1 セッション 1 デバイスに徹する」**

Issue #89826 は、複数の Claude Code セッションが同一 Mac 上で同時稼働するとシミュレータの操作対象が混線するという深刻なバグです。修正が入るまでの間、並列 UI テストを安全に運用するには **「1 Claude Code セッション＝1 シミュレータデバイス」** の原則を徹底することが唯一の現実的な回避策です。

**並列テストを安全に設計するには**

```yaml
# GitHub Actions の matrix を使って「1 ジョブ = 1 シミュレータ」に分割する例
jobs:
  ui-test:
    strategy:
      matrix:
        device: ["iPhone 16 Pro", "iPad Pro 13-inch (M4)"]
    steps:
      - name: Boot simulator
        run: |
          UDID=$(xcrun simctl create "test-${{ matrix.device }}" \
            "${{ matrix.device }}" "com.apple.CoreSimulator.SimRuntime.iOS-19-0")
          xcrun simctl boot "$UDID"
          echo "SIM_UDID=$UDID" >> $GITHUB_ENV

      - name: Run Claude Code UI test (single session per job)
        run: |
          claude --print "シミュレータ ${SIM_UDID} でログイン画面の E2E テストを実行してください"
        env:
          ANTHROPIC_DEFAULT_MODEL: claude-sonnet-5
```

ポイントは **各ジョブ（ランナー）が独立プロセスになる** ため、シミュレータ追跡ステートが混線しない点です。同一ランナー上で複数の Claude Code セッションを並列起動する構成は Issue #89826 が修正されるまで避けてください。

**v2.1.246 の Auto mode タブで CI の許可設定を棚卸しする**

v2.1.246 で `/permissions` に Auto mode タブが追加されたことで、どのツール呼び出しが自動承認されているかを一覧で確認できるようになりました。CI 環境でも `claude --print` 実行前に interactive セッションでタブを開き、意図しない `Bash(*)` や広すぎる allow ルールが設定されていないかチェックすることを推奨します。起動時の警告（v2.1.246 で追加）が出た場合は即座に設定を見直してください。
