# 関数

逆変換に用いられる関数の使い方を簡単に上げておきます。
間違っていたり、無いものがありますが許してください。

## converter.registerCallMethod

```js
converter.registerCallMethod(
  インスタンス名,
  メッソド名,
  引数の数,
  (params) => {}
);
```

基本的なブロック(`value`, `statement`, `hat`)が変換できる

まず 1 つ目の引数は基本的に`self`になり、
`command.puts`のようなインスタンスメソッドを呼び出す場合はインスタンス名になります。その場合は`command`となります。<br>
2 つ目の引数は変換するメッソド名です。<br>
3 つ目の引数は変換するメッソドの引数の数です。

## converter.registerCallMethodWithBlock

> 確認しきれていません

```js
converter.registerCallMethodWithBlock(
  インスタンス名,
  メッソド名,
  上の引数の数,
  下の引数の数, //たぶん
  (params) => {}
);
```

`end`がつくブロックを変換可能

3 つ目までの引数は`converter.registerCallMethod`と同じです<br>
4 つ目の引数は end 側につく引数の数？(未確認)
