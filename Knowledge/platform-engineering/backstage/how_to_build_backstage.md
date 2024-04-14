# chocott-backstage用いたBackstage環境構築

- [chocott-backstage](https://github.com/ap-communications/chocott-backstage?tab=readme-ov-file)を用いてBackstageの環境構築方法を記載

## 事前準備

### EC2インスタンスの作成

1. 下記の項目に従ってEC2インスタンスを作成する
   1. 名前とタグ
   2. インスタンスタイプ: t2-small
   3. キーペア: よしなに
   4. ネットワークの設定
      - VPC:デフォルト
      - サブネット：デフォルト
      - パブリックIPの自動割り当て
        - 無効化
      - セキュリティグループ: true
        - 名前と説明はデフォルト
        - セキュリティグループルール
          - 下記を追加
            1. 自分のIP
            2. カスタムTCP
               - 自分のIP 
               - port範囲:3000
            3. カスタムTCP
               - 自分のIP 
               - port範囲:7007
   5. ストレージ: デフォルト

2. 立ち上げたインスタンスの確認
   - セキュリティのインバウンドグループが設定されていること
   - パブリックIPが自動割り当てされないこと 

### Elastic IPの設定

1. Elastic IP開く
2. Elastic IPアドレスを割り当てるをクリック
3. 下記を設定する
   - ネットワークボーダーグループ: デフォ
   - パブリックIPv4アドレスプール：デフォ
   - タグ: 下記を設定
     - キー: pease
     - オプション: do_not_stop
4. 割り当てを実行
5. アクションを選択して、Elastic IPの関連付けをクリック
6. 下記設定して割り当てして関連付けるをクリック
   - リソースタイプ: インスタンス
   - インスタンス：作成したインスタンス名を検索して選択
   - プライベートIP：おすすめされたやつを選択(作成したインスタンスのやつ)
7. 作成したEC2インスタンスのパブリックIPv4アドレスとElastic IPアドレスが一致していることを確認する

### 作成したインスタンスに接続

1. vscodeのconfigにインスタンスを登録
2. SSH接続を実施する
3. yum updateを実施
   ```terminal
   $ sudo yum update
   ```

4. gitのインストールを実施する
   ```terminal
   $ sudo yum install -y git
   ```

5. git cloneを実行する
   ```terminal
   $ git clone https://github.com/ap-communications/chocott-backstage.git --depth 1
   ```

6. dockerのインストールを実施する

   ```terminal
   $ sudo systemctl start docker
   $ sudo systemctl enable docker
   $ sudo docker ps
   $ sudo docker images
   $ docker --version
   $ sudo curl -L https://github.com/docker/compose/releases/download/v2.26.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/dock
   ```

7. docker-composeをインストールする

   ```terminal
   $ sudo curl -L https://github.com/docker/compose/releases/download/v2.26.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/dock
   ```

### 本番

- 基本は[ドキュメント](TBD)を参照。

### Github App登録

-  [Authenticationのドキュメント](https://github.com/ap-communications/chocott-backstage/blob/main/chocott-contents/docs/quick-start/index.md)を参考にAppを登録し、Client IdとClient Secret、Secretファイルを作成してください。

  > 今回は個人アカウントで作成  
  > localhostで作成しているところを作成したEC2ンスタンスのURLで作成する  
  > ページ開かんけど無視

  - webhook URLを登録する
    - slack開く
    - appの管理する -> アプリを検索する
    - Incoming Webhookを検索してインストールする
    - slackの追加を実行
    - チャンネルの指定
    - 赤文字で
    - URLができあがるのでURLをコピー
    - webhook secretをよしなに登録

### Backend向けGitHubのインテグレーションの登録

- 続けて、Backend向けのGitHub Integrationを実現するため、GitHub App Private Keyを作成します。 [Integratonのドキュメント](https://github.com/ap-communications/chocott-backstage/blob/main/chocott-contents/docs/integration/index.md) を参考に、Credential fileを作成します。 作成したファイルパスを GITHUB_CREDENTIAL_FILE という環境変数に設定します。

### インスタンスにGithub Appを登録

   ```terminal
   $ export AUTH_GITHUB_CLIENT_ID="<Client IDの文字列>"
   $ export AUTH_GITHUB_CLIENT_SECRET="<Secretの文字列>"
   $ export GITHUB_CREDENTIAL_FILE="/<put your folder name>/github-credentials.yaml"
   ```

- パーソナルアカウントにGitHub Appを登録した場合には~の部分を実行する
  - `./app-config.local.yaml`を作成しないとない

### docker-composeを実行してBackstageを立ち上げる

- 下記コマンドを実行してコンテナ立ち上げる

   ```terminal
   $ sudo -E docker-compose up -d"
   ```




1.EC2インスタンスの作成
- Elastic IPの割り当て
https://qiita.com/Jerid/items/d5dd3a29ed9a0e374493
- セキュリティグループの設定
  - インバウンドルールに3000の追加
- EC2アクセス
- gitインストール
- dockerインストール
- docker-composeインストール
https://qiita.com/hiyuzawa/items/81490020568417d85e86
https://qiita.com/hiyuzawa/items/81490020568417d85e86
- sudo service docker start
https://qiita.com/DQNEO/items/da5df074c48b012152ee


- sudoつけてやる
2.github apps登録
3.integration登録
4.個人アカウントで作成

- アクセス
