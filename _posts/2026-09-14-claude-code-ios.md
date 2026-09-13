---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-14"
date: 2026-09-14 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（本日時点の最新版は v2.1.270。前回 2026-09-12 リリースのパッチ以降、新しいリリースは確認されていない）

## 🛠 GitHub の動き

- **[Issue #93949（2026-09-13 作成 / OPEN / duplicate ラベル）— iOS Simulator パネル: macOS 27.0 で `screenshot` がクラッシュループ、`inspect` は利用不可](https://github.com/anthropics/claude-code/issues/93949)** — macOS 27.0（build 26A428、プレリリース）上で Claude Code Desktop の iOS Simulator パネルが `screenshot` を呼ぶたびにクラッシュし自動再起動するループに入る。エラーは `"Claude Code iOS Simulator is restarting after a crash. Try again in 1s."` の繰り返しで、パネルは常に「Attach a simulator」プレースホルダーを表示したまま。`inspect` は `'inspect' is not available right now. Use 'screenshot' instead.` を返すが `screenshot` 自体が失敗するためデッドロック状態。一方、`attach` / `tap` / `open_url` はすべて正常動作する。クラッシュは iOS 26.5 / 27.0 ランタイムの両方で再現し、ScreenCaptureKit または CoreSimulator API の macOS 27 での変更が原因と推測されている（`xcrun simctl io <udid> screenshot` は問題なく動作するため、クラッシュは Claude Code の独自キャプチャパイプライン固有の問題）。`duplicate` ラベルが付いているが、これまで報告された #92442（Metal/CoreImage SIGABRT）や #80472（seatbelt の Metal シェーダーキャッシュブロック）とは独立した失敗モードで、macOS 27.0 GA ビルドでも再現する点が重要。**暫定回避策**: スクリーンショット取得は `xcrun simctl io <udid> screenshot /tmp/screen.png` で代替し、Claude Code パネルはタップ／URL 操作専用として使う。

## 📝 日本語コミュニティ

- 該当なし（2026-09-14 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[hmohamed01/swift-development — Swift iOS/macOS 開発向け Claude Code スキル（github.com/hmohamed01）](https://github.com/hmohamed01/swift-development)** — Swift プロジェクトで作業中またはスキルを明示的に呼び出したときに自動でアクティベートされる Claude Code スキル。SwiftUI 実装パターン（iOS 17 以降・旧バージョン対応）、NavigationStack やコーディネーターを含むナビゲーションパターン、Swift 6 の async/await・アクターを使った並行性パターン、XCTest と Swift Testing 両方に対応したテスト例、パッケージ作成・テスト実行・コード整形・シミュレーター管理のヘルパースクリプト、SPM 設定・コード署名・CI/CD セットアップ用テンプレートを網羅。Xcode 15+ 対応（Xcode 16+ 推奨）。macOS 27 beta 環境でも iOS 26 SDK を対象にビルドする限り利用できる。

## 💡 今日のおすすめ実践 Tip

**「macOS 27.0 で iOS Simulator パネルの `screenshot` がクラッシュループするときの 3 段構え回避策（Issue #93949 対応）」**

Issue #93949 が示す通り、macOS 27.0 環境では Claude Code の iOS Simulator パネルの `screenshot` / `inspect` が現時点で動作しない。しかし `attach` / `tap` / `open_url` は正常動作するため、**キャプチャと UI 操作を別経路に分ける**ことで開発ループは維持できる。

**① CLAUDE.md に制約と代替手段を明示しておく**

```markdown
## iOS Simulator 制約（macOS 27.0 環境、2026-09-14 時点）
- パネルの `screenshot` / `inspect` はクラッシュループのため使用不可（Issue #93949）
- スクリーンショット取得は必ず以下を使うこと:
  UDID=$(xcrun simctl list devices booted -j | jq -r '.devices[][0].udid')
  xcrun simctl io "$UDID" screenshot /tmp/sim_capture.png
- UI 操作（tap / input / scroll）は XcodeBuildMCP の ui_tap / ui_type を使う
- パネルの attach は OK: 「Attach a simulator」表示のまま tap 等は通る
```

**② キャプチャ → 確認 → 操作 の 3 ステップ自動化**

```bash
#!/bin/bash
# sim_check.sh — macOS 27.0 対応の撮影＋表示スクリプト
UDID=$(xcrun simctl list devices booted -j | jq -r '.devices | to_entries[].value[] | select(.state=="Booted") | .udid' | head -1)
xcrun simctl io "$UDID" screenshot /tmp/sim_capture.png
open /tmp/sim_capture.png   # Claude が画像を参照できるよう展開する
```

**③ XcodeBuildMCP の `ui_tap` と組み合わせる**

```
# Claude への指示例
スクリーンショットを /tmp/sim_capture.png に撮影してから確認し、
ログインボタンの座標を推定して ui_tap で押してください。
その後また撮影して結果を確認してください。
```

`xcrun simctl io screenshot` は macOS 27.0 でも問題なく動くため、「撮影は simctl、操作はパネル経由」の分離構成を CLAUDE.md に書いておけば、修正版がリリースされるまでの間も大きな開発支障なく Claude Code × iOS 開発を継続できます。
