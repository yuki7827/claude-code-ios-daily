---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-05-11"
date: 2026-05-11 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [New in Claude Managed Agents: dreaming, outcomes, and multiagent orchestration](https://claude.com/blog/new-in-claude-managed-agents) — Code with Claude SF 2026（5/6）で発表した 3 機能が公開。**Dreaming**（研究プレビュー）はエージェントが過去セッションを定期的に見直してパターンを学習し、Xcode プロジェクトの規約やコーディングスタイルを自律的に記憶。**Outcomes**（パブリックβ）は成功ルーブリックを書くと別 Claude インスタンスが評価し、コードレビュー品質が最大 10% 向上。**Multiagent Orchestration**（パブリックβ）はリードエージェントがタスクを分解し、サブエージェントが共有ファイルシステム上で並行作業。Pro/Max/Team/Enterprise の 5 時間レート制限も同時に倍増。

## 🛠 GitHub の動き

- [dpearson2699/swift-ios-skills v3.4.0](https://github.com/dpearson2699/swift-ios-skills) (2026-04-26 リリース、133 コミット) — iOS 26+・Swift 6.3・現代 Apple フレームワーク向けの **84 スキル** を 9 カテゴリで提供。SwiftUI（Liquid Glass・アニメーション・パフォーマンス・ナビゲーション）・Swift Concurrency・StoreKit 2・SwiftData・CloudKit・HealthKit・CoreML・Bluetooth・HomeKit など最新 API のみ収録し、レガシーパターンを完全排除。Claude Code・Codex・Cursor など 40 以上のエージェントに対応し、`npx skills add dpearson2699/swift-ios-skills` で全 84 スキルを一括導入可能。
- [patrickserrano/skills — iOS Swift スキルコレクション](https://github.com/patrickserrano/skills) — iOS 特化スキルを 3 本収録。`swiftui-liquid-glass`（iOS 26+ Liquid Glass API の実装・レビュー・移行）、`swiftui-performance-audit`（SwiftUI のレンダリング遅延・スクロールのカクつき・CPU/メモリ高負荷を監査）、`swift-concurrency-expert`（Swift 6.2+ Concurrency コンプライアンスのレビューとコンパイルエラー修正）。`claude plugin marketplace add https://github.com/patrickserrano/skills` で追加後、個別に `claude plugin install` 可能。

## 📝 日本語コミュニティ

- [Claude Codeのセットアップとモバイルアプリの環境構築](https://zenn.dev/solar/articles/6a2c9ffaf9fe16) (solar, Zenn) — Claude Code と Swift Package Manager の相性を検証した実践レポート。既存の Package.swift に定義済みの依存を Claude が実装に組み込むことはできるが、新パッケージの追加はできない制約を指摘。iOS プロジェクト開始前に SPM 依存を整備しておくことが Claude Code 活用の前提条件、という実務的な結論をまとめている。
- [Claude Code で5時間で2つのミニアプリを作った話](https://zenn.dev/hololab/articles/9b8eb0570975ac) (hololab, Zenn) — SwiftUI を使った BLE 通信アプリと汎用ミニアプリを 5 時間で同時開発した実録。Claude Code が CoreBluetooth のペリフェラル探索・接続・データ受信コードを一気通貫で生成し、SwiftUI と連携する過程を記録。短時間 PoC での工数感と Claude Code の有効範囲をつかむのに参考になる内容。

## 🌐 海外コミュニティ / Tips

- [Agentic Coding in Xcode 26.3 Using Claude Agent for Swift & SwiftUI — Part 3](https://medium.com/@chinthaka01/agentic-coding-in-xcode-26-3-8b62c1e46bf2) (Chinthaka Parana Widanalage, Medium) — Xcode 26.3 + Claude Agent の実践連載第 3 弾。iOS 18 以降をターゲットにした SwiftUI プロジェクトで、Claude Agent がデプロイターゲットの差異（iOS 17 vs 18 の API）を自動検出して条件分岐を挿入する手順と、エージェントが旧 API を使ってしまったときのリカバリーワークフローを具体的に解説。
- [Multi-agent orchestration for Claude Code in 2026](https://shipyard.build/blog/claude-code-multi-agent/) (Shipyard) — 今回パブリックβになった Multiagent Orchestration を Claude Code と組み合わせる実践解説。リードエージェントが iOS プロジェクトを UI 実装・ビジネスロジック・テスト・CI 設定の 4 タスクに分解し、各サブエージェントが XcodeBuildMCP を使って独立作業するパターンを紹介。公式ドキュメント `code.claude.com/docs/en/agent-teams` とあわせて読むと実装しやすい。

## 💡 今日のおすすめ実践 Tip

**Multiagent Orchestration で iOS フィーチャー開発を並行化する**

今回パブリックβになった Multiagent Orchestration は、Claude Code の git worktree 並行実行をマネージドサービスとして提供するイメージです。iOS 開発での活用例として、新機能を UI 層・ビジネスロジック層・テスト層に分けてサブエージェントに委任できます。

```
# リードエージェントへの指示例
「タスク管理機能を実装してください。
以下の 3 サブエージェントで並行してください：
1. SwiftUI View 層（TaskListView・TaskDetailView）
2. ビジネスロジック層（TaskRepository・CloudKit 同期）
3. テスト層（Swift Testing でユニット+UI テスト）
完成後 XcodeBuildMCP でビルドを確認してください」
```

サブエージェントは共有ファイルシステム上で並行動作するため、View・ViewModel・Repository を別エージェントが同時に書き進めます。Outcomes と組み合わせると「SwiftUI プレビューが壊れていないか」「Swift 6 Concurrency に違反していないか」を別インスタンスが自動採点する QA ループも構築できます。Claude Platform API で `managed-agents-2026-04-01` ベータヘッダを指定して利用可能です。
