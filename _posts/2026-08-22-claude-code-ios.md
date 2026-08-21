---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-08-22"
date: 2026-08-22 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.238（2026-08-20）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— v2.1.237 と同日リリースの追加アップデート。iOS 開発観点での主な変更点: ① **`keybindingFlavor: "readline"` 設定を追加** — `/config` で `keybindingFlavor` を `"readline"` に設定すると Ctrl+W で前の空白文字まで削除（Bash 風 word-erase）が使えるようになった。CLAUDE.md や Swift コードのプロンプトを素早く編集したいターミナル派の開発者に有用; ② **Plugin `headersHelper` 機能** — MCP プラグインのカタログエントリに `headersHelper` を追加することで HTTP 認証ヘッダー（短期アクセストークンなど）をインストール・更新時に自動生成できるように。社内 API ゲートウェイに接続する iOS チーム向け; ③ **Self-hosted Runner に `--defer-shutdown-max-min` オプションを追加** — SIGTERM 受信後も指定分数まで実行中のセッションを継続できる。Xcode archive ビルドや fastlane 配信パイプラインを CI ランナー上で Claude Code に回す場合のタイムアウト問題を緩和; ④ **バグ修正: 長時間セッションのメモリリーク** — Xcode ビルドログやテスト結果を大量に扱う長時間セッションでのメモリ使用量が安定化; ⑤ **バグ修正: カスタム出力スタイルがセッション途中でリセットされる問題** — `Concise` スタイル等を設定してもセッション継続中にデフォルトへ戻る問題が修正された。

## 🛠 GitHub の動き

- [Issue #79991 — Desktop app の iOS Simulator が Xcode 27（Device Hub）で動作しない — SimulatorKit.framework のパスがハードコードされている（OPEN / 2026-07-22 起票）](https://github.com/anthropics/claude-code/issues/79991) — Xcode 27 が Simulator.app を廃止して Device Hub へ移行した際に、SimulatorKit.framework の格納先が `Contents/Developer/Library/PrivateFrameworks/` から `Contents/SharedFrameworks/` に変わったが、Claude Code の内部バンドルツールが旧パスをハードコードしているため Simulator パネルが完全に起動しない。エラーは `"file at path '...SimulatorKit.framework', but it does not exist"`。**回避策**: Simulator パネルを使わず XcodeBuildMCP v2.7.0（Device Hub 対応済み）で `build`・`launch`・`test` を行う。Xcode 26.x を維持する環境では現状影響なし。

- [Issue #80041 — iOS Simulator パネルが `/var/db/xcode_select_link` 非存在時に「Xcode not selected」と誤報 — 自動ディスカバリーのフォールバックが未チェック（OPEN / 2026-07-22 起票）](https://github.com/anthropics/claude-code/issues/80041) — `xcode-select -p` や `xcodebuild` は正常動作しているのに iOS Simulator パネルへのアタッチが `idb_not_installed` / "Xcode is installed but not selected" で失敗するケース。Claude Code が `xcode-select` の解決順（① DEVELOPER_DIR 環境変数 → ② シンボリックリンク → ③ OS 自動ディスカバリー）のうち ② しか見ておらず、`xcode-select -s` 未実行の環境（クリーンインストール直後など）で誤判定する。**即効ワークアラウンド**: `sudo ln -sfn "$(xcode-select -p)" /var/db/xcode_select_link` でシンボリックリンクを手動作成。

## 📝 日本語コミュニティ

- [Xcode MCP × Claude Code プラグインで iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-plugin-xcode-mcp-hybrid) — Apple 公式 Xcode MCP と Claude Code プラグイン機構を組み合わせてビルドループを自動化するハイブリッドアプローチの解説記事。プラグインで Xcode MCP のツール呼び出しをラップし、ビルドエラー→修正→プレビュー確認のサイクルをスキルとして再利用する構成を紹介。

- [XcodeBuildMCP × Claude Code スキルシステムで iOS ビルドを自動化する（Zenn / kyoichi）](https://zenn.dev/kyoichi/articles/claude-code-xcodebuildmcp-ios-build) — 上記と同じ著者による XcodeBuildMCP 版の自動化スキル設計。Claude Code のスキル機構（`.claude/skills/`）に XcodeBuildMCP 呼び出しを組み込み、`/build-ios` のようなスラッシュコマンドでビルド・テスト・エラー修正を 1 コマンドで起動できるように整備する手順を詳解。

## 🌐 海外コミュニティ / Tips

- [XcodeBuildMCP v2.7.0 リリース — Xcode 27 Device Hub で UI オートメーション完全対応（getsentry/XcodeBuildMCP）](https://github.com/getsentry/XcodeBuildMCP/releases) — v2.7.0 でシミュレーターウィンドウ起動・タップ・スワイプ・キーボード操作が Xcode 27 の Device Hub 経由で完全に動作するようになった。Xcode 27 へ移行して Claude Code Desktop の Simulator パネルが壊れた環境（Issue #79991）でも、XcodeBuildMCP を使えば UI オートメーションのフルセットが利用可能。また `xcodebuildmcp purge` コマンドが追加されビルドキャッシュの一括削除が CLIから行えるようになった。

## 💡 今日のおすすめ実践 Tip

**「Issue #80041 の即効ワークアラウンド — `xcode-select -s` 未実行の Mac で iOS Simulator パネルが使えない問題をコマンド 1 行で解消する」**

クリーンインストール直後の macOS や CI ランナー、複数 Xcode バージョンを手動切り替えして使っている環境では、`/var/db/xcode_select_link` シンボリックリンクが存在しないことがあります。このとき Claude Code Desktop の iOS Simulator パネルがアタッチに失敗し、`idb_not_installed` エラーが出て Simulator を操作できなくなります。

**確認コマンド**

```bash
# シンボリックリンクの存在確認
ls -la /var/db/xcode_select_link 2>/dev/null || echo "リンクなし（Issue #80041 の影響を受ける可能性あり）"

# xcode-select の実際のパス（こちらは正常なら表示される）
xcode-select -p
```

**ワークアラウンド（シンボリックリンクを手動作成）**

```bash
sudo ln -sfn "$(xcode-select -p)" /var/db/xcode_select_link
```

1 行で完了します。その後 Claude Code を再起動（またはセッションを開始し直す）と Simulator パネルへのアタッチが成功するようになります。

**CI 環境（GitHub Actions / Xcode Cloud）での対策**

fastlane や `xcodebuild` を使う CI では `xcode-select -s` を明示的に実行しているケースが多いですが、Claude Code を CI で動かす場合は起動前に下記を追加しておくと安全です:

```yaml
# .github/workflows/ios-claude.yml
- name: Ensure xcode-select symlink (Claude Code workaround #80041)
  run: |
    if [ ! -e /var/db/xcode_select_link ]; then
      sudo ln -sfn "$(xcode-select -p)" /var/db/xcode_select_link
    fi
```

**v2.1.238 の `keybindingFlavor: "readline"` 設定（ついでに）**

今日の公式アップデート v2.1.238 で追加された設定も適用しておくと快適です:

```bash
# Claude Code の設定を開く
claude config set keybindingFlavor readline

# または /config コマンド内の keybindingFlavor を "readline" に変更
```

`readline` に設定すると Ctrl+W で単語単位の削除（`Bash`・`zsh` と同じ挙動）が使えるようになり、長いプロンプトを素早く編集できます。
