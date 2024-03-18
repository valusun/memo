# MongoDBと接続 with TypeScript

まずはパッケージのインストールを行う。  
`npm i monogdb`

接続するMongoDBの接続識別子を取得して下記を実装する。  

```typescript
import { MongoClient } from "mongodb";
// 接続識別子
const id = "";
const pass = "";
const host = "";
const port = "";
const url = "mongodb://${id}:${pass}@${host}:${port}/?authSource = admin";

// 接続に必要なuriを持ったインスタンスを定義
const client = new MongoClient(url);

// 接続関数
const connectToDatabase = async() => {
  try {
    await client.connect();
    console.log("Connected!");
  } catch(e) {
    console.log(`Connection Error: ${e}`);
  }
}

const main = async() => {
  try {
    await connectToDatabase();
    // 接続出来た状態なので、dbやcollectionの呼び出しが出来る。
    const db = client.db("sample_database");
    const collection = db.collection("sample_connection");
    // ドキュメントを1件取得する
    console.log(await collection.findOne());
  } catch(e) {
    console.error(`Error: ${e}`);
  } finally {
    console.log("close");
    await client.close();  // 接続を切るのを忘れない
  }
}

main();
```
