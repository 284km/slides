
## Brainf*ck をやってみた
### 秒速@284km
##### 表参道.rb #27 ~ RubyKaigiのおさらい ~

![goku.jpg](../goku.jpg)

---

# 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
  - Ruby/Rails 中心の生活
  - サービスを作る人
- Rails Developers Meetup

---

# RubyKaigi

- 行った方も
- 行けなかった方も
- おつかれさまでした
- 好きな発表？
- 美味しかった？

---

# 好きな発表？

---

# Quine

---

# Quine

### 自身のソースコードと完全に同じ文字列を出力するプログラムである。

---

# こういうのです

動かす

---

# 本当は今日 Quine の発表をやりたかった

---

# Brainf*ck
# Brainfuck

---

# Brainf*ck

- もう飽きてる方もいるかも
- 僕は初めて実装しました

---

# Brainf*ck

* [Brainfuck - Wikipedia](https://ja.wikipedia.org/wiki/Brainfuck)

Brainfuck（ブレインファック）は難解プログラミング言語のひとつ。なお名称に卑語が含まれるため、Brainf*ckなどと表記されることがある。

---

# 概要

開発者Urban Müllerがコンパイラがなるべく小さくなる言語として考案した。 実際、Müllerが開発したコンパイラのサイズはわずか123バイト、インタプリタは98バイトであった。

Brainfuckプログラムは非常に可読性・記述性が低いため実用性は期待できないが、チューリング完全である。その簡潔さから多くの派生言語を生み出すこととなった。

---

## Brainfuckの言語仕様

処理系は次の要素から成る: Brainfuckプログラム、インストラクションポインタ（プログラム中のある文字を指す）、少なくとも30000個の要素を持つバイトの配列（各要素はゼロで初期化される）、データポインタ（前述の配列のどれかの要素を指す。最も左の要素を指すよう初期化される）、入力と出力の2つのバイトストリーム。

Brainfuckプログラムは、以下の8個の実行可能な命令から成る（他の文字は無視され、読み飛ばされる）。

---

## Brainfuckの言語仕様

```
>  ポインタをインクリメントする。C言語の「ptr++;」に相当する
<  ポインタをデクリメントする。「ptr--;」に相当
+  ポインタが指す値をインクリメントする。「(*ptr)++;」に相当
-  ポインタが指す値をデクリメントする。「(*ptr)--;」に相当
.  ポインタが指す値を出力に書き出す。「putchar(*ptr);」に相当
,  入力から1バイト読み込んで、ポインタが指す先に代入する。「*ptr=getchar();」に相当
[  ポインタが指す値が0なら、対応する ] の直後にジャンプする。「while(*ptr){」に相当
]  ポインタが指す値が0でないなら、対応する [ の直後にジャンプする。「}」に相当
```

---

# Installation

```
$ brew install brainfuck
```

```
$ sudo apt-get install bf
```

---

# print foo

### お手元の ASCII コード表 を見ながら書くとよさそう

```
++++++++++++++++
++++++++++++++++
++++++++++++++++
++++++++++++++++
++++++++++++++++
++++++++++++++++
++++++.          0x66: f
+++++++++.       0x6f: o
.                0x6f: o
```

---

# Hello, World!

ループを使って短く書いている (解説する)

[9]
[0][8\*9][11\*9][5\*9]
```
+++++++++[>++++++++>+++++++++++>+++++<<<-]>.>++.+++++++..+++.>-.
------------.<++++++++.--------.+++.------.--------.>+.
```

---

# オレオレ実装

- [284km/brain_kemofure](https://github.com/284km/brain_kemofure)
- わりと簡単に作れる
- すでにもうたくさんの実装が世の中に溢れている
  - [brainf*ckでジョジョ言語 - 旧toyoshiの日記](http://d.hatena.ne.jp/toyoshi/20100208/1265587511)
- やったことなかったので、とりあえず作ってみた

---

### Input

わーい！わーい！すごーい！わーい！たーのしー！すごーい！わーい！わーい！たーのしー！食べないでくださいあなたは何のフレンズさんですか？わーい！わーい！わーい！わーい！わーい！わーい！わーい！わーい！あなたは何のフレンズさんですか？わーい！わーい！わーい！わーい！わーい！わーい！わーい！わーい！わーい！わーい！わーい！あなたは何のフレンズさんですか？わーい！わーい！わーい！わーい！わーい！われわれはかしこいのでわれわれはかしこいのでわれわれはかしこいのでまたやってしまったねぇ食べないよあなたは何のフレンズさんですか？やりますねあなたは何のフレンズさんですか？わーい！わーい！やりますねわーい！わーい！わーい！わーい！わーい！わーい！わーい！やりますねやりますねわーい！わーい！わーい！やりますねあなたは何のフレンズさんですか？またやってしまったねぇやりますねまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇやりますねわれわれはかしこいのでわーい！わーい！わーい！わーい！わーい！わーい！わーい！わーい！やりますねまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇやりますねわーい！わーい！わーい！やりますねまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇやりますねまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇまたやってしまったねぇやりますねあなたは何のフレンズさんですか？わーい！やりますね

---

## => Hello, World!

```
while index < @tokens.size
  case @tokens[index]
    when 'わーい', 'すごーい', 'たーのしー'
      array[now] ||= 0
      array[now] += 1
    when 'またやってしまったねぇ'
      array[now] ||= 0
      array[now] -= 1
    when 'あなたは何のフレンズさんですか？'
      now += 1
    when 'われわれはかしこいので'
      now -= 1
    when 'やりますね'
      n = (array[now] || 0)
      print n.chr
    when ','
      array[now] = $stdin.getc
    when '食べないでください'
      index = @jumps[index] if array[now] == 0
    when '食べないよ'
      index = @jumps[index] if array[now] != 0
  end
  index += 1
end
```

