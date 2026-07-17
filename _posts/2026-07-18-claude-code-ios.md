---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-18"
date: 2026-07-18 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.212（2026-07-17）](https://code.claude.com/docs/en/changelog) — 主要変更点は 4 つ。**① `/fork` コマンドの刷新**：会話をバックグラウンドセッション（`claude agents` の新規行）にコピーしながら元セッションを継続できるようになった。旧来の「インセッションサブエージェント起動」機能は `/subtask` に切り出された。複数の iOS ビルドタスクを並走させる際にセッションを汚さずに分岐できる。**② `/resume` のピッカー化**：エージェントビューで `/resume` を打つと、過去セッション（リストから削除したものを含む）のピッカーが表示され、バックグラウンドセッションとして再開可能になった。**③ MCP ツール自動バックグラウンド化**：MCP ツール呼び出しが 2 分超になると自動的にバックグラウンドへ退避し、フォアグラウンドのセッションが引き続き操作可能になる（`CLAUDE_CODE_MCP_AUTO_BACKGROUND_MS` で閾値変更・無効化可）。**XcodeBuildMCP での Xcode ビルドや `xcodebuild test` は容易に 2〜10 分かかるため、この変更は直接的な体験改善になる**。ビルド中でも iPhone からメッセージを送って別の指示を出せる。**④ セーフガード強化**：WebSearch のセッション上限（デフォルト 200 回、`CLAUDE_CODE_MAX_WEB_SEARCHES_PER_SESSION`）とサブエージェント生成上限（デフォルト 200、`CLAUDE_CODE_MAX_SUBAGENTS_PER_SESSION`、`/clear` でリセット）が追加。プランモードでファイル変更 Bash コマンドが許可プロンプトなしで自動実行されていたバグと、シンリンクが外部ディレクトリを参照する worktree を作成できていたセキュリティ問題も修正。

## 🛠 GitHub の動き

- 該当なし（2026-07-17 以降に更新された iOS / Swift / Xcode 関連の新規 issue は確認できず）

## 📝 日本語コミュニティ

- [XcodeBuildMCP×Claude Codeスキルシステムで、iOSビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — XcodeBuildMCP と Claude Code のスキルシステムを組み合わせ、ビルド・テスト・シミュレータ起動を自動化するワークフローを解説した記事。スキルとして手順をカプセル化することで毎回のプロンプト量を削減できる点が実践的な差別化ポイント。

- [Claude Code標準SkillをiOSアプリ開発でどう使うか：公式ドキュメントを根拠に整理する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/0ecf0fc7353d751f601c) — Claude Code CLI に同梱される標準 Skill（`/review`・`/test`・`/run` など）を iOS アプリ開発のどのフェーズで使うか、公式ドキュメントを根拠に体系化した記事。独自スキル（CLAUDE.md 記述）との使い分けも整理されており、環境構築初期に一読しておくと効率的な役割分担ができる。

- [Claude Code × iOSアプリ開発 一気通貫ガイド（Qiita / tobaru-hideyasu）](https://qiita.com/tobaru-hideyasu/items/f9ed60b136d95960b1d0) — 要件定義から App Store 申請まで Claude Code を使い通す一連のフローをまとめたガイド。XcodeBuildMCP・Xcode MCP・Fastlane との組み合わせによる CI 連携や、コンテキスト管理の工夫も含む実務向けの網羅的な記事。

## 🌐 海外コミュニティ / Tips

- [Building iOS Apps with AI Agents: The Practitioner's Guide（Blake Crosley）](https://blakecrosley.com/guides/ios-agent-development) — 本番 iOS アプリ 8 本（計 293 Swift ファイル）の実装経験から蒸留した実践ガイド。Claude Code が得意な領域（SwiftUI Views、SwiftData モデル、リファクタリング、ビルドエラー診断）と苦手な領域（`.pbxproj` の直接編集、コードサイニング、ビジュアルデバッグ）を明示した上で、各フェーズでのプロンプト戦略を解説。XcodeBuildMCP + Apple Xcode MCP の 2 MCP 構成で「編集 → ビルド → 検証」ループを安定して自律走行させるための設定例も含む。

- [XcodeBuildMCP: Let AI Handle iOS/macOS Development Like Web Development（AI Engineering / Medium）](https://ai-engineering-trend.medium.com/xcodebuildmcp-let-ai-handle-ios-macos-development-like-web-development-a6afd784b487) — XcodeBuildMCP（getsentry / Sentry 管理）が「Web 開発では当たり前の AI エージェント支援ループを iOS でも実現する」という視点で解説。v2.3.2 の 82 ツールのうち特に実用的な `BuildAndRun`・`RunTests`・`StartSimulator`・`GetBuildErrors` の 4 つのユースケースをコード付きで紹介。「生の `xcodebuild` ログを AI に渡すと解析ノイズが多い」という問題を構造化 JSON レスポンスで解決している点を重点的に説明している。

## 💡 今日のおすすめ実践 Tip

**「v2.1.212 の MCP 自動バックグラウンド化で、XcodeBuildMCP ビルド中でも iPhone から追加指示を送れるようになった」**

これまで `xcodebuild` が走っている間は Claude Code の TUI がビルドログで占有されてしまい、Remote Control から iPhone で指示を送っても「MCP 呼び出しが完了するまで処理されない」状態が続いていました。v2.1.212 からは MCP ツール呼び出しが 2 分を超えると自動的にバックグラウンドへ移行し、フォアグラウンドのループが解放されます。

**実務での活用イメージ**

1. Claude Code に `「XcodeBuildMCP で Release ビルドして TestFlight にアップロードして」` と指示
2. `BuildProject` が走り始める → 2 分後に自動バックグラウンド化
3. その間に iPhone の Remote Control（または NeriTerm / Tactic Remote）から「アップロード後は CHANGELOG.md も更新して」と追加指示が送れる
4. ビルドが終わり MCP 呼び出しが返ると、Claude がキューに積まれた指示を続けて処理

**閾値のカスタマイズ**（長いユニットテストを走らせる場合など）

```bash
# 5 分（300,000 ms）まで延ばす例
CLAUDE_CODE_MCP_AUTO_BACKGROUND_MS=300000 claude
```

`0` を設定すると自動バックグラウンド化を無効にし、従来の動作に戻せます。XcodeBuildMCP のビルド時間がプロジェクト規模によって大きく異なるため、まず `time xcodebuild build -scheme …` で実測値を確認してから閾値を設定するのがおすすめです。
