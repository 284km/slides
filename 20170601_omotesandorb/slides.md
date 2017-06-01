
## Ruby 入門の薄い本
### を目指して書き始めました (*ﾉω・*)ﾃﾍ
### 〜 道半ば 〜
### 秒速@284km
##### 表参道.rb #23 ~ Ruby/Railsの学び方 ~

![goku.jpg](../goku.jpg)

---

# 秒速@284km
![goku.jpg](../goku.jpg) 

- feedforce エンジニア
  - Ruby/Rails 中心の生活
  - サービスを作る人
---

# みなさん
# こんにちは

---

# Hello World!

```.rb
# hello.rb
puts("Hello, World!")

$ ruby hello.rb
#=> Hello, World
```

---

# 今日 Ruby を覚えたくて来ている方 ✋

---

# はじめての Ruby
# Yugui さん著

この本で Ruby に入門しました。
およそ 200 ページという薄さで必要な内容が纏まっているスゴイ本
（Ruby 1.8, 1.9 対応）

---

# Ruby 以前
# （私の場合）

- C
- Java
- Perl
- JavaScript
- とか
- COBOL も書いた

---

# 今日話すこと

- ### Ruby の良さそうな学び方（メイン）
- ### Rails の良さそうな学び方について少し

---

# 前提

- ### ~~初めてプログラミングをする方向け~~
- ### 初めて Ruby について学ぶ方向け
- 経験則で話します

---

# 経験則

- 自分で実際に書くのが学習効果が高い
- 読んで悩み続けるより、書きはじめると学習速度が上がっていく
- ### なので、必要なことを素早く取り入れて、何か書ける状態に早めに持っていけたら良さそう

---

# ではどうするか
## 順に見ていきます

---

# irb
## 対話的実行環境

irb を用いると、その場で対話的に Ruby
プログラムを実行することができます。便利。

```
$ irb
irb(main):001:0> 1+1
=> 2
```

---

# print デバッグ

式の値をちょっと見てみたい時に、irb と共に重宝します。

```
p "foo"  #=> "foo"
p 123    #=> 123
p 1 == 1 #=> true
```

---

# 配列

必要

```
a = 1
b = "str"
c = [a, b, 3, "string"] #=> [1, "str", 3, "string"]

p c[0]    #=> 1
p c[1]    #=> "str"
p c[-1]   #=> "string"
p c[0..1] #=> [1, "str"]
c[0] = 9  #=> 9
c[0]      #=> 9
```

---

# イテレータ

必要

```
foo = ["a", "b", "c"]
foo.each do |item|
  print item + " "
end
#=> a b c 

# 配列の各々の要素についてブロックを評価して、それらの評価値を纏めて新しい配列を構築します
foo.map{|f| f.upcase} #=> ["A", "B", "C"]

# &: とかググり難い
foo.map(&:upcase)

# ソート
bar = [3, 2, 5, 4, 1]
bar.sort #=> [1, 2, 3, 4, 5]
```

---

# ハッシュ

必要

```
months = { Jan: 1, Feb: 2, Mar: 3 }
months[:Feb] #=> 2
```

---

# 数値

## 比較 演算子

必要

```
1 == 1
1 ~= 1
1 < 1
1 <= 1
# 宇宙船演算子
1 <=> 2 #=> -1
1 <=> 1 #=> 0
3 <=> 2 #=> 1
```

---

# 文字列

## 式展開

- 二重引用符("")で括られた文字列内部では式展開が行われます
- 一重引用符('') の場合は行われません

```
a = 2
"a = #{a}" #=> "a = 2"
"a + 2 = #{a + 2}" #=> "a + 2 = 4"
```

---

# 文字列

## パーセント記法

検索して調べ辛いと思うシリーズ

```
%w(foo bar baz)
#=> ["foo", "bar", "baz"]
```

---

# 文字列

## ヒアドキュメント

検索して調べ辛いと思うシリーズ

```
s = <<"EOS"
sample
text
hello
EOS

#=> "sample\ntext\nhello\n"
```

---

# 変数

必要。説明は端折ります。

- ローカル変数
- インスタンス変数
- クラス変数
- グローバル変数
- 組み込み変数 ($$ とか、$ で始まる Perl っぽいの。）
- 定数

---

# 演算子と優先順位

- ### 必要な時、不安になった時に確認することになると思う。

---

# 制御式

必要

```
if foo
  ...
elsif bar
  ...
elsif baz
  ...
else
  ...
end

unless foo
  ...
end

case や while
```

---

# 脱出

break, next

```
1.upto(3) do |i|
  next if i == 2
  loop do
    puts "break するよ"
    break
  end
  # break でここに脱出するよ
  puts i
end
```

---

# 例外処理

必要そう

```
begin
  do_something # 例外を生じる可能性のある処理
rescue => error
  puts error.message
end
```

---

# メソッド

必要

```
def sum(x, y)
  x + y
end

sum(1, 2) #=> 3
```

---

# クラス

必要。クラス名は大文字で始まる。

```
class Foo
  def initialize(bar, baz) # 多言語で言うコンストラクタのようなもの
    @bar = bar # @ ではじまる名前の変数はインスタンス変数
    @baz = baz
  end
  attr_accessor :bar, :baz # 属性への getter, setter を定義する
end

foo = Foo.new(1, 2) # インスタンス化
p foo.bar #=> 1
p foo.baz #=> 2
```

---

# 継承

```
class Duration < Range
  ...
end
```

---

# モジュール

インスタンス化できないクラス のようなもの

```
module Foo
  def self.hello
    puts "hello"
  end
end
Foo.hello #=> hello
```

---

## インスタンスメソッド
## クラスメソッド

リファレンスマニュアルを読む時には知っておいた方が良い
(# と . の違い)
https://docs.ruby-lang.org/ja/latest/doc/index.html

- String#upcase 
- Time.now
- Kernel.#open

---

# Railsの学び方

---

# RailsTutorial 周回

おすすめ
感謝の Rails Tutorial 周回で強くなる。祈る時間が増える。

---

# Railsの学び方

- RailsTutorial 周回
- Ruby on Rails Guides
- Rails の教科書

---

# Rails の教科書

これは私は入門の時期にやった訳ではない。
比較的最近になってから読んだ。
rails tutorial が難し過ぎると感じて周りに聞ける人もいない状況などでは、Rails の教科書がとても良さそうに見える。

- プログラミング自体が初めての方を対象としているし、
- 環境構築についても易しく書かれている。

“はじめに” で書かれているとおり、Rails の教科書を読み進めると Rails Guides や Rails Tutorial を読み進める為の基礎知識が身につきます。というのが達成されそう。

---

# 別の視点で大事だと思うこと

- まわりに聞ける人がいたらより良いと思う。
- 聞ける人を学習前に作れたら便利で効果が高いと思う。
- もし悩みや困っていることがあれば、この場で聞くととても良いと思います。
- すごい人が身近にいると、かなりの時間短縮になるというのは私の経験談。
- カジュアルに発表したり話しかけたりして大丈夫。失敗してもメリットの方が大きいと思っている。

