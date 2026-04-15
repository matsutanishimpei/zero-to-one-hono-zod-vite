# 第1回：モダンJS復習とTypeScript入門

## 1. 今日の目標
- JavaScript の現代的な書き方を復習する
- TypeScript の「型」の考え方を理解する
- 開発環境（Node.js / VS Code）を整える

## 2. JavaScript の「これだけは」復習

### アロー関数
```javascript
// 従来の書き方
function greet(name) {
  return 'Hello ' + name;
}

// アロー関数（モダンな書き方）
const greet = (name) => {
  return 'Hello ' + name;
};

// 1行で書ける場合は return も省略できる
const greet = (name) => 'Hello ' + name;
```

### テンプレートリテラル
バッククォート（`` ` ``）で囲むと、文字列の中に変数を埋め込めます。
```javascript
const name = 'Taro';
const message = `Hello ${name}!`; // "Hello Taro!"
```

### 非同期処理 (async/await)
サーバーとの通信など「時間がかかる処理」を待つ仕組みです。
```javascript
const fetchData = async () => {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();
  console.log(data);
};
```

### 分割代入
オブジェクトや配列から必要な値だけを取り出します。
```javascript
const user = { name: 'Taro', age: 20, email: 'taro@example.com' };
const { name, age } = user; // name='Taro', age=20
```

## 3. TypeScript とは？
TypeScript は **JavaScript に「型」を追加した言語** です。ブラウザは直接読めないので、ビルド時に JavaScript に変換されます。

### なぜ型が必要か？
```javascript
// JavaScript: 実行するまでバグに気づかない
function add(a, b) {
  return a + b;
}
add('1', 2); // "12" ← バグ！文字列結合になってしまう
```

```typescript
// TypeScript: 書いた瞬間にエディタが教えてくれる
function add(a: number, b: number): number {
  return a + b;
}
add('1', 2); // ← ここで赤波線が出る！
```

### 基本の型
```typescript
const name: string = 'Taro';     // 文字列
const age: number = 20;           // 数値
const isStudent: boolean = true;  // 真偽値
const hobbies: string[] = ['読書', 'ゲーム']; // 配列
```

### 型推論（省略できるケース）
TypeScript は賢いので、明らかな場合は型を書かなくても推測してくれます。
```typescript
const name = 'Taro';  // string と自動で推論される
const age = 20;        // number と自動で推論される
```

### オブジェクトの型（type / interface）
```typescript
type User = {
  name: string;
  age: number;
  email: string;
};

const user: User = {
  name: 'Taro',
  age: 20,
  email: 'taro@example.com'
};
```

## 4. 開発環境の確認
```bash
node -v   # v20以上が表示されればOK
npm -v    # バージョンが表示されればOK
```

VS Code に以下の拡張機能を入れておきましょう：
- **ESLint**: コードの問題を自動検出
- **Prettier**: コードを自動整形

## 5. まとめ
- アロー関数、`async/await`、分割代入は今後のコードで常に使う。
- TypeScript は「型」でバグを事前に防ぐ仕組み。
- 型注釈（`: string` など）を書くことで、エディタの補完が強力になる。
