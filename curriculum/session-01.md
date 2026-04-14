# 第1回：モダンJS復習とCloudflare Hono入門

## 1. 今日の目標
- 何を作るのか（Cloudflareエコシステム）を理解する
- 開発環境（Node.js / npm）を確認する
- Honoを使って「Hello World」をブラウザに表示する

## 2. JavaScript/TypeScriptの「これだけは」復習
忘れていることが多い、あるいは現代的な書き方に馴染みがないポイントを復習します。
- **アロー関数**: `const greet = (name) => `Hello ${name}``
- **非同期処理 (async/await)**: `const res = await fetch(...)`
- **分割代入**: `const { name, age } = user;`

## 3. Cloudflare & Hono とは？
- **Cloudflare Workers**: 世界中のエッジ（利用者に近い場所）で動くサーバーレス環境。
- **Hono**: 日本発・世界標準の超軽量フレームワーク。TypeScriptとの親和性が抜群。

## 4. ハンズオン：最初のプロジェクト
ターミナルを開いて以下のコマンドを実行しましょう。

```bash
# プロジェクト作成
npm create hono@latest my-app
# (選択肢: cloudflare-workers を選び、ディレクトリ my-app に作成)

cd my-app
npm install
npm run dev
```

`src/index.ts` の内容を以下のように書き換えて、ブラウザで `http://localhost:8787` を確認しましょう。
```typescript
import { Hono } from 'hono'
const app = new Hono()

app.get('/', (c) => {
  return c.text('Hello Hono! 爆速でWebサーバーが立ち上がりました。')
})

export default app
```

## 5. まとめ
- `app.get('/', ...)` が「/」にアクセスした時の処理。
- `c.text()` でテキストを返す。
- `wrangler` というツールが裏で動いて開発サーバーを立てている。
