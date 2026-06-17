---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-18"
date: 2026-06-18 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.179（2026-06-16）](https://code.claude.com/docs/en/changelog) — バグ修正リリース。最大の注目点は Linux 環境での sandbox `denyRead`/`allowRead` glob 問題の修正で、iOS/macOS プロジェクトの DerivedData のような大規模ディレクトリツリーを glob 指定すると Bash ツール説明が肥大化してセッションが使えなくなる不具合が解消された。あわせて、mid-stream 接続切断時に途中レスポンスが保持されるようになり、CI など長時間ビルドの途中で接続が落ちても結果が失われにくくなった。リモートセッションでのプラグイン読み込みパフォーマンスも改善。

## 🛠 GitHub の動き

- [rshankras/claude-code-apple-skills](https://github.com/rshankras/claude-code-apple-skills) — Apple プラットフォーム開発向けの Claude Code Skills 集。約 150 スキル・23 カテゴリを収録し、SwiftData・SwiftUI（Liquid Glass・Charts 3D）・Apple Intelligence（Foundation Models・Visual Intelligence・App Intents）・visionOS・App Store 最適化・プライバシーマニフェスト・生体認証・パフォーマンスプロファイリングまでをカバー。`~/.claude/skills/` にコピーするだけで Claude Code に取り込める MIT ライセンス。

- [keskinonur/claude-code-ios-dev-guide](https://github.com/keskinonur/claude-code-ios-dev-guide)（★754） — PRD 駆動ワークフロー・ultrathink（拡張思考）・Swift/SwiftUI に最適化された Claude Code 設定ガイド。CLAUDE.md 階層構成、カスタムスラッシュコマンド、エージェントスキル、サンドボックス権限設定、XcodeBuildMCP 連携の具体的な設定例を網羅。「PRD → タスク分割 → 実装」のループと Plan Mode（読み取り専用アーキテクチャ分析）の組み合わせが特徴。

## 📝 日本語コミュニティ

- [Claude Code標準SkillをiOSアプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — Claude Code に同梱された標準スキル（`/batch`・`/code-review` 等）を iOS 開発で使う実践ガイド。公式ドキュメントに基づいて整理されているため信頼性が高く、どのスキルをいつ使うかの判断基準が明確にまとめられている。

## 🌐 海外コミュニティ / Tips

- [Two MCP Servers Made Claude Code an iOS Build System（blakecrosley.com）](https://blakecrosley.com/blog/xcode-mcp-claude-code) — XcodeBuildMCP（82 ツール：ビルド・テスト・シミュレータ・LLDB デバッグ）と Apple 公式 Xcode MCP（20 ツール：ファイル操作・診断・SwiftUI プレビュー）の 2 本を組み合わせることで、Claude Code が構造化 JSON でビルド結果を受け取れるようになる手順を解説。ビルドログの文字列解析ではなく「正確なエラー位置・テスト結果・シミュレータ制御」を得ることで、コード修正→ビルド→エラー確認→再修正のループを Claude Code が自律的に回せる。セットアップは 2 分以内。

## 💡 今日のおすすめ実践 Tip

**「rshankras/claude-code-apple-skills の Apple Intelligence スキルで、iOS 27 の Foundation Models 機能実装を Claude Code に任せる」**

rshankras/claude-code-apple-skills には iOS 27 の Apple Intelligence 関連スキル（Foundation Models API・Visual Intelligence・App Intents）が専用カテゴリとして収録されています。v2.1.179 の sandbox glob 修正で安定性が増した今、次の構成が試しやすくなりました。

```bash
# 1. Apple Skills を ~/.claude/skills/ に導入
git clone https://github.com/rshankras/claude-code-apple-skills.git
cp -r claude-code-apple-skills/skills/ ~/.claude/skills/

# 2. XcodeBuildMCP と Apple Xcode MCP を .mcp.json に追記
# （keskinonur/claude-code-ios-dev-guide の設定例を参考に）
```

Claude Code への指示例:

```
apple-intelligence スキルを使って、ユーザーがテキストを入力すると
Foundation Models の on-device LLM がリアルタイムで候補を提案する
SwiftUI コンポーネントを実装して。

実装が終わったら XcodeBuildMCP でビルドし、エラーがあれば
構造化 JSON の結果を読んで自律的に修正して。
シミュレータで動作確認まで完結させて。
```

`apple-intelligence` スキルが Foundation Models フレームワークの API を正確に伝えることで、ハルシネーションを抑えながら iOS 27 の on-device AI 機能を実装できます。`/code-review` 標準スキルと組み合わせて最後に品質チェックも任せると、実装→ビルド→検証→レビューの一連が Claude Code 内で完結します。
