
# CHANGELOG の更新について考えたこと
### 秒速@284km
##### 渋谷.rb[:20170517]

![goku.jpg](../goku.jpg)

---

# self.inspect

- 秒速@284km
- feedforce
  - Ruby/Rails 中心の生活

---

# CHANGELOG

- ## 仕事のプロジェクトで、CHANGELOG 書いていますか？

---

# 何故必要になったか？

- Product Owner の方がプロジェクトの説明をするのに助かるから
- 主に内部の人間向けの説明などに有用ということだと理解している
- あまり仕事のプロジェクトで書いている印象が無いんですよね...

---

# CHANGELOG

- ## 仕事のプロジェクトで、CHANGELOG 書いていますか？ ✋

---

# CHANGELOG

大きく 2 パターンの考えがあるのでは？

- ## 手動派
- ## 自動生成派 (p-r や issue から自動生成)

---

# 手動

- ### とにかく分かりやすく編集できる(やれるなら一番良いと思う。要約したり、良い感じのリンクを置いたり)

---

# Issues, p-r から自動で起こす

- 時間短縮になる
- 必要十分に分かりやすければそれで OK 👍
- commit log から取るとさすがに粒度が小さすぎる。分かり難い。
- Issues, p-r を、予め CHANGELOG を生成する前提で書けば良さそう?
  (自分達の場合は、Product Owner が大体 Issue を書くし、p-r も開発者が書くので共有できそう)

---

# 今日やった

- ### skywinder/github-changelog-generator

---

## skywinder/github-changelog-generator

- p-r, Issues ごとに良い感じに一覧が作成される
- Issues などに tag を付けておけば(bug とか) それ毎にまとまる。(以下は README に書いてあります)
  - Merged pull requests (all merged pull-requests) 🔀
  - Bug fixes (issues labeled bug) 🐞
  - Enhancements (issues labeled enhancement) 🌟
  - Issues (closed issues with no labels) 🚱

```.sh
# Usage
$ git tag v0.2.0; git push v0.2.0
$ gem i github_changelog_generator
$ github_changelog_generator -uuser_name -prepo_name -t github_access_token
# => Generated log placed in /path/to/user_name/repo_name/CHANGELOG.md
### (github_access_token)
### https://github.com/settings/tokens
### required "repo" scope
```

