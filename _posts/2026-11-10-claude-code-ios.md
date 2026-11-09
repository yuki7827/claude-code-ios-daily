---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-10"
date: 2026-11-10 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（v2.1.319 以降の新規リリースは 2026-11-10 時点で確認できず。[公式 Changelog](https://code.claude.com/docs/en/changelog) を参照）

## 🛠 GitHub の動き

- [conorluddy/ios-simulator-skill（GitHub・★1.3k）](https://github.com/conorluddy/ios-simulator-skill) — Claude Code などの AI エージェント向け iOS シミュレーター操作スキル。`xcodebuild` と `xcrun simctl` をラップし、長大な Xcode ログの代わりに 1 行サマリーを返す「プログレッシブ開示」設計が特徴。ビルド・ジェスチャー・キーボード入力・アクセシビリティ監査など 31 スクリプト、`IOS_SIM_*` 環境変数でタイムアウトや出力上限を調整可能。インストールは `/plugin marketplace add conorluddy/ios-simulator-skill` → `/plugin install ios-simulator-skill@conorluddy` の 2 コマンド。要件は macOS 15+・Xcode 26+・Python 3.12+・idb（`brew install facebook/fb/idb`）。

## 📝 日本語コミュニティ

- [Claude Code 標準スキルを iOS アプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano・2026-05-03、2026-07-21 更新）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — `/simplify` は `/code-review` に改名済みで、現在はバグ検出に特化した差分レビューツールになっている点など changelog ベースで実態を整理した記事。iOS 開発で使う際は、Xcode バージョン・SwiftUI / UIKit の別・対象領域（WKWebView、ログイン、アナリティクス等）を明記してレビューを依頼するのが効果的とされる。`/batch` は単一ファイル編集には過剰で、スキーマ変更など横断的改修に適している点も整理されている。

- [Xcode 26.3 の MCP とは何か — なぜ必要で、どう使うのか。7つのスキルで実践する（Zenn / dsgarage・2026-03-05）](https://zenn.dev/dsgarage/articles/xcode-263-mcp-skills) — Xcode 26.3 組み込みの MCP サーバーを「AI に目と手を与えるもの」として解説し、7 つのスキル（ビルド／テスト、プレビュー確認、非推奨 API 修正、ファイル操作、診断、`/ios-simplify`、`/ios-batch`）の使い分けを実例付きで紹介。セットアップは macOS 26 Tahoe と Apple Silicon が要件で、`xcrun mcpbridge` 経由で Claude Code と接続する。

## 🌐 海外コミュニティ / Tips

- [Xcode MCP × Claude Code plugin で iOS ビルドを自動化する（Zenn / kyoichi・2026-02-11）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Xcode 26.3 RC の組み込み MCP サーバーと `xcodebuild` を状況に応じて切り替える「ios-dev」プラグインの設計を解説した英語併用記事。MCP が使える場合は MCP 経由で、スキームや SPM プロジェクトが絡む場合は CLI フォールバックするハイブリッド判定ロジックが詳述されている。MCP 呼び出しはサブエージェント不可のためスキルで処理し、CLI ビルドは Sonnet サブエージェントに委譲してメインコンテキストを汚さない設計も参考になる。

## 💡 今日のおすすめ実践 Tip

**`ios-simulator-skill` でシミュレーター操作のトークンコストを削減する**

Claude Code でシミュレーターを操作させると、素の `xcodebuild` / `simctl` 出力がそのままトークンになって文脈を圧迫しやすい。`conorluddy/ios-simulator-skill` はビルド結果を xcresult ID 付きの 1 行サマリーで返し、必要なときだけ詳細を引き出す「プログレッシブ開示」でこれを解消している。

```bash
# セットアップ（前提: macOS 15+, Xcode 26+, Python 3.12+）
brew install facebook/fb/idb  # idb（オプションだが座標タップに必要）

# Claude Code でインストール
/plugin marketplace add conorluddy/ios-simulator-skill
/plugin install ios-simulator-skill@conorluddy
```

有効化後は Claude がビルドとシミュレーター起動を自律的に行い、エラー時のみログを取得するループになる。タップ操作はピクセル座標ではなくアクセシビリティラベルで指定できるため、UI を変更してもプロンプトを書き直す必要がない。

```
# Claude への例示プロンプト（プロジェクト CLAUDE.md に追記するとより確実）
シミュレーターを起動してアプリをビルドし、ログイン画面の「サインイン」ボタンをタップしてください。
タップはアクセシビリティラベルを使ってください（座標は使わないこと）。
```

トークンコストと操作精度のバランスをとりたい場合は、同スキルの `screen_mapper.py` によるコンパクトな画面サマリー機能も活用できる。
