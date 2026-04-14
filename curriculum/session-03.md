# 第3回：Zodによるデータバリデーション

## 1. 今日の目標
- Zodを使って「送られてくるデータの形」を定義する
- 正しくないデータ（名前が空、年齢が文字、メール形式でないなど）を自動で弾く仕組みを作る

## 2. なぜバリデーションが必要か？
サーバー側でチェックしないと、データベースにおかしなデータが入ったり、プログラムが予期せぬエラーで止まったりします。
「防御的なプログラミング」の第一歩です。

## 3. Zodの基本
```typescript
import { z } from 'zod'

// 「スキーマ（形）」を定義する
const userSchema = z.object({
  name: z.string().min(1, '名前は必須です').max(20),
  age: z.number().int().positive('年齢は正の数で入力してください'),
  email: z.string().email('正しいメールアドレスを入力してください')
})
```

## 4. Honoとの連携 (zValidator)
`@hono/zod-validator` を使って、リクエストを自動チェックします。

```typescript
import { zValidator } from '@hono/zod-validator'

app.post('/register', zValidator('json', userSchema), (c) => {
  // バリデーションを通過した安全なデータ
  const data = c.req.valid('json')
  return c.json({ message: `${data.name}さん、登録完了しました！` })
})
```

## 5. ハンズオン
わざと間違ったデータ（`age`に`-1`を入れる、`email`を適当にするなど）をAPIクライアントで送ってみましょう。
どのようなエラーメッセージが返ってくるか確認してください。

## 6. まとめ
- `z.object({...})` でデータのルールを決める。
- `zValidator` を挟むだけで、不正なデータを除去できる。
- フロントエンドにエラーを伝える手間が大幅に減る。
