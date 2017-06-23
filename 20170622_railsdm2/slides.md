
## データフィードの現場で書く
## Rails アプリケーション
### 秒速@284km
##### Rails Developers Meetup #2

![goku.jpg](../goku.jpg)

---

# 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
  - Ruby/Rails 中心の生活
  - サービスを作る人
- Rails Developers Meetup 運営

---

## データフィードの現場で書く
## Rails アプリケーション

---

# これから話すこと

- ### データフィードの現場で書く Rails アプリケーションとはなにか？ という話
- ### WebMock を多用しているのです という話
- ### VCR が便利です という話
- ### WebMock の実装はこうなっています という話
- ### railsdm いっしょにやっていきましょう❤️

---

## データフィードの現場で書く
## Rails アプリケーション

---

# データフィードの現場とは？

---

# そもそもデータフィードとは？？

---

# データフィード

- ### なんとなく知っているよという方 ✋

---

## データフィードとは

### 保有している商品などのデータを、外部のサービス（広告配信先など） のフォーマットに変換して送信する仕組みのこと

---

## データフィードの現場で書く
## Rails アプリケーション
### 結局それは

---

## 特殊なことはない。
## ふつうでありたいと日夜励む
## Rails アプリケーションでした

- 共通認識、規約、Rails way
- 特殊だな？と感じたら一度疑ってみた方が良い
- これについてはは onk さんの発表資料が最高

---

## そうは言っても特徴はあります

外部 API を利用するので、こういった gem を多用することになる

- ### arsduo/koala
Koala is a Facebook library for Ruby

- ### google/google-api-ruby-client
The Google API Client Library for Ruby

---

## そのような特徴のテストをそれなりの量書くことになります

---

## そのような特徴のテスト

- ### HTTPリクエストが発生する部分のテスト

---

## そのような特徴のテスト

- ### WebMock を使う

---

# WebMock

Library for stubbing and setting expectations
on HTTP requests in Ruby.

HTTP リクエストをあたかも処理されたように
レスポンスを返すことができる。

---

# Installation

テストの用途で使う話をします

```.rb
gem install webmock

# RSpec
require 'webmock/rspec'

# MiniTest
require 'webmock/minitest'
```

---

# 基本的な使い方


```.rb
# （stubするリクエストの登録）
# https://www.example.com/ に POST メソッドでアクセスした場合に、
# メッセージボディが "abc" であるレスポンスを返す
# method_name に :any を指定すると何でも可となる

stub_request(:post, "https://www.example.com").
  with(body: "abc", headers: { 'Content-Length' => 3 }).
  to_return(body: "abc")
```

こういうテストコードをたくさん書きます。
WebMock 用のデータ作成が大変。面倒。

---

## データ作成を楽にしたい
## VCR

Record your test suite's HTTP interactions and replay them during future test runs for fast, deterministic, accurate tests.

データを実際のHTTP通信から記録するライブラリ

```.rb
gem 'vcr'

VCR.configure do |c|
  c.cassette_library_dir = 'tmp/vcr'
  c.hook_into :webmock
end

# tmp/vcr/path/to/response.yml に作られる
VCR.use_cassette("path/to/response") do
  response = Net::HTTP.get_response(URI('http://example.com'))
end
```

---

# WebMock
## ここで何が起きているのか

```.rb
stub_request(:post, "https://www.example.com").
  with(body: "abc", headers: { 'Content-Length' => 3 }).
  to_return(body: "abc")
```

https://www.example.com/ に POST メソッドでアクセスした場合に、メッセージボディが "abc" であるレスポンスを返す

---

# WebMock の
# コードを紹介

ここで何が起きているのかコードを簡単に追います

```.rb
stub_request(:post, "https://www.example.com").
  with(body: "abc", headers: { 'Content-Length' => 3 }).
  to_return(body: "abc")
```

---

エントリーポイント

webmock/lib/webmock/api.rb

```.rb
module WebMock
  module API
    extend self

    def stub_request(method, uri)
      WebMock::StubRegistry.instance.
        register_request_stub(WebMock::RequestStub.new(method, uri))
    end

    alias_method :stub_http_request, :stub_request
```

- WebMock::StubRegistry.instance.register_request_stub で、RequestStub を保持する
- 保持されるのは WebMock::RequestStub.new(method, uri)

---

```.rb
stub_request(:post, "https://www.example.com").
  with(body: "abc", headers: { 'Content-Length' => 3 }).
  to_return(body: "abc")
```

- RequestPattern を保持する
- #with, #to_return メソッドがある

```.rb
module WebMock
  class RequestStub
    def initialize(method, uri)
      @request_pattern = RequestPattern.new(method, uri)
      @responses_sequences = []
      self
    end

    def with(params = {}, &block)
    def to_return(*response_hashes, &block)
```

---

```.rb
module WebMock
  class RequestPattern
    def initialize(method, uri, options = {})
      @method_pattern  = MethodPattern.new(method)
      @uri_pattern     = create_uri_pattern(uri)
      @body_pattern    = nil
      @headers_pattern = nil
      @with_block      = nil
      assign_options(options)
    end
```

- MethodPattern を保持する
- uri_pattern も保持する
- BodyPattern は body 部を見て multipart/form-data は扱わず、json なのか xml なのかなど判定して、よしなに parse されます。
- HeadersPattern は同じように header に対して行われます。

---

```.rb
  class MethodPattern
    def initialize(pattern)
      @pattern = pattern
    end

    def matches?(method)
      @pattern == method || @pattern == :any
    end
```
- ここで登録された method と、実行されたメソッド名がマッチしているか
- 又は、パターンが :any である場合にはマッチしたと判定する

---

これで stub_request による登録が完了

```.rb
module WebMock
  module API
    def stub_request(method, uri)
      WebMock::StubRegistry.instance.
        register_request_stub(WebMock::RequestStub.new(method, uri))
    end
```

---

次は with について

```.rb
stub_request(:post, "https://www.example.com").
  with(body: "abc", headers: { 'Content-Length' => 3 }).
  to_return(body: "abc")
```

register_request_stub は WebMock::RequestStub を返すので、
WebMock::RequestStub.with を見ます

```.rb
module WebMock
  class StubRegistry
    def register_request_stub(stub)
      request_stubs.insert(0, stub)
      stub
    end
```

---

```.rb
module WebMock
  class RequestStub
    def with(params = {}, &block)
      @request_pattern.with(params, &block)
      self
    end
```

RequestPattern#with で、
body や headers や uri pattern を保持します。

```.rb
module WebMock
  class RequestPattern
    def with(options = {}, &block)
      raise ArgumentError.new('#with method invoked with no arguments. Either options hash or block must be specified.') if options.empty? && !block_given?
      assign_options(options)
      @with_block = block
      self

    def assign_options(options)
      options = WebMock::Util::HashKeysStringifier.stringify_keys!(options, deep: true)
      HashValidator.new(options).validate_keys('body', 'headers', 'query', 'basic_auth')
      set_basic_auth_as_headers!(options)
      @body_pattern = BodyPattern.new(options['body']) if options.has_key?('body')
      @headers_pattern = HeadersPattern.new(options['headers']) if options.has_key?('headers')
      @uri_pattern.add_query_params(options['query']) if options.has_key?('query')
```

---

最後に to_return について

```.rb
stub_request(:post, "https://www.example.com").
  with(body: "abc", headers: { 'Content-Length' => 3 }).
  to_return(body: "abc")
```

with で self を返すので同様に
WebMock::RequestPattern#to_return です
よしなに responses_sequences に保持します

```.rb
module WebMock
  class RequestPattern
    def to_return(*response_hashes, &block)
      if block
        @responses_sequences << ResponsesSequence.new([ResponseFactory.response_for(block)])
      else
        @responses_sequences << ResponsesSequence.new([*response_hashes].flatten.map {|r| ResponseFactory.response_for(r)})
      end
      self
    end
    alias_method :and_return, :to_return
```

---

### 登録は出来た。ではどのように実行されるのか

lib/webmock/http_lib_adapters の中にアダプターがあります
Net::HTTP での前提で続きを追います

Net::HTTP を @webMockNetHTTP に書き換えています。

lib/webmock/rspec.rb
`require 'webmock/rspec'` すると `WebMock.enable!` されます

```.rb
module WebMock
  module HttpLibAdapters
    class NetHttpAdapter < HttpLibAdapter
      adapter_for :net_http
      def self.enable!
        Net.send(:remove_const, :BufferedIO)
        Net.send(:remove_const, :HTTP)
        Net.send(:remove_const, :HTTPSession)
        Net.send(:const_set, :HTTP, @webMockNetHTTP)
        Net.send(:const_set, :HTTPSession, @webMockNetHTTP)
        Net.send(:const_set, :BufferedIO, Net::WebMockNetBufferedIO)
      end
      @webMockNetHTTP = Class.new(Net::HTTP) do
        def request(request, body = nil, &block)
          request_signature = WebMock::NetHTTPUtility.request_signature_from_request(self, request, body)
          # ここで stub RequestPattern にマッチする stub を探して評価して結果を返している
          if webmock_response = WebMock::StubRegistry.instance.response_for_request(request_signature)
            build_net_http_response(webmock_response, &block)
```

---

# railsdm

## 一緒にやっていく方を募集中です
## 是非やりましょう❤️

