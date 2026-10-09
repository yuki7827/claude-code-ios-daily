---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-10"
date: 2026-10-10 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-10-10 時点で v2.1.293 が最新。前日キャッチアップ済み）

## 🛠 GitHub の動き

- **[Issue #100871（2026-10-09 作成 / OPEN）— Computer use: Device Hub（iOS Simulator）への全入力が "Could not verify which document this window is editing" で失敗 — Claude 2.31226.0 以降に発生](https://github.com/anthropics/claude-code/issues/100871)** — Claude Code のコンピューターユース機能で iOS Simulator（Device Hub / `com.apple.dt.Devices`）に対してキーボード入力・クリック等を送ろうとすると、「Could not verify which document this window is editing」エラーが発生してすべての入力が拒否される問題。Claude 2.31226.0 へのアップデート以降に発生しており、それ以前のバージョンでは正常動作していたとの報告がある。SwiftUI のフォーム入力テストや TestFlight 配信フロー前の UI 確認を自動化しているワークフローに影響する可能性がある。現在 OPEN でトリアージ中。ラベル: `bug`, `has repro`, `platform:macos`, `area:tools`。

## 📝 日本語コミュニティ

- 該当なし（2026-10-10 時点で Zenn / Qiita に iOS 文脈の新規記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[ClaudeCodeSDK — Swift Package で Claude Code SDK をラップしたサードパーティライブラリ（GitHub: jamesrochabrun/ClaudeCodeSDK）](https://github.com/jamesrochabrun/ClaudeCodeSDK)** — Claude Code の機能を Swift から呼び出すための非公式 SDK ラッパー。**macOS 専用**（iOS アプリはサンドボックス制限により外部プロセスを exec できないため、iOS ターゲットはサポート外）。Mac アプリや Swift CLI からエージェントループを組み込みたい場合に参考になる。iOS 開発ツールを Claude Code で作る（CI スクリプト・社内ツール等）際の選択肢として注目。

- **[Claude Code で iOS アプリ開発を「AI だけ」で試してみた（Classmethod: dev.classmethod.jp）](https://dev.classmethod.jp/en/articles/claude-code-ios-app-development-ai-only/)** — Claude Code に「できる限り自分でコードを書かず AI だけで iOS アプリを完成させる」という制約を課して挑戦した実験レポート。ビルドエラーの自己修正やテスト生成は概ね成功する一方、Xcode の GUI 操作（プロジェクト設定・コードサイニング・シミュレーター起動の最終ステップ）は依然として人間の介入が必要な場面が残ると報告。Claude Code が得意な領域と苦手な領域の境界線を確認するのに有用な記事。

## 💡 今日のおすすめ実践 Tip

**Issue #100871 の回避策 — Device Hub 入力失敗時は `xcrun simctl` と AppleScript を組み合わせる**

v2.31226.0 以降の Claude Code で iOS Simulator へのコンピューターユース入力が失敗する場合（Issue #100871）、修正が出るまでの間は以下の代替アプローチが有効。

```bash
# 方法 1: xcrun simctl でテキスト入力を直接送信
# フォーカスされているフィールドにテキストを流し込む
xcrun simctl io booted input text "入力したいテキスト"

# 方法 2: AppleScript で Device Hub のウィンドウを明示的にフォーカスしてから入力
osascript <<'EOF'
tell application "Devices"
  activate
  delay 0.5
end tell
tell application "System Events"
  keystroke "入力したいテキスト"
end tell
EOF
```

CLAUDE.md に以下のように明記しておくと、Claude Code が自動的にフォールバック手順を使うようになる。

```markdown
## iOS Simulator 入力の注意
computer_use ツールで iOS Simulator への入力が失敗した場合は、
代わりに `xcrun simctl io booted input text "<text>"` を使うこと。
テキストフィールドへのフォーカスは AppleScript の `tell application "Devices" activate` で先に確保すること。
```

Issue #100871 が修正されるまでの暫定対応として、CI/CD パイプラインの self-hosted ランナー上でも有効な方法。
