# 第2回：Cloudflare Hono 入門

## 1. 今日の目標
- Cloudflare Workers と Hono の概要を理解する
- Hono プロジェクトを作成し「Hello World」を表示する
- エラーが出たときのターミナルの読み方を知る

## 2. Cloudflare Workers とは？
通常のWebサーバーは「どこか1箇所のサーバー」で動きます。
Cloudflare Workers は **世界中の300以上の拠点** にコードが配置され、ユーザーに一番近い場所で実行されます。そのため爆速です。

## 3. Hono とは？
日本発のWebフレームワークで、Cloudflare Workers 上で動かすのに最適化されています。
- **超軽量**: ファイルサイズが非常に小さい
- **TypeScript ファースト**: 型の恩恵をフルに受けられる
- **Express ライク**: JavaScript の Express を使ったことがあれば馴染みやすい

## 4. ハンズオン：プロジェクト作成
```bash
npm create hono@latest my-app
```
選択肢が出たら：
- **Which template do you want to use?** → `cloudflare-workers`
- **Do you want to install project dependencies?** → `yes`

```bash
cd my-app
npm run dev
```
ブラウザで `http://localhost:8787` を開いて確認しましょう。

## 5. コードを読む
`src/index.ts` を開いてみましょう。
```typescript
import { Hono } from 'hono'

const app = new Hono()

app.get('/', (c) => {
  return c.text('Hello Hono!')
})

export default app
```

### コードの意味
| コード | 意味 |
|---|---|
| `import { Hono } from 'hono'` | Hono ライブラリを読み込む |
| `const app = new Hono()` | アプリケーションを作成 |
| `app.get('/', ...)` | 「/」にGETリクエストが来たときの処理 |
| `(c) => { ... }` | リクエストを処理する関数（`c` = Context） |
| `c.text('...')` | テキストをレスポンスとして返す |
| `export default app` | このファイルの「出口」を定義 |

## 6. ルーティングの基本

### パスパラメータ
URLの一部を変数として受け取ります。
```typescript
// /user/123 にアクセスすると id = '123'
app.get('/user/:id', (c) => {
  const id = c.req.param('id')
  return c.json({ message: `ユーザーID ${id} の詳細です。` })
})
```

### クエリパラメータ
`?` 以降のキーバリューを受け取ります。
```typescript
// /search?q=hono にアクセスすると query = 'hono'
app.get('/search', (c) => {
  const query = c.req.query('q')
  return c.json({ result: `「${query}」の検索結果` })
})
```

### POST リクエスト（データの送信）
```typescript
app.post('/posts', async (c) => {
  const body = await c.req.json()
  return c.json({
    message: 'データを受け取りました！',
    receivedData: body
  }, 201)
})
```

## 7. エラーが出たら？
ターミナルに赤い文字が出ても慌てないでください。
1. **エラーメッセージの最初の1行** を読む（何が問題か書いてある）
2. **ファイル名と行番号** を探す（`src/index.ts:5:10` = 5行目10文字目）
3. そこを見て、スペルミスや括弧の閉じ忘れがないか確認する

## 8. ハンズオン課題
**「今日の占いAPI」を作ってみよう**
- `/fortune` にGETリクエストが来たら、ランダムで `大吉` `中吉` `小吉` のどれかをJSONで返してください。
- ヒント: `Math.random()` と配列を使います。

## 9. まとめ
- Hono アプリの基本構造: `import → new Hono() → ルート定義 → export`
- `c.req.param()`, `c.req.query()`, `c.req.json()` でリクエストデータを取得。
- エラーが出たら「最初の1行」と「ファイル名:行番号」を確認。
