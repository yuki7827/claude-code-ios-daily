---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-10-24"
date: 2026-10-24 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code Changelog](https://code.claude.com/docs/en/changelog) — 本日時点で確認できた最新は v2.1.308（2026-10-22）。バグ修正と安定性改善のみ。10/24 時点では新規メジャーアップデートの記録なし。

## 🛠 GitHub の動き

- 該当なし（2026-10-24 時点で anthropics/claude-code への新規 iOS / Swift / Xcode 関連 Issue は確認できず。先日の #103991 Simulator フレーム表示バグは引き続き open 中）

## 📝 日本語コミュニティ

- [Claude Code がわからなかったので AI 縛りで iOS アプリを作ってみた（dev.classmethod.jp）](https://dev.classmethod.jp/en/articles/claude-code-ios-app-development-ai-only/) — Claude Code のみを使って iOS アプリを実装する実験記事。AI 主導・人間サポートの役割分担を探るなかで、Xcode プロジェクトファイル（.pbxproj）の直接編集が鬼門になることが判明。Swift 知識がある状態でも「何をやっているか確認しながら進める」姿勢が重要と結論付けている。

## 🌐 海外コミュニティ / Tips

- [Claude Can't See Xcode Previews — Here's How We Fixed It（Grindr Engineering）](https://www.grindr.com/blog/claude-cant-see-xcode-previews----heres-how-we-fixed-it) — Grindr のエンジニアが「Claude は SwiftUI の Xcode Preview を見られない問題」に正面から向き合い、CLI から Preview を取得する独自ワークフローを構築した事例（2026-06-01）。CLI ファースト開発への移行を進める中でのプラクティカルな解決策で、Xcode MCP ベースに移行する前の参考事例として有用。
- [greenstevester/fastlane-skill — Claude Code から fastlane を操作する公式プラグイン集](https://github.com/greenstevester/fastlane-skill) — TestFlight アップロード（beta プラグイン）・Match 証明書同期・スクリーンショット取得（snapshot）・リリース申請（release）など 5 つのプラグインで構成。`/plugin marketplace add greenstevester/fastlane-skill` でインストール可能。iOS リリース作業を Claude Code に任せたい場合の有力な選択肢。
- [Iron-Ham/XcodePreviews — CLI で SwiftUI Preview をキャプチャし Claude Code に送り込むプラグイン](https://skillselion.com/marketplace/Iron-Ham/XcodePreviews) — Xcode を開かずに SwiftUI の `#Preview` ブロックをレンダリングして Claude Code に渡せる Claude プラグイン（v2.0.7）。SPM パッケージ・ルールの SwiftUI ファイル・Xcode プロジェクトに対応。`/plugin marketplace add Iron-Ham/XcodePreviews` で導入でき、Xcode MCP なしでビジュアルフィードバックループを回せる。

## 💡 今日のおすすめ実践 Tip

**fastlane-skill でリリースフローを Claude Code から自動化する**

`greenstevester/fastlane-skill` の `beta` プラグインを使えば、TestFlight へのアップロードを Claude Code のスラッシュコマンドで実行できる。

**セットアップ手順:**

```bash
# 1. プラグインをインストール
/plugin marketplace add greenstevester/fastlane-skill

# 2. beta プラグインを有効化
/plugin install beta@fastlane-skill
```

**事前準備（fastlane 側）:**

- `fastlane/Appfile` に `apple_id` と `team_id` を設定
- Match で証明書を管理している場合は `Matchfile` も整備済みであること
- Apple ID のアプリ専用パスワードを環境変数 `FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD` に設定

**実行例（Claude Code 上で）:**

```
/beta changelog:"バグ修正とパフォーマンス改善"
```

これ一行で「ビルドナンバーをインクリメント → アーカイブ → TestFlight にアップロード → 外部テスターに配布」まで実行される。スナップショット取得（`snapshot`）・証明書更新（`match`）・App Store 申請（`release`）も別プラグインで対応しているため、リリース作業全体を Claude Code セッション内で完結させられる。

**注意点:**
- Fastlane は Homebrew でインストール済みであること（`brew install fastlane`）
- `cloud: true` のリモートセッションでは証明書ストアとキーチェーンへのアクセスが必要になるため、ローカルセッション（Mac）または CI 環境での利用を推奨
