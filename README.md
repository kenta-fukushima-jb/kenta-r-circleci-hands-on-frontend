
# CircleCIハンズオン初級編
## 準備
### 1. GitHubアカウントの準備
こちらのURLからアカウントを登録ください

https://docs.github.com/ja/github/getting-started-with-github/signing-up-for-a-new-github-account

### 2. Nodejsのインストール
こちらのサイトからインストールを実施ください

https://nodejs.org/ja/download/

### 3. Gitのインストール
こちらのサイトからインストールを実施ください

https://git-scm.com/downloads

### 4. S３の作成
S３作成は[こちら](準備.md)を実施ください

## ハンズオン①
今日のハンズオン手順を自分のリポジトリにコピーする

https://github.com/kenta-r/kenta-r-circleci-hands-on-frontend

以下のコマンドでフロントエンドアプリを動かす
```
git clone git@github.com:kenta-r/kenta-r-circleci-hands-on-frontend.git
cd kenta-r-circleci-hands-on-frontend
npm install
npm start
```
こちらの画面が表示されればOK!
![画像](images/001.png)

GitHubにリポジトリを作成（Privateでも可)し、先ほどCloneしたソースをプッシュしてください。
.gitフォルダを削除すると便利です。

## ハンズオン②
CircleCIの設定
### GitHubとの連携
CircleCIのアプリ画面に移動し、プロジェクトを連携してください。

https://circleci.com

![画像](images/002.png)
今回はすでにあるCircleCIの設定ファイルを使用します。
![画像](images/003.png)
今回はそのままビルドを開始してください。
![画像](images/004.png)

### 環境変数の設定
ビルドがスタートしますが、設定がまだ終わっていないのでキャンセルし、環境変数の設定に進みます。
![画像](images/005.png)
環境変数を選択したら、AWSへのアクセスキーやデプロイ先のS3名を記載してください。

| 設定値 | 値 |
| -------- | -------- | 
| AWS_ACCESS_KEY_ID | 各自入手 | 
| AWS_SECRET_ACCESS_KEY | 各自入手 |
| DEPLOY_S3_BUCKET | 各自入手 |


下の操作で進めてください。
![画像](images/006.png)

## ハンズオン③
### CircleCIを動かしてS3にアプリケーションをデプロイする
下の画像のように正常に動いていればOKです。
![画像](images/007.png)

## ハンズオン④　時間が余ったら
### 通知機能の実装

### 通知するためのWebhookを作成する
以下のURLから通知先を選択する

https://slack.com/services/new/incoming-webhook

取得したWebhookURLを環境変数に設定する

| 設定値 | 値 |
| -------- | -------- | 
| SLACK_WEBHOOK | 先ほど入手したURL | 

### CircleCIに設定する
以下の項目を変更し、再度実行する。(インデントも入っているので注意)

orbsの情報を追記
```
version: 2.0
→
version: 2.1
orbs:
  slack: circleci/slack@4.3.0
```

通知したい場所に追記①
```
      - buildApp:
→
      - buildApp:
          post-steps:
            - slack/status:
                fail_only: true
```

通知したい場所に追記②
```
      - devDeployApp:
          requires:
            - devholdApproval
          filters:
            branches:
              only:
                - main
→
      - devDeployApp:
          requires:
            - devholdApproval
          filters:
            branches:
              only:
                - main
          post-steps:
            - slack/status:
```

以下のように通知がされていればOK！！
![画像](images/008.png)

以上です。
お疲れ様でした。
