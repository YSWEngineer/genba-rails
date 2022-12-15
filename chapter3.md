# Chapter 3 タスク管理アプリケーションを作ろう

この章では、CRUD を備えたシンプルな「タスク管理アプリケーション」を、scaffold を使わずにゼロから作成します。その中で、Rails の基本的な機能について学んでいきます。

タスク管理アプリケーションの作成は、以下の表で示す環境が整っていることを前提に行なっていきます。手元に環境が整っていない場合は、 2 章を参考に各ツールをインストールしてください。


<details><summary>Chapter 3 - 1 アプリケーション作成の準備をしよう</summary>

アプリケーションの作成にあたっては、一般的に次のような準備を行います。

- 作成するアプリケーションの内容を考える。
- アプリケーションの名前を決める。
- アプリケーションの雛形を作成する。
- データベースを作成する。

今回は、さらに次のような準備を行なっていきます。

- ビュー層を効率良く書くために Slim を使えるようにする。
- アプリケーションの見栄えを良くするために Bootstrap を導入する。
- Rails のエラーメッセージなどを日本語で出せるようにする。


### 3 - 1 - 1 作成するアプリケーションの内容を考える

- 今回作成するのは、タスク管理のためのアプリケーションです。タスクとは、レポートの提出、ゴミ出し、買い物といったさまざまな用事のことです。タスク管理アプリケーションの一般的な使い方は、タスクをアプリケーションにデータとして登録しておいて、折々に確認したり、予定を済ませたら済ませたと言うことを登録したり削除したりできるようにするというものになるでしょう。
- 本章では CRUD と呼ばれる基本的なデータ操作を実現します。CRUD は、Create（作成）、Read（参照）、Update（更新）、Delete（削除）を表します。タスク管理のように、複数のデータについての CRUD を作成する場合、参照機能については一覧表示と詳細表示という 2 種類の参照機能を用意するのが一般的です。作りたい機能のリストは次のようになります。
1. 一覧表示機能：すべてのタスクの概要（名称と登録日時）を確認できる一覧画面を表示します。
2. 詳細表示機能：ある 1 つのタスクの全内容（ID、名称、詳しい説明、登録日時、更新日時）を確認できる詳細画面を表示します。※ID、登録日時、更新日時は Rails がデフォルトで扱う属性です。
3. 新規登録機能：新しいタスクのデータをフォーム画面で入力し、データベースに登録します。
4. 編集機能：登録済みのタスクのデータをフォーム画面で修正し、データベースを更新します。
5. 削除機能：登録済みのタスクをデータベースから削除します。

### 3 - 1 - 2 アプリケーションの名前を決める

- Rails でアプリケーションを作成するには、アプリケーションの（システム上の）名前が必要です。アプリケーションの名前は、例えばコード一式を格納するためのディレクトリ名として利用されます。愛着の湧くような、わかりやすい名前をアルファベットで付けると良いでしょう。
- 本書では **taskleaf** というアプリケーション名で実装を進めていきます。

### 3 - 1 - 3 アプリケーションの雛形を作成する

- それでは実際に PC 上での作業を始めましょう。先ず、rails newコマンドで、アプリケーションの基本的なディレクトリ・ファイル類一式を作成します。rails newコマンドの使い方は次のようになっています。

```ruby
$ rails new アプリケーション名 [オプション]
```

- 今回はデータベースに PostgreSQL を使用するので、[オプション]の部分に、利用するデータベースとして「postgresql」を指定します。具体的なコマンドは次のようになります。ターミナルを起動して、これから作成するアプリケーションディレクトリの親となるディレクトリに移動し、コマンドを入力してみてください。

```ruby
$ rails new taskleaf -d postgresql
```

- 入力すると、ただちに「create…」というような内容が表示されていきます。

```ruby
# rails newコマンドで、アプリケーションの基本的なディレクトリ・ファイル類一式を作成する。
# 「-d」オプションでデータベースの指定をする。オプションがないと SQLite3 用のファイルが生成されるので注意。
$ rails new taskleaf -d postgresql 
      create  
      create  README.md
      create  Rakefile
      create  .ruby-version
      create  config.ru
      create  .gitignore
			...
```

- この段階で、空っぽの新しいRailsアプリケーションができています。アプリケーションの雛形を作成すると、アプリケーションのコードだけでなく、開発用のサーバなどのツール類も使えるようになります。早速、サーバを起動して、作った空っぽのアプリケーションの動作を確認してみましょう。
- サーバの起動にはrails serverコマンドを利用します。rails server は rails s という短縮系でも動きます。本書では、便利な短縮系を使っていきます。
- ところで、rails newコマンドでアプリケーションを作成した段階では、私たちは、アプリケーションディレクトリの 1 つ上の階層（親ディレクトリ）にいます。サーバーを起動するなど、アプリケーションについての操作は基本的にアプリケーションディレクトリで行いますので、先ずはcdコマンドを使って、アプリケーションディレクトリに移動しましょう。アプリケーションディレクトリは、先ほどrails newコマンドによって作成された、現在いるディレクトリの下の taskleaf というディレクトリになります。

```ruby
$ cd taskleaf

# 実際のターミナルの表示。
cd taskleaf
yoshiwo@Yoshiwos-MacBook-Pro taskleaf % 
# プロンプトの前のディレクトリ名がアプリケーション名の「taskleaf」に変わった（移動した）。
```

- taskleafディレクトリに移動したら、データベースを作成しておきます。

```ruby
# データベースの作成。
$ bin/rails db:create

# 実際の表示
bin/rails db:create
Created database 'taskleaf_development'
Created database 'taskleaf_test'
```

- データベースが作成できればサーバーを起動できますので、サーバを起動してみましょう。以下のような情報が画面に出力されていきます。
- サーバは[Ctrl + C]（[Ctrl]キーを押したまま[C]キーを押下）で停止するまでずっと動作し続けます。

```ruby
# サーバーの起動。
$ bin/rails s

# 実際の表示
bin/rails s
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

- サーバーを起動したら、ブラウザを立ち上げて [http://localhost:3000](http://localhost:3000) にアクセスしてください。ブラウザ上に Rails のデフォルトページが表示されていれば、正しくサーバーが起動しています。
- なお、サーバーを起動するとそのコンソールにはプロンプトが表示されなくなるため、以降のコマンドは、新しくターミナルのウィンドウを開いて入力していってください。
- アプリケーションの動作確認を行う際にはサーバーを起動している必要があるため、本章では開発中は常にサーバーを起動しているものとして説明を行います。

### 3 - 1 - 4 データベースの環境ごとの使い分け

- データベースは、アプリケーションが生み出すデータを保存するところです。taskleafアプリケーションの場合の代表的なデータは、例えばタスクの情報ということになります。
- 開発の時に動作確認のために作るデータと、アプリケーションが完成して本当に利用し始めたことで作られるデータは、性質が違います。同じところに入れてしまうと、テスト用のデータが邪魔になってしまうこともあるでしょう。さらに、Rails には 5 章で説明する「自動テスト」の仕組みがあり、「自動テスト」のためにデータを格納しておくべき場所も他の用途のデータが混じり込まないように専用の場所にしたいものです。
- そこで、Rails ではデフォルトで次のような 3 種類の「環境」が用意されており、1 つの環境に対して 1 つのデータベースを対応させます。環境ごとに、データベースのほか、アプリケーションの動作に関わる様々な設定を行うことができます。※この環境は自由に追加・削除をしたり、名前を変更したりできます。

| 環境の種類 | 環境のシステム名 | 用途 |
| --- | --- | --- |
| 開発 | development | 開発時の動作確認を行う |
| テスト | test | 自動テストを行う |
| 本番 | production | ユーザーが利用可能な形で稼働させる |
- どの環境にどのようなデータベースを対応させるかは、2 章で解説した config/database.yml に記述します。ここでは、rails newコマンドで自動作成された config/database.yml のデフォルトの設定通りで進めていきます。
- 開発時は、基本的に開発・テストの 2 種類の環境を使います。先ほどサーバーを起動する前に db:create を実行しましたが、それによってこの 2 種類の環境のための 2 つのデータベースが作成済みとなっています。

```ruby
Created database 'taskleaf_development'
Created database 'taskleaf_test'
```

### 3 - 1 - 5 ビュー層を効率良く書くために Slim を使えるようにする

- webアプリケーションはブラウザで利用するため、最終的に画面を HTML として出力することになります。しかし、HTML には動的ないろいろなデータを埋め込む必要があり、そのためのプログラミングが必要です。プログラミングといっても、長大な HTML を作成するために延々と文字列を連結するといった方法では、画面の状況を思い浮かべづらく開発が難しくなってしまいます。
- そこで、Rails を使った開発では、テンプレートエンジンを使います。テンプレートエンジンを使うと、プリケーションの画面を HTML の構造が直感的にわかりやすいテンプレート形式で書くことができます。
- Rails はデフォルトで ERB というテンプレートエンジンを採用しています。ERB は HTML とほぼ同じ見た目で、各要素がタグで囲まれており、その中にRubyスクリプトを埋め込むことができます。例えば、次のような ERB のテンプレートは、h1タグの中に、@title というインスタンス変数の内容を動的に埋め込んでくれます。

```ruby
<html>
	<body>
		<h1><%= @title %></h1>
		<p>この画面では、見出しを動的に出力することができます。</p>
	</body>
</html>
```

- ERBは HTML に近い形をしているので、HTMLを知っていれば比較的簡単に理解することができます。只、Railsを使った開発の現場では、HTMLをツリー構造として簡潔に表現できる Haml や Slim といった別のテンプレートエンジンが利用されることが多くなっています。
- 先に挙げた ERB の例を Haml で記述すると次のようになります。

```ruby
# 先程のERBの例をHamlで記述。
%html
	%body
		%h1= @title
		%p この画面では、見出しを動的に出力することができます。
```

- Slimで記述。

```ruby
# 先程のERBをSlimで記述。
html
	body
		h1= @title
		p この画面では、見出しを動的に出力することができます。
```

- タグの開始と終了を両方記述しなければならない ERB に比べて、インデントでツリー構造を表現している Haml や Slim は、簡潔で読みやすいといえます。
- Hamlと Slim は似ており、どちらも実際の開発の現場で使われていますが今回は Slim を使います。そこで、Slimを利用するための設定を行なっていきます。
- Slimを利用するために 2 つの gem （Rubyのライブラリ）をアプリケーションに追加します。1つは Slim のジェネレータを提供してくれる slim-rails という gem 。もう 1 つは、ERB形式のファイルをslim形式に変換してくれるerb2slimコマンドを提供してくれる html2slim という gem です。
- アプリケーションが利用する gem は、Gemfileに定義します。そこで、エディタでアプリケーションフォルダ直下にある Gemfile を開いて最後の行にslim-rails と html2slim についての設定を加えましょう。

```ruby
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

![Gemfileにアプリケーションが利用するgemを設定.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/76f7812a-901d-44d7-b1bf-0973dc58411f/Gemfile%E3%81%AB%E3%82%A2%E3%83%95%E3%82%9A%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%8B%E3%82%99%E5%88%A9%E7%94%A8%E3%81%99%E3%82%8Bgem%E3%82%92%E8%A8%AD%E5%AE%9A.png)

- Gemfileの変更を保存したら、gemをインストールします。bundleというコマンドを実行すると、Gemfileに書かれた gem およびそれらの依存する gem を全てインストールされた状態にしてくれます。尚、サーバ起動中の場合は、インストールされた gem を利用するためにサーバを再起動する必要があります。

```ruby
# アプリケーションフォルダ直下のGemfileの最後の行にslim-railsとhtml2slimを加えたら、bundleコマンドを実行する。
$ bundle

# 実際の表示
bundle
Fetching gem metadata from https://rubygems.org/..........
Resolving dependencies...
（中略）
Fetching slim 4.1.0
Installing slim 4.1.0
Fetching slim-rails 3.5.1
Installing slim-rails 3.5.1
Fetching html2slim 0.2.0
Installing html2slim 0.2.0
Bundle complete! 20 Gemfile dependencies, 85 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```

- これで、今後 Rails のコマンドを通じて作成されるビュー層のテンプレートファイルは、Slim形式で作成されるようになりました。
- テンプレートエンジン Slim を利用するための設定
    1. エディタでアプリケーションフォルダ（taskleaf）直下にある Gemfile を開く。
    2. Gemfileの最後の行に、Slimのジェネレータを提供してくれる slim-rails という gem とERB形式のファイルをslim形式に変換してくれるerb2slimコマンドを提供してくれる html2slim という gem を追加するためのコマンドを書く。

```ruby
# 最後の行のコマンド
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]

# gemを追加するためのコマンド
gem 'slim-rails'
gem 'html2slim'
```

- 只、現時点でapp/views/layoutsディレクトリの中にERB形式のファイルが 3 つ存在します。このファイルは、3-1-6で詳しく扱いますが、アプリケーションの各画面の共通的な大枠の部分を記述する「レイアウト」のためのテンプレートファイルです。このファイルを、erb2slimコマンドを利用して Slim に変更しておきましょう。ここでは、Gemfileで指定されたgem環境の上でコマンドを実行できるように、bundle exec [コマンド] という形で入力します。


```ruby
$ bundle exec erb2slim app/views/layouts --delete
```


### 3 - 1 - 6 アプリケーションの見栄えを良くするために Bootstrap を導入する

- Railsにはデフォルトで特定のデザイン（CSSなど）は含まれていません。せっかくアプリケーションを作るからには見た目の良いアプリケーションにしたいものですが、自分で一から画面のタイトル、メニューバー、ボタン、リンクといった要素を格好よくデザインして CSS を書くということは、とても骨の折れる作業です。そこで、本書では [Bootstrap](https://getbootstrap.com/) （ブートストラップ）というフロントエンドフレームワークを利用します。手軽にほと良く見栄えの良い画面を作成することができるため、実際の開発現場でも Bootstrap はしばしば利用されています。
- bootstrapという gem を追加することで、Bootstrapを利用できるようになります。エディタで Gemfile を開いて、末尾に以下の行を追記してください。

```ruby
# 末尾に以下の行を追記する。
gem 'bootstrap'
```


- Gemfile を保存したら、インストールを行います。

```ruby
$ bundle

# 実際の表示
bundle
（中略）
Fetching bootstrap 5.2.3
Installing bootstrap 5.2.3
Bundle complete! 21 Gemfile dependencies, 90 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
```

- これで、Bootstrapのインストールができました。続いて Bootstrap の CSS を各画面のテンプレートに読み込ませてみましょう。
- rails new をした直後では、Railsはアプリケーション全体で 1 つのCSSファイル（app/assets/stylesheets/application.css）を読み込むようになっていて、他のCSSファイルは application.css からさらに読み込む形で記述することになります。従ってこれから利用する Bootstrap も、application.cssから読み込むようにしていきます。
- ところで、前述した Slim と同じように、CSSにも効率良く表現できる形式として [Sass](https://sass-lang.com/) がよく利用されます。そこで本書では、Sassが提供する「SCSS」と呼ばれる記法で CSS を記述することにします。そのため applicaiton.css をそのまま編集するのではなく、SCSSファイル（application.scss）を作成しましょう。SCSSファイルは最終的にプロセッサにより CSS に変換されてアプリケーションから利用されます。
    
    ※また、application.scssにインポートされた他のSCSSファイルは、最終的に変換された application.css に統合されます。これらは 6 章で紹介するアセットパイプラインという仕組みにより実現されています。
    
- 先ず、app/assets/stylesheets/application.cssを削除します。

```ruby
# rmコマンドでファイルやディレクトリを削除する。
$ rm app/assets/stylesheets/application.css
```

- 代わりに、エディタを開いて以下の内容のapp/assets/stylesheets/application.scss（拡張子はcssではなくscssにするように気を付ける）を作成します。

```ruby
@import "bootstrap":
```


- これで application.scss を用意できました。この application.scss （を通じて自動的に生成されるapplication.css）が、アプリケーション内の各画面の HTML から呼ばれるようにすることで、画面を Bootstrap のデザインが当たった状態にすることができます。
- それでは、これから作る全ての画面のslimファイルに、application.cssの読み込みを定義する必要があるのでしょうか。
- 幸い、Railsには各画面の共通的な大枠について記述するためのレイアウトという仕組みがあり、アプリケーションを作った段階で 1 つデフォルトのレイアウトファイルが用意されています。3-1-5で application.html.erb からslim形式に変換した application.html.slim がこれに当たります。このレイアウトファイルで application.css を読み込めば、各画面のslimファイルに定義せずとも全ての画面に Bootstrap のデザインを反映することができます。
- レイアウトファイルは、アクションごとに指定することができますが、個別の指定がなければコントローラ名に対応するものとなります。コントローラ名に一致するレイアウトファイルが見つからない場合は、継承しているコントローラクラスの名前を順に探していきます。Railsでは、基本的に全てのコントローラが ApplicationController を継承するように作るため、application.html.slimというレイアウトだけが存在している状態ならば、デフォルトですべてのアクションに対してこの共通レイアウトが利用されるというわけです。そして、同じ「application」という名前が冠されていることから推測できるとおり、application.html.slimはデフォルトで application.css を読み込むような内容になっています。
- gemでアプリケーションに追加した Bootstrap のデザインが各画面に実際に反映されるまでの流れはかなり複雑なので経路を図解しておきます。是非、本節で行った各種の手順が図の中のどこに該当するかを確認してみてください。


- ここまでで、各画面に Bootstrap のデザイン（CSS）を反映できる状態となりました。只、Bootstrapのデザインの大部分は、HTMLのタグに特定の部品に対応するCSSクラスをつけることで初めて実現できるので、今のままでは画面の見栄えが大きく変わるわけではありません。そこで、見栄えを具体的に改善するために、Bootstrapのコンテナという部品を使うことにします。また、HTMLの title や、画面の見出しなども整備しておきましょう。そのためには、app/views/layouts/application.html.slimファイルを次のように変更してください（図解①）。

```ruby
doctype html
html
  head
    title
      | Taskleaf
    = csrf_meta_tags
    = csp_meta_tag
    = stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload'
    = javascript_include_tag 'application', 'data-turbolinks-track': 'reload'
  body
    = yield

# 以下のように内容を変更する。
doctype html
html
  head
    title
      | Taskleaf
    = csrf_meta_tags
    = csp_meta_tag
    = stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload'
    = javascript_include_tag 'application', 'data-turbolinks-track': 'reload'
  body
    .app-title.navbar.navbar-expand-md.navbar-light.bg-light
      .navbar-brand Taskleaf
    .container
    = yield
```

- コード内に登場する `'data-turbolinks-track': 'reload'` という箇所は、<link>要素や<script>要素に、 `<link data-turbolinks-track=”reload”…>` といった Turbolinks のための属性を付与しています。Turbolinksについては Chapter8-3で詳しく説明します。
- Bootstrapは、CSSだけではなく JavaScript も含んでおり、Bootstrapの JavaScript を使う場合はもう少し設定が必要となります。本章では不要なため省略しています。

※JavaScriptの設定方法については [https://github.com/twbs/bootstrap-rubygem](https://github.com/twbs/bootstrap-rubygem) に書かれた説明を参考にしてください。

### 3 - 1 - 7 Railsのエラーメッセージなどを日本語で出せるようにする

- Railsにはよくあるエラーメッセージなどのコンテンツが含まれますが、それらは英語になっており、英語以外の言語を使いたい場合は、言語ごとの翻訳を追加して使う必要があります。また、デフォルトで利用する言語も初期状態では英語に設定されています。
- 本章で作成するtaskleafアプリケーションは日本語のアプリケーションにしたいので、日本語の翻訳を追加して、利用する言語を日本語に設定しましょう。
- 先ずは、エラーメッセージなどのコンテンツの日本語翻訳ファイルが必要となります。自身で作成することも可能ですが、GitHubのrails-I18nリポジトリに既に翻訳されたファイルがあるのでそれを利用するのが手っ取り早いでしょう。rawファイルを config/locales/ja.yml としてダウンロードします。

※ [https://github.com/svenfuchs/rails-i18n/blob/master/rails/locale/ja.yml](https://github.com/svenfuchs/rails-i18n/blob/master/rails/locale/ja.yml)

- 日本語の翻訳を追加する方法
    1. GitHubから直接コピーする
        
        [https://github.com/svenfuchs/rails-i18n/blob/master/rails/locale/ja.yml](https://github.com/svenfuchs/rails-i18n/blob/master/rails/locale/ja.yml) に書かれている内容をコピーして、自身のプロジェクトに ‘/locale/ja.yml’ を作成してコピーした内容をペーストする。
        
    2. wgetコマンドでファイルをダウンロードする。
        
        以下のコマンドを実行して、rawファイルを config/locales/ja.yml としてダウンロードする。
        
        ```ruby
        $ wget https://raw.githubusercontent.com/svenfuchs/rails/i18n/master/rails/locale/ja.yml -P config/locales/
        ```
        
- 続いて、デフォルトで日本語のコンテンツを使うようにアプリケーションの設定を変更します。エディタを開き、config/initializers/locale.rbというファイルを作成して、以下の行を記入してください。

```ruby
Rails.application.config.i18n.default_locale = :ja
```

- config/initializers/locale.rbファイルを作成する方法
    1. エディタを開き、taskleaf/config/initializersをクリック。
    2. 新しいファイルを……を選択して、locale.rbと名付けてファイルを作成する。
    3. テキストに載っているコードを書く。
    
    ```ruby
    Rails.application.config.i18n.default_locale = :ja
    ```
    
- これでアプリケーションを作成する下準備が整いました。
- 尚、ここで設定した config.i18n.default_locale や、コンテンツのymlファイルは、Railsアプリケーション内で複数の言語を併用する仕組み、すなわち国際化（Internationalization（←18文字）、アルファベットの数をとってi18nと略される）にも関係が深いものです。国際化については 6 章で改めて説明します。</details>


<details><summary>Chapter 3 - 2 タスクモデルを作成する</summary>

Chapter 3 - 1 でアプリケーション作成の下準備が完了しました。いよいよ、本格的なアプリケーションの開発を行なっていきます。

Railsでは、コントローラやビューからモデルを使うプログラムを記述することになります。そのため、開発手順としては、全体的な設計や下準備の後、先ず機能に関連するモデルを用意するという手順が一般的です。

Chapter 3 - 1 で設計したとおり、私たちはこれからタスクの登録や削除といったタスク管理の機能を実装していきます。そこで、先ずは操作の対象となる「タスク」モデルの実装から始めましょう。最初は、モデルのクラスの名前を決めます。ここでは素直に「Task」と名付けることにします。

Railsの「モデル」は、主に次の 2 つの要素から構成されます。

- **モデルに対応する Ruby のクラス**
- **モデルに対応するデータベースのテーブル**

モデルのクラス名とデータベースのテーブル名には以下のような命名の規約があります。

- **データベースのテーブル名はモデルのクラス名を複数形にしたもの**
- **モデルのクラス名はキャメルケース、テーブル名はスネークケース**

上記のルールに従い、モデルのクラス名を Task と決めたので、対応するデータベースのテーブル名は tasks ということになります。

次に、Taskモデルの抱えるデータ、つまり属性について検討していきます。モデルの属性は、基本的にデータベースのテーブル内のカラムに対応します。そこで、tasksテーブルにどのようなカラム（列）を持たせるかも合わせて考えることになります


### 3 - 2 - 1 タスクモデルの属性を設計する

- タスクモデルにはどんな属性が必要になるのでしょうか。それを考えるには、Chapter 3 - 1 で作成した各画面の使用をもう一度確認する必要があります。例えば、詳細画面については次のような使用を作成していました。
    - ある 1 つのタスクの全内容（ID、名称、詳しい説明、登録日時、更新日時）を確認できる。
- どうやら、タスクモデルには「ID」「名称」「詳しい説明」「登録日時」「更新日時」の 5 つの属性が必要そうです。
- 5つの属性のうち、「ID」「登録日時」「更新日時」の 3 つの属性は一般的にモデルでよく利用される属性なので、Railsが自動的に用意してくれます。そのため、私たちは残りの「名称」と「詳しい説明」について検討し、これらの 2 つの属性をタスクモデルに追加すれば良いということになります。
- 先ほど述べたように、属性はデータベースのテーブルのカラムに対応します。そこで、カラムについて検討する必要がありますが、その際はカラム名、データ型、NULLが入ることを許容するか、デフォルト値を設定するかなどを決める必要があります。また、データベースのことだけではなく、Rubyの中で利用するための検討も併せて行う必要があります。例えば、カラム名は属性名としても利用されるため、モデルクラスの中で意味のわかりやすい Ruby 的な名前を付けたいものです。このように、データベース・Rubyの両方の都合を勘案しながら、これらの要素を検討していきます。
- 先ずは「名称」について考えてみましょう。ここでは、カラム名・属性名は name 、データ型は文字列（string）、必須（NULLが入ることを許容しない）、デフォルト値なしということにしたいと思います。
- 続いて「詳しい説明」についても検討します。ここでは、カラム名・属性名は description 、データ型は文字列、必須でない（NULLが入ることを許容する）、デフォルト値なしにします。
- 尚、文字列型には、短い文字列を扱う「string型」と長い文字列を扱う「text型」の 2 種類が存在します。「名称」にはstring型、「詳しい説明」にはtext型を使うことにしましょう。

※ここで登場する string や text という型は、RDMS（Relational Database Management System、リレーショナルデータベース管理システム）の用意しているデータ型そのものではなく、Railsが RDMS ごとの差異を吸収した抽象的な型として用意している概念のこと指しています。利用時は、この Rails の用意している型が、実際に使う RDMS のデータ型にマッピングされます。

- それでは、今決めたことを表の形に整理してみましょう。実際の開発現場でも、モデルの設計時はこのような検討結果を表の形に整理して、チーム内で意見交換することがよくあります。

| 属性の意味 | 属性名・カラム名 | データ型 | NULLを許可するか | デフォルト値 |
| --- | --- | --- | --- | --- |
| 名称 | name | string | 許可しない | なし |
| 詳しい説明 | description | text | 許可する | なし |

### 3 - 2 - 2 タスクモデルの雛形を作成する

- 設計ができたので、タスクモデルを実装していきましょう。Railsには、モデルを作成するための便利なジェネレータがあるので、それを利用しましょう。

※ジェネレータを使えば、モデル以外にも、コントローラやマイグレーション、2章で取り上げた scaffold などを作成することができます。

- ジェネレータは、rails g の後に生成したいものの種類（ここではmodel）を指定し、さらに生成したいものの種類ごとの必要な情報を添えて実行します。モデルを生成する場合のコマンドは次のような構成になります。

※rails g は rails generator の短縮系です。

```ruby
$ bin/rails g model [モデル名] [属性名:データ型 属性名:データ型 ...] [オプション]
```

- 今回はタスクモデルを作りたいので、[モデル名]にはTask、[属性名 :データ型]の部分には name:string description:text を指定します。尚、ファイル名の先頭の数字はコマンド実行時によって作られるため以下では xxx… で表現しています。

```ruby
$ bin/rails g model Task name:string description:text

# 実際の表示
bin/rails g model Task name:string description:text
Running via Spring preloader in process 35265
      invoke  active_record
      create    db/migrate/20221203053345_create_tasks.rb # ファイル名の先頭の数字はコマンド実行時によって作成される。
      create    app/models/task.rb # モデルクラスのソースコード
      invoke    test_unit
      create      test/models/task_test.rb # モデルの自動テスト
      create      test/fixtures/tasks.yml # モデルの自動テストで使うfixtureファイル
```

- コマンドを実行すると、モデルのクラスファイル、マイグレーションファイル、モデルの自動テストのファイルの雛形が自動作成されます。生成されるファイルの意味は次のようになります。

| 生成されるものの種類 | 本節でのファイルパス | 用途 |
| --- | --- | --- |
| モデルクラスのソースコード | app/models/task.rb | Taskクラスの実装 |
| マイグレーションファイル | db/migrate/20221203053345_create_tasks.rb ※マイグレーションファイルの数字部分は、マイグレーションファイルの登録日時を表すので、コマンドを実行したタイミングによって異なります。 | データベースにtasksテーブルを追加する |
| モデルの自動テスト | test/models/task_test.rb | Taskクラスについての自動テストの実装 |
| モデルの自動テストで使うfixtureファイル | test/fixtures/tasks.yml | Taskクラスについての自動テストのためのデータ投入の定義 |

### 3 - 2 - 3 マイグレーションでデータベースにテーブルを追加する

- Railsのモデルクラスは、データベースのテーブルの定義を読み込んで動作します。そのため、モデルを作成する場合は、先ずデータベースのへのテーブルの追加から行うとスムーズです。
- データベースにテーブルを追加するには、Railsの用意しているマイグレーションという仕組みを使います。マイグレーションの主眼は、データベーススキーマ（テーブル構造など）への変更の 1 つ 1 つを、Rubyのプログラムとして実現し、開発の歴史に沿って順番に実行することで最新のスキーマの状態にできるようにすることです。この 1 つ 1 つの変更が 1 つ 1 つのマイグレーションファイル（以下、マイグレーションと呼びます）に対応します。マイグレーションは、スキーマの歴史を進めるだけではなく、戻す機能も備えているので、例えば「 1 ヶ月前のコードの状態でアプリケーションを動かしたい」といった時にも、必要なところまでデータベースのスキーマの状態を戻すことができます。また、複数の開発者がほとんど同時に別々のスキーマ変更を行なっているようなケースでも、混乱なく、必要な変更だけをデータベースに反映することができます。
- ここでは、Taskモデルの永続化を行うための tasks というテーブルを追加したいので、「tasksテーブルを追加する」というマイグレーションファイルが必要です。先程 generator でTaskモデルを作成した際に作成された db/migrate/XXXXXXXXXXXXXX_create_tasks.rb というファイルの中身を見てみましょう。

※マイグレーションファイルは generator でモデルを作成する以外にも、generatorでマイグレーションファイルの雛形を生成して自分で内容を記述する方法でも作成することができます。

```ruby
class CreateTasks < ActiveRecord::Migration[5.2]
  def change # changeメソッド作成。
    create_table :tasks do |t| # tasksという名前のテーブルを作成。
      t.string :name 
      t.text :description # tasksテーブルにはnameとdescriptionのカラムを備えている。

      t.timestamps # 打刻用のカラム(timestampsと指定するとcreated_atやupdated_atといった打刻用のカラムが生成されます)を持っている。
    end
  end
end
```

- changeというメソッドの中に、tasksというテーブルを作ること、tasksというテーブルが name と description のカラムを備えること、打刻用のカラム（timestampsと指定するとcreated_atやupdated_atといった打刻用のカラムが生成されます）を持つことなどが記述されています。幸い、私たちが必要としてる最低限の要件を満たしているので、このマイグレーションファイルは編集を加えずに、このままマイグレーションを実行して、データベースにtasksテーブルを追加します。

※厳密には、事前に行った設計で、名称では NULL を許可しないことにしていますが、自動生成されたコードはこの仕様には沿っていません。これについては Chapter 4 - 2 で詳しく説明します。

- マイグレーションをデータベースに適用するには、rails db:migrateコマンドを実行します。

```ruby
# マイグレーションファイルをデータベースに適用するコマンド。
$ bin/rails db:migrate

# 実際の表示。
bin/rails db:migrate
== 20221203053345 CreateTasks: migrating ======================================
-- create_table(:tasks)
   -> 0.0093s
== 20221203053345 CreateTasks: migrated (0.0094s) =============================
```

- これで、データベースにtasksテーブルが追加されました。
- モデルが動作するには、データベースとモデルクラスの両方が必要です。モデルクラスについては先程 generator で自動作成した雛形のファイル app/models/task.rb があるので、内容を確認してみましょう。

```ruby
class Task < ApplicationRecord
end
```

- このファイルはクラスを定義しているだけで、ほとんど何もコードがありませんが、十分な機能を備えています。Taskの親クラスの親クラスにあたる ActiveRecord::Base （app/models/application_record.rbファイル内にあるコード）が、tasksテーブルの構造に対応した属性の読み書きや、データベース操作などの様々な機能を提供してくれるからです。現時点ではこれで十分なので、何も編集せず、モデルの用意を一旦終わりとしましょう。</details>


<details><summary>Chapter 3 - 3 コントローラとビュー</summary>

モデルが作成できたので、続いてコントローラとニューを実装して、ブラウザ上で各操作を行えるようにしていきましょう。モデルと同じように、コントローラやビューの雛形もジェネレータで作成することができます。そのためのコマンドは次のような形になります。

```ruby
$ bin/rails g controller コントローラ名 [アクション名 アクション名 ...] [オプション]
```

- 最低限、コントローラを決めればジェネレータを動かすことはできますが、効率的に雛形を作るには、セットで画面の雛形を作れるように、アクションについて先に考えておくとスムーズです。アクションとはリクエストに対しての処理を実行するためのメソッドのことです。具体的にはモデルを呼び出したり、画面を表示したりします。そこで、先ずは必要なアクションについて考えていきましょう。
- 今回作りたいものは、タスクのCRUD機能です。CRUDはよくある機能のため、アクションに関する設計は完全にパターン化されています。そのパターンに沿ってタスクの CRUD のアクションを設計すると、次のようになります。

| URLの例 ※idの例として「17」を利用 | HTTPメソッド | アクション名 | 機能名 | 役割 |
| --- | --- | --- | --- | --- |
| /tasks | GET | index | 一覧表示 | 全タスクを表示する |
| /tasks/17 | GET | show | 詳細表示 | 特定の id のタスクを表示する |
| /tasks/new | GET | new | 新規登録画面 | 新規登録画面を表示する |
| /tasks | POST | create | 登録 | 登録処理を行う |
| /tasks/17/edit | GET | edit | 編集画面 | 編集画面を表示する |
| /tasks/17 | PATCH、PUT | update | 更新 | 更新処理を行う |
| /tasks/17 | DELETE | destroy | 削除 | 削除処理を行う |
- URL、HTTPメソッド、アクション名と、意識する必要がある項目が多くなってきました。URLはインターネット上の番地、HTTPメソッドはブラウザからアプリケーションにアクセスする際の大まかな要求のようなものです。Webアプリケーションをユーザーが使う画面では、先ず、ユーザーのWebブラウザからアプリケーションに向けて、特定の URL を指定して、特定のHTTPメソッドでHTTPリクエストを送ります。これに対して、アプリケーションは、URLとHTTPメソッドの組み合わせから、リクエストを処理すべきコントローラとアクションを特定して（この特定処理を「ルーティング」と呼びます）、特定したアクションに処理をさせ、結果のHTTPレスポンスを作ってブラウザに返します。


- なぜ各機能の URL やHTTPメソッドを前述の図のように設計するのが Rails のパターンなのかというと、RESTfulという考え方に基づいているためです。詳しくは 6 章で説明するのでここでは、以下の 2 つを理解するようにしましょう。

※REST(REpresentational State Transfer)という設計思想に基づいたシステムを RESTful であるといいます。

1. リクエストを処理するコントローラとアクションは、ブラウザからのリクエストに含まれる URL とHTTPメソッドによって決定される。
2. コントローラのアクションを設計する際には、入り口となる URLとHTTPメソッドを併せて考える必要がある。
- 前置きが長くなりましたが、開発するアクションの全容をはっきりさせたので、ジェネレータでコードの雛形を作ってみましょう。controllerのジェネレータでアクションを指定すると、アクションと同名のビューも一緒に作ってくれます。ちょっとしたコツとしては、HTTPメソッドが GET のアクションは同盟のビューを使うことが多いという法則があるため、HTTPメソッドが GET となるアクション名をジェネレータコマンドの引数として指定するのが効率的です。

※GETのアクションは、基本的に情報（リソース）の取得へ対応するリクエストなので、アクションの仕事は要求された情報を含む画面を表示することが中心になります。そのため、同名のビューを利用することが多くなります。GETでないアクションは、情報（リソース）の操作を行うリクエストであるため、操作後に情報参照用の画面にリダイレクトすることが多く、その場合はアクションと同名のビューが不要になりやすいという傾向があります。

- そこで、前ページの表を参考に、GETのアクションであるindex、show、new、editの 4 つのアクションを指定してジェネレータコマンドを実行してみましょう。

```ruby
# GETのアクションである、index、show、new、editの4つのアクションを指定してジェネレータコマンドを実行する。
$ bin/rails g controller tasks index show new edit

#実際の表示
bin/rails g controller tasks index show new edit
Running via Spring preloader in process 40165
      create  app/controllers/tasks_controller.rb
       route  get 'tasks/index'
              get 'tasks/show'
              get 'tasks/new'
              get 'tasks/edit'
      invoke  slim
      create    app/views/tasks
      create    app/views/tasks/index.html.slim
      create    app/views/tasks/show.html.slim
      create    app/views/tasks/new.html.slim
      create    app/views/tasks/edit.html.slim
      invoke  test_unit
      create    test/controllers/tasks_controller_test.rb
      invoke  helper
      create    app/helpers/tasks_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/tasks.coffee
      invoke    scss
      create      app/assets/stylesheets/tasks.scss
```

- これでコントローラとビューの雛形を作成できました。ところで、この方法では、Railsは 4 つのアクションについて個別にルーティングの設定を追加してくれます。しかし、今回は CRUD というよくあるパターンについて、パターン通りのルーティングを一括で設定したいので、config/routes.rbからこれらの 4 つのアクションについての設定を消してください。

```ruby
# CRUDというよくあるパターンについて、パターン通りのルーティングを一括で設定するため、config/routes.rbから以下の4つのアクションの設定を削除する。
get 'tasks/index'
get 'tasks/show'
get 'tasks/new'
get 'tasks/edit'
```


- 代わりに、次の設定を追加します。

```ruby
resources :tasks
```


- 追加したresourcesメソッドは、前ページの表で示した全てのアクション（index、show、new、create、edit、update、destroy）に関するルーティングを一括で設定してくれます。
- また、ついでにアプリケーションの玄関口であるルートパス、「/」（ローカルで実行している場合は [http://localhost:3000](http://localhost:3000) に対応します）にアクセスしたときに、Railsのデフォルト画面ではなくタスクの一覧が表示されるようにしましょう。それには、次のように routes.rb に設定を足します。

※Railsではデフォルトで 3000 番の port が指定されますが、「rails s -p 3001」と-pオプションでポート番号と指定することができます。この場合 [http://localhost:3001](http://localhost:3001) でアプリケーションが立ち上がります。

```ruby
# Railsのデフォルト画面にアクセスしたときに、デフォルトではなく、タスクの一覧が表示されるようにroutes.rbに設定を足す。
root to: 'tasks#index'
```

- 結果、routes.rbファイルは次のようになります。

```ruby
Rails.application.routes.draw do
  root to: 'tasks#index'
  resources :tasks
end
```

- それでは、一度サーバーを停止して再起動してからブラウザで [http://localhost:3000](http://localhost:3000) にアクセスしてみましょう。すると今までのRailsデフォルト画面と違って、Tasks#indexと表示された画面が現れます。少しだけ、タスク管理アプリケーションっぽくなってきましたね。


### 3 - 3 - 1 新規登録機能を実装する

- まず、タスクの新規登録について考えていきましょう。
- ユーザーの操作としては、まず一覧画面が表示され、そこに「新規登録」のリンクがあるのでそれをクリックして新規登録画面（登録フォーム）に遷移します。この新規登録画面に値を入力して「登録する」ボタンを押すと、タスクが登録されます。
- タスクの新規登録に関わる各機能の動きは、次のようになります。


- このような動きをするためには、それぞれのアクションや画面が次のようになっている必要があります。尚、カッコで括っているところは、新規登録機能を実装した後で、順次取り組んでいきます。
- それでは、一覧から順番に開発をしていきましょう。


### 3 - 3 - 1 - 1 一覧画面に新規登録リンクを追加する

- 一覧画面（app/views/tasks/index.html.slim）に、新規画面に遷移するためのリンクを設置します。その際は、btnと btn-primary という 2 つのcssクラスを与えます。こうすることで、bootstrapがリンクをボタンのような外見にしてくれます。

```ruby
h1 タスク一覧

= link_to '新規登録', new_task_path, class: 'btn btn-primary'
```


- Slimテンプレートにおいて、イコールで始まる行は、Rubyのコードを実行してその結果を出力するという意味になります。
- コードを実行したいだけで画面に出力したくないという場合は、イコールの代わりにハイフンから行を始めます。
- link_toメソッドは、 `<a href=”…”>新規登録</a>` というような HTML の要素を出力するメソッドです。それぞれの引数は次のように機能します。


- new_task_pathというのはURLヘルパーメソッドで、これを呼び出すことで “/tasks/new” というURL文字列が得られます。URLヘルパーメソッドは、routes.rbの定義によって自動的に生成されます。詳しくは Chapter 6 - 2 「ルーティング」で説明します。
