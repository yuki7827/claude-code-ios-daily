---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-20"
date: 2026-11-20 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（本日時点で v2.1.327 以降の新規リリースは確認できず。[公式 Changelog](https://code.claude.com/docs/en/changelog) を継続確認中）

## 🛠 GitHub の動き

- [Claude Code Desktop — iPhone Duo シミュレーターが 1〜179 度の任意角度折りたたみに対応（公開ベータ）](https://x.com/ClaudeDevs/status/2122439098740674839) — Claude Code Desktop の iOS Simulator ペインで iPhone Duo を 3 つの固定ポーズ（閉じた状態・半開き・完全展開）だけでなく **1〜179 度の任意角度**で折りたたんでレイアウトをテストできるようになった（Xcode 27.1+ 必須、Pro/Max/Team/Enterprise プラン。HIPAA 構成組織を除く）。SwiftUI の `.onHingeChange` によるヒンジ状態検出も確認可能。クラウドセッションや SSH セッションからは使えず macOS ローカルセッション限定。ヒンジ角度ごとにレイアウトが崩れないかをエージェントが自律的に検証するユースケースに適している。

## 📝 日本語コミュニティ

- [個人開発で使ってる Claude Code の自作スキル（Zenn / hiraly）](https://zenn.dev/hiraly/articles/b2d57af077ece1) — iOS 個人開発者が実務で作成したカスタムスキル集。ハイライトは **「DB スキーマと Swift モデルの整合チェックスキル」**: Core Data / Realm のスキーマ定義と Swift 側の struct/class を並べて不整合（プロパティ名のタイポ・型不一致・NULL 制約の違いなど）を自動検出する。その他「PR テンプレート生成スキル」「SwiftLint ルール説明スキル」など小回りが利く実用スキルが紹介されており、CLAUDE.md に書いた大まかなルールをスキル化する考え方も参考になる。

## 🌐 海外コミュニティ / Tips

- [How I Shipped 5 iOS Apps in 30 Days as a Solo Dev（Claude Code + Fastlane + ASC API）（dev.to / snake_sun）](https://dev.to/snake_sun/how-i-shipped-5-ios-apps-in-30-days-as-a-solo-dev-claude-code-fastlane-asc-api-31kd) — Windows マシン 1 台（Mac なし）で Claude Code + Fastlane + App Store Connect API を組み合わせ、30 日間で 5 本の iOS アプリを App Store / TestFlight に投入したインディー開発者の実践録。ポイントは「Claude Code に Fastlane の lane を書かせて ASC API でリリースを自動化し、Mac の手作業を完全排除したパイプライン」。作者は 30 日間の総支出 $123 でゼロ収益というリアルな数字も公開しており、ツールの可能性と市場開拓の難しさを両面から伝えている。

- [Agentic AI Engineering Workflows for iOS in 2026（Level Up Coding / Jacob's Tech Tavern）](https://levelup.gitconnected.com/agentic-ai-engineering-workflows-for-ios-in-2026-4150d709011d) — Claude Code の作り手が実践するアジェンダを参照しながら iOS 向けエージェント工学ワークフローを体系化した 14 分読み記事（2026/02 初出、Medium 転載）。**Builder セッション ＋ Reviewer セッションの 2 タブ分業パターン**（一方が実装し、もう一方がコードレビューと CI 分析を担う）が要点。ビルド検証ループを最初に構築することが「#1 生産性向上策」と明言しており、XcodeBuildMCP を使った閉ループの設計が解説されている。エージェントが書いたコードをエージェント自身にレビューさせない「独立検証」の原則も強調されている。

## 💡 今日のおすすめ実践 Tip

**Builder × Reviewer の 2 タブ分業でコードレビュー品質を上げる**

Level Up Coding の記事が示した「2 セッション分業パターン」を iOS 開発に適用する最小構成:

1. **Builder タブ**: 通常の実装セッション。CLAUDE.md に「実装が終わったら Reviewer セッションへ渡すためのサマリーをコードブロックに書くこと」と記載しておく
2. **Reviewer タブ**: 新しい Claude Code セッションを開き、プロジェクトの `CLAUDE.md` を読み込んだ上で「以下のコードを iOS エンジニアとしてレビューし、Swift/SwiftUI 固有のバグ・パフォーマンス問題・セキュリティ問題を列挙してください」と指示

```bash
# Reviewer タブでの起動例
claude --system "You are a senior iOS engineer. Review all code you receive for Swift 6 correctness, @MainActor isolation, memory leaks, and security. Be adversarial."
```

ポイントは同一セッションで実装とレビューを兼任させると Claude が自分の出力を過大評価しやすいため、**タブを分けることでバイアスを断つ**ことにある。PR を出す前の最終チェックとして特に有効で、CI が緑でも気づかない「コンパイルは通るが動的に崩れる」パターンを人間のレビュー前に摘み取れる。
