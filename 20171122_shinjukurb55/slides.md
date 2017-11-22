
## とある Ruby の私秘鏡裏
### 秒速@284km
##### Shinjuku.rb #55 Ruby "outside" Rails

![goku.jpg](../goku.jpg)

---

## 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
- Rails Developers Meetup

---

# Ruby "outside" Rails

---

# 仕事

Rails エンジニア。なのだと思う。

---

# #railsdm

やっている

---

## プライベート

あまり Rails じゃない ＼(^o^)／

---

## "outside" Rails とは...。

- Hanami ?
- sinatra ?
- padrino ?

---

## (´ε｀；)ｳｰﾝ…
## "outside"

---

## 趣味プログラミング

---

### 最近の発表

(全部趣味。Rails じゃないですね)

* [Brainf*ck をやってみた (omotesandorb27)](https://284km.github.io/slides/20171005_omotesandorb27/slides/#/)
* [おもに bison, flex での実装の説明 (shinjukurb54)](https://284km.github.io/slides/20171024_shinjukurb54/slides/#/)
* [Brainf*ck 亜種言語生成ライブラリ (gen_brain) を gem release します (omotesandorb28)](https://284km.github.io/slides/20171109_omotesandorb28/slides/#/)
* [Rubyで綾波Quine (shibuyarb)](https://284km.github.io/slides/20171115_shibuyarb/slides/#/)

---

### 最近書いていて楽しいと思うもの

- コードゴルフ
- 難解プログラミング
- 次はライフゲーム実装とかやろうかな

---

## 楽しいので紹介したい

よく使うもの

こちらが詳しいです
* [Ruby Code Golf Technic - Qiita](https://qiita.com/tbpgr/items/775636617d58bed56657)

見ていきましょう

---

### 不要な() や space

```ruby
puts("hi")
puts "hi"

#=>
puts"hi"
```

---

### $><<

print よりも 1 文字短い

```
print"hoge" # => hoge
$><<"hoge" # => hoge
```

---

### putc

文字コードがわかっている状態での標準出力が必要な場合に有効

```
$><<65.chr # => A
putc 65 # => A
```

---

### ?

1 文字出力したい時に有効


```
puts 'X'

puts ?X
```

---

### String#[]

```
puts 'hoge'.split('')[1] # => o
puts 'hoge'.chars[1] # => o
puts 'hoge'[1] # => o

puts 'hoge'.chars[1..-1]*'' # => oge
puts 'hoge'[1..-1] # => oge
```

---

### グローバル変数,クラス変数,インスタンス変数の文字列展開

```
# {} を省略可

class Hoge
  def hoge
    @a=1 # => インスタンス変数
    @@b=2 # => クラス変数
    $c=3 # => グローバル変数
    d=4 # => 変数（通常の変数は省略できない）
    puts "#@a,#@@b,#$c,#d"
  end
end

Hoge.new.hoge
# => 1,2,3,#d
```

---

### Array#*

```
puts [*1..5].join(':') # => 1:2:3:4:5
puts [*1..5]*':' # => 1:2:3:4:5
```

---

### 文字列の置換

sub! による置換を [regexp]= による置換で短くする

```
str = "0123456789"
str.sub! /[2-4]/,'@'
puts str # => 01@3456789

str = "0123456789"
str[/[2-4]/]='@'
puts str # => 01@3456789
```

---

* [Ruby を用いた超絶技巧プログラミング（夏のプログラミングシンポジウム 2012）](https://www.slideshare.net/mametter/ruby-2012)

---

### 第一段階:アルファベットと数字で Ruby

コード文字列 "p 1" を構築, eval するコード

```
class String
  def each
    concat 112 #=> p
    concat 32  #=> space
    concat 49  #=> 1
    eval self
    exit
  end
end
for i in
  String nil do
end
```

---

### 第二段階: 大文字を排除する

```
# String クラスには定義できない
# トップレベルは Object クラス
# トップレベルに定義すれば良い
public
def each
  clear
  concat 112 #=> p
  concat 32  #=> space
  concat 49  #=> 1
  eval self
  exit
end
for i in
  # 文字列を返す別のメソッドにする
  # String#clear で空文字列に
  inspect do
end
```

---

### 数字を排除する(1)

```
欲しい数字の長さの文字列を作り size を呼ぶ
e.g. 8 を生成するコード
clear
concat size
concat self
concat self
concat self
size
つまり、
[26] pry(main)> a = a.clear
=> ""
[27] pry(main)> a.size
=> 0
[28] pry(main)> a.concat a.size
=> "\u0000"
[29] pry(main)> a
=> "\u0000"
[30] pry(main)> a.concat a
=> "\u0000\u0000"
[31] pry(main)> a.concat a
=> "\u0000\u0000\u0000\u0000"
2^3 = 8 が得られる
```

---

### 数字を排除する(2)

```
例外処理の構文 を使う
begin A ensure B end
- A を評価し
- B を評価し
- B の評価結果を捨てて、A の評価結果を返す構文

つまり、文字列の先頭に1文字追加するイディオムが作れる

#=>
現在のself
"..."
concat begin
  dup
ensure
  concat begin
    65 を作る
  ensure
    clear
  end
end
新しいself
"A..."
つまり、
[38] pry(main)> a
=> "aaa"
[39] pry(main)> a.concat begin
[39] pry(main)*   a.dup
[39] pry(main)* ensure
[39] pry(main)*   a.concat begin
[39] pry(main)*     "A"
[39] pry(main)*   ensure
[39] pry(main)*     a.clear
[39] pry(main)*   end
[39] pry(main)* end
=> "Aaaa"
```

---

### おしまい

outside 楽しい

