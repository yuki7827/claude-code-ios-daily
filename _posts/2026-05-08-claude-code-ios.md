---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-08"
date: 2026-05-08 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.132 (May 6 リリース)](https://code.claude.com/docs/en/changelog) — Bash ツールのサブプロセス環境に `CLAUDE_CODE_SESSION_ID` 環境変数を追加（hooks に渡される session_id と同値）。`CLAUDE_CODE_DISABLE_ALTERNATE_SCREEN=1` でフルスクリーンレンダラーをオプトアウト可能に。IDE の「停止」ボタンや `kill -INT` による外部 SIGINT でターミナルモードが正しく復元されず `--resume` ヒントも出ないバグを修正。emoji 含む文字列での `--resume` 失敗・VS Code/Cursor でのマウスホイールスクロール不具合なども解消。iOS CI/CD パイプラインでのセッション追跡精度と IDE 操作安定性が向上。

## 🛠 GitHub の動き

- [AvdLee/Xcode-Build-Optimization-Agent-Skill](https://github.com/AvdLee/Xcode-Build-Optimization-Agent-Skill) — Antoine van der Lee（SwiftLee）による Xcode ビルド最適化スキル。3 回クリーンビルド + 3 回インクリメンタルビルドのベンチマーク実行→40+ 項目のビルド設定監査→コンパイルホットスポット検出→最適化プラン（`.build-benchmark/optimization-plan.md`）生成の 4 フェーズを 6 サブスキルで自動化。承認後のみプロジェクトファイルを変更する安全設計。`npx skills add https://github.com/avdlee/xcode-build-optimization-agent-skill` で追加。早期ユーザーはビルド時間を 78% 短縮した報告も。
- [twostraws/Swift-Agent-Skills](https://github.com/twostraws/swift-agent-skills) — Paul Hudson（Hacking with Swift）が運営する Swift エージェントスキルのキュレーションディレクトリ。SwiftUI Pro・SwiftData・Swift Concurrency・Swift Testing・Accessibility など 20+ スキルを一元管理。AvdLee/SwiftUI-Agent-Skill とは別の、全 Apple プラットフォームスキルを横断するインデックスリポ。新スキルの発見・評価・インストール手順をまとめる出発点として活用できる。

## 📝 日本語コミュニティ

- [CLAUDE.md × Skills × Rules のハイブリッド構成によるiOSアプリ開発の効率化](https://tech.pepabo.com/2026/01/21/claude/) (Pepabo Tech Portal) — minne iOS チームが CLAUDE.md 肥大化問題を解消するため、スキルでオンデマンド専門知識を分離し、Rules でコーディング規約を自動適用する3層構成を採用。Figma Context MCP でデザインファイルから直接 SwiftUI コードを生成し、Rules で配色・フォント基準を自動付与するワークフローを具体的に紹介。
- [Xcode 26.3 で iOS 開発の AI 活用はどう変わるのか ── タップルでの導入方針とあわせて](https://developers.cyberagent.co.jp/blog/archives/62551/) (CyberAgent Developers Blog) — CyberAgent の tapple iOS チームによる Xcode 26.3 導入方針レポート。Xcode MCP が現状 Scheme を指定できないため、複数 Scheme を持つプロジェクトでは「ユーザーが事前に正しい Scheme を選択」という回避策が必要な点など実務的な知見を公開。

## 🌐 海外コミュニティ / Tips

- [Agent skills in Xcode: How to install and use them today](https://www.hackingwithswift.com/articles/283/how-to-install-and-use-ai-agent-skills-in-xcode) (Hacking with Swift) — Paul Hudson による Xcode へのスキルインストール手順ガイド。Claude Code 向けのスキル配置先（`~/Library/Developer/Xcode/CodingAssistant/ClaudeAgentConfig/`）、シンボリックリンク方式でスキルを一元管理する方法、ターミナル版・デスクトップ版・Xcode 版 Claude で同一スキルを共有する設定をステップバイステップで解説。
- [Xcode Build Optimization using 6 Agent Skills](https://www.avanderlee.com/xcode/xcode-build-optimization-using-6-agent-skills/) (SwiftLee) — 上記 Xcode-Build-Optimization-Agent-Skill を使った実践解説。オーケストレータ→ベンチマーク→3 種類の分析（ビルド設定・ソースコード・パッケージ依存）→最適化適用という 6 スキル連携ワークフローを図解。中規模 iOS プロジェクトで平均 40〜78% のビルド時間短縮を達成した事例を紹介。

## 💡 今日のおすすめ実践 Tip

**`CLAUDE_CODE_SESSION_ID` を Hooks でビルドログに埋め込んでセッションを追跡する**

v2.1.132 で追加された `CLAUDE_CODE_SESSION_ID` は Bash ツールが起動するサブプロセスに自動注入されます。iOS 開発では `PreToolUse` / `PostToolUse` フックで xcodebuild 前後にこの値をログへ書き出しておくと、複数の Claude セッションを並走させる場合でも「どのセッションがどのビルドを起動したか」がトレースできます。`.claude/settings.json` に `"hooks": {"PostToolUse": [{"matcher": "Bash", "hooks": [{"type": "command", "command": "echo \"[$CLAUDE_CODE_SESSION_ID] $(date)\" >> ~/claude-builds.log"}]}]}` を追加するだけで記録が始まります。`CLAUDE_CODE_DISABLE_ALTERNATE_SCREEN=1` を CI の環境変数に設定すると GitHub Actions のログに制御文字が混入せず、Step Summary で結果を読みやすく表示できます。
