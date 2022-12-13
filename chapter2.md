# Chapter 2 Railsアプリケーションをのぞいてみよう

本章には 2 つのねらいがあります。

1. Chapter 2 - 1 から Chapter 2 - 5 では、Ruby や Rails が動作する環境（本書の前提となる環境）をローカルPC上に構築する方法を紹介します。本書ではサンプルコードを使って解説していくので、実際に動作環境を用意した上で、コードを書き、動かしながら読み進めていただければと思います。
2. Chapter 2 - 6 では、簡単なサンプルアプリケーションを作成し、中身の構成がどうなっているかを解説していきます。ここでは、個別のコード詳細ではなく、大まかにアプリケーション内でどんなパーツがどんな役割を担っているのかに着目します。大まかな構成を知ることで続く章の内容を理解しやすくするとともに、もしも読者の方が業務の都合などで既存のRailsアプリケーションを理解する必要性に迫られているといったケースでは、そのための手がかりを手っ取り早く提供することを意図しています。


<details><summary>Chapter 2 - 1 コマンド実行環境を準備しよう</summary>
Railsアプリケーションを作成するためには、コマンドを実行する環境が必要になります。お使いの環境が macOS の場合は、最初からインストールされている「ターミナル.app」を使います。</details>


<details><summary>Chapter 2 - 2 rbenv （アールビー・エンブ/アールベンブ）をインストールしよう</summary>

Ruby をインストールするには「[rbenv](https://github.com/rbenv/rbenv)」を使う方法が一般的です。rbenv を使うことで Ruby の管理が簡単になるほか、複数のバージョンの Ruby を自在に切り替えることができるようになります。アプリケーションのバージンアップを安全に行ったり、複数のアプリケーションで異なるバージョンの Ruby を利用したりする場面で便利です。
rbenv のインストール方法は、macOS と Windows（WSL）で異なります。

### 2 - 2 - 1 macOS で rbenv をインストール

- macOS での rbenv のインストールは、パッケージマネージャーである「Homebrew」を使うと簡単に行えます。Homebrew を使うためには、「Command Line Tools for Xcode」が必要になるため、ターミナルで以下のコマンドを入力してインストールしましょう。

```ruby
$ xcode-select --install
```

- 次のコマンドでバージョンが表示されたらインストール成功です。

```ruby
$ xcodebuild -version
# 「command line tools are already installed」となっていれば、インストールされています。
xcode-select: error: command line tools are already installed, use "Software Update" to install updates
```

- Homebrew をインストールするために、[公式サイト](https://brew.sh/index_ja)からインストールスクリプトをコピーし、ターミナルで実行します。

```ruby
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- インストールが完了した後、下記のコマンドを実行して、正常にインストールできたか確認します。

```ruby
$ brew doctor
```

</details>


<details><summary>Chapter 2 - 3 Ruby のインストール</summary>

macOS ではターミナルを、Windows では Ubuntu を起動して進めてください。

- 2018 年 9 月時点での最新のバージョン 2.5.1 を指定して、次のコマンドを実行してください。

```ruby
$ rbenv install 2.5.1
```

- インストールが終わったら、次のコマンドで、システム全体で使用する Ruby のバージョンを rbenv に設定します。

```ruby
$ rbenv global 2.5.1
```

- 最後に、正しく rbenv でインストールしたバージョンの Ruby が利用できるか確認します。

```ruby
% ruby -v
ruby 3.1.0p0 (2021-12-25 revision fb4df44d16) [x86_64-darwin20]
% which ruby
/Users/yoshiwo/.rbenv/shims/ruby
```

### 2 - 3 - 1 Ruby のパッケージ管理ツール「RubyGems」

- Ruby は本体だけでも動作しますが、公開されているサードパーティのライブラリを利用することで、素早く生産的にプログラミングをすることができます。これらのライブラリは「gem」という形式でパッケージ化されて配布されています。これから本書で取り扱っていく Rails も gem の 1 つです。「RubyGems」と呼ばれるパッケージ管理ツールが、gem のインストールや管理を簡単にしてくれます。
- RubyGems は Ruby に同梱されますが、同梱バージョンよりも新しいバージョンが公開されていることもあります。そこで確実に新しいバージョンを使えるようアップデートしておきましょう。それには、RubyGems の提供するgemコマンドを利用します。

```ruby
% gem update --system
```

- 現状の RubyGems のバージョンを確認するには次のコマンドを実行します。

```ruby
% gem -v
3.3.25
```

- すでに RubyGems によりインストールされている gem の一覧を確認するには、次のコマンドを利用します。

```ruby
% gem list

*** LOCAL GEMS ***

abbrev (default: 0.1.0)
actioncable (7.0.4, 7.0.3, 5.1.7, 5.1.3)
actionmailbox (7.0.4, 7.0.3)
...
yaml (default: 0.2.0)
zeitwerk (2.6.1)
zlib (default: 2.1.1)
```

### 2 - 3 - 2 Bundler のインストール

- gemコマンドによって、gem をインストールしたり、インストールされている gem を確認することができました。これだけでも Ruby から gem を利用することができるのですが、実際の開発プロジェクトでは、どの gem を、どのバージョンで利用するのか、ということが重要になります。gemコマンドではこれらの情報をプロジェクトに管理することができません。
- そこで、「[Bundler](https://bundler.io/)」を利用します。Bundler はその名前の通り、プロジェクトで使う gem を束ね、どの gem のどのバージョンが必要なのかを明示する仕組みです。プロジェクトのディレクトリに「Gemfile」という名前のファイルを作成し、gem の名前を記載しておくと、その通りにインストールしたり、それらの gem を Ruby から利用したりすることができるようになります。
- Bundler は RubyGems からインストールできます。gemコマンドでインストールしましょう。

```ruby
% gem install bundler
Fetching bundler-2.3.25.gem
Successfully installed bundler-2.3.25
Parsing documentation for bundler-2.3.25
Installing ri documentation for bundler-2.3.25
Done installing documentation for bundler after 0 seconds
1 gem installed
```

- Bundler にはいくつかサブコマンドが用意されています。代表的なものを表に示します。

| コマンド | 説明 |
| --- | --- |
| bundle install（もしくは単に「bundle」） | Gemfile に記述した gem をインストールする。このとき「Gemfile.lock」というファイルが作成され、実際に Bundler が管理する gem のバージョンや依存関係が記録される。 |
| bundle exec [コマンド] | Bundler が管理する gem を利用できる状態でコマンドを実行する。 |
| bundle init | 初期状態の Gemfile を可憐度ディレクトリに作成する。 |
| bundle update | Bundler が管理する gem のバージョンを更新する。 |
- もっとも頻繁に使われるのは、「bundle install」と「bundle exec」です。覚えておくとよいでしょう。
- プロジェクトで使われる gem の組み合わせとバージョンを固定できるようになると、他のチームメンバーの環境や、アプリケーションを実際に稼働させる環境でも同じ gem が利用されることが保証されます。ただ、Bundler で管理している環境で、Bundler を経由せずにgemコマンドを直接使って操作してしまうと、Bundler で保証しているはずの gem の状態と違った状態になってしまう可能性があります。そこで、Bundler で管理することにした環境では、gemコマンドを直接利用せず、Bundler 経由で gem に関する操作を行うようにしましょう。</details>


<details><summary>Chapter 2 - 4 Rails のインストール</summary>

いよいよ Rails のインストールを行います。本書ではバージョン「5.2.1」の Rails を扱うため、「-v」オプションでバージョンを指定してインストールします。

```ruby
# Rails のインストール
gem install rails -v 5.2.1

# Rails のバージョンが表示されなかったので、「PATHを通す」。
export PATH="$HOME/.rbenv/shims:$PATH"

# 正しくインストールされているか確認する。
rails -v
Rails 5.2.1
```

### 2 - 4 - 1 Node.js のインストール

- Webアプリケーションのフロントエンドに JavaScript を用いる場合、効率よく配信するために JavaScript を圧縮（最小化）するのが一般的です。Rails にも標準で JavaScript を圧縮する機能が備わっていますが、そのために JavaScriptランタイムが必要となるため、インストールしましょう。JavaScriptランタイムにはいくつかの選択肢がありますが、本書では Node.js を利用します。お使いの環境に合わせた方法でインストールしてください。
- macOS の場合
    
    Homebrew でインストールできます。次のコマンドを実行してください。
    
    ```ruby
    $ brew install node
    
    # 実際の表示画面。
    brew install node
    Running `brew update --auto-update`...
    ==> Auto-updated Homebrew!
    Updated 2 taps (homebrew/core and homebrew/cask).
    ==> New Formulae
    bindgen                                            corrosion                                          libunibreak
    ==> New Casks
    opensoundmeter                                     quiet-reader                                       rapidapi
    
    You have 4 outdated formulae installed.
    You can upgrade them with brew upgrade
    or list them with brew outdated.
    ...
    
    # Node.js がインストールされているかどうか確認。
    node -v
    v19.1.0 # バージョンが表示されているので正しくインストールされている。
    ```

</details>


<details><summary>Chapter 2 - 5 データベースのインストールとセットアップ</summary>
最後に、Railsアプリケーションのデータの永続化のために使うデータベースのインストールとセットアップを行います。本書ではデータベースとして PostgreSQL を利用するので、お使いの環境に合わせた方法でインストールしてください。

### Chapter 2 - 5 - 1 macOS の場合

- Homebrew を使用してインストールを行います。下記コマンドを実行してください。

```ruby
$ brew install postgresql

# 実際の表示画面。
brew install postgresql
Running `brew update --auto-update`...
==> Auto-updated Homebrew!
Updated 2 taps (homebrew/core and homebrew/cask).

You have 3 outdated formulae installed.
You can upgrade them with brew upgrade
or list them with brew outdated.
...

# 正しくインストールされているか確認。
postgres -V
postgres (PostgreSQL) 14.6 (Homebrew) #　正しくインストールされていると、バージョン情報が表示される。
```

- 次に PostgreSQL を起動します。Homebrew の「services」サブコマンドを利用して簡単に立ち上げることができます。

```ruby
$ brew services start postgresql

# 実際の表示画面
brew services start postgresql
Warning: Use postgresql@14 instead of deprecated postgresql # 警告：非推奨の postgresql の代わりに postgresql14 を使用してください。
==> Successfully started `postgresql@14` (label: homebrew.mxcl.postgresql@14) # postgresql14 の起動に成功しました（ラベル：homebrew.mxcl.postgresql@14）
```

- 尚、停止する場合は下記コマンドを実行してください。

```ruby
$ brew services stop postgresql

# 実際の表示画面
brew services stop postgresql
Warning: Use postgresql@14 instead of deprecated postgresql
Stopping `postgresql@14`... (might take a while)
==> Successfully stopped `postgresql@14` (label: homebrew.mxcl.postgresql@14)
```

- PostgreSQlサーバを立ち上げたら、実際にコンソールに入って動作確認をしておきましょう。下記コマンドを実行して正常に動作していることが確認できたら「\q」を入力して終了します。

```ruby
psql postgres
psql (14.6 (Homebrew))
Type "help" for help.

postgres=# \q      # \q を入力して終了。 
```

### 2 - 5 - 3 トラブルシューティング：Rails で PG::ConnectionBad というエラーになるとき

- これから Rails を実際に触っていきますが、Railsサーバを起動してアプリケーションにアクセスしたときに、「PG::ConnectionBad(could not connect to server: …)」というエラーが発生することがあります。これは Rails から利用可能な PostgreSQL が見つからないために発生します。PostgreSQL のセットアップのところで説明したやり方で PostgreSQLサーバを起動してから、もう一度アプリケーションにアクセスしてみましょう。
- 開発用 PC の再起動の後などは、PostgreSQL を立ち上げるのを忘れてしまいがちです。もしこのエラーが発生しても、焦らずに上記を思い出してください。</details>


<details><summary>Chapter 2 - 6 Rails に触れてみよう</summary>

長い環境構築作業でしたが、これで Railsアプリケーションを開発していく準備が完了しました。

実は、Rails を単に動かすだけであれば、もうちょっとだけ環境構築を楽にすることはできます（rbenv を利用せずに Ruby をインストールしたり、データベースを軽量なものにしたりするなど）。しかし、本書ではより実践的な開発フローを説明するために、最初から実践的な環境を用意しました。環境構築に苦労した分、実際の開発に近い状態で学習できるはずです。

まずは、scaffold というコマンドで自動生成されたアプリケーションを動かしてみましょう。その後、Rails の基本的な考え方である MVC について解説をします。

### 2 - 6 - 1 実際にアプリケーションを動かしてみよう

- まずは、簡単なアプリケーションを作って動かしてみましょう。作るものは「ユーザー管理アプリケーション」です。作るといっても自分でコードを書く必要はほとんどなく、Rails のコマンドで自動生成することができるので、心配しないでください。
- また、これから使っていくコマンドの詳細やコードの意味は次章以降で詳しく解説するので、ここでは全体的なアプリケーションの処理の流れや構造を把握していきましょう。

### 2 - 6 - 1 - 1 Railsアプリケーションを新規作成する

- 下記のコマンドを作業用のフォルダで実行してください。「rails new」コマンドは、Railsアプリケーションの雛形を生成します。ここでは、これから生成するアプリケーションの名前を「scaffold_app」と名付け、コマンドに指定しています。

```ruby
$ rails new scaffold_app -d postgresql
# PostgreSQL を使用するため「-d」オプションでデータベースの指定をする。
# 「-d」オプションがないと SQLite3 用のファイルが生成されるので注意。
# データベースの種類はアプリケーションを作成した後で変更することができる。
```

- すると、「scaffold_app」というディレクトリが関連ファイルとともに生成され、生成ログが出力されます。続けて「bundle install」が自動的に実行され、Rails の実行に必要な gem がインストールされます。完了までしばらく待ちましょう。
- コマンドの実行が完了したら、scaffold_appディレクトリに移動して、次のようにサブディレクトリやファイルが生成されていることを確認してください。

```ruby
$ cd scaffold_app
$ ls
Gemfile		README.md	Rakefile	app		bin		config		config.ru	package.json
```

- 次に、データベースを作りましょう。PostgreSQL を起動した状態で、次のコマンドを実行してください。設定ファイルの定義に従って、データベースが作成されます。

```ruby
# PostgreSQL を起動した状態で、次のコマンドを実行する。
$ bin/rails db:create
Created database 'scaffold_app_development'
Created database 'scaffold_app_test'
```

- ここでは「rails」コマンドではなく、「bin/rails」コマンドを利用しています。アプリケーションのルートディレクトリ直下のbinディレクトリにある rails というスクリプトを呼び出しています。このスクリプトを利用すると、「bundle exec rails」として実行した時と同様に、Gemfile どおりの gem を利用できる環境上でrailsコマンドを実行することができます。それに加えて、Spring という Rails の起動を効率的に行う機能も組み込まれます。このように、よく使うコマンドを包み込んで使いやすくするスクリプトを「binstub」といいます。以降、本書では「bin/rails」を利用してrailsコマンドにアクセスすることにします。
- データベースの準備ができたので、ここで一度サーバーを起動してみましょう。次のコマンドを入力すれば、簡単にサーバーを起動することができます。

```ruby
$ bin/rails s
=> Booting Puma
=> Rails 5.2.8.1 application starting in development 
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.12.6 (ruby 2.7.6-p219), codename: Llamas in Pajamas
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://localhost:3000
Use Ctrl-C to stop
```

- ログに表示されているように、Rails は標準の HTTPサーバーとして Puma を採用しています。アプリケーションを公開する際は要件に応じて他の HTTPサーバーを利用することもありますが、Puma は広く使われており、Rails の機能を利用する上での不足はありません。開発環境としても問題になることはまずないでしょう。
- サーバーが起動したら、次のアドレスをWebブラウザのアドレスバーに入力して確認しましょう。尚、Windows の場合は Ubuntu ではなく Windows にインストールされているブラウザを使ってください。
- [http://localhost:3000/](http://localhost:3000/)

```ruby
# サーバー起動後、http://localhost:3000にアクセスすると以下の内容が表示。
Started GET "/" for ::1 at 2022-11-21 15:33:36 +0900
Processing by Rails::WelcomeController#index as HTML
  Rendering /Users/yoshiwo/.rbenv/versions/2.7.6/lib/ruby/gems/2.7.0/gems/railties-5.2.8.1/lib/rails/templates/rails/welcome/index.html.erb
  Rendered /Users/yoshiwo/.rbenv/versions/2.7.6/lib/ruby/gems/2.7.0/gems/railties-5.2.8.1/lib/rails/templates/rails/welcome/index.html.erb (3.7ms)
Completed 200 OK in 13ms (Views: 9.4ms | ActiveRecord: 0.0ms)
```

- 起動が確認できたら、一旦サーバーを終了します。サーバーを起動したコンソールで「Ctrl + C」を押してください。

```ruby
# ターミナルで「Ctrl + C」を入力すると以下の内容が表示。
^C- Gracefully stopping, waiting for requests to finish
=== puma shutdown: 2022-11-21 15:39:57 +0900 ===
- Goodbye!
Exiting
```

### 2 - 6 - 1 - 2 ユーザー管理機能の雛形を作る
- ユーザー管理の機能を作っていきましょう。先ずは次のコマンドを実行して、「ユーザー」に関する scaffold を自動生成します。コマンドの実行が完了すると、データベース関連の設定や、実際のユーザー管理の処理が記述されたコードが生成されます。

```ruby
# 「ユーザー」に関する scaffold を自動生成する。
$ bin/rails generate scaffold user name:string address:string age:integer
Running via Spring preloader in process 84468
(中略)
create      app/assets/stylesheets/users.scss
invoke  scss
create    app/assets/stylesheets/scaffolds.scss
# データベース関連の設定、実際のユーザー管理の処理が記述されたコードが生成される。
```

- 次にユーザー管理機能に使うテーブルをデータベースに作成しましょう。下記コマンドを実行してください。

```ruby
# ユーザー管理機能に使うテーブルをデータベースに作成する。
$ bin/rails db:migrate   # db/migrateフォルダ下に生成された、テーブル操作を定義するファイルに沿って SQL が実行されます。
== 20221122010610 CreateUsers: migrating ======================================
-- create_table(:users)
   -> 0.0102s
== 20221122010610 CreateUsers: migrated (0.0103s) =============================
```

- 準備が整ったところで、再度アプリケーションを起動します。

```ruby
# アプリケーションを起動（サーバーを起動）。
$ bin/rails s
=> Booting Puma
=> Rails 5.2.8.1 application starting in development 
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.12.6 (ruby 2.7.6-p219), codename: Llamas in Pajamas
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://localhost:3000
Use Ctrl-C to stop
```

- 今度は、[http://localhost:3000/users](http://localhost:3000/users) にアクセスしてください。

### 2 - 6 - 1 - 3 アプリケーションの画面構成

- ユーザー情報を管理するアプリケーションがどんなふうにできあがっているか見てみましょう。
- あらためて、「ユーザー一覧画面」([http://localhost:3000/users](http://localhost:3000/users))を見てみましょう。この画面は登録されたユーザーを一覧するための画面ですが、今は 1 件もユーザーの情報が登録されていないため、表の見出しだけが表示されています。
- これでは寂しいため、実際にユーザーを登録してみましょう。一覧画面内にある「New User」リンクをクリックしてください。すると、ユーザーの登録画面に遷移します。フォームに適当な情報を入力してください。

- 「Create User」ボタンをクリックすると、ユーザーの詳細画面に遷移し、登録に成功した旨のメッセージが表示されます。

- この詳細画面で「Edit」リンクをクリックすると編集画面に、「Back」リンクをクリックすると一覧画面に遷移します。ためしに「Back」リンクをクリックし、一覧画面に戻ってみましょう。先程登録したユーザーが表示されているはずです。

- ここで、今回作成した scaffold_appアプリケーションの画面遷移を下記の図にまとめます。矢印の横にはリンク/ボタン名を記載しています。

- ユーザーを管理するために必要な機能である「作成（Create）」「読み出し（Read）」「更新（Update）」「削除（Delete）」が生成されていることがわかります。これらの機能は、データ操作する際によくまとまって登場します。そのため、一般に頭文字を取って「CRUD」と呼びます。
- scaffold 機能を使うことで、一通りの CRUD を備えたWebアプリケーションを、コードを書かずにコマンド一つで作成することができました。実際の現場ではもっと複雑な仕様が求められるため、scaffoldアプリケーションをそのまま使えるシチュエーションは殆どありませんが、自動生成されるコードは Rails というフレームワークを使いこなすためのお手本のような内容になっています。


### 2 - 6 - 1 - 4 コードの通り道

- コードの中身を見てみましょう。画面にアクセスがあった場合の流れに沿って簡単に解説していきます。コードの詳細は次章以降で詳しく紹介するので、ここでは雰囲気だけを掴んでいただくのが狙いです。
- コードを見ていく前に、簡単に用語について説明しておきます。Railsアプリケーションは、MVC（モデル・ビュー・コントローラ）という考え方で構成されています。
- 一覧画面（/users）にリクエストが来た時のコードの通り道を順に見ていきましょう。
- 先ずはコントローラが呼ばれます。コントローラにはリクエストに対する処理が書かれている「アクション」と呼ばれるメソッドが定義されています。一覧画面にアクセスした際に呼ばれるのは、下記の indexアクションです。

```ruby
class UsersController < ApplicationController
	before_action :set_user, only: %i[ show edit update destroy ]

	# GET /users or /users.json
	def index
		@users = User.all
	end
	# 省略
```

- 2 行目にある before_action では、index などのアクションメソッドが実行される前に行いたい処理（フィルタ）を指定することができます。フィルタについては 4 章で詳しく説明します。
- indexアクションは def index から end までの箇所で定義されています。ここでは、ユーザーに関する情報をデータベースから取得しています。その際、User というモデルを使っています。
- Userモデル（app/models/user.rb）を見てみましょう。Userモデルは、データベースとRubyオブジェクトとの間のやりとりを隠蔽し、外部から簡単にUserデータを取得したり、登録・更新・削除できる機能を提供してくれます。

```ruby
class User < ApplicationRecord
end
```

- たったこれだけです。親クラスを通じて、データベースとやりとりするための様々な機能を備えています。ここにメソッド等を書いて、任意の処理を自由に追加していくことができます。
- 次は実際にブラウザで表示される画面を出力するためのコードであるビューを見てみましょう。ビューのファイルは、決まった HTML の中に動的なデータを流し込むようなイメージのものが多いことから、テンプレートファイルとも呼ばれます。
- デフォルトでは、「app/views/コントローラ名/アクション名.html.erb」ファイルが呼び出されます。つまり、indexアクションの場合は「app/views/users/index.html.erb」ファイルが呼び出されます。

```ruby
<p id="notice"><%= notice %></p>

<h1>Users</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Address</th>
      <th>Age</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @users.each do |user| %>
      <tr>
        <td><%= user.name %></td>
        <td><%= user.address %></td>
        <td><%= user.age %></td>
        <td><%= link_to 'Show', user %></td> ①
        <td><%= link_to 'Edit', edit_user_path(user) %></td> ①
        <td><%= link_to 'Destroy', user, method: :delete, data: { confirm: 'Are you sure?' } %></td> ①

      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New User', new_user_path %>
```

- ここでは、コントローラで取得したユーザー情報（@users）をもとに、<table>タグを使って一覧表を出力しています。一覧表の中で使われているlink_toメソッドは、与えられた引数からリンク（<a>タグ）を生成する便利機能です。このような、ビューで利用できる便利な機能のことを「ヘルパーメソッド」と呼びます。
- また、①ではlink_toメソッドの引数に edit_user_path(user) や user というようなコードがありますが、これはパス（URL）を生成するヘルパーメソッドになります。パスを生成するヘルパーメソッドについては、Chapter 6 - 2 「ルーティング」で解説します。
- 上記のビューテンプレートファイルを見ていて、<table>といったタブが登場しているにもかかわらず、それを囲むはずの <html> などのタグが無いことを不思議に思われたかもしれません。scaffold のコードでは、アプリケーションのヘッダーやフッターなどの共通部分はレイアウトと呼ばれるファイルにまとめられています。app/views/layoutsディレクトリ内にあるレイアウトファイルの中身を見てみましょう。

```ruby
<!DOCTYPE html>
<html>
  <head>
    <title>ScaffoldApp</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= yield %> # アクションに対応するビューテンプレートファイル（app/views/users/index.html.erb）の実行結果が埋め込まれるようになっている。
  </body>
</html>
```

- コントローラ側で特に指定をしなければ、applicaiton.html.erb が適用されます。
- <%= yield %>の部分にアクションに対応するビューテンプレートファイル（例えばindexアクションであれば先程見たapp/views/users/index.html.erb）の実行結果が埋め込まれるようになっています。
- これらビューファイルは、Rubyコードを HTML に埋め込むことを可能にする ERB を使って書かれています。一見よくあるHTMLファイルのようですが、<% … %>や<%= … %>で Ruby のコードを記述することができます。これは一般的にテンプレートエンジンと呼ばれる仕組みで、3 章で詳しく説明します。
- scaffoldのユーザー一覧機能に関わるコードの通り道、コントローラ → モデル → ビュー。

### 2 - 6 - 1 - 5 ディレクトリ構成

- Railsアプリケーションのディレクトリ構成を見ていきます。rails newコマンドでアプリケーションを作成すると、自動的にディレクトリ群が生成されます。
- 特に重要なのは、アプリケーションのソースコードやデータベースの設定ファイルを格納するための以下のディレクトリで、どんなRailsアプリケーションでも頻繁に利用されます。
    - app/controllers
    - app/models
    - app/views
    - config
    - db
- app/以下のファイルについては、scaffoldアプリケーションの解説の際に一通り見たため、ここでは、主に設定ファイルが配置されている、config、dbディレクトリについて、簡単に説明します。
- 上記の中で最も重要な設定ファイルは、config/database.yml と config/routes.rb です。ここでは、この 2 ファイルについて解説します。
1. **database.yml**
    1. database.yml は、データベースと接続するための設定ファイルです。YAML という形式で記述されています。YAML については続くコラム「YAMLの基本」で詳しく解説しています。
    2. database.yml の中では、development、test、production という 3 種類の環境用に、それぞれデータベースの接続に必要な設定が記述されています。
    3. ここでは開発環境用の設定である development の項目と、その中から参照されている default の項目を見てみます。

```ruby
default: &default
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see Rails configuration guide
  # http://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

development:
  <<: *default
  database: scaffold_app_development

  # The specified database role being used to connect to postgres.
  # To create additional roles in postgres see `$ createuser --help`.
  # When left blank, postgres will use the default role. This is
  # the same name as the operating system user that initialized the database.
  #username: scaffold_app

  # The password associated with the postgres role (username).
  #password:

  # Connect on a TCP socket. Omitted by default since the client uses a
  # domain socket that doesn't need configuration. Windows does not have
  # domain sockets, so uncomment these lines.
  #host: localhost
```

- 上記に登場する設定項目を、コメントアウトされた状態のものも含めて表にすると、次のようになります。

| 項目名 | 説明 |
| --- | --- |
| adapter | データベースの接続に使用するアダプタの名前を指定します。アダプタには各データベースに対応する sqlite3、postgresql、mysql2、oracle_enhancedなどがあります。 |
| encoding | 文字コード |
| pool | コネクション数の上限 |
| database | データベース名 |
| username | データベースに接続するユーザ名 |
| password | データベースに接続するユーザのパスワード |
| host | データベースが動作しているホスト名またはIPアドレス |
- pool の設定項目は、デフォルトでは <%= ENV.fetch(”RAILS_MAX_THREADS”) { 5 } %> と書かれています。ERBの「<%= %>」によってRubyコードが埋め込まれていて、実行時に動的に設定が決まるのです。具体的には、RAILS_MAX_THREADS環境変数に値が設定されていれば環境変数の値を使い、設定されていなければ 5 を使うという動作になります。

### Column YAMLの基本

- YAML は構造化されたデータを表現するためのフォーマットです。用途としては XML と似ています。人間に優しいシンプルな記法となっており、Rubyプログラムではポピュラーな存在です。YAML はインデントでデータの階層構造を表現し、配列やハッシュも表すことができるため、データの保存や設定ファイルなどによく利用されます。Rails の設定ファイルではハッシュ形式の書き方がよく登場します。下記に例を示します。

```ruby
animal:
	cat: 'ネコ'
	dog: 'イヌ'
```

- 「キー: 値」の形でハッシュを表現することができ、スペースによるインデントを使って入れ子構造にすることも可能です。
- 複数の箇所で共通して使いたいキーについては、「エイリアス」と「アンカー」という機能を使って、共通化することができます。次の例に示すように、共通して使いたい箇所の親となるキーに「&エイリアス名」を書くことでエイリアスを定義します。そしてエイリアスを参照したい箇所では「<<: *参照するエイリアス名」を書くことで、エイリアスの内容がまるでそこに書かれているかのように扱うことができます。

```ruby
animal: &animal
	cat: 'ネコ'
	dog: 'イヌ'

animal_shop_1:
	<<: *animal
	hamster: 'ハムスター'

animal_shop_2:
	<<: *animal
	parrot: 'オウム'
```

- database.yml における各環境のデフォルト定義も、このエイリアスを使って &default として実現していました。
- YAML形式のデータは、Ruby の標準ライブラリである「YAML」を使って簡単に読み込むことができます。Rails の「console」コマンドを使って試してみましょう。先程の例の YAML を、scaffold_appディレクトリの直下に「example.yml」ファイルとして配置してから（方法：1. ターミナルにてtouchコマンドで「example.yml」ファイルを作成。2. VSCode や Atom などのエディターで、作成した「example.yml」ファイルを開き、テキストに載っているコードを書き込み・保存）、「bin/rails c」コマンドを実行します。

```ruby
# rails cコマンドで Rails の環境を読み込んだ状態でRubyコードを対話的に実行することを可能にする。
bin/rails c
Running via Spring preloader in process 91807
Loading development environment (Rails 5.2.8.1)
irb(main):001:0> YAML.load_file('./example.yml')
=> {"animal"=>{"cat"=>"ネコ", "dog"=>"イヌ"}, "animal_shop_1"=>{"cat"=>"ネコ", "dog"=>"イヌ",
"hamster"=>"ハムスター"}, "animal_shop_2"=>{"cat"=>"ネコ", "dog"=>"イヌ", "parrot"=>"オウム"}}
```

- コンソールの出力から、データをハッシュとして読み込めたことがわかります。また、エイリアスとして定義した animal の項目がanimal_shop_1、animal_shop_2にも含まれています。このように、YAML では設定ファイルで扱われるようなデータをわかりやすく定義することができ、簡単にRailsアプリケーションでも利用することができます。
1. **routes.rb**
- routes.rbは、アプリケーションのルーティングを定義するファイルです。ルーティングとは、簡単に言えば、リクエストに対応するレスポンスを作るためにどの処理を実行するかを定義したものです。それでは、routes.rb の中身を軽く見ていきましょう。

```ruby
Rails.application.routes.draw do
  resources :users
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
```

- scaffold_appアプリケーションには scaffold によって作成したユーザーの CRUD の機能がありますが、2 行目の resources :users がそのための一括のルーティング定義になっています。
- では、どのようなルーティングが作成されているのか見てみましょう。アプリケーションのルートディレクトリでbin/rails routesコマンドを実行してみてください。このコマンドでは、routes.rb で定義されたすべてのルーティングを確認することができます。

```ruby
bin/rails routes
   Prefix Verb   URI Pattern                          Controller#Action --①
    users GET    /users(.:format)                     users#index
          POST   /users(.:format)                     users#create
 new_user GET    /users/new(.:format)                 users#new
edit_user GET    /users/:id/edit(.:format)            users#edit
     user GET    /users/:id(.:format)                 users#show
          PATCH  /users/:id(.:format)                 users#update
          PUT    /users/:id(.:format)                 users#update
          DELETE /users/:id(.:format)                 users#destroy
......
```

- 表の形に整ったルーティング定義が出力されました。①の行はヘッダーで、次行以降がルーティング定義です。
- ここでは、URI Pattern の列と Controller#Action の列に注目しておきましょう。URI Pattern のところに表示されている URL のパターンにマッチする URL にブラウザからアクセスする（もしくはフォームを送信する）と、Controller#Action のところに表示されているコントローラのアクションが呼び出され、処理が実行されることになります。

### 2 - 6 - 2 Rails の MVC

- Railsアプリケーションの概要が掴めてきたところで、Rails で採用している MVC という考え方についての理解を深めておきましょう。
- MVC とは、UI（ユーザーインターフェース）を持つソフトウェアのアーキテクチャの 1 種です。要は、ソフトウェアをどのような構造にするかについての考え方のパターンの 1 つということです。
- 一般に、アプリケーションは様々な処理によって構成されますが、中でも UI の部分は、アプリケーション固有のデータや処理の扱いとは性質が異なり、両者を混ぜて記述するとコードが複雑化して保守性が悪くなってしまいます。そのため、UI に関わる部分（ビュー）と、アプリケーション固有のデータや処理の扱いの部分（モデル）、モデルやビューを総合的に制御する部分（コントローラ）の 3 つに役割を分けて、管理しやすくしよう、というのが MVC の主な目的です。
- Rails ではこの考えを採用していて、アプリケーションの構造や名前付けも、M（model）、V（view）、C（controller）の 3 つの要素に従っています。
- モデルは、データとデータに関わるビジネスロック（アプリケーション特有の処理）をオブジェクトとして実装したものです。データは多くの場合、データベースで永続化されますが、データベースへの保存や読み込みもモデルが担当します。
- ビューは、ブラウザに表示する画面、すなわち HTML などの HTTPレスポンスの中身を実際に組み立てる部分です。必要に応じてコントローラからモデルのオブジェクトなどを受け取り、画面表示に利用します。
- コントローラは、ユーザーが操作するブラウザなどのクライアントからの入力（リクエスト）を受け、適切な出力（レスポンス）を作成するための制御を行う部分です。必要に応じてモデルを利用したり、ビューを呼び出したりして、レスポンスを作り上げます。名前の通り、M と V のコントロールを行う存在です。


### 2 - 6 - 2 - 1 コントローラ層の詳細

- MVC の概要がわかったところで、コントローラ層について、もう一歩だけ踏み込んでおきましょう。
- コントローラ層では、ブラウザなどから発行されたHTTPリクエストの宛先となっている URL や、GET や POST といったHTTPメソッドをもとに「そのリクエストを処理する担当箇所」をまず特定します。この特定は、前述の routes.rb で定義されたルーティングによって行われます。「そのリクエストを処理する担当箇所」とは何でしょうか。具体的には、どのコントローラ（クラス）の、どのアクション（メソッド）が処理を担当するのかを決めるということになります。

- 従って、ある機能をアプリケーションに加える際には、まず、その機能を何というコントローラ（クラス）の何というアクション（メソッド）で処理するかを決める必要があります。


### 2 - 6 - 2 - 2 scaffold_appアプリケーションを振り返って

- 改めて、scaffold_appアプリケーションのコード構成を振り返ってみましょう。scaffold_appアプリケーションでは、UsersController という 1 種類のコントローラを作成し、その中に、index、show、edit…といった複数のアクションを配置していました。また、User というモデルクラスを作成し、各アクションから利用していました。index などの各アクションに対応するビューテンプレートファイルも生成され、これらをもとにレスポンスの HTML が作られ、ブラウザに送られていました。
- それぞれの番号に対応する処理の内容は、次のようになります。

①クライアントからのリクエストはWebサーバを介してRailsアプリケーションへと受け渡されます。

②ルーティング機能がそのリクエストの処理を行うべきコントローラ（クラス）とアクション（メソッド）を特定し、アクションを呼び出します。

③アクションでは、必要に応じてモデルを利用します。たとえば一覧機能であるindexアクションでは、全ユーザーデータをUserオブジェクト群としてUserクラスから取得します。

④モデルがデータベースで永続化されている場合、モデルはデータベースへの書き込みや読み出しも担当します。indexアクションの場合は、データベースのusersテーブルから全レコードを読み出す SQL が実行されます。

⑤アクションごとに、対応するビューテンプレートを用いて HTML 等を生成します。

⑥コントローラがレスポンスを作成し、Webサーバを介してクライアントへと返します。

- 以上、本章では、scaffold でユーザーのCRUD機能を作成した scaffold_appアプリケーションを題材に、Railsアプリケーションのソースコードや設定ファイルがどのような構造になっているかを見てきました。既存のRailsアプリケーションの中身を確認するといったシーンで、何に注目して読めばいいのか少し検討がつくようになったのではないかと思います。例えば、リクエストごとの挙動（機能）に着目するならば app/controllers の下のコントローラクラスのファイルやアクションらしきメソッドを見ていくことができるし、データ構造やデータに関係するビジネスロジックに着目するならば app/models の下のモデルクラスの様子を見ていくことができるでしょう。
- 次章以降では、scaffold を使わずにゼロから新しいRailsアプリケーションを開発する際の手順を学んでいきます。その際には、Rails のより詳しい機能や開発手法にも踏み込んで解説していきます。</details>
