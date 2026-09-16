---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-17"
date: 2026-09-17 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- 該当なし（2026-09-17 時点の最新版は引き続き v2.1.273。9/15 リリース以降、新しい Claude Code リリースは確認されていない）

## 🛠 GitHub の動き

- **[Issue #94767（2026-09-16 作成 / OPEN / duplicate ラベル）— iOS Simulator パネル: シミュレーター attach のたびに `claude-ios-sim` が SIGABRT クラッシュ、`dispatch_once` で回復不能](https://github.com/anthropics/claude-code/issues/94767)** — macOS 27.0・Apple Silicon (Mac16,8)・Xcode 27.0・Claude.app 1.52386.6 の環境で、iOS Simulator パネルでシミュレーターを選択するたびに `claude-ios-sim` が SIGABRT でクラッシュし、デバイスピッカー画面に跳ね返る。クラッシュスタックは `CI::PrecompiledUberFunctions → _MTLBinaryArchive loadFromURL: → dispatch_once → NSArray → 例外` で、CoreImage がプリコンパイル済みの Metal バイナリアーカイブをロードする際に nil 要素を配列へ渡す例外が `dispatch_once` ブロック内で発生するため、once-token がセットされたまま次の試行でも同じクラッシュが起きる。7 回連続で同一スタックを確認。これまでの #92442 / #94433 と同根だが、`dispatch_once` による回復不能メカニズムが明確に記録されたのは今回が初めて。**暫定回避策**: `xcrun simctl io <udid> screenshot` でスクリーンショット、Maestro / XcodeBuildMCP で UI 操作。

- **[Issue #94360（2026-09-14 作成 / OPEN）— iOS Simulator パネル: macOS 26.6.2 安定版でストリームが死んで回復不能（`detach()` が成功を返すが実際にはリセットされていない）](https://github.com/anthropics/claude-code/issues/94360)** — macOS 27 系だけの問題ではなく、**安定版の macOS 26.6.2** でも iOS Simulator パネルのビデオストリームが死ぬことが確認された。症状: `xcrun simctl` でアプリを再インストール等するとストリームが `captureFailed` を返すようになり、`detach()` を実行しても「Detached」と返しながら内部の attach フラグがクリアされないため、次の `attach()` が「already attached」で失敗して回復不能になる。一方、`tap` / `swipe` / `launch` は引き続き正常動作する。**暫定回避策**: Claude Desktop を一度終了して再起動するだけで `claude-ios-sim` ヘルパーが再生成され、即座に復旧する。Xcode 27 / macOS 27 環境に限定した話ではないため、安定版環境の iOS 開発者も注意が必要。

## 📝 日本語コミュニティ

- 該当なし（2026-09-17 時点で新規の Zenn・Qiita 記事は確認できず）

## 🌐 海外コミュニティ / Tips

- **[anthropics/ClaudeForFoundationModels v0.2.0（2026-09-09）— Xcode 27 beta 5 採用・claude-fable-5 / fable-5-1 追加・サーバーサイドフォールバック](https://github.com/anthropics/ClaudeForFoundationModels/releases/tag/0.2.0)** — Claude を Apple Foundation Models フレームワークの drop-in モデルとして使える Swift パッケージの v0.2.0 がリリース。**主な変更**: ① Xcode 27 beta 5 を採用（assistant turn のリプレイ方法が変更、破壊的変更）、② `claude-fable-5` / `claude-fable-5-1` モデルを追加、③ サーバーサイドフォールバックを追加して信頼性向上、④ リクエストをユーザープロファイルに紐付けられるようになった、⑤ バグ修正: plain ターンと guided ターン（`@Generable` 使用）でシステムプロンプトが一致しない問題を解消、⑥ セキュリティ修正: 設定済み base URL の authority 外へのリダイレクトを拒否するようになった。ストリーミング・`@Generable` によるガイド生成・ツール呼び出しは引き続き Apple Foundation Models の API と同一の書き方で動作する。Xcode 27 で Apple Intelligence を使う iOS アプリで Claude モデルへの透過的なフォールバックを実装したい開発者に有用。

## 💡 今日のおすすめ実践 Tip

**「macOS 26.x 安定版でも iOS Simulator ストリームが死んだときの即時復旧コマンド（Issue #94360 対応）」**

macOS 27 系の不具合だけでなく、**安定版 macOS 26.6.x でも**アプリの再インストール後などに iOS Simulator パネルのビデオストリームが死ぬことが Issue #94360 で判明しました。`detach()` → `attach()` では回復できないため、CLAUDE.md に復旧手順と予防策を明示しておくと開発ループが止まらずに済みます。

**① CLAUDE.md に復旧手順を追記する**

```markdown
## iOS Simulator パネル — ストリーム復旧手順（macOS 26.6.x 安定版）
# 症状: screenshot が captureFailed / attach が "already attached" と返す場合
# → Claude Desktop を再起動するだけで即時復旧する（データ損失なし）
# → 再起動後は /clear なしで同じセッションを継続できる
```

**② アプリ操作後に毎回スクリーンショットを撮って生存確認する**

```
# Claude への指示パターン（ストリーム生存確認を挟む）
1. xcrun simctl install <UDID> MyApp.app でアプリをインストールしてください
2. iOS Simulator パネルでスクリーンショットを撮って表示を確認してください
   （失敗したら Claude Desktop を再起動して続けてください）
3. タップして動作確認してください
```

**③ ストリームが死にやすい操作の前後でスクリーンショットを取得するシェルスクリプト**

```bash
#!/bin/bash
# sim_safe_install.sh — アプリ再インストール前後でストリームを確認する
UDID=$(xcrun simctl list devices booted -j \
  | python3 -c "import sys,json; d=json.load(sys.stdin)['devices']; \
    print(next(dev['udid'] for devs in d.values() for dev in devs if dev.get('state')=='Booted'))")
echo "[before] capturing..."
xcrun simctl io "$UDID" screenshot /tmp/before_install.png

xcrun simctl install "$UDID" "$1"   # 第一引数に .app パスを渡す

echo "[after] capturing..."
xcrun simctl io "$UDID" screenshot /tmp/after_install.png
echo "Done. Before: /tmp/before_install.png / After: /tmp/after_install.png"
```

```
# 使い方
bash sim_safe_install.sh ./build/Debug-iphonesimulator/MyApp.app
```

`xcrun simctl io screenshot` は macOS 26.x / 27.x のいずれでも安定して動作するため、**Claude Desktop パネルではなく `simctl` を正とするスクリーンショット取得フロー**を CLAUDE.md に確立しておくことで、パネルのストリーム障害に気づかず開発を止めてしまうリスクを最小化できます。
