---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-06-11"
date: 2026-06-11 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.169（2026-06-08）](https://code.claude.com/docs/en/changelog) — **セルフホストランナーに `post-session` ライフサイクルフックを追加**。セッション終了後・ワークスペース削除前に任意のスクリプトを実行できるようになり、Mac セルフホストランナー上で `xcodebuild` のログやシミュレータのスクリーンショットをアーティファクトストレージへ退避する用途に使える。あわせて、プロンプトキャッシュを壊さずにセッションの作業ディレクトリを切り替えられる **`/cd` コマンド**、バンドル済みスキルを無効化する **`disableBundledSkills`** 設定（環境変数 `CLAUDE_CODE_DISABLE_BUNDLED_SKILLS` も追加）、トラブルシュート用にカスタマイズを全無効化して起動する **`--safe-mode`** フラグも追加。iOS アプリ + バックエンドのモノレポで作業ディレクトリを行き来するケースや、自作の Xcode 系スキルとバンドル済みスキルが衝突するケースで役立つ。

- [Claude Code v2.1.170（2026-06-09）](https://code.claude.com/docs/en/changelog) — Claude Fable 5（`claude-fable-5`）が利用可能に（詳細は昨日分参照）。VS Code 統合ターミナル経由で起動したセッションのトランスクリプトが保存されないバグも修正。

## 🛠 GitHub の動き

- [conorluddy/ios-simulator-skill（★1.1k）](https://github.com/conorluddy/ios-simulator-skill) — iOS アプリのビルド・テスト・自動化に特化した Claude Code 用スキル。27 本のスクリプトで `xcodebuild` / `xcrun simctl` / `idb` をラップし、スクリーンショット圧縮や段階的な情報開示でトークン消費を抑えつつ、アクセシビリティ情報ベースで UI 操作までこなす。最新の v1.4.0（2026-04-12）で Plugin Marketplace 対応・Model Inspector を追加。`xclaude-plugin`（06-06 で紹介）と同じ作者によるもので、より軽量・単機能な「ビルド＆シミュレータ操作」用途に向く。

- [kylehughes/apple-platform-build-tools-claude-code-plugin（★57）](https://github.com/kylehughes/apple-platform-build-tools-claude-code-plugin) — iOS / macOS / tvOS / watchOS / visionOS 向けのビルド・テスト・アーカイブを支援する Claude Code プラグイン。`xcodebuild` や Swift Package Manager、コード署名・プロビジョニングのリファレンスをまとめた Agent Skill「building-apple-platform-products」と、プロジェクト構造の検出からビルド・テスト・アーカイブ・デバイス管理までを自動化する Subagent「builder」の 2 構成。自然言語の指示に応じて適切な `xcodebuild` コマンドを組み立ててくれる。

## 📝 日本語コミュニティ

- [XcodeBuildMCP×Claude Codeスキルシステムで、iOSビルドを自動化する](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build)（Zenn / kyoichi）— 「いつテストを実行するか」のような高レベルな判断はオーケストレーター側のスキルに、`xcodebuild` の細かい呼び出し手順やエラー解析はサブエージェントのプロンプトに分離する設計を提案。ビルドログでメインの会話コンテキストが汚染されるのを防ぎつつ XcodeBuildMCP を活用するノウハウがまとまっている。

- [【第2弾】【初心者向け】VS CodeのClaude Codeで「iOSコードレビュー → 修正 → Xcodeビルド確認 → テスト項目作成」を自動化する](https://qiita.com/4q_sano/items/effe51de5e9654777f1e)（Qiita / 4q_sano）— 第1弾のビルド確認自動化に続き、コードレビュー→修正→ビルド確認に加えてテスト項目の自動生成までを VS Code 上の Claude Code でつなげる手順を解説。初心者向けに各ステップのプロンプト例も具体的に示されている。

## 🌐 海外コミュニティ / Tips

- [Building iOS Apps with AI Agents: The Practitioner's Guide](https://blakecrosley.com/guides/ios-agent-development)（blakecrosley.com）— Claude Code CLI（MCP 経由）・Codex CLI・Xcode 26.3 のネイティブエージェントという「3 つのエージェントランタイム」が iOS 開発に使える現状を整理。SwiftUI ビュー・SwiftData モデル・リファクタリング・ビルドエラー診断は得意な一方、`.pbxproj` の直接編集・コード署名・見た目のビジュアルデバッグは依然苦手と分析しており、エージェントに任せる範囲の線引きの参考になる。

- [Boris Cherny（Claude Code チーム）の Fable 5 所感](https://x.com/bcherny/status/2064402671898075579) — 「これまで使った中で最もコーディングに優れたモデル」とし、プロンプト数の削減・トークン効率向上・ツール利用精度向上・自己検証能力の強化・長時間セッションでの安定性を挙げている。昨日紹介した Dynamic Workflows との組み合わせに加え、通常のシングルエージェントセッションでも iOS の大規模リファクタで体感差が出やすいとの声。

## 💡 今日のおすすめ実践 Tip

**Mac セルフホストランナー + `post-session` フックで iOS ビルド/UI検証ログを自動アーカイブする**

v2.1.169 で追加されたセルフホストランナーの `post-session` ライフサイクルフックは、`conorluddy/ios-simulator-skill` のような「ビルド→シミュレータ起動→UI操作→スクリーンショット取得」を行うスキルと組み合わせると効果的です。セッション終了後・ワークスペース削除前に任意のスクリプトを走らせられるため、Claude Code が生成したシミュレータのスクリーンショットや `xcodebuild` のログ、テスト結果を CI のアーティファクトストレージへコピーしてから片付けることができます。

```bash
# .claude/hooks/post-session.sh（セルフホストランナー設定）
#!/bin/bash
ARTIFACT_DIR="${CI_ARTIFACT_DIR:-./artifacts}/$(date +%Y%m%d-%H%M%S)"
mkdir -p "$ARTIFACT_DIR"

# シミュレータのスクリーンショットとビルドログを退避
cp -r ./.claude-workspace/screenshots "$ARTIFACT_DIR/" 2>/dev/null
cp -r ./.claude-workspace/build-logs "$ARTIFACT_DIR/" 2>/dev/null
```

自作の iOS 系スキル（`ios-simulator-skill` や社内製スキル）とバンドル済みスキルの動作が衝突する場合は、`disableBundledSkills` を併用してバンドル側を無効化すると挙動が安定します。また、iOS アプリと API サーバーを同一モノレポで管理しているチームは、`/cd` コマンドでプロンプトキャッシュを維持したまま作業ディレクトリを切り替えられるため、1 セッション内で「サーバー側修正→`/cd`でiOSアプリへ移動→ビルド確認」という往復がしやすくなります。
