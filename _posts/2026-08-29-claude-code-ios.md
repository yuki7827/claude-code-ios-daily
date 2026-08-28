---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-29"
date: 2026-08-29 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.248（2026-08-27）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更点: ① **`--restricted` フラグ追加**（`CLAUDE_CODE_RESTRICTED=1` でも有効）— ファイル I/O ツールを作業ディレクトリ内に限定し、コマンド実行系ツールと WebFetch を除外、`bypassPermissions` を拒否するモードが追加された。iOS CI パイプラインで Claude Code を走らせる際に、リポジトリの外への書き込みや意図しないコマンド実行を防ぐ用途に直接使える; ② **`experimental.cacheTtl` をエージェントフロントマターに追加** — エージェントごとにプロンプトキャッシュの TTL を設定できるようになった。ビルドログ解析用エージェントと実装用エージェントで TTL を分けることが可能; ③ **クロスセッションメッセージングを Bedrock・Vertex・Foundry でも有効化** — AWS Bedrock 経由で Claude Code を使う iOS チームでも `SendMessage` によるセッション間連携が使えるようになった; ④ **長時間セッションでのプロンプトキャッシュ ミス（約1時間に1回）を修正** — OAuth トークンリフレッシュ後にツール定義が再描画されてキャッシュミスが発生していた問題を解消。Xcode ビルドログを積み上げる iOS セッションでのコスト節約に効く; ⑤ **`/ultrareview` がコミット前の認証情報ファイルをアップロードしていたバグを修正** — iOS プロジェクトで `.p8` や `Secrets.xcconfig` が未コミット状態でも安全に動作するように

- **v2.1.250（2026-08-28）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— バグ修正と信頼性の改善。詳細なリリースノートは割愛。

- **v2.1.251（2026-08-28）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発観点での主な変更点: ① **シムリンクを悪用した承認外ファイルへのアクセスを修正（重要セキュリティ修正）** — `Read`・`Write`・`Edit` ツールが権限チェック後にパス内のシムリンクがすり替わっていた場合、作業ディレクトリ外のファイルを読み書きできた問題を修正。iOS プロジェクト内に動的生成シムリンクを含むケース（Derived Data や SPM キャッシュへのリンクなど）で悪用される可能性があった; ② **プラグインコマンドのパストラバーサルエラーを修正** — マーケットプレースのエントリがプラグインディレクトリ外を指す場合にパストラバーサルエラーとして拒否するようになった; ③ **`PreModelSwitch` / `PostModelSwitch` フックイベントを追加** — モデル切り替えをフックでブロック・確認・注釈付けできるようになった。iOS CI でモデルを意図せず切り替えてしまうことを防ぐユースケースに応用できる; ④ **フォアグラウンドサブエージェントのツール呼び出しを Remote Control クライアントにライブストリーミング** — Remote Control（iPhone）経由でサブエージェントの動きをリアルタイム監視できるようになった; ⑤ **`/cost` にプロンプトキャッシュのヒット率・ウォーム/コールド状態を表示** — セッションのキャッシュ効率を数値で確認できる; ⑥ **並列サブエージェント実行時の TUI ラグを修正** — 複数の Xcode ビルド解析タスクを並列で走らせる際の UI 遅延が解消

## 🛠 GitHub の動き

- [Issue #23550 — Xcode MCP の Automation 権限要求ツールが bundle ID 未設定の CLI クライアントでハングする（OPEN）](https://github.com/anthropics/claude-code/issues/23550) — Xcode 26.3 が提供するネイティブ MCP サーバーの `RenderPreview`・`BuildProject`・`ExecuteSnippet`・`RunSomeTests` などのツールは macOS の Automation（AppleEvents / TCC）権限を要求するが、Claude Code CLI のような bundle ID を持たないプロセスはこの権限ダイアログに「許可」しても TCC が指紋を記録できず、以降の AppleEvent が承認されないまま無限ハングに陥る問題。SwiftUI プレビューキャプチャなど Xcode 固有ツールを CI ランナーから呼ぼうとすると詰まるケース。**現時点の回避策**: bundle ID を付与したラッパースクリプトを挟む、またはデスクトップセッションから一度 GUI で権限承認してから CI に渡す。

## 📝 日本語コミュニティ

- [iOSアプリ開発を行う際に、ビルドやファイル追加で失敗しない方法 【Claude Code編】（NIFTY engineering）](https://engineering.nifty.co.jp/blog/37202) — Claude Code の標準 `Write` コマンドで作成したファイルは `.pbxproj` に自動追加されずビルドターゲット外になる問題と、シミュレータ起動失敗の原因・対策をまとめた実務記事。Xcode 26.3 の `XcodeWrite` ツールを使う方法やフォルダリファレンスを活用して `.pbxproj` 直接操作を避ける方法を解説している。iOS 初学者が Claude Code を使い始めて最初に躓くポイントを網羅している。初出。

## 🌐 海外コミュニティ / Tips

- [AgentHub（jamesrochabrun / GitHub、483 stars）](https://github.com/jamesrochabrun/AgentHub) — Claude Code と Codex のセッションを一元管理する macOS アプリ。iOS 開発者向けに特に有用な機能: **iOS Simulator 統合**（シミュレータのビルド・インストール・起動・ミラーリング・UI 要素のアノテーションとホットリロードをアプリ内から操作）、**Git Worktree 管理**（並列開発のための sibling worktree の作成・管理）、**差分レビュー**（スプリットペインの diff 表示とインライン編集）。`claude` CLI と `codex` CLI の両方のセッションを同一 UI で管理できる点が特徴。初出。

- [Claude Code Plugins for iOS Teams — Automation, Agents, and Guardrails（Medium / Wesley Matlock）](https://medium.com/@wesleymatlock/claude-code-plugins-for-ios-teams-automation-agents-and-guardrails-221a68eb57d5) — iOS チーム向けに Claude Code プラグインシステムを活用する方法を解説した記事。自動化スクリプト・エージェントの設定・ガードレール（権限制限・hook によるチェック）の組み合わせで、チーム全員が一貫した Claude Code 利用環境を持てるようにする構成を紹介。`--restricted` モード（v2.1.248 で追加）との組み合わせも解説している。初出。

## 💡 今日のおすすめ実践 Tip

**「v2.1.248 の `--restricted` モードを iOS CI に組み込んで意図しない書き込みを防ぐ」**

v2.1.251 のシムリンク脆弱性修正（ファイルツールが権限チェック後に差し替えられたシムリンクを経由して作業ディレクトリ外にアクセスできた問題）と、v2.1.248 の `--restricted` モードを合わせると、iOS CI パイプラインでの Claude Code 実行を大幅に安全にできます。

**`--restricted` モードでできること・できないこと**

```
できること:
  - ファイルの Read / Write / Edit（作業ディレクトリ内のみ）
  - --tools で明示的に許可したツールの呼び出し

できないこと:
  - Bash / シェルコマンドの実行（xcodebuild 等を直接呼べない）
  - WebFetch
  - bypassPermissions の使用
  - user / project / local の設定ファイルの読み込み
```

**iOS CI での基本的な使い方（GitHub Actions）**

```yaml
- name: Claude Code でビルドエラーを解析（制限モード）
  run: |
    claude --restricted \
           --tools "Read,Write,Edit" \
           --print "以下のビルドログのエラーを分類し、
                    修正案を _claude_report.md に書き出してください:
                    $(cat build.log | tail -200)"
  env:
    CLAUDE_CODE_RESTRICTED: "1"  # フラグの代わりに env var でも有効
    ANTHROPIC_DEFAULT_MODEL: claude-haiku-4-5-20251001  # ログスキャンは軽量モデルで十分
```

ポイントは `--restricted` モード下では Bash が使えないため、**Claude Code にシェルコマンドを叩かせる作業は別ステップで行い、その結果を Claude Code に渡して解析・報告だけをさせる**という役割分担です。

**`experimental.cacheTtl` でエージェントのキャッシュを最適化する**

v2.1.248 で追加されたエージェントフロントマターの `cacheTtl` を使うと、ビルドエラー解析エージェントのキャッシュ TTL をセッション全体の設定と独立して制御できます:

```markdown
---
description: Xcode ビルドエラーを解析して修正案を返すエージェント
experimental:
  cacheTtl: 300  # 5 分（ビルドのたびにログが変わるので短く設定）
---

以下のビルドログを受け取り、エラーを種類別に分類して修正案を提示してください。
```

ビルドログは実行ごとに内容が変わるため、`cacheTtl` を短くして古いキャッシュを素早く破棄するのが iOS CI では合理的です。逆に、コードの静的解析用エージェントはコミットをまたいでキャッシュが活きるため、長い TTL が有効です。

**シムリンク脆弱性（v2.1.251 修正）への対応**

Derived Data（`~/Library/Developer/Xcode/DerivedData`）や SPM キャッシュ（`~/.swiftpm/cache`）への sibling symlink を含む iOS プロジェクトでは、今すぐ `claude update` で v2.1.251 以降に更新してください:

```bash
claude update
claude --version  # v2.1.251 以降であることを確認
```
