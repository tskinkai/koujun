# 中國菜 香醇（Koujun）公式サイト — 引き継ぎメモ

神戸・三宮の中華料理＋ワインの店「中國菜 香醇（こうじゅん）」の静的サイト。
本番URL: https://chinese-kojun.com/ （GitHub Pages: https://tskinkai.github.io/koujun/ も有効）
リポジトリ: `git@github.com:tskinkai/koujun.git`（ブランチ `main`、push = 本番公開）

店主: 村川 貴泰（むらかわ たかひろ / Takahiro Murakawa）。ユーザーとのやり取りは日本語。

## 構成

ビルド工程なし。1ページ1ファイルの素のHTML/CSS/JS（フレームワーク・外部JS不使用、Googleフォントのみ）。
`index.html`（TOP） / `about.html` / `menu.html`（コース・アラカルト・ワインの3タブ） / `access.html` / `reserve.html`（予約・問い合わせ） / `news.html` / `news-article.html` / `press.html` / `privacy.html` / `images/` / `CNAME`（独自ドメイン。GitHub PagesのUI操作でも自動コミットされる）

- CSSは各HTMLの `<style>` に内包（共通部分はコピー運用）。変数: `--bg #F8F6F3 / --bg2 #F0EDE8 / --white / --ink #1A1A1A / --gold #6E1F2B（ワインレッド） / --gold2 #8C2E3D / --muted #6B6560 / --rule`。共通部品（nav・mobile-reserve-bar・footer）を変えるときは全ページに反映すること。
- 写真枠は `.photo-ph`（`<img class="ph-img">`）。写真が無い枠は `<img>` を省いた空プレースホルダーにする運用。「写真を差し替える」ホバーUI（`.photo-ph-swap` + `replacePhoto()`）は **TOPページのみ削除済み**。about / access / press には残っている（ユーザーから指示があるまで触らない）。
- `menu.html` のタブは `switchTab()`（ハッシュ `#course` `#alacarte` `#wine` で直接開ける）。アラカルト/ワインは `.alacarte-*` クラスを共用。ワイン・ドリンク側の見出しは「日本語→英語」順、アラカルト（料理）側は「英語→日本語」順のまま（ユーザー指示はドリンク側のみだった）。
- 「ご予約はこちら」ボタン（ヘッダー・スマホ下部固定バー）は全ページ TableCheck へ直リンク: `https://www.tablecheck.com/ja/kojun/reserve/message`（`target="_blank" rel="noopener"`）。フッターの「ご予約・お問い合わせ」だけが `reserve.html`（問い合わせ窓口）へ行く。

## コンテンツ運用ルール（重要）

- **PDFは絶対にコミット・pushしない。** ルート直下の `*.pdf`（メニュー・価格・ワインリスト等の支給資料、新旧複数版）はローカル専用で `.gitignore` 済み。`git add -A` / `git add .` は使わず、**ファイルを指定して add** する（過去に一度PDFを巻き込みかけた）。最新版の目安: `おまかせコース（説明文あり）.pdf` / `アラカルト（説明文あり）.pdf` / `ワインリスト.pdf`。
- **グラスワインの銘柄は非公開**（ユーザー指示）。サイトでは「白ワイン」「赤ワイン」とだけ表記し、産地・味わい・価格・ペアリングのみ載せる。
- 価格は **すべて税込**。おまかせコースは **¥7,700（税込）**（新PDFの「¥7,000／税抜」表記は誤りだったとユーザー確認済み）。コース内容は2か月に1回変わる想定。
- メニューの料理名・説明文・ワイン文は支給PDFの文面どおりに反映してある（改変しない）。メニュー更新時は新PDFを読み、HTMLと差分確認する。
- おまかせコース欄の料理写真・TOP最下部の店主写真・ワイン導入の写真枠は、ユーザー指示で削除済み。復活させない。
- 画像は実店舗の写真のみ。ダミー/ストック画像は全削除済みなので、仮画像を入れない。

## 公開・デプロイ手順

1. 変更したファイルだけ `git add`（PDF・`.DS_Store` は含めない）→ 日本語でコミット → `git pull --rebase origin main` → `git push origin main`。
   - GitHub Pages設定画面の操作で `CNAME` が自動コミットされるため、pushが rejected（non-fast-forward）になることがある。`pull --rebase` で解消する（過去に発生済み）。
2. 反映確認（1〜2分かかる）: `curl -sk --resolve chinese-kojun.com:443:185.199.108.153 https://chinese-kojun.com/<page>` で本文を確認。ポーリングするシェルでは変数名に **`status` を使わない**（zshの予約語でクラッシュする。`http_code` などを使う）。
3. 動作確認の注意: Claude側のBrowserペインで `file://` のローカルHTMLを開くと相対パスの画像が壊れて見えることがある（サイトの不具合ではない）。確認は公開後のURLで、`curl` や DOM チェック（`img.naturalWidth`）で行う。この環境では `http://`（80番）が407になることがあるが環境側の都合。

## ドメイン・DNS

- ドメイン `chinese-kojun.com` は ConoHa（GMOインターネット/お名前.comレジストラ）で取得。DNSも ConoHa（`ns-a1/a2/a3.conoha.io`）。
- レコード: `@` に A×4（GitHub Pages: `185.199.108.153 / .109.153 / .110.153 / .111.153`）、`www` に CNAME → `tskinkai.github.io`。GitHub側は Custom domain = `chinese-kojun.com`、Enforce HTTPS 有効。
- **`mail` `ml-cp` の A、MX（`mail1060.conoha.ne.jp`）、SPF/DKIMのTXT は独自メール用。触らない。**
- 2026-09-24頃、登録者情報メール認証の未完了で `clientHold`（NXDOMAIN・`ERR_EMPTY_RESPONSE`）になった。認証後に解消。サイトが突然見えなくなったら `whois -h whois.verisign-grs.com chinese-kojun.com` のステータスと `dig @ns-a1.conoha.io chinese-kojun.com` をまず確認。登録者情報を変更した場合も再認証メールが来る可能性あり。
- ConoHa・GitHub・TableCheckの管理画面はユーザー自身のアカウント。Claudeはパスワードを入力しない。Claude in Chrome は別プロファイルでユーザーのログイン状態を共有できない場合があり、画面操作はユーザーに手順を案内して行ってもらう形になった。

## 未対応・要確認（引き継ぎ時点）

- 営業時間・定休日は「決まり次第お知らせします」のまま（`index.html` / `access.html`）。確定したら更新。
- `reserve.html` の問い合わせフォームは `mailto:info@kojun-kobe.example`（ダミーアドレス）。本番のメールアドレスか外部フォームサービスへ差し替えが必要。
- `news.html` はお知らせ0件、`press.html` はメディア掲載実績なし。`index.html` の meta description は「オープン予定」表記のまま（開店後に修正）。
- メニューページから「所要時間（約120分）／ワインペアリング可／ご予約（前日まで）」のボックスを、ユーザー指示で削除済み（料金のみ見出し横に表示）。必要なら再掲を提案する。
- `koujun-main/` は初期テンプレートの古い複製で、サイトの一部として公開されたまま（`/koujun-main/index.html` が200）。ユーザーに確認のうえ削除を提案。
- 予備の実写真（未使用）: `chef-cooking-2/3/4.jpg`、`shark-fin-soup-2.jpg`、`exterior-2.jpg`、`building-entrance-2.jpg`、`shop-door-1/2/3.jpg`。
- 小さな掃除候補: `index.html` の未使用CSS `.menu-note` と未使用JS `replacePhoto`、リポジトリに入っている `.DS_Store` と `.claude/settings.local.json`。

## コミット

メッセージは日本語、変更の意図を簡潔に。コミット末尾の共著者表記はそのときのClaude Codeの指示に従う。
