# デプロイ履歴 — domain-check

デプロイした内容・日付・環境を記録する（デプロイとログ更新は1セット）。

## 2026-09-04 Cloudflare Pages へ初回デプロイ

- **環境**: Cloudflare Pages `domain-check`（Squad社内アカウント `ce95fa768c0c138ecccca0a902fd2989`）
- **URL**: https://domain-check-3h2.pages.dev
  ※ `domain-check.pages.dev` は他アカウントに取られていて、Cloudflare が `-3h2` を自動で付けた
- **方式**: ダイレクトアップロード（`wrangler pages deploy`）。GitHub連携なし
- **アップロード対象**: `index.html` の1ファイルのみ
- **ビルド**: なし

### 内容
GitHub Pages（https://squad-customersupport.github.io/domain-check/）で公開していたものを、
中身を変更せずそのまま Cloudflare Pages にも載せた。GitHub Pages 側も稼働したまま残っている。

### 確認したこと
実ブラウザで `squadbeyond.com` を実際に照会し、以下がすべて動作することを確認：

- DoH（Cloudflare）での A / CNAME / AAAA / TXT / MX / CAA の取得
- 一致・不一致の判定（`squadbeyond.com` は配信用ドメインではないので「不一致」が正しい表示）
- NS からの DNS 管理元の判定（Amazon Route 53 と正しく表示）
- コンソールエラーなし

サーバー側の処理は無く、照会は閲覧者のブラウザから直接出るため、
ホスティング先を移しても挙動は変わらない。

### 未実施
GitHub と Cloudflare Pages は連携していない。GitHub で `index.html` を直しても
Cloudflare 側は古いまま。反映には再アップロードが必要（`notes/decision-log.md` 参照）。

## 2026-09-04 ② GitHub push で自動デプロイされるようにした／旧URLを案内ページに

### 内容
- `.github/workflows/deploy.yml` を追加。`main` の公開ファイルが更新されると
  GitHub Actions が `cloudflare/wrangler-action` で Cloudflare Pages へアップロードする。
  公開するのは index.html のみ（README・notes は載せない）。
- GitHub Pages の公開元を `main` から `gh-pages` ブランチへ変更。
  `gh-pages` には https://domain-check-3h2.pages.dev への案内ページ（meta refresh + JS）だけを置いた。
  旧URLを案内済みの人が新URLにたどり着けるようにするため、公開自体は止めていない。

### 環境・設定
- 認証は組織シークレット `CLOUDFLARE_API_TOKEN` / `CLOUDFLARE_ACCOUNT_ID`
  （Squad-customersupport の Organization secrets、対象リポジトリは media-shrink と domain-check のみ）。
- Cloudflare のトークンは「アカウント > Cloudflare Pages > 編集」の権限だけを持つカスタムトークン。

### 確認したこと
- `main` の `index.html` にマーカーを入れて push → Actions 成功 → https://domain-check-3h2.pages.dev にマーカーが出ることを確認。
  マーカーを消して push → 再び消えることも確認（両方向で反映される）。
- 旧URL（https://squad-customersupport.github.io/domain-check/）が案内ページを返し、
  転送先が https://domain-check-3h2.pages.dev になっていることを確認。

### つまずいた点（次回のため）
シークレット登録で3回失敗した。順に「値に `Secret: ` が混入」「トークン欄にアカウントIDを貼っていた」
「Cloudflareのコピーボタンが効かず、直前のクリップボード（アカウントID）が貼られていた」。
GitHub側の値は読み出せないので、`${#TOKEN}` の文字数と `/user/tokens/verify` の応答を出す
一時ワークフローを置いて切り分けた（原因判明後に削除済み）。

## 2026-09-04 ③ 公開URLを squadbeyond-domain-check.pages.dev に変更

- **新URL**: https://squadbeyond-domain-check.pages.dev
- **旧URL**: `domain-check-3h2.pages.dev`（本人に案内する前に変更したので移行の影響なし。プロジェクトごと削除）
- Pages プロジェクト名を `domain-check` → `squadbeyond-domain-check` に。
  Cloudflare は `*.pages.dev` を変更できないため、プロジェクトを作り直す形になった。
- `.github/workflows/deploy.yml` の `--project-name` を差し替え、`gh-pages` の案内ページの
  転送先も新URLに更新。

### 確認したこと
- `main` への push で新プロジェクトへ自動デプロイされること
- 旧GitHub Pages URL の案内ページが新URLへ転送すること
