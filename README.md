---

theme : "black"
transizion : "default"

---

# Vue.js入門 4章
###  Vue Router を活用したアプリケーション開発

--

## 初めに
- このドキュメントは、Vue.js 入門 の4章の勉強会用です。
- reveal js で見ることを想定して作成しています。
    - 参考：https://jyun76.github.io/revealjs-vscode/

--

## 自己紹介
- 今村昌平
- 普段はWebシステムの受託会社で SE として勤務
- プログラムを書くことは少ない
- 個人開発する予定なので、リリースしたら使ってください
 - (共有マニュアル) 

--

## 全体の流れ

### 前半
- シングルアプリケーション
- ルーティングの基礎
- 実践的なルーティングのための機能

### 後半
- サンプルアプリケーションの実装
- Vue Router の高度な機能

---

## 4.1 Vue router によるシングルアプリケーション

- シングルアプリケーションとは何か？
- Vue Router の特徴はなにか？
- 本章ではどんな作成するアプリケーションを作成するか？

--

### 4.1.1 シングルアプリケーションとルーティング

--

#### シングルアプリケーション(SPA)とは

初めに１つのHTMLをロードし、以降はユーザインタラクションに応じて Ajax で情報を取得し、動的にページを更新刷るWebアプリケーションのこと

例：trello 
https://trello.com/b/UYVBgNXG/

--

#### 通常のWebアプリケーションとの違い

|通常|SPA|
|---|---|
|遷移ごと|最初のみ|
|HTML全体をロードする|必要なデータのみを取得|
|サーバサイド|クライアントサイド|
---
- クライアントサイドでの履歴管理なども含めたページ遷移(ルーティング)
- 非同期によるデータ取得
- view のレンダリング
- モジュール化されたコードの管理

--

### 4.1.2 Vue Router とは

- Vue.js の公式のプラグイン
- SPA構築のためのルーティングライブラリ
- Vue.js でのルーティング管理を担う
- Vue.js そのものに URL を管理させる

--

#### ルーターの基本機能

- ページ遷移(ルート)のルールを定義する
- URLに対応する Vue.js のコンポーネントがアクティブになる

![VueRouterの基本概念図](https://user-images.githubusercontent.com/39234750/58983521-fc496200-8811-11e9-937d-835a7a813b04.jpg)


--

#### 基本機能以外の機能
- ネストしたルーティング
- リダイレクトとエイリアス
- HTML5History API と URL Hash による履歴管理
- 自動的に CSS クラスがアクティブになるリンクの仕組み
- Vue.js のトランジションの仕組みを使ったページ遷移時のトランジション
- スクロールの振る舞いのカスタマイズ

---

## 4.2 ルーティングの基礎
- ルーターのインストール方法
- ルーティングを実装する流れ、使い方

--

### 4.2.1 ルーターのインストール

- 直接ダウンロード / CDN 
```js
<script src="https://unpkg.com/vue@2.6.10"></script>
<script src="https://unpkg.com/vue-router@3.0.6"></script>
```

- npm でインストール
```
npm install vue-router
```
- 参考
https://router.vuejs.org/ja/installation.html

--

### 4.2.2 ルーティング設計

- ルートとは、URL と Viewの情報(このURLのときは、このページを表示する)

- VueRouterにおけるルート： URLとVue.jsのコンポーネントの情報

```js
// ルート定義
{
    path: '/someurl', // URLを指定 ファイル名 #/someurl でアクセスできる
    component: {
        template: '...' // 3章で用いたコンポーネントの構文、もしくはコンストラクタベースのコンポーネントを用いる
    }
}
```

```js
//　ルータコンストラクタ、これをnew Vue() にわたす
new VueRouter({
    // コンポーネントをマッピングしたルート定義を配列で渡す
    routes: [] // ルート定義を配列で渡す
})
```


--

#### 例


||path|component|
|---|---|---|
|トップ| '/top' | {template:'`<div>`トップページです`</div>`'}|
|ユーザ一覧| '/users' | {template:'`<div>`ユーザ一覧ページです。`</div>`'}|


 例）https://jsfiddle.net/imamura_shohei/ow92jrsu/


```html
<!-- Vue.js と Vue Router を読み込んでおく -->
<script>
// ルートオプションを渡してルーターインスタンスを生成
var router = new VuewRouter({
    // コンポーネントをマッピングしたルート定義を配列で渡す
    routes: [
        {
            path: '/top',
            component: {
                template: '<div>トップページです.</div>'
            }
        },
        {
            path: '/users',
            component: {
                template: '<div>ユーザー一覧ページです。</div>'
            }
        }
    ]
})
// ルーターのインスタンスを root となる Vue インスタンスにわたす
var app = new Vue({
    router: router
}).$mount('#app')
</script>

```

--

### HTML へ反映させる

** router-link **
- router 作成したルートへのリンクの定義
- `<a>`タグとしてレンダリングされる

** router-view **
- ルート内でマッピングしたコンポーネントをレンダリングする部分


```html:例
<div id="app">
    <!-- リンク先を`to`プロパティに指定する -->
    <!-- デフォルトで<router-link>は`<a>`タグとしてレンダリングされる -->
    <router-link to="/top">トップページ</router-link>
    <router-link to="/users">ユーザー一覧ページ</router-link>
    <router-view></router-view>
</div>

```

--

### $mount

- Vue インスタンス生成時に el オプションを受け取らない  
    => DOM 要素は関連付けなし  
    => アンマウント(マウントされていない) 状態
- vm.$mount() は マウンティングを手動で開始するために使用

```js
// コンポーネントを定義
var MyComponent = Vue.extend({
  template: '<div>Hello!</div>'
})

// el オプションを受け取りインスタンス生成
new MyComponent({ el: '#app' })

// インスタンスを生成し、#app にマウント(#app を置換)
new MyComponent().$mount('#app')
```

---

### 4.3 実践的なルーティングのための機能
- URLパラメーターの扱いとパターンマッチング
- 名前付きルート
- router.push を使った遷移
- フック関数

--

### 4.3.1 URL パラメータの扱いとパターンマッチング

|path|component|
|---|---|
|'/user/:userId'|{template:`'<div>`ユーザーIDは{{$route.params.userId}}です。`</div>'`}|


例）https://jsfiddle.net/imamura_shohei/os9q7pzm/10/

--

### 4.3.2 名前付きルート

ルートの呼び出し方
1. url を指定
2. ルート名を指定 <=

```js
var router = new VueRouter({
    route: [
        {
            path: '/user/:userId',
            name: 'user', // ここでルート名を定義します。
            component: {
                template: '<div>ユーザIDは{{ $route.params.userId }}です。</div>'
            }
        }
    ]
})
```
```html
<router-link :to="{name: 'user', params: {userId: 123 }}">ユーザ詳細ページ</router-link>
```

--

### 4.3.3 router.push を使った遷移

遷移の仕方
1. html 内のリンク
2. プログラム内の記述 <= 


```
router.push({name:'user', params: {userId: 123}})
```

- 引数は、 :to プロパティのオブジェクトと同じ
- 名前付きルートを使える  
例）https://jsfiddle.net/imamura_shohei/1dcgufsr/59/
_※うまくできず..._

--

### 4.3.4 フック関数

VueRouter ではページ遷移が実行される前後に行う関数  
 （利用例：リダイレクト、ページ遷移前の確認）


--

#### 実装方式パターン

||設定方法|設定先|
|---|---|---|
|①|グローバル(全ての遷移)に対して|Routerインスタンス|
|②|特定のルート単位|Vue Routerのルート定義|
|③|コンポーネント単位|コンポーネント内のオプション|

①https://jsfiddle.net/imamura_shohei/e8j3amos/4/  
②https://jsfiddle.net/imamura_shohei/gLyvzekj/23/  
③

---

## 休憩

---

## 4.4 サンプルアプリケーションの実装

--

### サンプルアプリケーションの概要
[図]

#### 機能
- ユーザ情報登録、閲覧
- ログイン、ログアウト

[完成像]

--

### サンプルアプリケーションの実装手順

1. リストページ
2. リストページの改修
3. 詳細ページ
4. ユーザー登録ページ
5. ログインページとログイン状態

--

### 4.4.1 リストページの実装
- リストページの中身を実装する
- /users と UserList コンポーネントをマッピングする

--

### 4.4.2 API によるデータ通信
- ページ切替時に、ユーザ情報を取得する
    - Json を返す関数（疑似API）を作成する
    - UserList コンポーネントHTML定義を変更する
    - `watch` と `created` を用いてUserList コンポーネントを改修する

[例]

--

### 4.4.3 詳細ページの実装
- 一覧の名前選択時に、詳細ページを表示する
    - ルート定義を追加
    - コンポーネントを実装(HTML, JS)
    - 疑似APIの実装(ユーザデータ、関数)

--

### 4.4.4 ユーザー登録ページの実装
- ユーザ登録ページのルート定義
- コンポーネント追加
    - データのバリデーション
    - 更新系 POST API 発行

--

### 4.4.5 ログイン・ログアウトの実装
- 新規ユーザ登録ページのルート定義に、フック関数を定義する
- 認証用モジュール Auth を作成する
    - ローカルストレージに認証情報を保持する
- ログインページに相当するログインコンポーネントを作成する
    - 認証に失敗した場合にエラーページを表示する
    - v-show でログイン、ログアウトメニューの出し分けを行う

--

### サンプルアプリケーションの全体像

---

## 4.5 Vue Router の高度な機能
1. RouterインスタンスとRouteオブジェクト
2. ネストしたルーティング
3. リダイレクト・エイリアス
4. 履歴の管理

--

### 4.5.1 Router インスタンスと Route オブジェクト


|Router インスタンス|Route オブジェクト|
|---|---|
|Web アプリケーションに一つのみ|ページ遷移によるルーティング発生ごとに生成|
|全般的な Router 機能を管理<br>・履歴管理<br>・プログラムによるページ遷移|現在アクティブなルートの状態を保持<br>・パス、URLの取得|
|$router でアクセス|$route でアクセス|

--

#### Router インスタンスの代表的なプロパティとメソッド

[写真]

--

#### Route オブジェクトの代表的なプロパティ
[写真]

--

### 4.5.2 ネストしたルーティング
- 任意のコンポーネントに対して、入れ子となるコンポーネントのルート定義

例

|内容|url|
|---|---|
|ユーザ詳細|/user/{ユーザID}|
|ポスト情報|/user/{ユーザID}/post|
|プロフィール情報|/user/{ユーザID}/profile|

--

#### ネストしたルーティング(続き)

```js

var Router = new VueRouter({
    routes: [
        {
            path: '/user/:userId',
            name: 'user',
            component: User,
            children: [
                {
                    path: 'profile',
                    component: UserProfile
                },
                {
                    path: 'post',
                    component: UserPosts
                }
            ]
        }
    ]
})

```

--

### 4.5.3 リダイレクト・エイリアス

|リダイレクト|エイリアス|
|---|---|
|遷移先のURLに書き換わる|アクセス時のURLを保持する|
|path , redirect を指定|path, component, alias を指定|

[例]

--

### 4.5.4履歴の管理

|URL Hash|HTML5 History API|
|---|---|
|URLに #/が付く|URLに #/がつかない<br>(サーバサイドと同じ)|
|VueRouterデフォルト|modeオプションとして'history'を指定|
|ブラウザの履歴に追加|履歴スタックを操作|
||URL直アクセス時の処理を追加する必要あり|

--

### [column] Vue Router を使った大規模なアプリケーションの実装


||単純なコンポーネント構成|複雑なコンポーネント構成|
|---|---|---|
|単一ページ(遷移)|-|Vuexのみ|
|複数ページ遷移|Vue Routerのみ|VueRouterとVuex|


- アプリケーションの特性や設計方針から、VueRouter 、Vuex の要否を判断するべき

--

### [column] Vue router と react Router

|Vue Router|React Router|
|---|---|
|Vue.js 公式開発チームが拡張・メンテナンス|FaceBook とは独立してメンテナンス実施|