---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-12"
date: 2026-09-12 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.269（2026-09-11）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発者に影響するアップデートが複数。

  ① **`claude plugin eval` コマンドを追加** — プラグインの eval suite を Claude Code に対して実行し、採点済み・再現可能な結果を JSON + HTML レポートで取得できるようになった。CarolaneLFBV/ios-development-agents（後述）や xclaude-plugin など iOS 向けプラグインの品質を CI で継続的に検証する際に活用できる。

  ② **`/output-style [name]` コマンドを追加** — 出力スタイルの一覧表示・切り替えが Remote Control セッション（iPhone からの操作）やクラウド / ヘッドレスセッションでも可能に。夜間バッチで iOS ビルドを監視する際にログの冗長度を手元で調整できる。

  ③ **Bash ツール結果にファイル変更の diff を表示**（`bashEditDiffEnabled` 設定時） — `xcodebuild` や `simctl` 等の Bash コマンド実行後、変更ファイルの diff をツール結果に含めることができるようになった。ビルドスクリプトがどのファイルを書き換えたかを Claude が即座に把握でき、デバッグループの精度が向上する。

  ④ **`CLAUDE_CODE_WORKFLOW_MAX_CONCURRENT_AGENTS`（1〜256）を追加** — マルチエージェントワークフローの最大並列エージェント数を環境変数で設定可能に。`xcodebuild test` を複数スキームで並列実行するようなワークフローに対してスループットを調整できる。

  ⑤ **バグ修正: 日本語等スペースなし言語でプロンプト候補が落ちる問題** — 日本語で Claude Code に iOS 関連の指示を打ち込む際、プロンプト入力候補（補完サジェスト）がドロップされていた問題を修正。

  ⑥ **バグ修正: Plugin アーカイブのセキュリティ問題** — Plugin アーカイブが同一マシン上の他ローカルユーザーに読み取られる可能性があり、かつ world-writable ビットが残留していた問題を修正。CI サーバーやチーム共有環境で iOS 開発プラグインを利用している場合は最新版への更新を推奨。

## 🛠 GitHub の動き

- **[CarolaneLFBV/ios-development-agents v5.2.0（github.com/CarolaneLFBV）](https://github.com/CarolaneLFBV/ios-development-agents)** — 「1 plugin = 1 job」のコンセプトで設計された超粒度 iOS 開発プラグイン。7つの Opus エージェント（`swift-specialist` / `swiftui-specialist` / `architecture-specialist` / `performance-specialist` / `testing-specialist` / `security-specialist` / `devops-specialist`）と 15 コマンド（`implement` / `test` / `localize` / `publish` など）を備え、Context7 経由で公式ドキュメントを参照しながら開発タスクを自律実行する。v5.2.0 の主な変更点: ①タスクの種類に応じて適切なエージェントへ自動委譲するルーティング機能を強化、②システムプロンプトを約30%削減してコンテキスト消費を改善、③スマートフック機能のアンチパターン検出を向上。`/plugin marketplace add CarolaneLFBV/ios-development-agents` でインストール可能。

- **[kylehughes/apple-platform-build-tools-claude-code-plugin（github.com/kylehughes）](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin)** — `xcodebuild` / `xcrun simctl` / `devicectl` / Swift Package Manager を活用して Apple プラットフォームのビルド・テスト・アーカイブを Claude Code から実行するプラグイン。スキームの自動検出、シミュレーター管理、テストフィルタリング・並列実行・カバレッジ分析、iOS 17 以降の実機デプロイに対応。プロジェクト固有の設定を抽出してコンテキストを汚染せずに Claude へ渡す設計になっており、ビルドログの raw 出力をコンテキストに流し込む方法よりトークン効率が高い。

## 📝 日本語コミュニティ

- 該当なし（2026-09-12 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[xclaude-plugin — 8 modular MCPs × 24 iOS tools でコンテキストを節約する Claude Code プラグイン（github.com/conorluddy）](https://github.com/conorluddy/xclaude-plugin)** — `xcodebuild` の raw ログをそのままコンテキストに流すと数千トークンを消費する問題を解決するプラグイン。ビルドエラー・テスト結果・ビルドログを構造化 JSON として圧縮して Claude に渡すことで最大 87% のトークン削減を実現する。8つのモジュラー MCP（`xc-build` / `xc-launch` / `xc-interact` / `xc-testing` 他）から使いたいものだけを選択してロードする設計で、各 MCP のコンテキスト消費は 300〜3500 トークン程度。アクセシビリティツリーを用いた UI オートメーション（スクリーンショット不要）により UI 検証コストも低減できる。`claude plugin eval` が利用可能になった v2.1.269 と組み合わせて eval テストを追加することで、プラグインの信頼性を継続的に検証する体制を整えるのが今後の活用イメージとして期待される。

## 💡 今日のおすすめ実践 Tip

**「`claude plugin eval` で iOS 開発プラグインの品質をテストする（v2.1.269 〜）」**

v2.1.269 で追加された `claude plugin eval` コマンドを使うと、自分が利用（または開発）している iOS 向け Claude Code プラグインの eval suite を実行し、採点済み・再現可能な結果を得られるようになりました。CarolaneLFBV/ios-development-agents や kylehughes/apple-platform-build-tools-claude-code-plugin のような公開プラグインに eval suite が含まれている場合は、以下の手順で動作確認・バージョン比較が可能です。

**基本的な使い方**

```bash
# プラグインの eval suite を実行
claude plugin eval

# 結果は JSON + HTML レポートとして出力される
# .claude/plugins/<plugin-name>/evals/<case-name>/ 以下に保存
```

**eval suite の構造（プラグイン開発者向け）**

```
evals/
  build-swiftui-view/
    prompt           # 例: "SwiftUI で検索バー付きのリスト画面を作って"
    graders/
      has_list.py    # List ビューが含まれるか採点するスクリプト
      builds.sh      # xcodebuild で実際にビルドできるかチェック
  fix-build-error/
    prompt           # "このビルドエラーを修正して: [エラーログ]"
    graders/
      compiles.sh    # 修正後にコンパイルが通るか確認
```

**CI での活用例（GitHub Actions）**

```yaml
- name: Run iOS plugin evals
  run: |
    claude plugin eval --output json > eval-results.json
    # 合格率が閾値未満なら CI 失敗
    python3 check_eval_results.py eval-results.json --threshold 0.8
```

プラグインの更新に伴って iOS 関連タスクの成功率が下がっていないか、プラグインの eval suite で定期的にチェックする習慣をつけておくと、チームで共有している CLAUDE.md の設定変更などが意図せずエージェントの挙動を壊したときに素早く検知できます。
