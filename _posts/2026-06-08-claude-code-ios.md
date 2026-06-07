---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-08"
date: 2026-06-08 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.168（2026-06-06）](https://github.com/anthropics/claude-code/releases) — バグ修正と信頼性向上のみのパッチリリース。直前の v2.1.166（`fallbackModel` 設定・MCP deny ルールのグロブ対応・クロスセッション SendMessage のセキュリティ強化など）に続く安定化対応で、ユーザー向けの機能追加はなし。iOS チームで CI/CD に組み込んでいる場合は、v2.1.166 系の新設定を試す前提として最新パッチへの追従を確認しておくとよい。

## 🛠 GitHub の動き

- [CharlesWiltgen/Axiom（★955）](https://github.com/CharlesWiltgen/Axiom) — iOS/iPadOS/watchOS/tvOS 向けに特化した Claude Code 用プラグイン。245 のスキル・40 の自律エージェント・15 の診断コマンドを同梱し、メモリリーク・並行処理違反・ビルド失敗を自動スキャンする `/axiom:audit memory`・`/axiom:audit concurrency`・`/axiom:health-check` などを提供。シミュレータ/実機のコンソール出力を解析する `xclog`、`.ips` クラッシュレポートをシンボリケートする `xcsym` という専用 CLI も付属し、`/plugin marketplace add CharlesWiltgen/Axiom` で導入できる。Swift 6・SwiftUI・Liquid Glass・Apple Intelligence など iOS 26+ の文脈に寄せて設計されている点が特徴。

## 📝 日本語コミュニティ

- [Claude Codeで1日でアプリを作ってApp Store公開](https://zenn.dev/aun_phonogram/articles/claude-code-one-day-app)（Zenn / aun_phonogram）— 「感情を選ぶだけ」のミニマルな日記アプリ「Wordless-Diary」を、設計書（MVP 仕様書）を渡すだけで Claude Code に一括実装させ、プライバシーポリシー作成からエラー解消まで完遂し 1 日で App Store 公開まで終えた記録。Expo + EAS + Claude Code の組み合わせがアプリ開発の参入障壁を大きく下げると結論づけている。

- [「アプリは作れた。でもリリースの仕方がわからない」— 初心者がClaude Codeと一緒にApp Storeに申請するまで](https://note.com/spark_tron/n/n2352abb73fa9)（note / TRON）— SwiftUI + SwiftData で作った支出管理アプリ「SpendTap」を、リリース未経験の著者が Claude Code と二人三脚で App Store 申請まで約 2 時間で完了させた体験記。証明書発行やスクリーンショット準備など「実装後の壁」でどう Claude Code に頼ったかが具体的に書かれている。

## 🌐 海外コミュニティ / Tips

- [getsentry/XcodeBuildMCP v2.6.0](https://github.com/getsentry/XcodeBuildMCP/releases) — UI オートメーション機能に「Runtime Context」を追加。操作後に安定した要素参照（element reference）と画面のハッシュ値を返すようになり、決定的なタスクでの実行時間を約 70%、トークン消費を約 68% 削減できるという。新ツール `wait_for_ui`（条件成立までポーリング）・`batch`（要素参照ベースの操作をまとめて実行）・`drag` も追加され、ビルド状況やログをプレーンテキストの解析に頼らず構造化データで返す改善も入った。XcodeBuildMCP を使った UI テスト自動化のトークン効率が一段と上がる更新。

## 💡 今日のおすすめ実践 Tip

**Axiom の監査エージェント × XcodeBuildMCP v2.6 の UI オートメーションで「ビルド→監査→UI 検証」を 1 セッションで回す**

今日紹介した 2 つの更新は組み合わせると相性が良いです。`CharlesWiltgen/Axiom` の `/axiom:health-check` でメモリリークや並行処理違反を静的にスキャンしたあと、`XcodeBuildMCP` v2.6.0 の `wait_for_ui` / `snapshot_ui`（画面ハッシュ付き）でシミュレータ上の実際の挙動を検証すれば、コードレベルの問題と実行時の UI バグを同じセッション内で連続して洗い出せます。

導入例:

```bash
# Axiom プラグインを追加
/plugin marketplace add CharlesWiltgen/Axiom
```

Claude Code への指示例:

```
1. axiom:health-check で MyApp の並行処理・メモリ周りの問題を静的にスキャンして報告して。
2. 問題があれば修正案を出した上で、XcodeBuildMCP でビルド→シミュレータ起動。
3. wait_for_ui でホーム画面の表示完了を待ってから snapshot_ui で screen hash を取得し、
   主要な導線（タブ切り替え・詳細遷移）を batch で操作して結果を報告して。
```

静的解析（Axiom）と実行時検証（XcodeBuildMCP の Runtime Context）を 1 つのワークフローに繋げることで、「コードは直ったが実機で見るとレイアウトが崩れていた」という典型的な見落としを減らせます。とくに UI オートメーション部分はトークン消費が大幅に圧縮されているため、CI 上で毎回フルチェックを回す運用にも乗せやすくなっています。
