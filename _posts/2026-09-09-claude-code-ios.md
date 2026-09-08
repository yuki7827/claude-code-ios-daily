---
layout: post
title: "Claude Code × iOS キャッチアップ 2026-09-09"
date: 2026-09-09 08:00:00 +0900
categories: [daily]
tags: [claude-code, ios]
---

## 🆕 公式アップデート

- **v2.1.265（2026-09-08）リリース**（[Claude Code 変更ログ](https://code.claude.com/docs/en/changelog)）— iOS 開発者に関係する主な新機能: ① **`--plugin-dir` フォルダ指定 + 子フォルダ自動検出・ホットロード対応** — スキルや MCP 設定を格納したディレクトリを指定するだけで配下のプラグインを自動検出し、再起動なしで更新を反映できるようになった。iOS プロジェクトで用途別に複数スキルを管理する場合に整理しやすくなる; ② **`--worktree` 起動時の並列チェックアウト対応（git 2.32 以上）** — XcodeBuildMCP での並列ビルドや複数ブランチを worktree で管理する大規模 iOS リポジトリでの起動が大幅に高速化; ③ **ツール結果の 1 GB キャップ追加** — 大容量 `.xcresult` や長大なビルドログがディスクに書き出される際に上限が明示されトランケーション表示が付くようになった。

## 🛠 GitHub の動き

- [Issue #88006 — iOS Simulator MCP: アプリへの起動引数を渡す方法がない（control action:"launch" が simctl launch をそのまま実行し `text` は無視される）（anthropics/claude-code / OPEN・2026-09-08 更新）](https://github.com/anthropics/claude-code/issues/88006) — iOS Simulator パネルの `control` アクション `"launch"` が内部で `simctl launch` を bare 呼び出しするため、`ENABLE_TESTING_MODE=1` 等のデバッグ用起動引数がシミュレーターに渡らない問題。ディープリンクのテストや Launch Arguments を使った機能フラグ切り替えを Claude Code に任せたい場合に直接影響する。ユーザーからは `launchArguments` プロパティのサポートが要望されており、暫定回避策は `xcrun simctl launch <UDID> <BundleID> <arg1> <arg2>` を Bash ツール経由で実行すること。

- [Issue #87875 — iOS Simulator パネルにブラウザペインのアノテート（鉛筆）ツールを追加してほしい（anthropics/claude-code / OPEN・2026-09-07 更新）](https://github.com/anthropics/claude-code/issues/87875) — ブラウザペインに存在するスクリーンショット上への手書き注釈（鉛筆アイコン）機能を iOS Simulator パネルにも追加してほしいというフィーチャーリクエスト。UI バグ報告を Claude にテキストで説明する代わりに、シミュレーター画面に直接マークアップして Claude に渡せるようになれば、UI フィードバックループが効率化できる。多くの開発者がコメントで「+1」を付けており、優先度が上がっている状況。

- [Issue #80041 — iOS Simulator ペインが `/var/db/xcode_select_link` 不在時に「Xcode not selected」を誤報する問題（anthropics/claude-code / OPEN）](https://github.com/anthropics/claude-code/issues/80041) — `xcode-select -p` が正常に Xcode を返す環境でも、シンボリックリンク `/var/db/xcode_select_link` が存在しないと iOS Simulator ペインがエラー表示になるバグ。コマンドラインツールの独立インストールや `DEVELOPER_DIR` 環境変数での指定が考慮されていない。`DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer` を設定するか、`sudo xcode-select -s /Applications/Xcode.app` でシンボリックリンクを再生成する回避策がある。

## 📝 日本語コミュニティ

- [Copilot Vision と Claude Code で iOS アプリを新規作成して比較（Zenn / yamazaking）](https://zenn.dev/yamazaking/articles/copilot-vision-claude-code-ios) — GitHub Copilot Vision（画像入力でデザインモックから SwiftUI を生成する機能）と Claude Code による iOS アプリ新規作成を比較した記事。ゼロから同一仕様のアプリを両ツールで作り、コード品質・ビルド成功率・プロンプト試行回数を比較している。Claude Code は XcodeBuildMCP と連携してビルドエラーの自己修正ループが動作した点が高く評価されていた。

- [Swift × Kiro × Claude Code（Zenn / jambo_dev）](https://zenn.dev/jambo_dev/articles/34fe2c1db1ae05) — Amazon の AI ファースト IDE「Kiro」と Claude Code の組み合わせで SwiftUI アプリを開発した体験記。Kiro の仕様駆動開発フロー（`spec/` → `design/` → 実装自動化）と Claude Code のリアクティブな修正能力を補完的に使う構成が紹介されている。「仕様を Kiro に書かせ、コード修正は Claude Code に任せる」という分業パターンは iOS 個人開発で試せる実践的なアプローチ。

## 🌐 海外コミュニティ / Tips

- [Claude Code iOS Simulator: Where Real Devices Fit（Kobiton ブログ）](https://kobiton.com/blog/claude-code-ios-simulator-real-device-testing/) — Claude Code の iOS Simulator 統合では再現できないケース（Bluetooth・カメラ・NFC・プッシュ通知・特定の ARKit 機能・実機特有のパフォーマンス問題など）を整理し、実機テストプラットフォームと Claude Code を組み合わせた CI パイプラインの設計指針をまとめた記事。「シミュレーターで完結させるタスク」と「実機が必要なタスク」を事前に CLAUDE.md に列挙しておくことで Claude が誤ってシミュレーターで済ませてしまうのを防ぐプロンプト設計例も掲載されている。

## 💡 今日のおすすめ実践 Tip

**「大規模 iOS リポジトリで `--worktree` × v2.1.265 並列チェックアウトを活かす設定」**

v2.1.265 で `--worktree` の起動に並列チェックアウト（git 2.32+）が導入され、worktree を使った複数ブランチ並行作業がより現実的なスピードになりました。大規模 iOS プロジェクト（`Pods/` 除外後でも数万ファイル規模）での設定例を紹介します。

**git 側の準備**

```bash
# git のバージョン確認（2.32 以上が必要）
git --version

# 並列チェックアウト workers 数を CPU コア数に合わせる（例: M4 Max = 16 コア）
git config --global checkout.workers 16

# FSMonitor でファイルシステム監視を有効化（大規模 repo の status 高速化）
git config --global core.fsmonitor true
```

**Claude Code worktree の典型的な使い方（iOS 開発）**

```bash
# feature ブランチ用の worktree を作成（.claude/ は共有なので設定も引き継ぐ）
git worktree add ../myapp-feature feature/new-payment-flow

# Claude Code を worktree で起動
claude --worktree ../myapp-feature
```

`Pods/` や `DerivedData/` は worktree に含めず共有または別パスにすることで、チェックアウト対象ファイル数を最小限に抑えるのが重要です。v2.1.265 の並列チェックアウトが最も効くのは「ファイル数が多く I/O に律速されていたケース」です。`--plugin-dir` の hot-loading と組み合わせて、ブランチ切り替えと同時に iOS 用スキルが自動リロードされる構成にしておくとさらに快適です。
