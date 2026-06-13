---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-14"
date: 2026-06-14 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.174〜v2.1.176（2026-06-12）](https://code.claude.com/docs/en/changelog) — v2.1.176でフックの `if` 条件のglobパターンマッチが修正され、`Edit(**/*.swift)` のような拡張子指定パターンが意図どおりに機能するようになった。SwiftLint自動整形のような Swift 向けカスタムフックを `.claude/settings.json` に設定している場合は動作を再確認するとよい。あわせて Remote Control のモデル切替・切断処理も安定化し、iPhone の公式Claudeアプリやサードパーティ製 Mobile IDE からの遠隔セッション運用がしやすくなった。

## 🛠 GitHub の動き

- [moritztucher/swift-agent-skills](https://github.com/moritztucher/swift-agent-skills)（v0.1.0・新規） — SwiftUIファーストの Claude Code 向け Agent Skills 集。Setup→Plan→Build→Verify→Ship のライフサイクルに沿い、StoreKit・WidgetKit・SwiftData・CloudKit・HealthKit など 63 本のフレームワーク別スキルと SwiftLint 自動修正フックを同梱。Context7 経由で Apple 公式ドキュメントとの整合性を継続チェックする「currency-checked」設計が特徴。

- [greenstevester/fastlane-skill](https://github.com/greenstevester/fastlane-skill)（v1.1.5） — fastlane のセットアップ・コード署名（Match）・スクリーンショット生成・TestFlight へのベータ配信・App Store 審査提出までを自然言語の指示だけで Claude Code に任せられるスキル。Xcode プロジェクト構成を読み取って fastlane の設定ファイルを自動生成する。

- [Maples7/VibeChard](https://github.com/Maples7/VibeChard)（v1.2.0） — Claude Code / Codex / Copilot / Cursor など複数の AI エージェントが同じ Xcode プロジェクトを並行操作する際に発生する `DerivedData` 競合・`build.db` ロック・シミュレータ衝突を、タスクごとの git worktree + 専用 DerivedData + `simctl clone` で解消する CLI ツール。`brew install maples7/tap/vch` で導入可能。

## 📝 日本語コミュニティ

- [個人開発で使ってる Claude Code の自作スキル](https://zenn.dev/hiraly/articles/b2d57af077ece1)（Zenn / hiraly） — Swift/SwiftUI で iOS アプリをネイティブ開発している筆者が、実機へのデプロイやビルド自動化など個人開発向けに作り込んだ Claude Code の自作スキルを紹介。

## 🌐 海外コミュニティ / Tips

- [akshaypimprikar/pragma](https://github.com/akshaypimprikar/pragma) — 「2回承認するだけで、あとは Claude が出荷する」をコンセプトに、`/spec` → `/plan` → `/feature` → `/gates` → `/review` → `/test` → `/release` → CI の8段階で iOS アプリ開発を Claude Code のスラッシュコマンド13個と GitHub Actions 3本で自動化するスキャフォールド。`.claude/context/` に設計判断や過去の却下案を残し、セッションをまたいで方針のブレを防ぐ。

## 💡 今日のおすすめ実践 Tip

**「SwiftUI実装 → 並行ビルド → TestFlight配信」を、今日紹介した3スキルでひと続きにする**

今日の GitHub セクションで紹介した3つのプロジェクトは、iOSアプリ開発の「実装」「並行検証」「配信」の各フェーズにちょうど対応しています。v2.1.176 のフック修正もあわせて、次のような構成が組みやすくなりました。

```bash
# 1. SwiftUI実装用のスキルを導入
/plugin marketplace add moritztucher/swift-agent-skills

# 2. 複数セッションでの並行ビルドを安全にするツールを導入
brew install maples7/tap/vch

# 3. リリース自動化スキルを導入
/plugin marketplace add greenstevester/fastlane-skill
```

Claude Code への指示例:

```
1. swift-agent-skills のビルドフェーズで、新しい設定画面のSwiftUI実装を進めて。
   SwiftLint自動修正フックは v2.1.176 で Edit(**/*.swift) パターンが
   正しく効くようになっているので、保存時に自動整形されることを確認して。

2. 実装とテスト作成を別セッションで並行させたいので、vch を使って
   このタスク用に独立した DerivedData とシミュレータクローンを用意して。

3. 両セッションの作業が完了したら、fastlane-skill を使って
   TestFlightへのベータ配信まで実行して。
```

`vch` によるビルド分離は、UI実装担当とテスト担当のセッションが同じプロジェクトに対して `xcodebuild` を同時実行してもキャッシュやシミュレータが衝突しないことを保証します。リリース直前の `fastlane-skill` 連携まで一気通貫にすることで、「実装→検証→配信」のたびに手動でコマンドを切り替える手間を減らせます。
