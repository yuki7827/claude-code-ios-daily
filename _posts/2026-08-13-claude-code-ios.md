---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-13"
date: 2026-08-13 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.228（2026-08-11）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— 前日（08-12）記事の v2.1.227 に続くリリース。iOS / Remote Control 運用に関係する変更を含む: ① **インタラクティブセッションの描画エラーを修正** — iPhone の Code タブや `claude.ai/code` で会話中にレイアウトが崩れるケースが解消; ② **claude.ai から同期されたスキルのサンドボックス化を強化** — iOS アプリ経由でチームメンバーと共有したスキルが、意図しないホスト側コマンドを実行しないよう隔離が強化された（`claude.ai/code` からスキルを同期する運用をしているチームに特に有効）; ③ **Remote Control セッション間のメッセージング改善** — iPhone ↔ Mac 間でクロスセッション `SendMessage` を使った際の信頼性が向上; ④ **ターミナルタイトルのビジーインジケータ改善** — XcodeBuildMCP でビルド中に Mac 側ターミナルタイトルが正しく更新されるようになり、iOS から接続してセッション状態を確認しやすくなった。

## 🛠 GitHub の動き

- [Issue #85132 — iOS Simulator MCP の `text` アクションが US キーボードレイアウトを前提とするため、Nordic 等の非 US 環境で `@` が `"` に化ける（OPEN / 2026-08-08）](https://github.com/anthropics/claude-code/issues/85132) — iOS Simulator MCP の `text` アクションでメールアドレスを入力しようとすると、`rider1@example.com` が `rider1"example.com` として入力される問題。原因はキーストローク合成が **US キーボードレイアウトを前提として `@` を「Shift+2」に変換**するため、Nordic/Swedish レイアウト（`@` は `Option+2`）ではまったく異なる文字が出力されること。`\ | { } [ ] $` など他の記号も同様に誤変換される。**日本語 JIS キーボードでも `@` のキー位置が US と異なるため同様の問題が発生する可能性がある。回避策: `text` アクションは使わず、シミュレータ上でフィールドを長押し → 「ペースト」で `pbcopy` / クリップボード経由で文字列を渡す。またはホスト側を US レイアウトに一時切り替えて入力する。**

- [Issue #80472 — macOS 27 beta で iOS Simulator ヘルパーがサンドボックスプロファイルの制限により Metal シェーダーキャッシュディレクトリを作れずクラッシュ（OPEN / 2026-07-23）](https://github.com/anthropics/claude-code/issues/80472) — macOS 27 beta から Metal のシェーダーキャッシュが「バンドル ID ごとのサブディレクトリ（`$(getconf DARWIN_USER_CACHE_DIR)/<bundleID>/com.apple.metal/`）」に分離されたが、`claude-ios-sim.sb` サンドボックスプロファイルがキャッシュディレクトリへの書き込みを許可していないため `MTLGetShaderCachePath()` が `nil` を返し、`+[NSArray arrayWithObjects:count:]` に `nil` が渡って `NSInvalidArgumentException` で SIGABRT クラッシュが発生する問題。提案修正は `claude-ios-sim.sb` に `DARWIN_USER_CACHE_DIR/com.anthropic.claude.ios-sim` への `file-write*` を追加すること。**検証済みの回避策（ディレクトリを外部から事前作成しサンドボックス内の書き込みを不要にする方法）が Issue 内に記載されており、macOS 27 beta ユーザーは適用することで iOS Simulator パネルを一時的に使用可能にできる。**

## 📝 日本語コミュニティ

- [SwiftUI × Claude Code で小規模事業者向け iOS アプリを MVP から App Store 申請まで全自動で作った話（Qiita / sorabcjanne1）](https://qiita.com/sorabcjanne1/items/f0f6bc44c91d38d5e9c2) — `xcodegen` で `project.yml` からプロジェクトを生成し、`xcodebuild` ビルド・`fastlane` の REST API 自動化と組み合わせて、Claude Code で MVP から App Store 申請まで一気通貫で自動化した体験記。直近 5 投稿での掲載なし。

- [Copilot Vision と Claude Code で iOS アプリを新規作成して比較（Zenn / yamazaking）](https://zenn.dev/yamazaking/articles/copilot-vision-claude-code-ios) — Copilot Vision と Claude Code それぞれで同じ iOS アプリを新規作成し、コード品質・操作感・自動化の深さを比較した記事。Xcode プロジェクト生成から SwiftUI 実装まで一通り比較されており、Claude Code の強みと弱みが具体的に整理されている。直近 5 投稿での掲載なし。

## 🌐 海外コミュニティ / Tips

- [I Tried Claude Agent SDK in Xcode 26.3 for a Week. My iOS Dev Workflow Will Never Be the Same.（Medium / Iniyarajan S. / 2026-08）](https://medium.com/@iniyarajan/i-tried-claude-agent-sdk-in-xcode-26-3-for-a-week-my-ios-dev-workflow-will-never-be-the-same-cc899ed7a776) — Xcode 26.3 に統合された Claude Agent SDK（`xcrun mcpbridge` 経由）を 1 週間実際に使い込んだ体験記。**サブエージェント・バックグラウンドタスク・プラグインが Xcode を離れずに使えるようになり**、設定画面・空状態・オンボーディングフローなど従来 30〜40 分かかっていた画面が 3〜4 分で完成するようになったと報告。SwiftUI Preview のスクリーンショットを取りながら Claude がリアルタイムで差分を評価するループが、品質担保と速度の両立に最も効いたと述べている。直近 5 投稿での掲載なし。

## 💡 今日のおすすめ実践 Tip

**「Issue #85132 対策: iOS Simulator MCP の `text` アクションで @ 記号などが化ける問題 — JIS/Nordic ユーザー向け回避フロー」**

Issue #85132 が示す通り、iOS Simulator MCP の `text` アクションはキーストローク合成を **US キーボードレイアウト固定** で行うため、非 US レイアウト環境ではメールアドレスのような記号を含む文字列が正しく入力されません。JIS キーボードを使う日本語環境でも `@`（JIS では `[`（英数）キー付近）の位置が US と異なるため、同様の誤入力が発生する可能性があります。

**影響を受ける記号の例（US vs. JIS）**

| 入力したい文字 | US レイアウト | JIS レイアウト |
|---|---|---|
| `@` | `Shift + 2` | `[ ` (英数キー直打ち) |
| `[` | `[` | `Option + [` など |
| `\` | `\` | `¥` キー |

**回避策 1: クリップボード経由で貼り付け（最も確実）**

`text` アクションの代わりに、`pbcopy` でクリップボードにコピーし、シミュレータ上でテキストフィールドを長押し → 「ペースト」で入力します。Claude Code に以下のように指示することで自動化できます:

```bash
# ホスト側でクリップボードに文字列をセット
echo -n "user@example.com" | pbcopy
```

その後、Claude Code の iOS Simulator MCP で:
```
control { action: "tap", ... }  # フィールドをタップしてフォーカス
# 長押し → ペーストは現在 tap の長押し duration で対応
control { action: "tap", x: ..., y: ..., duration: 1.5 }
```

**回避策 2: CLAUDE.md に明示してクリップボード方式を標準化**

テスト・UI 自動化で記号を含む文字列入力が頻繁に出る場合は、CLAUDE.md に以下を追記しておくと、Claude が自動でクリップボード方式を選択するようになります:

```markdown
## iOS Simulator テキスト入力ポリシー

- `@` `[` `\` `{` `}` などの記号を含む文字列は
  `text` アクションを使わず、以下の手順で入力する:
  1. `echo -n "<string>" | pbcopy` でホスト側クリップボードにコピー
  2. フィールドを長押し（duration: 1.5）して「ペースト」
- Issue #85132 が修正されるまでの暫定対応
```

Issue #85132 は 2026-08-08 に起票され現在 OPEN のままです。修正リリースが出たら `text` アクションに戻してください。
