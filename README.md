# claude-code-ios-daily

Claude Code を iOS 開発で活用するための最新ノウハウを日次でキャッチアップする自動更新サイトです。

- **公開サイト**: https://yuki7827.github.io/claude-code-ios-daily/
- **更新頻度**: 毎朝 8:00 JST（自動）
- **更新の仕組み**: Anthropic Routines（リモートエージェント）が `CLAUDE.md` の手順に従って `_posts/` 配下に新しい記事を追加し、push する。GitHub Pages（Jekyll）が自動デプロイ。

## このリポでやらないこと

- 個人情報・APIキー・トークン・社内情報を一切コミットしない（[CLAUDE.md](./CLAUDE.md) で明示的に禁止）
- 公開ウェブから取得できる情報のみを扱う

## ローカルでの手動実行

このディレクトリで Claude Code を起動すれば、`CLAUDE.md` を読み込んで同じ手順を実行できます。
