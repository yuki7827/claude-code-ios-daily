---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-15"
date: 2026-08-15 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.232（2026-08-13）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— 同日の v2.1.231（MCP OAuth 修正）に続いて公開された Remote Control 重点リリース。iOS × Remote Control 運用に直接影響する変更をまとめる: ① **Remote Control セッションが同一名を複数マシン間で維持できるよう改善** — セッション名が競合する場合は `name-word-word` 形式のバリアントを自動生成し、iPhone の `ListAgents` から複数 Mac を識別しやすくなった; ② **Claude Desktop / IDE から起動したセッションが `resume` 時に新規 claude.ai セッションとして誤登録される問題を修正** — 以前は IDE から `claude remote-control` を起動して一度切断すると、再接続時にセッション履歴が別 ID で孤立していたバグが解消; ③ **アイドル中のセッションが新規接続クライアントから「unreachable」に見える問題を修正** — iPhone から Remote Control に接続したとき、Mac 側が待機中であっても「未接続」と誤表示されるケースが解消; ④ **ブリッジセッションがワーカー再起動後に会話履歴を復元しない問題を修正** — XcodeBuildMCP ビルド中にネットワーク切断が起きてワーカーが再起動しても、会話コンテキストが失われなくなった; ⑤ **削除済みセッションに対する `resume` が代替セッションを起動するように変更** — `claude.ai/app` 側で古いセッションを削除してしまった場合も `--continue` が次のセッションにフォールスルーする; ⑥ **/config に「Dialog expiry」と「Messages from your other sessions（accept/hold/refuse）」の設定行を追加** — クロスセッション `SendMessage` の受信ポリシーを iPhone 側のセッションから直接設定できるようになった。

## 🛠 GitHub の動き

- [Issue #86757 — macOS: iOS Simulator sidecar が毎回呼び出しで abort する — サンドボックスがバンドル ID ごとの Metal シェーダーキャッシュディレクトリ作成をブロック（OPEN / 2026-08-14）](https://github.com/anthropics/claude-code/issues/86757) — 本日起票された最新 Issue。これまで報告されてきた Metal シェーダーキャッシュ系クラッシュ（#80472・#83011・#84531）と同一の根本原因（`claude-ios-sim.sb` サンドボックスプロファイルがバンドル ID ごとのキャッシュサブディレクトリへの書き込みを拒否）だが、**今回は「sidecar」コンポーネントが独立したサンドボックスセッションで呼ばれるたびに abort する**という新たな症状が確認されている。具体的には、iOS Simulator パネルが「Attach」に一度成功した後も、続くツール呼び出し（`screenshot`・`tap` 等）のたびに sidecar プロセスが再起動してクラッシュを繰り返すため、パネルが「接続済み」表示のまま何も操作できない状態になる。**現時点での回避策は引き続き XcodeBuildMCP の `build`・`launch_simulator`・`screenshot` ツールを使うこと（こちらはサンドボックスプロファイルに依存しないため影響なし）。**

## 📝 日本語コミュニティ

- [【第2弾】【初心者向け】VS Code の Claude Code で「iOS コードレビュー → 修正 → Xcode ビルド確認 → テスト項目作成」を自動化する（Qiita / 4q_sano）](https://qiita.com/4q_sano/items/effe51de5e9654777f1e) — 第1弾（レビュー → 修正 → ビルド）の続編として、**XcodeBuildMCP でビルドを通した後に Claude Code がテストケースを自動生成し、`xcodebuild test` で実行まで完結させる**フローを追加した記事。`CLAUDE.md` に「ビルド成功後は必ずユニットテスト項目を提案せよ」と記述することで、Claude Code がテスト作成フェーズを自律的に挟むようになる点が実践的。直近 5 投稿での掲載なし。

- [XcodeBuildMCP × Claude Code スキルシステムで、iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — XcodeBuildMCP の `xcodebuildmcp init` CLI（v2.1.0 で追加）を使って Agent Skills をインストールし、Claude Code のスキルシステムと組み合わせてビルド・テスト・シミュレータ起動を宣言的に自動化する手順を解説。スキル経由で呼ぶことで「ビルドしてエラーがあれば自動修正し、再ビルドするループ」を CLAUDE.md に数行書くだけで実現できる点が要点。直近 5 投稿での掲載なし（同筆者の plugin 記事は 08-11 既出だが本記事は別 URL・別テーマ）。

## 🌐 海外コミュニティ / Tips

- [XcodeBuildMCP v2.7.0 リリース — Xcode 27 Device Hub シミュレータ対応・Schema v3・テスト成果物の再利用（GitHub / getsentry/XcodeBuildMCP）](https://github.com/getsentry/XcodeBuildMCP/releases/tag/v2.7.0) — Sentry 管理の XcodeBuildMCP が v2.7.0 に更新。iOS × Claude Code 開発への影響が大きい主な変更: ① **Xcode 27 Device Hub シミュレータ UI 自動化に対応** — Xcode 27 が `Simulator.app` を廃止して Device Hub に移行した問題（Issue #79991）に対し、XcodeBuildMCP 側で Device Hub 経由でのウィンドウ起動・キーボード操作に対応。Xcode 27 beta ユーザーはクラッシュせず Claude Code からシミュレータ操作が可能に; ② **`.xctestproducts` パッケージの再利用サポート** — ビルド済みテスト成果物をシミュレータ・実機・macOS テストで使い回せるようになり、CI でのフルビルド時間を削減できる; ③ **`extraArgs` セッションデフォルト** — `xcodebuild` 共通フラグをプロジェクト設定に一度書いておくことでエージェントが毎回指定しなくて済む; ④ **Breaking change**: ビルド・テストツールの `schemaVersion` が `"3"` に変更。v2 を前提としたバリデータやスクリプトは更新が必要。

## 💡 今日のおすすめ実践 Tip

**「XcodeBuildMCP v2.7.0 の Xcode 27 Device Hub 対応 — 移行時の注意点と動作確認手順」**

Xcode 27 beta に移行した開発者が直面する最大のボトルネックは「Claude Code から iOS Simulator が操作できない」問題でした。Claude Desktop 組み込みの iOS Simulator パネルは Issue #79991（Device Hub 移行で `SimulatorKit.framework` パスが変わりクラッシュ）が未修正のため現状では使えませんが、**XcodeBuildMCP v2.7.0 がこの問題を先んじてサポートしたため、XcodeBuildMCP 経由ならば Xcode 27 でもシミュレータ操作が可能になりました。**

**移行手順（Xcode 26.x → Xcode 27 beta）**

```bash
# 1. XcodeBuildMCP を v2.7.0 以上に更新
claude mcp add XcodeBuildMCP -s user \
  -e XCODEBUILDMCP_SENTRY_DISABLED=true \
  -- npx -y xcodebuildmcp@latest mcp

# あるいは Homebrew でインストール済みの場合
brew upgrade xcodebuildmcp

# 2. xcode-select を Xcode 27 に向ける
sudo xcode-select -s /Applications/Xcode-beta.app/Contents/Developer

# 3. Agent Skills を再インストール（v2.7.0 の新形式に更新）
npx xcodebuildmcp@latest init
```

**スキーマ v3 対応確認**

v2.7.0 では `schemaVersion` が `"3"` になりました。以前 CLAUDE.md にビルド出力のパースロジックを書いている場合は確認が必要です:

```markdown
## ビルド結果確認ポリシー（CLAUDE.md への追記例）

- XcodeBuildMCP のビルド出力は schemaVersion: "3" 形式
- エラー情報は `result.errors[]` を参照（v2 の `buildErrors[]` から変更）
- テスト結果は `result.testSummary` を参照
```

**`.xctestproducts` 再利用でCI時間短縮**

v2.7.0 の目玉機能として、ビルド済みテスト成果物（`.xctestproducts`）を一度作成すれば複数ターゲット（シミュレータ・実機・macOS）で再利用できるようになりました。Claude Code に以下のように指示することで活用できます:

```
# Claude Code への指示例
「まず .xctestproducts を生成してください。その後、同じ成果物を
 iPhone 16 シミュレータと iPad Air シミュレータそれぞれでテスト実行してください」
```

再ビルドが不要なため、複数シミュレータでの並行テストが大幅に高速化されます。Xcode 26.x ユーザーも後方互換性が維持されているため v2.7.0 への更新は推奨されます。

**Claude Desktop 組み込みパネルとの使い分け（現状）**

| 環境 | Claude Desktop iOS Simulator パネル | XcodeBuildMCP v2.7.0 |
|---|---|---|
| Xcode 26.x / macOS 26.x | 動作（ただし Metal キャッシュ問題あり） | 動作 ✅ |
| Xcode 27 beta | 動作しない（Issue #79991 未修正） | 動作 ✅ |
| Intel Mac | 動作しない（Issue #80612 未修正） | 動作 ✅ |

Xcode 27 beta または Intel Mac を使用中の場合は、XcodeBuildMCP v2.7.0 一択です。
