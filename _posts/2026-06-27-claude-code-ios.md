---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-27"
date: 2026-06-27 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.193（2026-06-25）](https://code.claude.com/docs/en/changelog) — iOS 開発・CI 環境に直結する 3 つの改善を含む。①**`autoMode.classifyAllShell` 設定を追加**：`true` にすると `xcodebuild`・`swift test`・`simctl` を含む*すべての* Bash コマンドが auto-mode クラシファイアを通過するようになり（デフォルトは任意コード実行パターンのみ対象）、CI 上での意図しないシェル実行をより厳密に制御できる。②**アイドル化したバックグラウンドシェルコマンドのメモリ自動回収**を実装（`CLAUDE_CODE_DISABLE_BG_SHELL_PRESSURE_REAP=1` で無効化可）：長時間の xcodebuild セッション後にアイドル化したシェルプロセスが占有するメモリを自動解放し、Mac のサーマルスロットリング防止に貢献。③**OpenTelemetry `claude_code.assistant_response` イベント追加**：デフォルトはレスポンス内容を伏字にした状態で計装され、`OTEL_LOG_ASSISTANT_RESPONSES=1` でフル出力が有効になる。iOS CI で Claude Code の動作を Datadog・Honeycomb 等で監視する際に活用できる。

## 🛠 GitHub の動き

- [patrickserrano/skills](https://github.com/patrickserrano/skills) — iOS / Swift 開発に特化した 10 スキル集。目玉は **`swiftui-liquid-glass`**（iOS 26+ の Liquid Glass API 全バリアント・`GlassEffectContainer`・モーフィング効果を網羅）、**`native-app-profiling`**（`xctrace` の CLI ラッパーで Time Profiler 相当の計測をエージェントから実行可能）、**`release-app-store-changelog`**（git 履歴からアプリの App Store リリースノートを自動生成）。他に `ios-debugger-agent`・`swift-concurrency-expert`（Swift 6.2+ 準拠レビュー）・`swiftui-performance-audit`・`swiftui-view-refactor` など実務で即使えるスキルを網羅。

- [199-biotechnologies/swiftui-claude-skills](https://github.com/199-biotechnologies/swiftui-claude-skills) — Claude Opus 4.6・GPT-5.4 Codex・Gemini 3.1 Pro の 3 AI によるクロスバリデーション（「AI スウォーム検証」）で全スニペットがコンパイル済みであることを確認済みの SwiftUI スキル集。**`swiftui-liquid-glass`**（`.regular`・`.clear`・`.identity` の 3 グラスバリアント、`tabViewBottomAccessory` 統合、アクセシビリティ自動適応）・**`swiftui-ux-review`**（HIG 準拠チェックの自動化）・**`on-device-ai`**（Foundation Models フレームワークで構造化 AI 出力）の独自スキル 4 本に加え、Paul Hudson（twostraws）の `swiftui-pro`・`swift-testing-pro`・`swift-concurrency-pro`・`swiftdata-pro` を同梱。ハルシネーション API の混入を 3 AI の差分検出で排除している点が既存スキル集との差別化ポイント。

## 📝 日本語コミュニティ

- [Claude Code のスキルで個人開発アプリの iOS リリースを楽にした話（Qiita / @_it_）](https://qiita.com/_it_/items/45127d2e41cafff8477c) — 個人開発 iOS アプリの TestFlight アップロード・審査対応・リリースノート作成などの繰り返し作業を Claude Code のカスタムスキルで自動化した実践報告。SKILL.md ベースで自前スキルを組み立てる手順とハマりどころを解説。

## 🌐 海外コミュニティ / Tips

- [TechNickAI/claude_telemetry](https://github.com/TechNickAI/claude_telemetry) — `claude` コマンドのドロップイン代替として `claudia` コマンドを提供する OpenTelemetry ラッパー。ツール呼び出し・トークン数・コスト・実行トレースを Logfire / Sentry / Honeycomb / Datadog / Grafana Cloud などへ送信する。v2.1.193 の `claude_code.assistant_response` OTEL イベントと組み合わせると、iOS CI パイプライン上での Claude Code セッションを完全にトレース可能になる。利用は `claude` を `claudia` に置き換えるだけで既存フラグは変更不要。

## 💡 今日のおすすめ実践 Tip

**「`autoMode.classifyAllShell` + `native-app-profiling` スキルで、iOS CI の安全性とパフォーマンス計測を同時に強化する」**

v2.1.193 の新設定と `patrickserrano/skills` の `native-app-profiling` スキルを組み合わせると、「CI での意図しないコマンド実行を防ぎつつ、パフォーマンス問題を自律的に特定する」フローが実現できます。

**1. `autoMode.classifyAllShell` で xcodebuild 系コマンドを厳密に管理**

```json
// .claude/settings.json
{
  "autoMode": {
    "classifyAllShell": true
  },
  "allowedTools": [
    "Bash(xcodebuild:*)",
    "Bash(xcrun:*)",
    "Bash(swift:*)",
    "Bash(xctrace:*)"
  ]
}
```

`xcodebuild`・`xcrun`・`swift`・`xctrace` のみを許可リストに入れることで、Claude Code が CI 上で実行できるコマンドを明示的にホワイトリスト管理できます。

**2. `native-app-profiling` スキルで遅い処理を自律特定**

```
native-app-profiling スキルを使って MyApp-iOS のメインスレッドをプロファイルして。
xctrace で 30 秒間計測し、5% 以上の CPU を占めている関数を特定したうえで
改善案を 3 つ提示して。
```

`native-app-profiling` は `xctrace record` → 結果 parse → ホットスポット特定 → 改善提案の一連を Claude Code に委譲するスキルです。`allowedTools` に `xctrace:*` を追加してあれば、`classifyAllShell: true` 環境でも安全に実行できます。

加えて `TechNickAI/claude_telemetry` の `claudia` コマンドで実行すると、どの xctrace 呼び出しに何トークン/何秒かかったかが Datadog 等で可視化でき、CI コスト最適化にも役立ちます。
