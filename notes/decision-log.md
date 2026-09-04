# 意思決定ログ — domain-check

<!--
フォーマット
## YYYY-MM-DD 決めたこと（見出しは「何を決めたか」）
- **背景**: なぜ決める必要があったか
- **決定**: どうすることにしたか
- **理由**: なぜそれを選んだか / 他の案を採らなかった理由
- **確度**: 事実 / 推測 / 確定仕様
-->

## 2026-09-04 Cloudflare Pages はダイレクトアップロードで開始する

- **背景**: 社内の他ツールを Cloudflare に集約しているため、このツールも同じ場所に置きたい、という依頼。
- **決定**: `wrangler pages deploy` によるダイレクトアップロードで公開した。
  GitHub リポジトリとの連携（push で自動デプロイ）は設定していない。
- **理由**: 社内の既存 Pages プロジェクトはすべてダイレクトアップロード（Git Provider: No）で揃っている。
  GitHub 連携は Cloudflare ダッシュボード上で GitHub の認可が必要で、CLI からは設定できない。
- **確度**: 事実（`wrangler pages project list` で全プロジェクトが Git Provider: No）。

## 2026-09-04 プロジェクト名は domain-check、URLは domain-check-3h2.pages.dev になった

- **背景**: `domain-check.pages.dev` は Cloudflare 全体で他の誰かが先に使っていた。
- **決定**: プロジェクト名は `domain-check` のままにし、URL は Cloudflare が自動で付けた
  `domain-check-3h2.pages.dev` を使う。
- **理由**: プロジェクト名を変えても URL の重複は解消しない。
  README にも書いたとおり、顧客に出すなら本来は `squadbeyond.com` 配下のサブドメインが望ましく、
  そのときカスタムドメインを当てれば `-3h2` は表に出なくなる。
- **確度**: 事実（プロジェクト作成時に Cloudflare が suffix を付与）。
