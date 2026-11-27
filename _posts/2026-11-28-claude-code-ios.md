---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-28"
date: 2026-11-28 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [公式 Changelog（v2.1.331）より — 2026-11-30 以降: Managed Marketplace が自動更新に（Claude Code Docs）](https://code.claude.com/docs/en/changelog) — v2.1.331（11/23）の Changelog に **「2026-11-30 以降、`extraKnownMarketplaces` で管理されているプラグインマーケットプレイスがデフォルトで自動更新される」** という告知が含まれている。該当するのは Team/Enterprise 環境でマネージド設定に追加されたマーケットプレイス（Apple 公式 `xcode` プラグインを管理配布している組織も含む）。**オプトアウトするには、マーケットプレイスエントリに `"autoUpdate": false` を設定する**必要がある。バージョンを固定したい CI 環境や大規模 iOS プロジェクトは今日中に確認・対応を推奨。

## 🛠 GitHub の動き

- 該当なし（本日時点で直近 5 投稿と重複しない iOS 関連の新規 Issue / PR は確認できず）

## 📝 日本語コミュニティ

- 該当なし（本日時点で直近 5 投稿と重複しない iOS 関連の新規日本語記事は確認できず）

## 🌐 海外コミュニティ / Tips

- [How I Shipped a Production iOS App Solo with Claude（DEV Community / sahil_kat）](https://dev.to/sahil_kat/how-i-shipped-a-production-ios-app-solo-with-claude-26l3) — 2014 年から iOS を離れていた開発者が、Claude を「コードを生成する道具」ではなく「構造化されたペアプログラマー」として活用して App Store リリースまで達成した実践録。①コードを書く前に iOS の最新 API との知識ギャップを洗い出す、②主要サブシステムに取り掛かる前にフレームワーク選定を相談する（早期に Core Motion と HealthKit の設計上の違いを確認し大規模な書き直しを回避）、③小さな設計判断は「理論上の正解ではなく自分の状況に合った答え」を求める——の 3 つのワークフローを提案。**iOS のバージョン・言語・チーム規模を明示する、import 文込みでビルドできる形のコードを要求する、API の存在が不確かな場合はその旨を明示させる**などの実践 Tips も参考になる。

- [iOS Testing Skill（@ioloro/ios-testing、GitHub）](https://github.com/ioloro/iOS-Testing) — Claude Code に「いつ Swift Testing を使い、いつ XCTest を使うか」を教えるコミュニティスキル（Xcode 26.4 / Swift 6.3 動作確認済み）。**ユニットテスト・インテグレーションテスト・非同期コードは Swift Testing、パフォーマンスベンチマーク（CPU・メモリ・ストレージ）は XCTest** というガイドラインを実装している。Claude のデフォルト動作では XCTest が選ばれやすいため、このスキルはその傾向を矯正する目的で設計されている。スキル本体に加えシミュレーター・実機で自動実行する Swift CLI（`iostesting`）とオプションのブロックフックが同梱。`npx skills add @ioloro/ios-testing` でインストール可能。

## 💡 今日のおすすめ実践 Tip

**明日（2026-11-30）発効: Managed Marketplace 自動更新の事前チェック**

v2.1.331 の Changelog に記載された「2026-11-30 以降、`extraKnownMarketplaces` で管理されるマーケットプレイスがデフォルト自動更新になる」という変更が明日発効する。

影響を受けるのは主に **Team/Enterprise 環境でマネージド設定（`extraKnownMarketplaces`）にマーケットプレイスを追加している組織**。個人の Pro/Max 利用者は通常このフィールドを使わないため影響は限定的。

対応が必要な場合は、`.claude/settings.json`（または Enterprise 管理設定）の `extraKnownMarketplaces` エントリに `"autoUpdate": false` を追加する:

```jsonc
{
  "extraKnownMarketplaces": [
    {
      "name": "my-ios-marketplace",
      "url": "https://example.com/marketplace",
      "autoUpdate": false
    }
  ]
}
```

Xcode プラグイン（`claude-plugins-official` 配下の `xcode` プラグイン）を使っている場合、公式マーケットプレイス自体はもともと自動更新がデフォルトであり今回の変更対象外。ただし Xcode バージョンアップに連動したプラグイン更新がビルドフローを変える可能性もあるため、**CI や大規模 iOS プロジェクトでは更新タイミングを制御する運用**を検討しておくとよい。
