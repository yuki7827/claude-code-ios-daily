---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-07-25"
date: 2026-07-25 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [Claude Code v2.1.219（2026-07-24）](https://code.claude.com/docs/en/changelog) — iOS 開発ワークフローに効く変更が複数含まれるリリース。**① `DirectoryAdded` フック追加**：`/add-dir` または SDK の `register_repo_root` が実行された直後に発火する新フック。Xcode プロジェクトフォルダを Claude Code に追加した瞬間に CLAUDE.md 書き換えや XcodeBuildMCP の設定適用を自動トリガーできる。**② `sandbox.network.strictAllowlist` 追加**：ホワイトリストに登録されていないホストへの外向き通信を確認なしで拒否する厳格モード。CI 環境や MDM 管理端末で `developer.apple.com` / `appstoreconnect.apple.com` などの Apple ドメインのみを許可しつつ未知の宛先を自動ブロックする運用が可能になる。**③ セッショントランスクリプトサイズ最大 79 倍削減**：XcodeBuildMCP のビルドログが大量に積み重なる長時間セッションのメモリ使用量が大幅に改善。**④ Claude Opus 5（`claude-opus-5`）をデフォルト Opus モデルとして追加**：1M トークンのコンテキストウィンドウを持ち `/fast` にも対応。大規模 Xcode プロジェクト全体を一度に読み込んでアーキテクチャ解析・全ファイルリファクタリング・ビルドエラー一括修正などに活用できる。

## 🛠 GitHub の動き

- [Issue #80827 — Remote Control (macOS/launchd): ユーザーアイドル時にヘッドレスワーカーが tool_use の瞬間にプロセス全体でフリーズし、最初の HID 入力で一斉復帰（OPEN / 2026-07-24）](https://github.com/anthropics/claude-code/issues/80827) — `claude remote-control` を launchd LaunchAgent として常時起動し iOS アプリから接続する構成で、Mac のユーザーアイドル中にあらゆるツール呼び出し（`ls` 1 行でも）が 60〜70 分以上止まるバグ。テキスト送受信は問題ないが tool_use に達した瞬間ワーカープロセスが macOS の省電力 throttle に落ちる。**回避策**：launchd plist に `<key>ProcessType</key><string>Interactive</string>` を追加する（bootout+bootstrap の再登録が必要）。または watchdog プロセスで `GET /v1/code/sessions` の `last_event_at` を監視し、失速したワーカーを `TERM → CONT → KILL` で再起動する方法も有効。iPhone から離れた場所でビルドをモニタリングしているユーザーに直撃するバグで、ログの「Response stalled mid-stream」エラーがこの症状のサインになる。

- [Issue #80841 — Dispatch（モバイル）: セッション起動時に作業ディレクトリとパーミッションモードを選べるようにしたい（OPEN / 2026-07-24）](https://github.com/anthropics/claude-code/issues/80841) — iPhone から Dispatch でセッションを新規起動する際、作業ディレクトリの選択肢がなく（プリセット一覧や入力欄がない）、パーミッションモードも常に Manual になるという Feature Request。Manual モードで起動すると承認プロンプトを 1 つずつタップする必要があり、Auto モードを使いたければ「Mac を離れる前にあらかじめ Auto モードのセッションを開いておく」しか手がない。iOS アプリから XcodeBuildMCP のビルドを Dispatch で起動してアウトドアでモニタリングするワークフローを壊す制約として報告されている。

- [Issue #80806 — computer-use の `request_access` 承認が Remote Control クライアント（iOS アプリ含む）に届かない（OPEN / 2026-07-24）](https://github.com/anthropics/claude-code/issues/80806) — computer-use MCP の `request_access` 承認ダイアログがセッションのローカル TUI にしか現れず、macOS Claude アプリ・iOS アプリ・claude.ai/code のいずれの Remote Control クライアントにも転送されないバグ。通常の MCP ツール承認は Remote Control 経由で正常に届くが、`request_access` だけが例外。iOS アプリから Mac の computer use を管理したい場合、承認のためだけにターミナルアクセスを別途維持しなければならない状態。

## 📝 日本語コミュニティ

- [Apple のデザイン原則を AI コーディングへ。apple-design Skill を Codex / Claude Code に導入する（Qiita / TakanobuSano）](https://qiita.com/TakanobuSano/items/52c6bee94c1e5360461e) — Apple の Human Interface Guidelines（HIG）を Claude Code のスキルとして組み込み、SwiftUI の UI コード生成に反映させる方法を解説した記事。San Francisco フォントの Display/Text しきい値・システムカラーのライト/ダーク対応・Liquid Glass エフェクトの正しい適用方法など、Apple らしい UI を Claude Code に一貫して出力させるための設定が中心。`/apple-design` スキルを CLAUDE.md に登録するだけで、SwiftUI のコンポーネント生成時に HIG 準拠のコードが自動的に出力されるようになる点が実践的。

- [Claude / Anthropic 関連ニュースまとめ（2026-07-24）（Qiita / homhom44）](https://qiita.com/homhom44/items/cf3b8986a965465f169e) — 2026-07-24 分の Claude / Anthropic 関連ニュースをコンパクトにまとめた記事。v2.1.219 の変更点と iOS 関連 Issue（#80827・#80841・#80806）が概要レベルでカバーされており、週次でまとめを追いたい場合に便利。

## 🌐 海外コミュニティ / Tips

- [Feature Request #80861 — Desktop: iOS シミュレーターパネルと同様のインウィンドウプレビューを macOS ネイティブアプリにも（OPEN / 2026-07-24）](https://github.com/anthropics/claude-code/issues/80861) — iOS シミュレーターパネルの設計（computer use 不要・アクセシビリティ/画面録画許可なしで直接制御）をネイティブ macOS アプリ開発にも適用してほしいという Feature Request。「iOS シミュレーターパネルが問題を 3 つ同時に解決した（アプリごとの毎回再承認・フォーカス横取りによる承認失敗・セッション自動アーカイブ後の権限消滅）。macOS インウィンドウペインも同じ設計で同じ 3 つを解決できる」という論旨が明快。iOS + macOS 対応アプリを Claude Code で並行開発しているチームには直接的な関心事となる。

- [rshankras/claude-code-apple-skills（GitHub）](https://github.com/rshankras/claude-code-apple-skills) — iOS・macOS・iPadOS 向け Claude Code スキルのコレクション。プロダクト検証・コード生成・App Store 最適化・アクセシビリティチェックなど、Apple プラットフォーム開発の各フェーズをカバーするスキルが揃う。XcodeBuildMCP との組み合わせを前提に設計されており、`/validate-product`・`/generate-swift`・`/review-appstore` 等のスラッシュコマンドとして Claude Code に直接組み込める。iOS 開発チームで Claude Code を標準化する際の出発点として参考になる。

## 💡 今日のおすすめ実践 Tip

**「launchd Remote Control のフリーズを根本解決する `ProcessType=Interactive` 設定と、Dispatch Auto モードの暫定回避策」**

**Remote Control + launchd のフリーズ（Issue #80827）を `ProcessType=Interactive` で修正する**

常時起動の Mac に `claude remote-control` を launchd LaunchAgent として登録し、iPhone から接続している環境でビルドが「Running…」のまま止まる場合は、macOS の省電力 throttle が原因である可能性が高いです。以下の手順で修正できます。

1. 既存の LaunchAgent plist（例: `~/Library/LaunchAgents/com.yourname.claude-rc.plist`）に `ProcessType` キーを追加します：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.yourname.claude-rc</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/local/bin/claude</string>
    <string>remote-control</string>
    <string>--permission-mode</string>
    <string>bypassPermissions</string>
    <string>--name</string>
    <string>MyMac</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <true/>
  <!-- ここを追加: ユーザーアイドル時の throttle から除外 -->
  <key>ProcessType</key>
  <string>Interactive</string>
</dict>
</plist>
```

2. 変更を反映するために bootout → bootstrap を実行します（`kickstart -k` では `ProcessType` が反映されないため注意）：

```bash
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/com.yourname.claude-rc.plist
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.yourname.claude-rc.plist
```

**Dispatch Auto モードの暫定回避策**

Issue #80841 が修正されるまで、iPhone から Dispatch でセッションを開始して Auto モードを使いたい場合は、出発前に Mac 側で以下を実行してセッションを待機状態にしておくのが最もシンプルな回避策です：

```bash
# Auto モード + bypassPermissions でセッションを事前に起動
# （プロジェクトディレクトリに cd してから実行）
cd /path/to/MyXcodeProject
claude --permission-mode bypassPermissions
```

または `.claude/settings.json` で `defaultPermissionMode` を設定しておけば、Dispatch 起動時も設定が引き継がれる環境もあります（現時点では再現性が一定でないため、Issue #39889 の続報を待ちながら試す形になります）：

```json
{
  "permissions": {
    "defaultMode": "bypassPermissions"
  }
}
```
