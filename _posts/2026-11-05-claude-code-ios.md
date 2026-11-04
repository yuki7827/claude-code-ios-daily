---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-11-05"
date: 2026-11-05 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- [v2.1.318（2026-11-04）](https://code.claude.com/docs/en/changelog) — 昨日リリース。iOS 開発・CI に関連する主な変更点: ① **`claude auth status` / `claude -p` JSON 出力に認証期限フィールドを追加**（`loginExpiresAt` / `credential_expires_at`）。期限10日前からは警告も表示されるようになった。Xcode Cloud や GitHub Actions など CI 環境で Claude Code を動かしている場合、セッション期限切れによる無言の失敗を事前に検知しやすくなる。② **クラウドセッションで「No repository」オプションを追加**。リポジトリを紐付けずにスタンドアロンタスクを走らせられる。③ **IDE ターミナルから起動しても IDE 拡張機能を自動インストール・更新しなくなった**。以前は起動のたびに拡張が更新され、VS Code + Xcode 連携でプラグイン設定が上書きされることがあった。今後は `/ide` コマンドか拡張機能画面から手動で導入する方式に変更。④ **`--continue` 実行時に前の会話が消える不具合を修正**（v2.1.314 リグレッション）。iOS 開発で長い会話を続けながら `/continue` するフローで前のコンテキストが飛んでいた問題が解消。⑤ **macOS で Bash コマンドを多数実行するとメモリが増え続ける問題を修正**。Xcode ビルドループで大量の `xcodebuild` を走らせる構成で顕在化していたリークが解消された。

## 🛠 GitHub の動き

- 該当なし（2026-11-05 時点で新規 iOS / Xcode / Swift 関連 Issue は確認できず。直近の主要 Issue は 11-04 キャッチアップを参照）

## 📝 日本語コミュニティ

- [AI に iOS アプリを実装させるとき、触らせてはいけないファイルと守り方（Qiita / yasu-ya）](https://qiita.com/yasu-ya/items/c5b28fe4781d96723b20) — Xcode 26.6・GitHub Actions・Claude Code で動作確認済みの実践ガイド。**絶対に触らせないファイル**として `.github/`・`Scripts/`・`fastlane/`・`*.entitlements`・`Package.swift`・`GoogleService-Info.plist` などを列挙。変更一覧を禁止パターンと照合して CI で自動検査する仕組みと、`project.pbxproj` だけは「ファイル名」ではなく「差分の中身」（署名設定・Run Script・ライブラリ取得元など）で検査する手法が実践的。見張りスクリプト自体を AI 作業ブランチではなくブランチ保護された main から取得することで、見張り役の改ざんを防ぐ設計も解説されている。

## 🌐 海外コミュニティ / Tips

- [Headless Xcode: From Prompt to Simulator with MCP（artemnovichkov.com）](https://artemnovichkov.com/blog/headless-xcode-from-prompt-to-simulator-with-mcp) — Xcode 27 の「External Agent Access」を `Always` に設定することで Xcode を閉じたままエージェントからツールを呼べるようになる点を詳解。リポ直下の `.mcp.json` に `xcrun mcpbridge` を登録し、`xcrun agent skills export` でスキルを `.claude/skills/` に書き出す手順を整理。シミュレーター操作ではアクセシビリティ階層の座標を使ってボタンをタップし、OSLog で内部状態も確認できる構成が紹介されている。設定をリポジトリに残せる `.mcp.json` 方式が再現性の面で推奨されている。

- [ndrblinov/xcode-mcp-manager（GitHub）](https://github.com/ndrblinov/xcode-mcp-manager) — Xcode の起動を2秒ごとに検知し、起動後すぐに `xcrun mcpbridge` を自動起動・再接続し、AppleScript で許可ダイアログも自動承認するツール。ビルド・テスト・SwiftUI プレビュー・診断など24個のMCPツールを提供し、60秒ごとの keepalive で接続を維持する。「毎回ダイアログを手動で承認する手間を完全に省きたい」という用途向け。要件は macOS・Xcode 26.3 以上・Node.js 22 以上。

## 💡 今日のおすすめ実践 Tip

**AI に触らせてはいけないファイルを `project.pbxproj` の差分検査で守る**

Qiita/yasu-ya の記事で紹介されている手法が参考になる。iOS プロジェクトでファイル名による禁止リストを作っても、`project.pbxproj` だけは新規ファイル追加のたびに必ず変わるため名前では弾けない。そこで CI スクリプトで `git diff` の差分を精査し、以下のような危険な変更パターンを検知したら即失敗させる：

```bash
#!/bin/bash
# project.pbxproj の差分から危険な変更を検査する
DIFF=$(git diff --staged -- "*.pbxproj")

# 署名 ID の変更を禁止
if echo "$DIFF" | grep -qE '^\+.*DEVELOPMENT_TEAM\s*=|^\+.*CODE_SIGN_IDENTITY\s*='; then
  echo "ERROR: 署名設定の変更を検知。AI からの変更は禁止。" >&2
  exit 1
fi

# Run Script の追加・変更を禁止
if echo "$DIFF" | grep -qE '^\+.*shellScript\s*='; then
  echo "ERROR: Run Script の変更を検知。AI からの変更は禁止。" >&2
  exit 1
fi

# Package 参照の変更を禁止
if echo "$DIFF" | grep -qE '^\+.*XCRemoteSwiftPackageReference'; then
  echo "ERROR: SPM 依存の変更を検知。AI からの変更は禁止。" >&2
  exit 1
fi

echo "pbxproj 差分チェック: OK"
```

このスクリプトを `.github/workflows/` に置き、AI の作業ブランチの PR で必ず走らせることで、Claude Code が誤って署名設定やライブラリ参照を変更しても自動でブロックできる。見張りスクリプト自体は `CLAUDE.md` の「触らせないファイル」リストに追記して Claude に認識させておくことも忘れずに。
