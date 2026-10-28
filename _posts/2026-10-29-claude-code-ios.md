---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-29"
date: 2026-10-29 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 本日時点（2026-10-29）で v2.1.312（2026-10-27）以降の新規リリースは確認できず。v2.1.312 の内容（CLAUDE.md 大ファイル上書きバグ修正・クリックで即送信・Cloud sessions のルーティン effort 設定など）は 10/28 キャッチアップを参照。

## 🛠 GitHub の動き

- [#105773（2026-10-28 open）Desktop: iOS Simulator パネルがリサイズ中・Claude 操作中に点滅する](https://github.com/anthropics/claude-code/issues/105773) — Claude Desktop の Code タブに埋め込んだ iOS Simulator パネルが、①パネル端をドラッグしてリサイズするたびに全体が激しく点滅する、②Claude がデバイスを操作（タップ・スクリーンショット）するたびにフラッシュとアーティファクトが発生する、という 2 点のバグ報告。Claude Desktop 4.29.0 + macOS 27.0.1 で再現確認済み。パネルを切り離して独立ウィンドウにすると点滅は止まるため、現時点の暫定策は「パネル分離」。#102761（先週クローズ）で指摘されたキャプチャインジケーターによる白フラッシュとは別の問題として改めて報告されている。
- [#101489（2026-10-12 open → 2026-10-28 closed）iOS Simulator ペインがフランス語 Mac の物理キーボード入力を US QWERTY として扱う](https://github.com/anthropics/claude-code/issues/101489) — AZERTY 配列の Mac で iOS Simulator パネルに物理キーボードから入力すると、すべて US QWERTY の位置として誤認識されてしまう問題（#87322 の再発）。2026-10-28 にクローズされ修正が確認された。フランス語・ベルギー語配列など非 QWERTY の Mac で Simulator パネルを使っていてキー入力がズレていた場合は、最新バージョンで解消される見込み。

## 📝 日本語コミュニティ

- [Claude Code にシミュレーターを渡したら勝手にタップしてスクリーンショットを撮り始めた（Zenn / shimo4228 → DEV Community にも掲載）](https://dev.to/shimo4228/i-gave-claude-code-a-simulator-and-it-started-tapping-and-taking-screenshots-on-its-own-57bj) — iOS Simulator パネルと MobileBuildMCP（旧 XcodeBuildMCP）を Claude Code に渡したところ、Claude が自律的にビルド → シミュレーター起動 → タップ → スクリーンショット確認のループを回し始めたという実録レポート。「指示したのはアプリの要件だけ。操作はすべて Claude が判断した」という体験が報告されており、Claude Code の iOS 自律開発ループの現状を把握できる記事。Zenn 原文が DEV Community にも転載されている。

## 🌐 海外コミュニティ / Tips

- 該当なし（2026-10-29 時点で海外コミュニティに Claude Code × iOS 関連の新規記事は確認できず）

## 💡 今日のおすすめ実践 Tip

**iOS Simulator パネルの点滅 (#105773) が気になる間は「パネル分離」で作業する**

#105773 で報告された「Claude が操作するたびに Simulator パネルが点滅する問題」は、パネルを Claude Desktop のサイドパネルとして埋め込んだ状態（docked）で発生する。報告者も確認しているように、**パネルを切り離して独立ウィンドウにすると点滅は止まる**。

Claude Desktop でパネルを分離するには、パネルヘッダー右端の「分離（detach）」アイコンをクリックする。分離後は Simulator が独立ウィンドウとして表示され、Claude による操作中も安定したフレームが維持される。

**使い分けの目安:**

| 状況 | 推奨 |
|---|---|
| Claude が連続操作する UI テスト・自律開発ループ | パネル分離（独立ウィンドウ） |
| Claude の操作が少ない設計・確認作業 | 埋め込みパネルのまま |

また、分離したウィンドウは画面の端や別モニターに配置しておくと、Claude の操作と自分のコード編集を並行して確認しやすくなる。修正版がリリースされるまでの暫定策として活用してほしい。
