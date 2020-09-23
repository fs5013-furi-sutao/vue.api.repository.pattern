# vue.api.repository.pattern
Vue.js を使用した Web API 呼び出しの Repository パターンサンプル

## Vue で API を呼ぶシンプルな方法
Repository パターンは、アプリケーションを作成するのに一般的となるパターンの 1 つである。これにより、アプリケーション内のデータを直接操作することが制限され、データベース操作、ビジネスロジック、およびアプリケーション UI の新しいレイヤーが作成される。

リポジトリ設計パターンのメリットは以下の通り。
1. データアクセスのコードが再利用できる
2. ドメインロジックの実装が容易となる
3. ロジックの分離に役立つ
4. データアクセスロジックがなくても、ビジネスロジックを簡単に単体テストすることができる
5. コードをよりテストしやすくする依存性注入を実装するための良い方法でもある

リポジトリ設計パターンでは、最終的にデータストアに格納またはデータストアから取得されるデータの詳細を隠蔽する。

リポジトリ設計パターンについて多くのプログラミング言語を考えると、様々な実装方法が考えられます。が、ここでは Vue.js アプリケーションにリポジトリ設計パターンを実装する方法を示す。

### Repository.js
axios の設定を定義する。ファイル名はリソースの接続を確立する責任があるため、Repository.js とする。こうした機能を service や API、axiosclient と名付ける人もいるが、ここでは Repository と名付ける。

Repository.js
```javascript
import axios from "axios";

const baseDomain = "https://jsonplaceholder.typicode.com";
const baseURL = `${baseDomain}`; // Incase of /api/v1;

// ALL DEFUALT CONFIGURATION HERE

export default axios.create({
  baseURL,
  headers: {
    // "Authorization": "Bearer xxxxx"
  }
});
```
