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

## 2026-09-04 Cloudflare純正のGit連携ではなく GitHub Actions で自動デプロイする

- **背景**: GitHubで `index.html` を直す運用なので、Cloudflare が自動で追従しないと
  「直したのに反映されない」が起きる。
- **決定**: GitHub Actions（`cloudflare/wrangler-action`）から `wrangler pages deploy` を実行する。
- **理由**: Cloudflare の仕様で、ダイレクトアップロードで作ったプロジェクトは後から Git 連携に変更できない
  （[公式ドキュメント](https://developers.cloudflare.com/pages/get-started/direct-upload/)に明記）。
  純正連携にするならプロジェクトを作り直す必要があり、domain-check は自動採番のURLが変わってしまう。
  Actions 方式なら今のプロジェクトとURLのまま自動化できる。
- **確度**: 事実。

## 2026-09-04 旧URL（GitHub Pages）は止めずに案内ページに差し替える

- **背景**: 旧URLは既に顧客・社内に案内済み。止めるとその人たちがエラー画面に当たる。
- **決定**: GitHub Pages はオンのまま、公開元を `gh-pages` ブランチに変更し、
  そこには新URLへ転送する案内ページだけを置いた。`main` はツール本体のまま。
- **理由**: 静的ホスティングなのでサーバー側の301転送は使えず、meta refresh + JS が唯一の手段。
  `main` の `index.html` を差し替える案は、ツール本体を壊すので採らなかった。
  ブランチを分ければ、編集する人の手順（`main` を直す）は今まで通りで済む。
- **確度**: 確定仕様。
- **副作用**: ブラウザのブックマークは自動では書き換わらないため、案内ページに
  「ブックマークを登録し直してください」を表示している。

## 2026-09-04 公開URLは squadbeyond-domain-check.pages.dev にする

- **背景**: 自動採番の `domain-check-3h2.pages.dev` は、顧客が「これは公式のツールか」と判断しにくい。
- **決定**: プロジェクトを作り直し、`squadbeyond-domain-check.pages.dev` にした（本人選択 2026-09-04）。
- **理由**: 顧客が知っている製品名が入るため。`squad-domain-check` は社外には会社が伝わりにくく、
  `squaddomain-check` は単語がくっついて読みにくいので採らなかった。
  Cloudflare は `*.pages.dev` を後から変更できないが、URLを案内する前だったので作り直しの痛みがなかった。
- **確度**: 事実（`*.pages.dev` の変更不可は Cloudflare の仕様）。

## 2026-09-04 独自ドメインは当てていない（要インフラ依頼）

- **決定**: `domain-check.squadbeyond.com` のような独自ドメインは今回は当てず、`pages.dev` のままにした。
- **理由**: `squadbeyond.com` のDNSは **Route 53（AWS）** で管理されており（このツール自身で確認）、
  Cloudflare のこのアカウントにゾーンが無い。CNAMEの追加はAWS側の権限を持つ人への依頼が必要。
- **確度**: 事実（NSレコードの実測）。
- **やる場合**: Cloudflare Pages 側でカスタムドメインを追加 → 指示されたCNAMEを Route 53 に追加。
