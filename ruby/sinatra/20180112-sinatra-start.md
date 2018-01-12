## sinatraアプリについて

Rubyをふんわり分かるためにチャットアプリを書き始めた。

[sinatra](sinatrarb.com)というフレームワークを使っている。薄いフレームワークが好き。

https://github.com/whywaita/sinatra-chat

### チャット書き込み

チャットログを残すためには何らかの永続ストレージが必要なんだけど、DBを用意するのは面倒なのでCSVでゆるふわに書いている。

https://github.com/whywaita/sinatra-chat/commit/f91e885381b3c99e21d5dd4a2e35b9a7c8b82d73#diff-cc95738088603531796e0d0f246a5d77R20

[とほほ](http://www.tohoho-web.com/ruby/variables.html)さんによると、変数の種類も色々ある。この辺は覚えたらささっと書けるようになってるんだろうなという気がする。

自分のコードだと気になるのは

```ruby
get '/bbs' do
  if request.env["warden"].user.nil?
    redirect "/"
  end

  @title = "Sinatra chat"
  @bbs_data = CSV.read("resources/bbs_data.csv")

  haml :index
end
```

こんなような書き方をしている時で、`@title`なんかはインスタンス変数なのは分かるけど、この場合 `get '/bbs' do ... end` が1つのインスタンスなんだろうか？

他にハマったポイントとしてはグローバル変数周りをどうするかという話で、アプリ起動時にRedisに繋ぎにいきたい時にこう書いていた。

```ruby
redis = Redis.new host:"redis", port:"6379"
```

一見良さそうに見えるけどこれはだめで、関数内から`redis`は呼び出せない。これは小文字から始まる変数がローカル変数になるからで、別のブロックからは呼び出せないせい。なので、大文字に変更して対応した。

```ruby
REDIS = Redis.new host:"redis", port:"6379"
```
