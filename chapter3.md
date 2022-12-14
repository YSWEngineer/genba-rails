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
- 尚、ここで設定した config.i18n.default_locale や、コンテンツのymlファイルは、Railsアプリケーション内で複数の言語を併用する仕組み、すなわち国際化（Internationalization（←18文字）、アルファベットの数をとってi18nと略される）にも関係が深いものです。国際化については 6 章で改めて説明します。
