
## Life Is Strange
### 秒速@284km
##### 表参道.rb #35

![goku.jpg](../goku.jpg)

---

# こんにちは

---

# 仙台以来ですね？

---

# 疲れてますか？

---

# 日記はもう書きました？

![nikki.png](../nikki.png)

---

## 表参道.rb #30 以来の発表のようです

やっと落ち着きつつあります

- 沖縄Ruby会議
- railsdm 2018
- 技術書展4
- RejectKaigi 2018
- RubyKaigi 2018 LT

---

# \#kaigieffect

* [#kaigieffect ー RubyKaigi効果 - Togetter](https://togetter.com/li/162817)

---

# \#kaigieffect

- 思い出を語るに留まらず、何か新しいことをという気持ち
- 僕も今日、小さいけどひとつ

---

## ライフゲーム (Conway's Game of Life)

https://ja.wikipedia.org/wiki/%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B2%E3%83%BC%E3%83%A0

- 書いてみたいと以前から言っていて、まだ書いたことがなかった
- RubyKaigi で何回か登場してましたよね
- よっしゃ機運だ

---

## ライフゲーム (Conway's Game of Life)

```
- 誕生
    - 死んでいるセルに隣接する生きたセルがちょうど3つあれば、次の世代が誕生する。
- 生存
    - 生きているセルに隣接する生きたセルが2つか3つならば、次の世代でも生存する。
- 過疎
    - 生きているセルに隣接する生きたセルが1つ以下ならば、過疎により死滅する。
- 過密
    - 生きているセルに隣接する生きたセルが4つ以上ならば、過密により死滅する。
```

---

## つまり

ルールは単純だ

- 生きてるセル
    - 隣接する生きたセルが 2 つか 3つなら生き続ける
- 死んでるセル
    - 隣接する生きたセルが 3 つなら生き

---

## 実装は世にたくさんある

* [jonan (デカ外人) のライブコーディング - RailsConf 2018 - Twitch](https://www.twitch.tv/videos/251736888)
* [@youhan のブログより - One-Liner Game of Life](http://blog.youchan.org/2018-05-03)
- and so on ...

RubyKaigi 2018 でも、何度かライフゲームが現れましたよね。

---

## そういうわけで書いてみた

284km/cgol

- ちょっとだけ jonan の動画とかは見たことがあった
- field とか cell とかこんな感じで実装できそうだなとは頭にあった

---

## 284km/cgol

- 書いてみたけど、そんなにここから何か言いたいことがなかった...w
- でも、書いていておもしろかったからいいか。

今日は、書いたコードが動くところを見せびらかして終わりです。ではいきますよ！

