
# Rails アンチパターン
### 秒速@284km
##### 表参道.rb #25 ~ Rails アンチパターン ~

![goku.jpg](../goku.jpg)

---

# 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
  - Ruby/Rails 中心の生活
  - サービスを作る人
- Rails Developers Meetup 運営

---

# 今日話すこと

- ## アンチパターン関連の tips

---

## View に文字列を直に書く

最初は楽かもしれない。しかし複数箇所に同じ内容を書いてしまうことを誘発する。すると修正が必用になった時に、直し漏れが発生したり。そもそもどの view に何があるのか管理するのは難しすぎる。

```rb
<%= "タイトル：" %>

とか

<%= "こんにちは。ご登録ありがとうございます。" %>
```

---

## I18n の辞書ファイルを使おう

```rb
# config/locales/ja.yml
ja:
  hello: "こんにちはこんにちは"

# view
<%= t(:title) %>
```

---

## migration は commit する前に rollback も確認しよう

いざ戻したい事態に陥った時に戻せないと最悪死ぬ。それにそういう時は人は慌てている。余裕のあるとき（余裕を作って）確認してからコミットすることで身を守ろう。

---

## bundle update をどこかのタイミングで一気にやろうとする

一気にやると、どこが変わった影響か判断するのが大変。ためれば貯めただけ、返済額は指数関数的に増加する感覚がある。金利が高い。

---

## default_scope

- 全部理解していないと、本来やりたかったこととずれていたり...
- unscope 用のメソッドを用意するというのも不毛
- 外したくなった時にかなり面倒
    - 対象箇所全てが影響範囲
    - 一気に全部直すしかない
    - やるならちゃんと全箇所のテストを書いて臨むべき

```
class User < ApplicationRecord
  default_scope { where(foo: 'bar') }
end
```

```rb
User.limit(3)
#=> SELECT `users.* FROM `users` WHERE `users`.`foo` = 'bar' LIMIT3

# default_scopeを無効にしたいときはこんな風に書く
User.unscoped.limit(3)
#=> SELECT `users.* FROM `users` 'bar' LIMIT3
```

---

## Rails AntiPatterns

- 無料で読める
- ちょっと古い。Rails のバージョンは 3 が対象
- だけど今読んでも十分に役立つエッセンスがたくさん。

![rails_antipatterns](https://user-images.githubusercontent.com/1458041/28918095-053e0df2-7883-11e7-9ac4-eb2e1e402836.png)

---

## 本書の最初の方を紹介します

---

変更に弱い例

```
class Address < ActiveRecord::Base
  belongs_to :customer
end
class Customer < ActiveRecord::Base
  has_one :address
  has_many :invoices
end
class Invoice < ActiveRecord::Base
  belongs_to :customer
end
```

---

変更に弱い例

欠点は、クラスに多くの小さなラッパーメソッドが散在していることです。
変更が必要な場合は、これらのラッパーメソッドをすべて維持する必要があります。

```
<%= @invoice.customer.name %>
<%= @invoice.customer.address.street %>
<%= @invoice.customer.address.city %>
<%= @invoice.customer.address.state %>
<%= @invoice.customer.address.zip_code %>
```

デメテルの法則
https://ja.wikipedia.org/wiki/%E3%83%87%E3%83%A1%E3%83%86%E3%83%AB%E3%81%AE%E6%B3%95%E5%89%87
簡潔に言うと「直接の友達とだけ話すこと」と要約できる。

---

delegate をうまく使いましょう

```
class Address < ActiveRecord::Base
  belongs_to :customer
end
class Customer < ActiveRecord::Base
  has_one :address
  has_many :invoices
  delegate :street, :city, :state, :zip_code, :to => :address
end
class Invoice < ActiveRecord::Base
  belongs_to :customer
  delegate :name,
           :street,
           :city,
           :state,
           :zip_code,
           :to => :customer, :prefix => true
end
```

---

こうなります。

```
<%= @invoice.customer_name %>
<%= @invoice.customer_street %>
<%= @invoice.customer_city %>,
<%= @invoice.customer_state %>
<%= @invoice.customer_zip_code %>
```

---

scope を活用しようというはなし。

この View に書かれたロジックは、アプリケーション全体で何度も複製され、保守性を下げる問題を引き起こすと言える。

```html
<html>
  <body>
    <ul>
      <% User.find(:order => "last_name").each do |user| -%>
        <li><%= user.last_name %> <%= user.first_name %></li>
      <% end %>
    </ul>
  </body>
</html>
```

PHP では実際に HTML ページに直接 SQL を置くことは珍しいことではありません。的な事が書いてある...

---

Controller に持ってくると、

```ruby
class UsersController < ApplicationController
  def index
    @users = User.order("last_name")
  end
end
```

View からは消えた。

```html
<html><body><ul>
<% @users.each do |user| -%>
  <li><%= user.last_name %> <%= user.first_name %></li>
<% end %>
</ul></body></html>
```

---

もうひとつこうすると、Model に収まった。

```
class UsersController < ApplicationController
  def index
    @users = User.ordered
  end
end

class User < ActiveRecord::Base
  scope :ordered, order("last_name")
end
```

---

## さいごに

### 人間がやらなくても良い部分はツールに頼るという手段も
### 考えること、やること減らせる部分は減らしていきたい

- ### rubocop
- ### rails_best_practices
- ### reek (使えてないけど、使いこなしたい)

---

# Rails Developers Meetup #4
# 8/24 (木)
# みんな来てね💖

