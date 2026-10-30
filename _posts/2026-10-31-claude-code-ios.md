---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-31"
date: 2026-10-31 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 2026-10-31 時点で v2.1.312（2026-10-27）以降の新規リリースは確認できず。v2.1.312 の内容（CLAUDE.md 大ファイル上書きバグ修正・クリックで即送信・Cloud sessions ルーティン effort 設定など）は 10/28 キャッチアップを参照。

## 🛠 GitHub の動き

- [#106195（2026-10-30 open）iOS Simulator パネル：iPhone SE が #103991 の修正後もノッチ付きフレームに引き伸ばされる再発](https://github.com/anthropics/claude-code/issues/106195) — #103991（2026-10-21 open → 10-24 closed）で「ホームボタン端末が横に引き伸ばされノッチ付きフレームに見える」問題が修正済みとなっていたが、iPhone SE（第 3 世代）で同症状が再発しているとの報告。SE 固有のフォームファクター判定に別パスが残っている可能性がある。Xcode 27 + iPhone SE シミュレーターを使っているプロジェクトは引き続き注意。

## 📝 日本語コミュニティ

- [Claude Codeで5日でiOSアプリをApp Storeに出した全記録 — 何を任せて何を任せちゃダメか（Zenn / seeda_yuto）](https://zenn.dev/seeda_yuto/articles/claude-code-ios-app-5days) — ゼロから App Store 提出まで 5 日間で完走した実録。「API 通信・テスト・SwiftUI コンポーネント実装はすべて Claude Code に任せ、プロビジョニングプロファイルと Signing 設定だけ自分でやった」という結論が明快。何を委ねるかの判断基準として実務の参考になる記事。

- [個人開発で使ってる Claude Code の自作スキル（Zenn / hiraly）](https://zenn.dev/hiraly/articles/b2d57af077ece1) — Swift モデルと DB スキーマの整合性チェックなど、個人 iOS 開発で日常使いしているカスタムスキルを紹介。プロジェクト固有のルールを CLAUDE.md ではなくスキルとして分離することで、複数プロジェクト間の再利用性を高める方針が解説されている。

- [AI と OWASP MASTG で、iOS アプリのセキュリティ自主点検をやってみた（Qiita / yasu-ya）](https://qiita.com/yasu-ya/items/4967da45c5e6c3ab8176) — OWASP MASTG のテストケースを Claude Code に渡して iOS アプリの脆弱性を自主点検した実録。Keychain の `kSecAttrAccessibleAlways` 使用・HTTP 通信の混在・ATS 無効化（`NSAllowsArbitraryLoads`）などを検出したプロセスを解説しており、テストケース番号を CLAUDE.md に明示することで検出精度が上がると報告している。yasu-ya 氏による同シリーズ 3 本目。

- [チケット URL を渡すだけ。iOS 不具合を調べて報告まで行う AI チームの設計（Qiita / TakanobuSano）](https://qiita.com/TakanobuSano/items/4b696164714c0bb58f3b) — Redmine / GitHub Issues のチケット URL を Claude Code のサブエージェントに渡すだけで、ソースコード調査→再現手順確認→原因特定→報告書作成まで自動化した設計を紹介。iOS 特有の非同期処理や UIKit ライフサイクルの問題も特定できると報告されており、不具合調査の省力化に具体的な事例。

## 🌐 海外コミュニティ / Tips

- [Driving a Real iPhone from Claude Code (and Why It's Harder Than Android)（Ruminations / Fuad Kamal, 2026-09-17）](https://fuadkamal.org/2026/09/17/driving-a-real-iphone-from-claude-code/) — 実機 iPhone を Claude Code から直接操作しようとした際の技術的障壁をまとめた記事。シミュレーターと異なり実機には①WebDriverAgent のデバイスへのインストール②ホスト側の go-ios③デバイスへの開通トンネルの 3 点が必要と解説。Android より手順が多いが実現可能で、実機 UI テスト自動化の起点として参考になる。

## 💡 今日のおすすめ実践 Tip

**OWASP MASTG のテストケース番号を CLAUDE.md に明示してセキュリティ自主点検を自動化する**

Qiita の yasu-ya 記事（[AI と OWASP MASTG で iOS アプリのセキュリティ自主点検をやってみた](https://qiita.com/yasu-ya/items/4967da45c5e6c3ab8176)）では、「セキュリティ問題を探して」という曖昧な指示より **MASTG のテストケース番号を明示する**方が検出精度が上がると報告されている。

CLAUDE.md に以下のブロックを追加してから Claude Code を呼び出す:

```markdown
# セキュリティ点検ルール
以下の OWASP MASTG テストケースをコードベース全体に実施せよ。
- MSTG-STORAGE-1: 機密データを Keychain 以外のストレージ（UserDefaults・plist 等）に保存していないか
- MSTG-STORAGE-2: Keychain に kSecAttrAccessibleAlways / kSecAttrAccessibleAlwaysThisDeviceOnly を使っていないか
- MSTG-NETWORK-2: ATS が無効化（NSAllowsArbitraryLoads: true）されていないか
- MSTG-NETWORK-3: 証明書ピンニングが未実装または容易にバイパスできないか
発見事項は「ファイルパス : 行番号 / リスク / 修正案」の形式で報告せよ。
```

番号を指定することで Claude が探す対象を具体化でき、Keychain 属性・Info.plist・ATS 設定ファイルを横断的にスキャンしやすくなる。ただし Keychain まわりは誤検知が出やすいため、**点検結果は必ず人間がレビューしてからコードへ適用すること**。
