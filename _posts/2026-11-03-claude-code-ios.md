---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-03"
date: 2026-11-03 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 2026-11-03 時点で新規リリースなし。最新は v2.1.316（2026-10-31）。v2.1.316 の内容（`CLAUDE_CODE_DISABLE_ULTRATHINK` 環境変数追加・`CLAUDE_CODE_SUBAGENT_EFFORT` 追加・Opus 4.8 fast mode 非推奨化）は 11-01 キャッチアップを参照。

## 🛠 GitHub の動き

- 該当なし（2026-11-03 時点で iOS / Xcode / Swift 関連の新規 Issue は確認できず。直近の主要 Issue は 11-02 キャッチアップを参照）

## 📝 日本語コミュニティ

- [Claude Code で5時間で2つのミニアプリを作った話（Zenn / hololab）](https://zenn.dev/hololab/articles/9b8eb0570975ac) — SwiftUI ベースのミニアプリ 2 本を Claude Code で 5 時間以内に完成させた実録。「1 本目で失敗したパターンを CLAUDE.md に追記してから 2 本目を着手した」という学習ループが印象的。Context が長くなった際に `/compact` でリセットしたり、XcodeBuildMCP でビルドを走らせながら小さいサイクルで進める手法が紹介されている。

## 🌐 海外コミュニティ / Tips

- [Claude Code for Swift and iOS: a practical workflow（agentvera.dev）](https://agentvera.dev/en/blog/claude-code-for-swift/) — 実務ベースの Swift × Claude Code ワークフローをまとめた記事。「Claude には `.pbxproj` を触らせない」「ターゲット・スキーム変更は Xcode や XcodeGen で行う」という鉄則に加え、`xcodebuild`・`swift test`・`swiftlint` を CLAUDE.md のカスタムコマンドとして登録することで Claude が自律的に検証ループを回せるようになると解説している。

- [SwiftShip — スペック駆動 iOS/macOS 開発のための Claude Code スキル集（GitHub / rshankras）](https://github.com/rshankras/SwiftShip) — `/apple:*` スラッシュコマンドで仕様書から App Store 提出まで一気通貫に進められるスキル集。設計・実装・テスト・リリースそれぞれに専門エージェントを持ち、App Store Connect や TestFlight への操作は**必ず人間に確認を求めてから実行**するよう設計されている点が安全。MIT ライセンス。

## 💡 今日のおすすめ実践 Tip

**失敗パターンを CLAUDE.md に溜めながら開発するループが最速への近道**

hololab の記事で紹介されているアプローチが実践的だ。1 本目のアプリで Claude が引っかかったパターン（`@MainActor` の付け忘れ、`AsyncImage` のエラー処理不備、`NavigationStack` と `NavigationLink` の混在など）を、そのまま CLAUDE.md に追記してから 2 本目の開発を始めることで、同じ失敗を繰り返さずに済む。

```markdown
# 既知の失敗パターン（繰り返さないこと）
- AsyncImage: .failure はカスタム View で必ず表示する（空のままにしない）
- List + NavigationLink: List 内は NavigationLink でラップしない（iOS 26 では NavigationStack + .navigationDestination に統一）
- @MainActor: UI を更新するクロージャには常に付与する
- Preview: #Preview マクロ内でネットワーク通信を行わない（モックに差し替える）
```

これを「プロジェクト固有のルール」として育て続けることで、CLAUDE.md は単なる設定ファイルから**チームの iOS ナレッジベース**に進化する。SwiftShip のように複数のスキルに分割してもよいが、まずはこのシンプルな失敗ログの蓄積から始めることをおすすめする。
