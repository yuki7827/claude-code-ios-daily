---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-21"
date: 2026-07-21 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Fable 5 が Max・Team Premium プランに正式含有（2026-07-20 開始）](https://support.claude.com/en/articles/15424964-claude-fable-5-on-your-plan) — Anthropic が 7 月 20 日より、Claude Fable 5 を Max および Team Premium プランに標準含有（週次利用枠の 50% まで追加費用なし）すると発表。Pro・Team Standard ユーザーは引き続き従量制クレジット（$100 一度限りクレジット付き）でのみアクセス可能。Claude Code を Max プランで使っている iOS 開発者は、`claude --model claude-fable-5` で Xcode プロジェクトの複雑なアーキテクチャ解析・Swift Concurrency のリファクタリング・大規模コンテキストを要する作業に Fable 5 を惜しみなく投入できるようになった（詳細は Tips セクション参照）。なお最新の Claude Code 自体は v2.1.215（2026-07-19 リリース）が引き続き最新版。

## 🛠 GitHub の動き

- [Issue #36151 — Claude モバイルアプリ（iOS/Android）: 異なるメールアドレスのアカウント間マルチアカウント切り替えが不可（OPEN・👍628件・2026-07-20 更新）](https://github.com/anthropics/claude-code/issues/36151) — iOS の Claude アプリで、個人（Pro/Max）と会社（Team/Enterprise）など異なるメールアドレスに紐づく複数アカウントを切り替えるには、毎回サインアウト → サインインが必要な問題。現行のアカウントスイッチャーは「同一メールの複数組織」間でしか機能しない。XcodeBuildMCP のビルドセッションを iPhone の Remote Control でモニタリングしながら、個人アカウントと仕事アカウントをシームレスに切り替えたいというユースケースで直接影響する。関連する Desktop 版要望（Issue #18435、👍666件）と合わせて 2026-07-20 に再度コメントが集まっており、Anthropic チームの対応が注目されている。

## 📝 日本語コミュニティ

- [iPhoneだけでiOSアプリ開発するワークフロー（Zenn / oikon）](https://zenn.dev/oikon/articles/dev-from-mobile) — Mac を使わず iPhone 単体で iOS アプリ開発を完結させるワークフロー。Claude Code GitHub Actions または Codex Cloud で PR を作成し、CodeRabbit の自動レビュー → `@claude` / `@codex` メンションで修正指示 → Xcode Cloud が成功ビルドを TestFlight に自動アップロードするパイプラインを構築。「iPhone でスクリーンショットを撮って PR にアップロードするだけで Claude が UI 修正案を出してくれる」という運用が最大の差別化ポイント。Remote Control や NeriTerm なしで完全クラウドサイドに処理を逃がす構成として参考になる。

- [iOSアプリ開発に役立つAgentic Coding集（Zenn / mtfum）](https://zenn.dev/mtfum/articles/ios_agentic_coding) — iOS アプリ開発で活用できる Agentic Coding ツール・手法をまとめたキュレーション記事（2026-06-22）。Xcode 26.3 で導入され Xcode 27 で実用安定した公式 Xcode MCP（`mcpbridge`）の概要と設定方法（macOS 26 Tahoe + Apple Silicon 必須・クラウドリレーなしのローカル完結）から、`RenderPreview` による SwiftUI プレビュー画像の AI 検証フロー、Claude Code・Cursor・Codex の使い分けまで体系的に整理している。初めて Agentic Coding に取り組む iOS 開発者の出発点として有用。

## 🌐 海外コミュニティ / Tips

- [The State of Agentic iOS Engineering in 2026（Thomas Ricouard / dimillian / Medium）](https://dimillian.medium.com/the-state-of-agentic-ios-engineering-in-2026-c5f0cbaa7b34) — Ice Cubes（Mastodon クライアント）作者の Thomas Ricouard が agentic iOS 開発の現状を包括的にレビューした記事。「2026 年は AI 活用が実験から本番エンジニアリングへ移行した転換点」と位置づけ、Claude Code を核とした自己診断・自己修正ループ（ビルド → テスト → 結果確認 → 修正）が安定して回るためにコードベース側が提供すべき「ツールの足場」の重要性を解説。「ドライバー（AI を操る人間）のスキル次第で、凄まじい量の高品質コードを従来の何分の一かの時間で生産できる」という実感が Hacker News でも多くのコメントを集めた。

- [ios-simulator-mcp v1.3.3 — コマンドインジェクション脆弱性修正（joshuayoes / 2026-06-20）](https://github.com/joshuayoes/ios-simulator-mcp/releases/tag/v1.3.3) — iOS シミュレーターを操作する MCP サーバー `joshuayoes/ios-simulator-mcp` の v1.3.3 でコマンドインジェクション脆弱性（GHSA-6f6r-m9pv-67jw、CWE-78、中程度）が修正された。`ui_tap`・`ui_type`・`ui_swipe`・`screenshot` 等のツールで文字列補間による unsafe なシェルコマンド構築が行われていた問題を、`child_process.exec` を `execFile` に置き換え・zod による入力バリデーション・UDID 正規表現チェック等で対処。v1.3.3 未満を使っている場合は即アップデートが必要。

## 💡 今日のおすすめ実践 Tip

**「Claude Fable 5 が Max プランに含まれた今、iOS 開発の"重い仕事"を惜しみなく投げる」**

7 月 20 日より Max・Team Premium プランで Fable 5 が利用枠の 50% まで追加費用なしで使えるようになりました。Fable 5 は長いコンテキスト処理・高度な推論・複雑なコード変換が得意なため、従来は「トークン節約のためにやりたくなかった」作業が解放されます。

**Claude Code での Fable 5 指定方法**

```bash
# セッション単位で Fable 5 を使う
claude --model claude-fable-5

# 環境変数でデフォルトモデルを設定する
ANTHROPIC_MODEL=claude-fable-5 claude

# settings.json でプロジェクト全体に適用する（.claude/settings.json）
# "model": "claude-fable-5"
```

**iOS 開発で Fable 5 が特に威力を発揮するケース**

| タスク | Fable 5 が有効な理由 |
|---|---|
| 大規模 Swift リファクタリング（数万行） | 長コンテキストに強く、ファイル間の依存関係を広く把握して整合性を保てる |
| TCA（The Composable Architecture）の State / Effect 設計 | 抽象度が高い設計パターンの推論精度が高い |
| `async / await` + `actor` モデルの並行バグ調査 | 並行処理の微妙な state 遷移を複数ファイルにまたいで追跡できる |
| `.pbxproj` を含む Xcode プロジェクト全体解析 | 大量のバイナリテキストが混在しても重要な箇所を抽出できる |
| 全テストスイートのモデルコード生成 | 長い出力が安定して高品質 |

**Max プランの 50% 枠管理のコツ**

Fable 5 は 1 リクエストあたりのトークン消費が大きいため、「全タスクに Fable 5」は枠を使い切りやすくなります。Claude Code の `/status` コマンドで現在の利用率を確認しながら、軽い作業（コメント修正・命名変更）は Sonnet/Haiku に任せ、複雑な設計判断だけ Fable 5 に回す**モデル使い分け戦略**が Max プランの持続可能な運用には有効です。

```bash
# 現在の利用状況を確認
/status

# 軽い作業は Sonnet に切り替える
/model claude-sonnet-5
```

Issue #36151（iOS アプリでのマルチアカウント切り替え）が解決するまでの間は、仕事アカウント（Team）と個人アカウント（Max）の Fable 5 利用枠が別管理になる点にも注意が必要です。Remote Control で iPhone からモニタリングしながら Mac 側の Claude Code を Fable 5 で走らせる運用では、どちらのアカウントで認証しているかを意識しておきましょう。
