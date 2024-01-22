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

### 作成したインスタンスに必要なツールのインストールを行う

1. Pip Installs Packagesのインストール
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

2. podmanのインストール

   - 下記コマンドを実行し、podmanをインストールする

      ```console
      $ sudo dnf install podman -y
      ```

   - podmanがインストールされたことを確認する

      ```console
      $ podman --version
      ```

3. ansible-navigatorのインストール

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

### 実行環境の設定ファイル作成

- 作業用ユーザー（/home/ec2-user）のディレクトリ配下に、以下のようなファイルを作成する

  ```text
  .
  ├── ansible.cfg
  ├── ansible-navigator.log
  ├── context
  │   ├── _build
  │   │   ├── ansible.cfg
  │   │   ├── bindep.txt
  │   │   ├── config
  │   │   │   └── ansible.cfg
  │   │   ├── requirements.txt
  │   │   ├── requirements.yml
  │   │   └── scripts
  │   │       ├── assemble
  │   │       ├── check_ansible
  │   │       ├── check_galaxy
  │   │       ├── entrypoint
  │   │       ├── install-from-bindep
  │   │       └── introspect.py
  │   └── Containerfile
  └── execution-environment.yml
  ```

- 作業用ディレクトリを作成する

1. `ansible_verification`に作成する
       ```console
       $ mkdir ~/ansible_verification
       ```
2. `execution-environment.yml`を作成する

   ```console
   $ cd ansible_verification/
   $ vi execution-environment.yml
   ```

   - `execution-environment.yml`の中身

   ```yml
   ---
   version: 3

   dependencies:
     ansible_core:
       package_pip: "ansible-core==2.15.8"
     ansible_runner:
       package_pip: "ansible-runner==2.3.4"
     galaxy:
       collections:
         - name: "community.general"
           version: "8.2.0"
     python:
       - "ansible-lint==6.22.2"
       - "jmespath==1.0.1"
     system:
       - "git"

   images:
     base_image:
       name: "quay.io/centos/centos:stream9"

   additional_build_files:
     - src: ansible.cfg
       dest: config
   ```

3. `ansible.cfg`を作成する

   ```console
   $ vi ansible.cfg
   ```

   - `ansible.cfg`の中身

   ```text
   [defaults]
   [galaxy]
   host_key_checking = False
   ```

4. 下記コマンドを実行し、実行環境イメージ(execution-environment)を作成する

    ```console
    $ ansible-builder build -f execution-environment.yml -t ansible_verification:v0.0.1 -v 3
    ```

5. 実行環境イメージが作成されたことを確認する(下記のどちらでも可能)

   - `podman images`コマンドで確認する方法
     ```console
     $ podman images
     REPOSITORY                      TAG                 IMAGE ID      CREATED        SIZE
     localhost/ansible_verification  v0.0.1              0ee875da97ae  5 minutes ago  378 MB
     ```

   - `ansible-navigator images`コマンドで確認する方法
     - ansible-navigatorの基本コマンドは[Ansible Navigator Documentation ansible-navigator subcommands](https://ansible.readthedocs.io/projects/navigator/subcommands/#available-subcommands)を参照

     ```console
     $ ansible-navigator images
       Image               Tag                Execution environment     Created        Size
     0│ansible-runner      stable-2.12-latest True                      21 months ago  816 MB
     1│ansible_verification v0.0.1            True                      6 minutes ago  378 MB
     2│centos              stream9            False                     5 days ago     161 MB
     3│creator-ee          v0.21.0            True                      3 months ago   683 MB
     ```

### ansible-navigatorを使ったPlaybookの実行

- 下記のようにinventory/hosts.ymlと、playbooks/hello_world.ymlを作成する
  - サイドバーからも作成可能

  ```text
  .
  └── ansible_verification
      ├── ansible.cfg
      ├── ansible-navigator.log
      ├── context
      │   ├── _build
      │   │   ├── bindep.txt
      │   │   ├── config
      │   │   │   └── ansible.cfg
      │   │   ├── requirements.txt
      │   │   ├── requirements.yml
      │   │   └── scripts
      │   │       ├── assemble
      │   │       ├── check_ansible
      │   │       ├── check_galaxy
      │   │       ├── entrypoint
      │   │       ├── install-from-bindep
      │   │       └── introspect.py
      │   └── Containerfile
      ├── execution-environment.yml
      ├── inventory
      │   └── hosts.yml
      └── playbooks
          └── hello_world.yml
  ```

1. 下記内容で`hosts.yml`を作成する

   ```yml
   ---
   all:
     hosts:
       localhost:
   ```

2. 下記内容で`hello_world.yml`を作成する

   ```yml
   ---
   - name: "Sample Play"
     hosts: localhost
     gather_facts: false
     tasks:
       - name: "Sample Play"
         ansible.builtin.debug:
           msg: "Hello World!"
   ```

3. 下記コマンドを実行し、playbookを実行する

   ```console
   $ cd ansible_verification/
   $ ansible-navigator run playbooks/hello_world.yml -i hosts.yml
   ```
