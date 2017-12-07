
## module とは
### 秒速@284km
##### 表参道.rb #29 ~ Module ~

![goku.jpg](../goku.jpg)

---

## 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
- Rails Developers Meetup

---

# Class

- 他のクラスを継承できる
- インスタンスを生成することができる

```
class C < Parent
  def foo
    puts "bar"
  end
end
c = C.new()
```

---

# Module

スーパークラスを指定できず、インスタンスも作れないクラス

```
module M
end
```

---

インスタンスが作れないので直接は呼び出せないが、
include してモジュールをクラスが継承したかのように使うことができる

```
module M
  def myupcase( str )
    return str.upcase()
  end
end

class C
  include M
end

p(C.new().myupcase("content"))  # "CONTENT"と表示される
```

---

モジュールはスーパークラスを指定できないが、 別のモジュールをインクルードすることはできる

OneMore
|
M
|
C

```
module OneMore
  def method_OneMore()
    p("OneMore")
  end
end

module M
  include OneMore

  def method_M()
    p("M")
  end
end

class C
  include M
end

C.new().method_M()         # "M"が表示される
C.new().method_OneMore()   # "OneMore"が表示される
```

---

Cls
|
|-Mod
|
C

```
class Cls
  def test()
    return "class"
  end
end

module Mod
  def test()
    return "module"
  end
end

class C < Cls
  include Mod
end

p(C.new().test())   # "class"？ "module"？

#=> "module"
```

---

### extend

extend したモジュールのメソッドを、クラスメソッドとして使うことができる

```
module Mod
  def hello
    puts 'Hello'
  end
end

class Obj
  extend Mod
end

Obj.hello
```

---

### prepend

- prepend したクラスの、前に入る

```
module M
  def foo
    puts "bar"
  end
end

class C
  prepend M
end

[6] 2.5.0-p-1(main)> C.ancestors
=> [M, C, Object, PP::ObjectMixin, Kernel, BasicObject]

```

---

## ここから全然別の話。というか相談

何か案を思いつく方がいたら教えてほしい

- 作っている quine がある(demo)
- | ruby する度に質力を変えるやつをやりたい
- money forward ruby 勉強会で、backtrace を | cat すると、元の順で出力される。というのを聞いた。
- STDOUT.tty を見たらできるのか！
- 事前に array に仕込んでおいて、添字に | した回数を仕込めればできそう！
- array に事前に仕込むの、大変。リソースの無駄遣い
- 数字の部分だけ、動的に組み立てられるようにすれば、いけそう。

```
# こんな感じ
eval s="i = 0; STDOUT.tty? ? puts(i+1) : puts('eval s='+s.inspect.sub(/([0-9]+)/){($1.to_i + 1).to_s})"
```

---

## その他最近気になること

懇親会とかで、教えてほしいです

- ジョブスケジューラ、何つかっていますか？


