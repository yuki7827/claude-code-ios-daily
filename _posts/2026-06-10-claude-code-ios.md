---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-10"
date: 2026-06-10 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.170（2026-06-09）](https://code.claude.com/docs/en/changelog) — **Claude Fable 5 が Claude Code から利用可能に**。Mythos クラスの新モデルが一般公開され、v2.1.170 以降の Claude Code で `claude-fable-5` を指定するだけで呼び出せる。あわせて VS Code 統合ターミナルや Claude Code 環境変数を引き継いだシェルから起動したセッションでトランスクリプトが保存されず `--resume` に表示されなかったバグを修正。

- [Claude Fable 5（claude-fable-5）一般公開 — Anthropic 初の Mythos クラスモデル](https://www.anthropic.com/news/claude-fable-5-mythos-5) — コンテキストウィンドウ 1M トークン・最大 128k 出力トークンを持つ最上位モデル。内部ベンチマークでは Opus 系より少ないツール呼び出し・低トークン消費で同等の自律コーディング作業を完遂し、2 ヶ月かかるとされた Stripe のコード移行を 1 日で終えたと報告されている。**GitHub Copilot（VS Code・Visual Studio・JetBrains・Xcode・Eclipse）でも同日から順次 GA**（[GitHub Changelog](https://github.blog/changelog/2026-06-09-claude-fable-5-is-generally-available-for-github-copilot/)）。

- [Claude Code Dynamic Workflows — Research Preview 開始](https://claude.com/blog/a-harness-for-every-task-dynamic-workflows-in-claude-code) — Claude Code が自前のオーケストレーションスクリプトを生成し、**数十〜数百の並列サブエージェントを 1 セッション内で起動**して複雑なタスクを分担・相互検証する新機能。大規模バグ調査・コードベース全体の移行・セキュリティ監査・パフォーマンスレビューなど「従来の単一エージェントでは手が届かなかった規模」の作業を対象とする。Max・Team・エンタープライズプラン対象の Research Preview として提供開始（API・Amazon Bedrock・Google Vertex AI・Microsoft Foundry 経由でも利用可能）。トークン消費が通常セッションの数倍になる場合があるため、小規模タスクから試すことを Anthropic は推奨している。

## 🛠 GitHub の動き

- [Claude Fable 5 が GitHub Copilot for Xcode で GA](https://github.blog/changelog/2026-06-09-claude-fable-5-is-generally-available-for-github-copilot/) — Xcode の Copilot 拡張でモデル切替を行うと `claude-fable-5` が選択可能になった。チャット・ask・edit・agent の各モードで利用でき、Xcode 27 のデュアルエンジン AI と Copilot for Xcode の両方が Fable 5 を呼び出せる体制になった。iOS アプリ開発でよくあるマルチファイル設計変更（たとえば TCA → Observation + SwiftUI State マイグレーション）のような大規模リファクタにも十分な出力長（128k トークン）が確保される。

- [Foundation Models `LanguageModel` プロトコル + Anthropic Swift Package（WWDC 2026）](https://www.techtimes.com/articles/318039/20260609/wwdc-2026-developer-tools-foundation-models-now-swaps-ai-providers-without-code-changes.htm) — Apple が WWDC 2026 で公開した `LanguageModel` プロトコルは、サードパーティクラウドモデルが実装する公開 Swift インターフェース。**Anthropic が同プロトコルを実装した Swift Package を公開**しており、SPM 依存をひとつ変えるだけで Apple オンデバイスモデルから Claude へ（またはその逆へ）切替が可能で、セッションロジック・SwiftUI View 側のコードは変更不要。ストリーミング・ツール呼び出し・構造化レスポンスをそのままハンドリングできる設計で、iOS 27 / iPadOS 27 / macOS 27 / watchOS 27 / visionOS 27 のすべてに対応している。

## 📝 日本語コミュニティ

- [コードを1行も書かずに25,000行のiOSアプリを作った話 ― Claude Codeで完全Vibe Coding](https://qiita.com/john-rocky/items/b5a24858e797c954b16f)（Qiita / john-rocky）— Claude Code に PRD とゴールだけを渡し、Swift を一切書かずに 25,000 行規模の iOS アプリを完成させた実録。「Vibe Coding」スタイルで Claude が設計・実装・バグ修正を連続して行うフローがまとめられており、どの段階で人間が介入したか（設計意図の軌道修正・App Store 審査用メタデータ）も明記されている。

- 該当なし（Dynamic Workflows 発表・Fable 5 公開が重なり、日本語コミュニティの技術記事は 6/11 以降に集中する見込み）

## 🌐 海外コミュニティ / Tips

- [WWDC 2026: Foundation Models が AI プロバイダーをコード変更なしに切替可能に（TechTimes）](https://www.techtimes.com/articles/318039/20260609/wwdc-2026-developer-tools-foundation-models-now-swaps-ai-providers-without-code-changes.htm) — `LanguageModel` プロトコルの実用上の強みを整理した記事。プロトタイプ段階は Apple オンデバイスモデル（コストゼロ）で開発し、複雑な推論が必要な機能だけ `claude-fable-5` にルーティングする「ハイブリッド構成」がもっとも現実的なコスト最適化パターンとして推奨されている。Claude Code でこの切替ロジック自体を生成させるワークフローも紹介されている。

- [Anthropic releases Claude Fable 5 and Mythos 5 — major gains in coding and science（The Decoder）](https://the-decoder.com/anthropic-releases-claude-fable-5-and-mythos-5-with-major-gains-in-coding-and-science/) — Fable 5 の性能概要記事。コーディングベンチマークで Mythos 5 に肉薄しつつ、サイバーセキュリティ・生物学など高リスク領域には安全クラシファイアが組み込まれている。iOS 開発文脈では「Stripe 移行 1 日完遂」のような大規模リファクタ能力が注目点。Dynamic Workflows と組み合わせて使うことで、単一エージェントでは難しかった Swift コードベース全体の一括移行が現実的になってきている。

## 💡 今日のおすすめ実践 Tip

**Dynamic Workflows で iOS コードベースの大規模 Swift 6 マイグレーションを一括実行する**

Dynamic Workflows（Research Preview）は、単一エージェントでは手が届かなかった「ファイル数の多い Swift 6 Strict Concurrency マイグレーション」に特に向いています。Claude が自前でオーケストレーションスクリプトを書き、ファイル群を分割して並列サブエージェントに割り当て、完了後に整合性チェックまで行います。

Claude Code への指示例:

```
Dynamic Workflows を使って、このリポジトリ全体（Swift ファイル約 200 本）を
Swift 6 の Strict Concurrency に対応させてください。

要件:
1. まずコードベースを静的解析して、concurrency 警告・エラーの多い順にグループ化
2. グループを並列サブエージェントに分配して同時修正
3. 各サブエージェントは修正後に swift build --strict-concurrency=complete でエラーゼロを確認
4. 全サブエージェントの完了後にインテグレーションチェック（全体ビルド＋テストスイート実行）
5. 修正サマリー（変更ファイル数・主な修正パターン）を MIGRATION_REPORT.md に出力
```

注意点:
- Max / Team / Enterprise プランのみ利用可能（Research Preview）
- トークン消費は通常セッションの数倍になるため、まず 20〜30 ファイルの小規模サブセットで試して動作を確認する
- `.claude/settings.json` に `"fallbackModel": ["claude-sonnet-4-6"]` を設定しておくと、Fable 5 が過負荷のときにサブエージェントが自動でフォールバックして停止を防げる

Dynamic Workflows は Fable 5（128k 出力・1M コンテキスト）と組み合わせることで、Swift 6 移行のような「全体を俯瞰しながら細部を修正する」タスクに対して最大限の効果を発揮します。
