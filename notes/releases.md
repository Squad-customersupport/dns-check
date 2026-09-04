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
