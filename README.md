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

### API へのインタフェースを作成する
axios の設定を定義する。ファイル名はリソースの接続を確立する責任があるため、Repository.js とする。こうした機能を service や API、axiosclient と名付ける人もいるが、ここでは Repository と名付ける。

Repository.js
```javascript
import axios from "axios";

const baseDomain = "https://jsonplaceholder.typicode.com";
const baseURL = `${baseDomain}/api/v1`;

// ALL DEFUALT CONFIGURATION HERE

export default axios.create({
  baseURL,
  headers: {
    // "Authorization": "Bearer xxxxx"
  }
});
```

### 個別のリポジトリクラスを作成する
これらのクラスに何が含まれるか、アプリケーションの特定の機能内で実行されるさまざまな API 操作を推測する必要がある。アプリケーションの 1 つの機能である Student を使用してデモを行う。
そこで、`repositories` フォルダー内に StudentRepository.js ファイルを作成し、以下のコードなどを追加する。

StudentRepository.js
```javascript
import Repository from './Clients/Repository';
const resource = '/students';

export default {
    get() {
        return Repository.get(`${resource}`);
    },
    getStudent(id) {
        return Repository.get(`${resource}/${id}`);
    },
    create(payload) {
        return Repository.post(`${resource}`, payload);
    },
    update(payload, id) {
        return Repository.put(`${resource}/${id}`, payload);
    },
    delete(id) {
        return Repository.delete(`${resource}/${id}`)
    },

    // MANY OTHER ENDPOINT RELATED STUFFS
};
```

### RepositoryFactory.js クラスを作成する
`RepositoryFactory` と呼ばれる` repositories` フォルダ内にファクトリクラスを作成して、作成したすべての個別のリポジトリをエクスポートする。これにより、アプリケーション全体でどこでも簡単に使用できるようになる。

RepositoryFactory.js
```javascript
import PostRepository from './TeacherRepository';
import UserRepository from './StudentRepository';

const repositories = {
    'teachers': TeacherRepository,
    'students': StudentRepository
}
export default {
    get: name => repositories[name]
};
```

### モデル、コントローラー、Vuex ストアのいずれかで使用する
Vue.js を扱っているため、Vue コンポーネントと Vuex ストアでの使用方法を示す。しかし、モデル、コントローラ、実際にはどこでもそれを使用するアプローチ同じとなる。

Posts.js 
```javascript
<template>
  <div class="row">
    <Post v-for="(post, i) in posts" :key="i" :posts="post" />
    <div class="col-lg-8 col-md-10 mx-auto">
      <div class="clearfix">
        <a class="btn btn-primary float-right" href="#">Older Posts &rarr;</a>
      </div>
    </div>
  </div>
</template>

<script>
import Repository from "../repositories/RepositoryFactory";
const PostRepository = Repository.get("posts");

import Post from "./Post";
export default {
  name: "Posts",
  components: {
    Post
  },
  data() {
    return {
      posts: []
    };
  },
  created() {
    this.getPosts();
  },
  methods: {
    getPosts: async function() {
      const { data } = await PostRepository.get();
      this.posts = data;
    }
  }
};
</script>
```

### Vuex ストアで使用する
`store.js` ファイルを作成する。

store.js
```javascript
import Vue from 'vue';
import Vuex from 'vuex';

import Repository from "./repositories/RepositoryFactory";
const StudentRepository = Repository.get("students");

Vue.use(Vuex)

const store = new Vuex.Store({
    state: {
        stiudents: [],
    },

    actions: {
        async getStudents({
            commit
        }) {
            commit('loadPosts', await StudentRepository.get());
        },
    },

    mutations: {
        loadStudents: (state, res) => {
            const {
                data
            } = res;
            state.students = data
        },

    }
});
export default store
```
