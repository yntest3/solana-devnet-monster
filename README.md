# solana-devnet-monster

AIモンスター生成バトル — 名前と性格からAI（Claude）がモンスターの設定・能力・必殺技を生成し、CPUと自動バトル。結果は Phantom で承認のうえ Solana Devnet に記録できます。

## 起動

Phantom は `file://` では動作しないため、ローカルサーバーで開いてください。

```bash
python -m http.server 8000   # または npx serve .
# → http://localhost:8000/index.html
```

## GitHub Pages で公開する

ビルド不要の単一 HTML なので、リポジトリをそのまま公開できます。GitHub Pages は HTTPS で配信されるため Phantom も動作します。

1. 変更をコミットして `main` に push する

   ```bash
   git add index.html README.md
   git commit -m "Add AI monster battle app"
   git push origin main
   ```

2. GitHub でリポジトリを開き、**Settings → Pages** を開く
3. **Build and deployment** の **Source** で「Deploy from a branch」を選ぶ
4. **Branch** で `main`、フォルダで `/ (root)` を選んで **Save**
5. 1〜2分待つと、Pages 画面上部に公開 URL が表示される

   ```
   https://yntest3.github.io/solana-devnet-monster/
   ```

   `index.html` がルートにあるので、この URL でそのままアプリが開きます。

`gh` CLI を使う場合は、手順 2〜4 を次のコマンドで代用できます。

```bash
gh api repos/yntest3/solana-devnet-monster/pages -X POST \
  -f "source[branch]=main" -f "source[path]=/"
gh api repos/yntest3/solana-devnet-monster/pages --jq .html_url   # 公開URLの確認
```

以降は `main` に push するたびに自動で再デプロイされます（進行状況はリポジトリの **Actions** タブで確認できます）。

**注意**

- 無料プランで Pages を使うにはリポジトリが **Public** である必要があります。
- 公開サイトでは APIキー欄を空欄のまま（デモモード）で使ってください。キーを入力するとブラウザから直接送信されるため、共有PCや他人の環境では入力しないでください。キーは「このブラウザに保存」をオンにしない限り保存されません。
- 404 になる場合は、数分待ってから再読み込みするか、Settings → Pages で Branch 設定が保存されているか確認してください。

## 使い方

1. （任意）Anthropic APIキーを入力 → AIモード。空欄ならデモモード（ルールベース生成）
2. モンスターの名前・性格を入力して生成 → 「CPUとバトル」
3. Phantom を接続（設定 → 開発者設定 → テストネットモード → Solana Devnet）
4. 確認ダイアログで内容を確認し、Phantom で署名すると Memo プログラムで結果が記録されます
5. 「オンチェーン戦績」に自分のアドレスの記録が一覧表示されます

## 構成

- 単一の `index.html`（React 18 + htm、ビルド不要・CDN読み込み）
- AI: `@anthropic-ai/sdk`（`claude-opus-5-5`、構造化出力で JSON 生成）。失敗時はデモ生成にフォールバック
- チェーン: `@solana/web3.js` + SPL Memo プログラム（Devnet RPC に送信）
- バトルはシード付き乱数で再現可能。seed とハッシュをメモに含めます

> APIキーはブラウザから直接 Anthropic API に送信されます。個人の検証用途に限定してください。
