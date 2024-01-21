# AWS EC2でAnsible-Navigatorのコントロールノードを作成したい

## how to 

### AWS上にEC2インスタンスを作成する

1. AWSにログインしEC2を選択

    - APCの検証環境では東京リージョンで作成

2. `インスタンスを起動`を選択し、下記の条件でEC2インスタンスを作成する

    | 項目                                               | 値                                                                                                                                                                                                                                                       | 備考                                                                                           |
    |--------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|
    | 名前とタグ                                            | 任意の名前を指定                                                                                                                                                                                                                                                | -                                                                                            |
    | Application and OS Images (Amazon Machine Image) | Red Hat Enterprise Linux 9 (HVM), SSD Volume Type                                                                                                                                                                                                       | -                                                                                            |
    | インスタンスタイプ                                        | t2.micro                                                                                                                                                                                                                                                | -                                                                                            |
    | キーペア (ログイン)                                      | 自分用のキーペアを指定                                                                                                                                                                                                                                             | 今回はED25519でプライベートキーとパブリックキーを生成                                                               |
    | ネットワーク設定                                         | ・ネットワーク: デフォルト ※1<br/>・サブネット: 先順位なし (アベイラビリティーゾーンのデフォルトサブネット)<br/>・パブリック IP の自動割り当て: 有効化<br/>・ファイアウォール (セキュリティグループ): セキュリティグループを作成 ※2<br/>・Allow SSH traffic from: true ※3<br/>・インターネットからの HTTPS トラフィックを許可: false<br/>・インターネットからの HTTP トラフィックを許可: false | ※1: デフォルトで自動割り当てされる<br/>※2: 自分のIPからSSH接続が可能になるように設定<br/>※3: traficはデフォルトでOK(デフォルトは0.0.0.0/0) |
    | ストレージを設定                                         | デフォルト                                                                                                                                                                                                                                                   | -                                                                                            |

3. vscode上から作成したインスタンスにログインする

    - `アクティビティバー`の`Remote Explorer`を選択
    - `サイドバー`にある歯車マークの`Open SSH Config File`を選択
    - vscodeのconfigファイルを開き、下記の例のようにログイン情報を設定する
        ```text
        Host {{作成したインスタンス名}}
        HostName {{インスタンスのパブリックIPv4アドレス}} # パブリックIPはインスタンスの起動時に都度割り当て
        User ec2-user
        IdentityFile {{プライベートキーの保管先}}
        ```

    - `サイドバー`にある`Reflash`を押下し、SSHの接続情報に作成したインスタンスが表示されていることを確認する
    - `サイドバー`にある作成したインスタンス名へ接続する

4. 必要なツールのインストール

    - Pip Installs Packagesのインストール
        - pythonがインストールされていることを確認する

            ```console
            $ python --version
            Python 3.9.18
            ```

        - 下記コマンドを実行し、Pip Installs Packagesのインストールを実施

            ```console
            $ sudo dnf install python3-pip
            ```

    > RHEL9系ではpythonが標準搭載されているため、pythonのインストールは不要

    - podmanのインストール
        - 下記コマンドを実行し、podmanをインストールする

            ```console
            $ sudo dnf install podman -y
            ```

        - podmanがインストールされたことを確認する

            ```console
            $ podman --version
            ```

    - ansible-navigatorのインストール
        - pipを最新化する

            ```console
            $ pip install --upgrade pip
            ```

        - 下記コマンドを実行し、ansible-navigatorをインストールする

            ```console
            $ pip install ansible-navigator
            ```

        - ansible-navigatorがインストールされたことを確認する

            ```console
            $ ansible-navigator --version
            ansible-navigator 3.6.0
            ```

5.  実行環境の設定ファイル作成

    - 作業用ユーザー（/home/ec2-user）のディレクトリ配下に、以下のようなファイルを作成する
      ```text
      ```

      - 作業用ディレクトリを作成する

          ```console
          $ mkdir ~/ansible_verification
          ```
      - `execution-environment.yml`を作成する

          ```console
          $ cd ansible_verification/
          $ vi execution-environment.yml
          ```

          - `execution-environment.yml`の中身

          ```yml
          ---
          version: 1

          build_arg_defaults:
            EE_BASE_IMAGE: 'quay.io/ansible/ansible-runner:stable-2.12-latest'

          ansible_config: 'ansible.cfg'

          dependencies:
            galaxy: requirements.yml
            python: requirements.txt

          additional_build_steps:
            prepend: |
              RUN whoami
              RUN cat /etc/os-release
            append:
              - RUN echo This is a post-install command!
              - RUN ls -la /etc
          ```

      - `ansible.cfg`を作成する

          ```console
          $ vi ansible.cfg
          ```

          - `ansible.cfg`の中身

          ```text
          [defaults]
          host_key_checking = False
          ```

      - `requirements.yml`を作成する

          ```console
          $ requirements.yml
          ```

          - `requirements.yml`の中身

          ```yml
          ---
          collections:
            - community.general
          ```

      - `requirements.txt`を作成する

          ```console
          $ $ vi requirements.txt
          ```

          - `requirements.text`の中身

          ```text
          ansible-lint==6.22.2
          jmespath
          ```
