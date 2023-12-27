# About Linux

## Linux directory structure

- ディレクトリはファイルが保管されている場所。Windowsのフォルダと同じ概念 

directory|meaning
-|-
/|ルートディレクトリ。<br>ルートディレクトリを頂点とした構造を**ディレクトリツリー**と呼ぶ。
/bin|Binary Folderの略。<br>一般ユーザーおよび管理ユーザー両方が使用する。<br>基本的なコマンドが保存されたディレクトリ。
/dev|deviceの略。<br>デバイスファイル(ハードウェアを扱えるようにしたファイル)を格納
/etc|Linuxの設定ファイルを格納
/home|ホームディレクトリ(ユーザー毎の個人ディレクトリ)が配置される<br>ホームディレクトリ内にファイルやディレクトリを作成できる。
/sbin|super user binの略。<br>管理者ユーザー(rootユーザー)用のコマンド実行ファイル
/tmp|temporaryの略。<br>アプリケーション使用中の結果を一時的に保存する
/usr|userの略。<br>ユーザーアプリケーションがインストールされるディレクトリ。<br>実行ファイルは bin、依存ライブラリは lib などにインストールされる
/var|variableの略。<br>変化するデータを格納<br>アプリケーションのログファイルなどを格納<br>ファイルが増えて容量が逼迫しやすい。
/lib|libraryの略。<br>bin/と/sbin/にあるバイナリに必要なライブラリ。<br> カーネルモジュールファイルとプログラム(C、C++など)に必要な各種ライブラリファイルが存在するディレクトリ。
-|-
-|-

### Reference
[Linuxディレクトリ構造](https://qiita.com/nys9302/items/a8ddeedd3cd9d0deb332)

## What are Absolute path and Relative path?

### Absolute path

- 絶対パス。
- ルートディレクトリ(``/``)から始まるパス

    ```console
    /home/tasukusato/code/README.md
    ```

### Relative path

- 相対パス。
- カレントディレクトリから始まるパス。
    ```console
    /code/README.md
    ```

- ``.``はカレントディレクトリを表す。
- ``..``は親ディレクトリを表す。


## What is text file?

- 文字コードで定められた文字だけが並んで格納されたファイル
  - 文字コードは文字をコンピュータで処理・通信するために文字の種類に数値を割り振ったもの
  - ex) Aは100001、Bは100010

## what is binary file?

- 文字コード以外の数値も含むようなファイル
  - テキストファイル以外のファイルがバイナリファイルと捉えてOK

## What is link?

- リンクを張る：windowsのショートカットのようなもの

### what is hard link?

- 1つのファイルの実体に複数の名前をつける機能
- 元のファイルを削除しても消えない
- 全てのハードリンクがなくなった時に削除される

#### Reference

[「分かりそう」で「分からない」でも「分かった」気になれるIT用語辞典 -> ハードリンク](https://wa3.i-3-i.info/word1152.html)

### What is symbolic link?

- リンク先のパス名が書かれた特殊ファイル。<br> リンク先がファイルの実体
- シンボリックリンクを残したままファイルの実体を削除したり、ファイルを移動すると、ファイルを参照できなくなる

#### Reference

[「分かりそう」で「分からない」でも「分かった」気になれるIT用語辞典 -> ハードリンク](https://wa3.i-3-i.info/word1151.html)

## What is standard input(stdin)/output(stdout)?

- プログラムが、特に何も指定されていない場合に利用するデータの標準的な入出力元

- 標準入力 (stdin): コンピュータにデータを受け渡すこと<br>キーボードやファイルが使用される

- 標準出力(stdout): コマンドやプログラムの結果を外に出すこと<br>ディスプレイなどが使用される

- 標準エラー出力(stderr): プログラムのエラーメッセージを出力する<br>ディスプレイなどが使用される

## What is [>] (redirect)?

- 入出力先を変更する機能

### input redirect

- キーボードの代わりにファイルから入力する機能

  ```console
  # /etc/hostsファイルを入力元にしてcatコマンドを実行

  $ cat < /etc/hosts
  ```

### output redirect

- コマンドの実行結果を画面に表示するのではなくファイルに保存する機能

  ```console
  # lsコマンドの出力先をls.txtにして保存

  $ ls > output.txt
  $ cat > output.txt
  ```

### error redirect

入出力 | 数値
-|-
標準入力 | 0
標準出力 | 1
標準エラー出力 | 2

- エラー結果を画面に表示するのではなくファイルに保存する機能

  ```console
  $ ls /hoge 2> error.txt
  $ cat error.txt
  ```

### other

- まとめる場合は出力をリダイレクトした後に`2>&1`と書く
- `>`でリダイレクト指定するとファイルを上書きする
- `>>`でリダイレクト指定するとファイルに追記する

## What is /dev/null ?

- 入力先として指定しても何も返さず、出力先として指定してもデータは消え何も表示されない、スペシャルファイル

### input destination

- 入力が何もない状態になる
- コマンドのテストなどで入力を空にしたいときに使用する

  ```console
  $ cat < /dev/null
  $       # 何も表示されない
  ```

### output destination

  ```console
  $ ls / > /dev/null
  $       # 何も表示されない
  ```

## What is [ | ] (Pipeline) ?

- 複数のコマンドを連携させる機能
- コマンドの標準出力を別のコマンドの標準入力につなげる

  ```console
  $ ls /bin | less

  $ ls /bin | grep systemd | less
  ```

## What is permission?

- ファイルやディレクトリを「誰がどのように操作できるか」定めたのがパーミッション
- `ls -l`コマンドでファイルやディレクトリのパーミッションが確認できる

### file types

記号 | ファイルタイプ
-|-
-|レギュラーファイル
d|ディレクトリ
l|シンボリックリンク
c|キャラクターデバイスファイル
b|ブロックデバイスファイル
s|ソーシャルソケットファイル
p|named pipe(FIFO)

### permission symbol

記号 | 内容
-|-
r | read(読み取り)
w | write(書き込み)
X | execute(実行)

1. example

  - `rwxr-xr-x`の場合

    対象 | 読み取り | 書き込み | 実行
    -|-|-|-
    オーナー | 〇 | 〇 | 〇
    グループユーザー | 〇 | X | 〇
    その他ユーザー | 〇 | X | 〇

### superuser

- 管理者権限を持つスーパーなユーザーのこと＝rootユーザー
  - システムの変更作業などが誰でもできる状態は危険。よって、スーパーユーザーのみが実行権限を持つように設定する

  command|meaning|sample|example|Other
  -|-|-|-|-
  sudo|コマンドの前にsudoをつけるとスーパーユーザー(root)としてコマンドを実行|sudo <コマンド>|sudo mkdir /storage|sudoは"switch user and do"の略

## What is process and job?

### process

- 実行中のプログラムのこと
- コマンドを実行すると、実行ファイルをメモリに格納して、メモリ上でCPUがプログラムを実行する。1つ実行すると新しいプロセスが1つ作成される 

