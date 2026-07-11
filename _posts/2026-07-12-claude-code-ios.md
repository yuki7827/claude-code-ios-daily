---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-12"
date: 2026-07-12 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.207（2026-07-10）— Auto モードが Bedrock・Vertex AI・Foundry でデフォルト有効化・ターミナルフリーズ修正](https://code.claude.com/docs/en/changelog) — iOS 開発者に関連する主な変更が 4 点。① **Auto モードが Bedrock・Vertex AI・Foundry でデフォルト有効化** — 従来は `CLAUDE_CODE_ENABLE_AUTO_MODE` 環境変数でのオプトインが必要だったが、v2.1.207 から標準で有効に。AWS Bedrock や GCP Vertex AI 経由で Claude Code を利用する企業の iOS 開発チームにとって、Auto モードへの移行が即座に可能になった（無効化は設定の `disableAutoMode: true` で）。Bedrock・Vertex・Foundry 上のデフォルトモデルも Claude Opus 4.8 に変更。② **長いリスト・テーブル・コードブロックのストリーミング中にターミナルがフリーズする問題を修正** — `xcodebuild` や `swift build` の長大な出力をストリームしている最中にキー入力が遅延する問題で、大規模 iOS プロジェクトのビルド中に操作不能になるケースを解消。③ **ルール設定の glob パターンに誤った括弧記法がある場合にファイル読み込みが失敗するバグを修正** — `.claude/settings.json` の `ignorePatterns` や CLAUDE.md のルール定義でカスタムパターンを使っていた場合の誤動作を解消。④ **エージェントビューのパフォーマンスを改善** — 複数の iOS ビルドエージェントを並行監視しているときの UI レスポンスが向上。

- [Claude Code 週間利用上限が 50% 増加（〜 7月13日 6PM PDT）](https://aicatchup.com/news/claude-code-weekly-limits-50-percent-promo) — Pro・Max・Team・Enterprise（シートベース）ユーザー全員に自動適用されており、日本時間 7月14日 10AM まで有効。`/usage` コマンドで現在の上限を確認できる。iOS 開発での活用シーンとしては、夜間に XcodeBuildMCP ビルドループを複数 worktree で長時間走らせるワークフローや、App Store 審査前のコードレビュー・テスト生成・コンプライアンスチェックを一気に実行するバッチ処理で、従来より 1.5 倍長く回せるようになる。**明日（日本時間 7月14日）失効するため、今日中に集中的な自律タスクを走らせるのが得策。**

## 🛠 GitHub の動き

- [RevylAI/greenlight — App Store 申請前の静的コンプライアンス検査 CLI、Claude Code スキルとしても統合可能](https://github.com/RevylAI/greenlight) — Revyl が公開したオープンソースの Go 製 CLI。ソースコード・プライバシーマニフェスト・IPA バイナリ・App Store Connect メタデータを 30 以上のルールで検査し、Apple のレビューガイドラインに対するリジェクトリスクを提出前に洗い出す。**完全オフライン・アカウント不要・1 秒以内に完了**が特徴。検査カテゴリはプライベート API 使用・ハードコードされた秘密情報・IAP コンプライアンス・プライバシーマニフェスト・必須 Reason API 対応など。`brew install revylai/tap/greenlight && greenlight preflight /path/to/project` で即実行可能。Claude Code スキルとして連携すると「`Run greenlight preflight and fix everything until it passes`」だけで Claude が検査→修正→再検査のループを自律実行する。CI/CD 向けの JSON・SARIF 出力形式にも対応。07-11 に紹介した `devsemih/appstore-review-skill` が App Store レビューガイドラインの 5 カテゴリ（Safety/Performance/Business/Design/Privacy）を Claude が LLM ベースで判断するのに対し、Greenlight は静的解析で高速・確定的に検査する点が補完的で、両者を組み合わせた 2 段階チェックが有効。

- [devsemih/appstore-review-skill — Apple App Store Review Guidelines の全セクションを自動監査する Claude Code スキル](https://github.com/devsemih/appstore-review-skill) — Apple の App Store Review Guidelines（2026年2月6日版）に基づいて iOS アプリを審査提出前に自動検査するスキル。Swift・SwiftUI・UIKit・Flutter・React Native・Expo・KMP・.NET MAUI など主要フレームワークを自動検出して対応。Safety（有害コンテンツ・UGC モデレーション・医療免責事項）・Performance（Info.plist・プライベート API・iPad 対応）・Business（IAP・定期購読条件・ルートボックス確率開示）・Design（最小機能要件・Apple でサインイン）・Privacy（プライバシーマニフェスト・アカウント削除機能・ATT）の 5 カテゴリを網羅。プラグインマネージャから `claude plugin add devsemih/appstore-review-skill` でインストール可能。

## 📝 日本語コミュニティ

- [【第1弾・第2弾】AIエージェント4体が連携して ASO 業務を自動化する「claude-code-aso-skill」— インストール手順と実践レポート（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/fec3a66a1c7fc25ae201) — 4体の Claude Code サブエージェントが役割分担しながら App Store 最適化（ASO）業務を自動実行するスキル集を導入・実践した連載記事。[第1弾](https://qiita.com/4q_sano/items/fec3a66a1c7fc25ae201)は `claude plugin marketplace add alirezarezvani/claude-code-aso-skill` でのインストール手順と初回設定、[第2弾](https://qiita.com/4q_sano/items/91f246e790c25f30ac41)は実際の ASO 分析を実践した結果レポートで、タイトル最適化・キーワード調査・競合分析・レビュー感情分析の 4 タスクをエージェントが並行実行してコピペ即使用の成果物を生成する流れを紹介。iOS 個人開発者がリリース後の ASO を Claude Code に委任できることを示した実証記事。07-11 に紹介した `kotaro_ai_lab` の 7 種カスタムエージェントに含まれる `aso-optimizer` スキルと組み合わせると、初期 ASO 設計から継続的キーワード最適化まで一貫したフローが構築できる。

- [Claude Code標準スキルを iOS アプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — 「Claude Code 実務運用シリーズ」STEP 4 として、カスタムスキルを自作する前に公式提供の標準スキルを iOS 開発フローに当てはめて整理した記事（2026-07-04 更新）。`/code-review`（コードレビュー）・`/security-review`（セキュリティ確認）・`/verify`（動作確認）・`/run`（アプリ起動）などの組み込みスキルを iOS 開発の「実装 → ビルド → レビュー → テスト → リリース」各フェーズに対応付け、「どのスキルをどのタイミングで呼ぶか」の判断軸を公式ドキュメントに根拠を置いて整理している。カスタムスキル乱立を防ぎ、まず標準スキルを理解することで Claude Code の活用精度を上げるというアプローチが実務的。

## 🌐 海外コミュニティ / Tips

- [Claude Code Weekly Limits Jump 50% Through July 13 — iOS ビルドループ・夜間自律タスクへの活用（AI Catchup）](https://aicatchup.com/news/claude-code-weekly-limits-50-percent-promo) — 週間利用上限の 50% 増加がなぜ iOS 開発者に有利かを解説した記事。5時間ウィンドウ制限と週間上限の両方が引き上げられたことで、`/goal` コマンドによる自律ループが途中でキャップに達して止まるリスクが大幅に低下。XcodeBuildMCP を使った「Swift 6 移行 → ビルド → テスト → PR 作成」の長時間ループや、Fastlane スキルを使ったリリース自動化の通しランが 1 セッションで完結しやすくなる。増加は明日（日本時間 7月14日 10AM）に失効するため、週次で重いビルドタスクを予定している場合は今日中に投入するのが効果的。

- [Greenlight — An Open Source CLI Tool to Squash App Store Rejections "Before Submission"（Note / AI-Driven Lab）](https://note.com/ai_driven/n/n3bae9c441792) — Greenlight の設計思想と実際のスキャン結果例を紹介した英語混じりの日本語解説記事。プライベート API 使用・ハードコードされた API キー・プライバシーマニフェストの不備・必須 Reason API への未対応といった「審査でよくリジェクトされる原因 Top 10」をそれぞれ Greenlight がどのルールで検出するかを実例付きで説明。Claude Code スキルとの組み合わせデモも収録されており、`greenlight preflight` の出力を Claude が読んで `PrivacyInfo.xcprivacy` を自動修正するフローを動画で確認できる。

## 💡 今日のおすすめ実践 Tip

**「RevylAI/greenlight + devsemih/appstore-review-skill で App Store 申請前の 2 段階コンプライアンスチェックを自動化する」**

App Store リジェクトの主な原因はパターン化されており、提出前に Claude Code が自動検出・修正できます。静的解析（Greenlight）と LLM 解析（appstore-review-skill）を組み合わせた 2 段階フローを構築します。

**1. Greenlight のインストール**

```bash
# Homebrew でインストール（Go バイナリ・macOS 用）
brew install revylai/tap/greenlight

# バージョン確認
greenlight --version
```

**2. プロジェクトの静的コンプライアンス検査（高速・確定的）**

```bash
# ソースコード + IPA バイナリ + メタデータを一括検査
greenlight preflight /path/to/MyApp \
  --ipa path/to/MyApp.ipa \
  --format json > greenlight-report.json

# CI/CD 向け：問題があれば非ゼロ終了コードを返す
greenlight preflight /path/to/MyApp --ci
```

**3. Claude Code スキルで LLM ベースの深い審査チェック**

```bash
# appstore-review-skill のインストール
claude plugin add devsemih/appstore-review-skill

# セッション内でレビュー実行
/appstore-review
"MyApp の App Store 審査 Guidelines 準拠をチェックして。
特に以下を重点確認：
- Sign in with Apple の要件（サードパーティログインがある場合）
- アカウント削除機能の実装
- ATT（AppTrackingTransparency）の正しいタイミング
- IAP がある場合の利用規約表示"
```

**4. Greenlight を Claude Code スキルとして「Fix until green」ループ**

```
# Claude Code セッション内で指示するだけ
"Run greenlight preflight and fix everything until it passes.
Priority order: critical issues first, then high, then medium."
```

**よくあるリジェクト原因と Greenlight の検出ルール対応表**

| リジェクト原因 | Greenlight ルール | 自動修正難易度 |
|--------------|-----------------|-------------|
| `PrivacyInfo.xcprivacy` の不備 | `privacy-manifest-required-reasons` | ★★★ 高（内容が明確） |
| ハードコードされた API キー | `hardcoded-secrets` | ★★★ 高（`.env` 切り出しに誘導） |
| プライベート API 使用 | `private-api-usage` | ★ 低（代替 API を人間が判断） |
| IAP の外部決済誘導 | `iap-external-payment` | ★★ 中（UI の変更が必要） |
| 必須 Reason API の未申告 | `required-reason-api` | ★★★ 高（宣言の追加） |

週間利用上限 50% 増（〜 7月14日 10AM JST）の今日は、`greenlight preflight → Claude が修正 → 再スキャン → appstore-review-skill で深掘り` の一連フローを長時間ループで一気に完走させるチャンスです。
