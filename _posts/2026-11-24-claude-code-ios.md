---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-24"
date: 2026-11-24 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [週次まとめ（week of November 22, 2026）より — v2.1.328 補足：`mcp_tool` フックに `"onFailure": "block"` が追加（DEV Community / aicoding-guide）](https://dev.to/aicoding-guide/this-week-in-claude-code-codex-and-gemini-cli-week-of-november-22-2026-1ofi) — v2.1.328 のリリースで追加された機能のうち、iOS CI 観点で見落とされがちな点。MCP ツール呼び出しが失敗した際にアクションをブロックできる `"onFailure": "block"` オプションが `mcp_tool` フックに追加されている。XcodeBuildMCP や Xcode MCP Bridge の呼び出しが失敗した場合にビルドをサイレントに続行させないセーフガードとして有用。`.claude/settings.json` の `hooks.mcp_tool` に `"on_failure": "block"` を設定するだけで、MCP 接続トラブルが原因の「CI が緑なのにビルドされていない」問題を防げる。

## 🛠 GitHub の動き

- [conorluddy/ios-simulator-skill（GitHub、スター 1.3k）](https://github.com/conorluddy/ios-simulator-skill) — Claude Code・Codex 向けの iOS シミュレーター特化スキル（MIT）。最大の特徴は **「Progressive Disclosure」** 設計で、画面状態を 1 画面あたり約 35 トークンの要約として返し、詳細が必要な場合のみ追加取得する。生の `idb ui describe-all` 出力（約 6,700 トークン/画面）と比べてトークン消費を 99% 削減し、画面数が多い E2E フローでもコンテキストが溢れにくい。座標タップではなくアクセシビリティ API でラベル・ロール・識別子を使って要素を特定する方式で誤操作リスクが低い。ビルド・ジェスチャー・キーボード入力・権限管理・ローカライズ検査を含む 31 本のスクリプトを同梱。**iPhone Duo サポートも追加**（`duo.py` でヒンジ姿勢・角度を設定し `--display inner/outer` で操作対象ディスプレイを指定する方式）。

## 📝 日本語コミュニティ

- [Swift で作る 10×5 マスの数字パズル：計算と演出の分離、ボット 1,000 回での難易度校正、Claude Code での開発（Qiita / andygrave）](https://qiita.com/andygrave/items/6d7f4d4807b302c22a4f) — iPhone 向けパズルゲーム「GRID BLAST 50」（SwiftUI + SpriteKit）を Claude Code で開発した実践記録（2026-11-20）。コミット 92 件のうち 45 件が Claude Code 名義。難易度調整には自作 CLI ボットに各ステージを 1,000 回プレイさせるアプローチを採用。ゲームのルール層を UI に依存しない設計にして Linux 上でも `swift test` を通し、iOS ビルド確認は GitHub Actions の macOS ランナーで実施するパターンが参考になる。「決まっていない仕様は理由付きで仮決めさせて設計書に書かせ、ルール変更は番号付きの合議として記録する」運用方針も実用的。

## 🌐 海外コミュニティ / Tips

- [I Gave Claude Code a Simulator and It Started Tapping and Taking Screenshots on Its Own（DEV Community / shimo4228）](https://dev.to/shimo4228/i-gave-claude-code-a-simulator-and-it-started-tapping-and-taking-screenshots-on-its-own-57bj) — XcodeBuildMCP をクイズアプリに適用し、コード 1 行も書かずにビルド・テスト（608 件、失敗 0 件）・アプリ起動・スクリーンショット確認・UI 操作をすべて Claude Code に任せた実践報告。`snapshot_ui` でアクセシビリティツリーを読み取り、ラベルや識別子でタップする方式で、実機テストで修正不要だった。**「SwiftUI コンポーネントに `accessibilityIdentifier` を実装前に設計しておくと自動検証が安定する」** という Tips が使いやすい。XCUITest との住み分けとして「XCUITest は事前シナリオの回帰テスト、XcodeBuildMCP は AI が画面を見て判断する探索的テスト」という整理も明快。

## 💡 今日のおすすめ実践 Tip

**`conorluddy/ios-simulator-skill` でシミュレーター操作のトークン消費を劇的に削減する**

XcodeBuildMCP だけで `idb` の UI ツリー全体を都度 Claude に渡すと 1 画面あたり 6,700 トークンを超えることがあり、複数画面を連続検証するとコンテキストが急速に消費される。`ios-simulator-skill` の Progressive Disclosure 設計では、まず 35 トークン前後の要約を返し、エラーや追加詳細が必要なときだけ詳細を取得するため、E2E フロー全体でも文脈が溢れにくい。

```bash
# Claude Code プラグインとして導入（1 コマンド）
/plugin marketplace add conorluddy/ios-simulator-skill

# または npx 経由
npx skills add ios-simulator-skill
```

Duo 対応で追加された `duo.py` スクリプトは、起動中の iPhone Duo の UDID と、ヒンジ角度（1〜179 度）・ポーズ（0: closed / 1: half-folded / 2: open）を引数として受け取る。操作対象のディスプレイは `--display inner` または `--display outer` で切り替え可能。内側ディスプレイへの座標タップは現時点で未検証のため、アクセシビリティ ID でのタップを使うこと。

XcodeBuildMCP との組み合わせでは「ビルドは XcodeBuildMCP、UI 操作とスクリーンショットは ios-simulator-skill」という役割分担がトークン効率・操作精度の両面でバランスが取れている。
