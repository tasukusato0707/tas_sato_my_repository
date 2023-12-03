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
-|-
-|-
-|-

### 参考
[Linuxディレクトリ構造](https://qiita.com/nys9302/items/a8ddeedd3cd9d0deb332)

## Linux commands

command|meaning|example|Other
-|-|-|-  
date | 日時を表示する| aa|-
cd | ディレクトリを移動する| ``cd /home/tasukusato``|カレントディレクトリ``.``<br>親ディレクトリ``..``<br>ホームディレクトリ``~``<br>ルートディレクトリ``/``
pwd|カレントディレクトリを表示する|``pwd /home/tasukusato``|-
ls|ファイルやディレクトリの一覧を表示する|``ls /``<br>``ls *.html``|パス名展開<br>``*``=任意の文字列<br>``?``=任意の1文字
-|-|-|-
-|-|-|-
-|-|-|-
-|-|-|-