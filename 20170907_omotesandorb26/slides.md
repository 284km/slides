
## Web Application Framework の良さについて考えてみた
### + おまけ
### 秒速@284km
##### 表参道.rb #26 ~Webフレームワーク~

![goku.jpg](../goku.jpg)

---

# 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
  - Ruby/Rails 中心の生活
  - サービスを作る人
- Rails Developers Meetup 運営

---

# Web Aapplication Framewark

---

# WAF

長いので以降は WAF

---

# WAF とは？

https://ja.wikipedia.org/wiki/Webアプリケーションフレームワーク

wikipedia にはこう書いてあった。長い。読んではいない

Web アプリケーションフレームワーク（ウェブアプリケーションフレームワーク、英: Web Application Framework）は、動的な ウェブサイト、Webアプリケーション、Webサービスの開発をサポートするために設計されたアプリケーションフレームワークである。 フレームワークの目的は、Web開発で用いられる共通した作業に伴う労力を軽減することである。たとえば、多数のフレームワークがデータベースへのアクセスのためのライブラリや、テンプレートエンジン（→Webテンプレート）、セッション管理を提供し、コードの再利用を促進させるものもある。

---

# ふむ。

---

# まずは

---

### WAF のこういうところが良いと思う
あるいは
### こういう WAF が良いと思う

- 安全が誰でも確保される。たとえば
  - デフォルトで HTML エスケープ とかされる
  - POST 時に CSRF 対策の自動チェックとかしてくれる
  - 危険なこと(コード)が簡単には出来ない(書けない)ようになっていたり
- 同じことを何度も書く必要がない
- 十分にはやい

---

# 必要な構成

この辺り、ほしいと思う。

- ディスパッチャ
- （コントローラ)
- DB アクセスできる何か
- ビュー

---

# 流れ

- browser だったり、browser じゃなかったり
- -> request
- -> ディスパッチャ(routing)
  - url mapping
  - controller を探して action() 実行
- コントローラ
  - model に作用したり
  - render view したり
- ビュー
  - render

---

# 書いてみましょう

rack アプリケーションにちょっとずつ足すやつ

(実演)

---

### rack application

```rb
# rack application
require 'rack'
Rack::Server.start(
  app: ->(env) { [200, {}, ["OK\n"]] },
  Port: 9292,
  environment: 'development'
)
```

---

### おもむろに action_dispatch を使ってみる

```
require 'action_dispatch'

# header もつけてみた
header = {'Content-Type' => 'text/html'}

routes = ActionDispatch::Routing::RouteSet.new
routes.draw do
  root to: -> env { [200, header, ['OK\n']]}
  get 'hello', to: ->(env) { [200, header, ['hello']] }
end

Rack::Handler.default.run routes, Port: 3000
```

---

### view 層を用意? template engine は erb

```
require 'action_dispatch'
require 'erb'

header = {'Content-Type' => 'text/html'}

def render(template)
  path = File.expand_path("../views/#{template}", __FILE__)
  ERB.new(File.read(path)).result(binding)
end

routes = ActionDispatch::Routing::RouteSet.new
routes.draw do
  root to: -> env { [200, header, ['OK\n']]}
  get 'hello', to: ->(env) {
    # erb を使ってみた
    Rack::Response.new(render("index.html.erb"))
  }
end

Rack::Handler.default.run routes, Port: 3000
```

---

### controller class (と言い張る) を追加してみる

```
require 'action_dispatch'
require 'erb'

# controller と言い張るそれっぽいものを追加してみた
class OmotesandoController
  def initialize
  end

  def index
    Rack::Response.new(render("index.html.erb"))
  end
end

header = {'Content-Type' => 'text/html'}

def render(template)
  path = File.expand_path("../views/#{template}", __FILE__)
  ERB.new(File.read(path)).result(binding)
end

routes = ActionDispatch::Routing::RouteSet.new
routes.draw do
  root to: -> env { [200, header, ['OK\n']]}
  get 'hello', to: ->(env) {
    # controller と言い張るそれっぽいものを追加してみた
    OmotesandoController.new.index
  }
end

Rack::Handler.default.run routes, Port: 3000
```

---

### モデル層を用意しようとしたが、、、

用意できたのここまででした。。

- まずは db:create とか db:migrate とかしたい
- config/database.yml を用意して、
- Rakefile

```
require 'active_record'
require 'yaml'
require 'erb'
require 'logger'

desc "Migrate database"
task :migrate => :environment do
  ActiveRecord::Migrator.migrate(
    # default: ["db/migrate"]
    ActiveRecord::Migrator.migrations_paths,
    # target_version
    ENV["VERSION"] ? ENV["VERSION"].to_i : nil
  )
end

task :drop_table do
  require 'active_record'
  ActiveRecord::Base.establish_connection(
    :adapter => 'sqlite3',
    :database => 'db.sqlite3'
  )
  ActiveRecord::Migration.drop_table :samples
end

task :create do
  # create_samples.rb
  require 'active_record'
  ActiveRecord::Base.establish_connection(
    :adapter => 'sqlite3',
    :database => 'db.sqlite3'
  )
  ActiveRecord::Migration.create_table :samples do |t|
    t.string :body
    t.timestamps
  end
end

task :environment do
  dbconfig = YAML.load(ERB.new(File.read('config/database.yml')).result)
  ActiveRecord::Base.establish_connection(dbconfig[ENV['ENV']])
  ActiveRecord::Base.logger = Logger.new('db/database.log')
end
```

