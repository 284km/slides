
## おもに bison, flex での実装の説明
### 秒速@284km
##### Shinjuku.rb #54 言語のしくみ

![goku.jpg](../goku.jpg)

---

# 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
  - Ruby/Rails 中心の生活
  - サービスを作る人
- Rails Developers Meetup

---

# 言語のしくみ

### おまけ（時間が余ったら）brainf*ck の実装のはなし

---

### [Bison](https://ja.wikipedia.org/wiki/Bison)

Bison（バイソン）とは構文解析器を生成するパーサジェネレータの一種であり、CコンパイラとしてのGCCのサポートのために[要出典]開発されたフリーソフトウェアである。

### パーサジェネレータ

* [パーサジェネレータ - Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%91%E3%83%BC%E3%82%B5%E3%82%B8%E3%82%A7%E3%83%8D%E3%83%AC%E3%83%BC%E3%82%BF)

構文解析器を作成するプログラムである。

---

* [Lex - Wikipedia](https://ja.wikipedia.org/wiki/Lex)

Lex(レック、レックス)はレキシカルアナライザ（字句解析プログラム、字句解析器）を生成するプログラムである。コンパイラの作成のためにパーサジェネレータのyaccとともに使用されることも多い。Lexはエリック・シュミットとマイク・レスクによって書かれunixにおける標準のレキシカルアナライザとなっており、POSIX標準ともなっている。Lexと同等の機能を有し性能が改善されているFlex

---


字句解析
ソースプログラムを、「字句(トークン)」の並びに分割する処理

構文解析
トークンの並びから、解析木を構築する処理

---

### 残りはコードで説明します

---

### このページは、発表後に追記

2年前に書いたコードが約に立った😼

- https://github.com/284km/nira

説明入りの最初のファイルはそのままこちらに置きました

- https://github.com/284km/shinjukurb54_nira_description

