# Backstage環境構築

- AWS EC2のAmazon Linuxを使用したを用いてたBackstageの環境構築方法を記載

## 事前準備

### EC2インスタンスの作成

1. 下記の項目に従ってEC2インスタンスを作成する

   | 項目                                               | 値                                                                                                                                                                                                                                                       | 備考                                                                                           |
   |--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
   | 名前とタグ                                            | 任意の名前を指定                                                                                                                                                                                                                                                | ー                                                                                            |
   | Application and OS Images (Amazon Machine Image) | Red Hat Enterprise Linux 9 (HVM), SSD Volume Type                                                                                                                                                                                                       | ー                                                                                            |
   | インスタンスタイプ                                        | t2.micro                                                                                                                                                                                                                                                | ー                                                                                            |
   | キーペア (ログイン)                                      | 自分用のキーペアを指定                                                                                                                                                                                                                                             | 今回はED25519でプライベートキーとパブリックキーを生成                                                               |
   | ネットワーク設定                                         | ・ネットワーク: デフォルト ※1<br/>・サブネット: 先順位なし (アベイラビリティーゾーンのデフォルトサブネット)<br/>・パブリック IP の自動割り当て: 有効化<br/>・ファイアウォール (セキュリティグループ): セキュリティグループを作成 ※2<br/>・Allow SSH traffic from: true ※3<br/>・インターネットからの HTTPS トラフィックを許可: false<br/>・インターネットからの HTTP トラフィックを許可: false | ※1: デフォルトで自動割り当てされる<br/>※2: 自分のIPからSSH接続が可能になるように設定<br/>※3: traficはデフォルトでOK(デフォルトは0.0.0.0/0) |
   | ストレージを設定                                         | デフォルト                                                                                                                                                                                                                                                   | ー                                                                                            |

2. 作成したインスタンスが下記内容の通りに立ち上げられていることを確認する
   - セキュリティのインバウンドグループが設定されていること
   - パブリックIPが自動割り当てされないこと 

### Elastic IPの設定

1. EC2ホーム画面のサイドバーにある`ネットワーク&セキュリティ`から`Elastic IP`をクリック
2. `Elastic IPアドレスを割り当てる`をクリックしてElastic IPの作成画面に移る
3. 下記を項目を設定の後`割り当て`を実行

   | 項目               | 値                                     | 備考                             |
   |------------------|---------------------------------------|--------------------------------|
   | ネットワークボーダーグループ   | デフォルト                                 | ー                              |
   | パブリックIPv4アドレスプール | デフォルト                                 | ー                              |
   | タグ               | key = `please`, value = `do_not_stop` | 該当tagを使用すると、インスタンスが自動削除の対象外となる |

4. アクションを選択して、Elastic IPの関連付けをクリック
5. 下記設定して割り当てして関連付けるをクリック

   | 項目       | 値                              | 備考 |
   |----------|--------------------------------|----|
   | リソースタイプ  | インスタンス                         | ー  |
   | インスタンス   | 作成したインスタンス名を検索して選択             | ー  |
   | プライベートIP | おすすめされたものを選択(自分で作成したEC2インスタンス) | ー  |

6. 作成したEC2インスタンスのパブリックIPv4アドレスとElastic IPアドレスが一致していることを確認する

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
