# Blockchain Hands-On

## 本資料について

2018年8月28・29日開催のイベント、[**Research Experience Day**](https://ibm-research-experience.connpass.com/) のDay2 午後のセッション [体験しよう！ブロックチェーン -その仕組みから実践まで-](https://ibm-research-experience.connpass.com/event/95083) のハンズオン資料である。

## ハンズオンの進め方

説明する内容はすべてここに書いてあるので、先に進みたい方は本資料をもとに進めてもらって構わない。また、機材のトラブルなどで遅れても、この資料をもとに追体験できるのでご心配なきよう。

## 予定所要時間

1時間 (当該セッションの後半)

## 講師

いずれも所属は、日本アイ・ビー・エム株式会社 東京基礎研究所:
- 齋藤 新 
- 岩間 太
- 榎 美紀

# 1. 講習内容

## 1.1 概要

パーミッション型のブロックチェーン実装である、Hyperledger Fabricにおけるスマートコントラクト開発の概要について学ぶ。

- 開発フレームワークとしてはHyperledger Composer (以下、Composer) を使用する
- 開発プラットフォームとしてWeb上で動作するComposer Playground (以下、Playground) を使用する

## 1.2 Composerを使用するメリット

- 低レベルのコード (KVSへの読み書きやデータのmarshallingなど) を書かなくてよい
- アクセス制御の宣言的な設定が可能
- Webシミュレータやモックオブジェクトなどを用いて、実際にスマートコントラクトをデプロイすることなくテストができる
- フロントエンド開発支援が充実。サーバ環境やAngularJSコンポーネントの自動生成ができる

## 1.3 Playgroundを使用するメリット

- エディタを含む開発環境のインストールが不要 (もちろんインストールしたほうが便利である)
- 簡単な対話的テスト (トランザクションを作成・発行してKVSの更新を閲覧すること) が可能。なお、本格的なテストはローカル環境で

# 2. ComposerとPlaygroundによる開発フロー

詳細については当該セクションを参照のこと。各セクションには練習問題があるので挑戦してみてほしい。後半の2つについてはインストールなどの必要な作業があるので、余裕があったら行うか、後日この資料をもとに取り組んでみてほしい。

- 2.1 Playgroundでサンプルをもとに新規Business Netoworkを作成してみる
- 2.2 Business Netoworkとダッシュボードの構成要素
- 2.3 試しにトランザクションを実行してみる (対話的テスト)
- 2.4 アプリケーションの中身を理解してみる
- 2.5 コードを書き換えてみる
- 対話的テストを実行してみる
- (ローカル環境) スクリプトによる単体テストの実行
- (IBM Cloud使用) BNAのネットワークへのデプロイ

## 2.1 Playgroundでサンプルをもとに新規Business Netoworkを作成してみる

必要なもの: モダンブラウザ

1. https://composer-playground.mybluemix.net をブラウザで開く (HTTPS版のページを開くことを推奨)
2. "Let's Blockchain!" を押す
    1. もし `Error: Object with ID 'undefined' in collection with ID 'chaincodes' does not exist` というエラーなどが表示される場合は https://composer-playground.mybluemix.net/login をリロードしてみる
1. 1つか複数の白い四角が表示される。それぞれがアプリケーション (Composerでいう "Business Network") である。
1. いちばん左のアプリケーション (Hello! Composerと書いてある) の "Get Started" を押す
1. "Basic Sample Business Network" のダッシュボードが表示される

## 2.2 ダッシュボードとBusiness Networkの構成要素

### 2.2.1 ダッシュボード

上部にはタブが2つ

- Define: Business Networkの構成要素であるParticipant、Asset、Transaction、EventおよびAccess Control Listを定義する (2.3)
- Test: Defineタブで定義したBusiness Networkを対話的にテストする (2.4)

### 2.2.2 Business Network

Defineタブで定義する。基本的にはNode.jsのプロジェクト構成と類似。左のナビゲーションで編集するファイルを切り替える:

- 初期状態では `About` でBusiness Networkの概要 (README.mdファイル) が表示されている。右上の "<>" をクリックすると `README.md` のソースファイルが表示される。歯車マークをクリックすると `package.json` のソースが表示される。
- `Model File` ではParticipant、Assetなどのデータ型を定義する。Transactionの引数の型もここで定義する。正しく書けていれば、エディタ下部に "Everything looks good!" を表示されている。エラーがあれば "Error found!" が表示される。
- `Script File` ではTransactionの処理内容 (= ビジネスロジック) をNode.jsの関数として定義する。エラー表示については上と同様。
- `Access Control` ではアクセス権について (ほぼ) 宣言的に定義する。「どの **Participant** がどのような **Resource** (Assetなど) にどのような **Operation** (READ、CREATEなど) ができるか」を記述していく。

ソースを編集したら左下の "Deploy Changes" をクリックするとアプリケーションが更新され、前述のTestタブで試すことができる。

また、作成したBusiness Networkは "Export" ボタンからBNAファイルとしてセーブすることができ、Fabricのネットワークにデプロイ可能である。

> 注意: Playgroundで作成したアプリケーションはセッションが切れたりすると消えてしまうので、保存したい場合はこまめにセーブしておくこと。

## 2.3 試しにトランザクション (以下、TX) を実行してみる (対話的テスト)

必要なもの: モダンブラウザ

アプリケーションの中身の説明は後回しにするとして、まずはTestタブでのトランザクションの実行を試してみる。**これができればとりあえずは「コードを編集」→「対話的テスト」のサイクルを回せるようになる。**

1. Testタブを開く。左のナビゲーションをクリックすることにより、Participant、Asset、TXのレジストリの内容を表示できる。ただし前者2つは空である。TXだけは例外で、今回編集するサンプルアプリはすでにデプロイされているので、そのTransaction履歴が表示される。
1. Participantを作成する。`SampleParticipant` をクリックし、右上の `+ Create New Participant` をクリックする。
1. 新しい `SampleParticipant` インスタンスの値を決める。IDは自動で振られるが、決まった値を使ったほうが楽なのでここでは `"0001"` に設定。残りのフィールドは適当に設定。下部の `Generate Random Data` で自動生成もできる。右下の `Create New` をクリック。
1. レジストリにインスタンスが追加されたことがわかるはず。
1. 次にAssetを作成する。`SampleAsset` をクリックし同様に `+ New ...` する。
1. 新しい `SampleAsset` の値を決める。ここでもIDは決め打ちで `"0001"` とするのが楽。エンティティの種類が違うのでID被りは問題にならない。`owner` 値は `resource:<クラス名>#<ID値>` の形式を取る。さきほど作成したParticipantを参照したいので、`"resource:org.example.basic.SampleParticipant#0001"` を指定する。`value` はなんでもよいがここでは `100` としておく。できたら `Create New` をクリック。
1. レジストリにインスタンスが追加されたことがわかるはず。
1. ちなみにここまで行ったのちに `All Transactions` をクリックすると、さきほどエンティティを追加したときのTXが先頭に新たに表示されているはず。
1. いよいよTXの実行。どのページにいてもいいので、左下の `Submit Transaction` をクリック。
1. TXの引数を決める。`asset` は先ほど追加したAssetを参照したいので `"resource:org.example.basic.SampleAsset#0001"` とする。`newValue` は何でもよいのでここでは `"1"` としておく。入力したら `Submit` をクリック。
1. 正常に完了すれば画面右上にnotificationが出るはず。
1. TX履歴に新たに `SampleTransaction` が追加されているはず。
1. Assetも更新されているはず。左側の `Sample Asset` をクリック。IDが `"0001"` のAssetがあり、その `value` 値は初期値の `"100"` から `"1"` に変更されていることがわかるはず。

### 演習

- Asset作成時に、`owner` にエンティティとして存在しないIDを指定したらどうなるか
- Asset作成時に、`owner` にすでに存在するエンティティのIDを指定したらどうなるか
- TX実行時に、`asset` に存在しないAssetを指定したらどうなるか
