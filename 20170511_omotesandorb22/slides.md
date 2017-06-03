
# ふつうの print debug 集
### @284km
##### 表参道.rb #22 \~ロギング、デバッグ~

![goku.jpg](../goku.jpg)

---

# self.inspect

- @284km
- feedforce
  - Ruby/Rails 中心の生活

---

# 本題

- ロギング
- デバッグ

---

# デバッグ

---

# デバッグの地味な話をします

---

# よく使う

- binding.pry
- print debug

---

# print debug

手軽に試せる。これで済めばうれしい😇

絶対にコミットしてはいけない。用法に注意。

---

# print debug

```.rb
# こういうやつ
puts "#" * 80
puts foo
puts "#" * 80

if foo == 1
  bar = 10
else
  bar = 20
end
```

---

# 何処から呼ばれているか知りたい時
# Kernel.#caller

---

# Kernel.#caller

呼び出し元の情報をバックトレース(文字列の配列)として返す

- caller(0) # 現在の場所から
- caller(1) # 呼び出し元から(default)
- caller(2) # 呼び出し元の呼び出し元から

```
def f1
  f2
end

def f2
  f3
end

def f3
  p caller(0)  #=> ["1.rb:10:in `f3'", "1.rb:6:in `f2'", "1.rb:2:in `f1'", "1.rb:15:in `<main>'"]
  p caller     #=> ["1.rb:6:in `f2'", "1.rb:2:in `f1'", "1.rb:15:in `<main>'"]
  p caller(2)  #=> ["1.rb:2:in `f1'", "1.rb:15:in `<main>'"]
end

f1
```

---

## ファイル名, 行数付きで出力したり

```
def debug_puts(message)
  caller()[0] =~ /(.*?):(\d+)/
  filename, linenum = $1, $2
  $stderr.puts "[DEBUG] #{filename}:#{linenum}"
  $stderr.puts "[DEBUG] #{message}"
end

x = "foo"
debug_puts "x=#{x.inspect}"
x = "bar"
debug_puts "x=#{x.inspect}"

#=> [DEBUG] 2.rb:9
#=> [DEBUG] x="foo"
#=> [DEBUG] 2.rb:11
#=> [DEBUG] x="bar"
```

---

# Excelption の発生元を知りたいとき

---

### Exception の発生元を知りたい時
- Exception#backtrace()
- Exception#set_backtrace

何らか、呼び元が知りたい時に使える...
(良い例がパッと思いつかなかった
テストなどがそうです？

```
class AssertionError < StandardError
end

def debug_assert(expr)
  return if expr
  ex = AssertionError.new("assertion failed")
  ex.set_backtrace(caller())
  raise ex  # 本当はここで例外が発生している
end

def f1
  debug_assert 1 + 1 == 3 #=> ここで発生したように
end

f1()
# => 8.rb:12:in `f1': assertion failed (AssertionError)
# =>         from example.rb:15:in `<main>'
```

---

## メソッドの定義場所を知りたい時
## source_location

pry も便利
?: 指定したメソッドのドキュメント(コメント)を表示
$: 指定したメソッドのソースを表示

---

## メソッドの定義場所を知りたい時
### - Kernel#method
### - Method#source_location

Kernel#method と Method#source_location を組み合わせてメソッドの定義場所を見つけられる

```
$ rails c
Loading development environment (Rails 5.0.2)
[1] pry(main)> 'x'.method(:blank?).source_location
=> ["/Users/furuhashi/src/github.com/feedforce/ecbooster/vendor/bundle/ruby/2.4.0/gems/activesupport-5.0.2/lib/active_support/core_ext/object/blank.rb",
 114]
```

---

## super の場合

```
class Super
  def foo
    "foo"
  end
end

class Sub < Super
  def foo
    "foobar"
  end
end

x = Sub.new

x.method(:foo).super_method.call #=> "foo"
x.method(:foo).super_method.source_location
#=> example.rb
#=> 2
```

---

## ObjectSpace.trace_object_allocations_start を使ったやり方

trace_object_allocations でブロックを囲むか、
trace_object_allocations_start, trace_object_allocations_stop で処理を囲む。

```
require 'objspace'
ObjectSpace.trace_object_allocations_start
o = Object.new
ObjectSpace.trace_object_allocations_stop
puts ObjectSpace.allocation_sourcefile(o) #=> "example.rb"
puts ObjectSpace.allocation_sourceline(o) #=> 5
# trace した情報を clear してます
ObjectSpace.trace_object_allocations_clear
```

---

### Kernel.#set_trace_func
### でフックして調べる

- Ruby インタプリタのイベントをトレースする
- Proc オブジェクトとして 指定された proc を登録します

```
class Foo
  def foo; bar; end
  def bar; end
end
class Bar < Foo
  def bar; end
end

set_trace_func lambda { |event, file, line ,id, binding, klass|
  if event == 'call' && id == :bar
    puts "call method bar file:#{file} line:#{line}"
  end
}

Foo.new.foo
Bar.new.foo
# => call method bar file:example.rb line:5
# => call method bar file:example.rb line:10
```

