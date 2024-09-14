# 関数

逆変換に用いられる関数の使い方を簡単に上げておきます。
間違っていたり、無いものがありますが許してください。

## converter クラス

### registerCallMethod

```js
converter.registerCallMethod(
  インスタンス名,
  メソッド名,
  引数の数,
  (params) => {}
);
```

基本的なブロック(`value`, `statement`, `hat`)が変換できます。

1 つ目の引数は基本的に`self`になり、
`command.puts`のようなインスタンスメソッドを呼び出す場合はインスタンス名になります。その場合は`command`となります。<br>
2 つ目の引数は変換するメソッド名です。<br>
3 つ目の引数は変換するメソッドの引数の数です。

### registerCallMethodWithBlock

> 確認しきれていません

```js
converter.registerCallMethodWithBlock(
  インスタンス名,
  メソッド名,
  上の引数の数,
  下の引数の数, //たぶん
  (params) => {}
);
```

`end`がつくブロックを変換可能

3 つ目までの引数は`converter.registerCallMethod`と同じです<br>
4 つ目の引数は end 側につく引数の数？(未確認)

### isString

```js
converter.isString(確認する値);
```

引数が文字列か確認します。

引数には基本的に`args[n]`を入れます

### isNumber

```js
converter.isNumber(確認する値);
```

引数が数字か確認します。

引数には基本的に`args[n]`を入れます

### isBlock

```js
converter.isBlock(確認する値);
```

不明<br>
名前からしてブロックか確認？

### isStringOrBlock

```js
converter.isStringOrBlock(確認する値);
```

引数が文字列もしくはブロックか確認します。

引数には基本的に`args[n]`を入れます

### isNumberOrBlock

```js
converter.isNumberOrBlock(確認する値);
```

引数が数字もしくはブロックか確認します。

引数には基本的に`args[n]`を入れます

### createBlock

```js
const block = converter.createBlock(メソッド名, ブロックの形);
```

変換するブロックを指定します。

1 つ目の引数は`ruby-generator`側で定義したメソッド名<br>
2 つ目の引数はブロックの形

ブロックの形は以下のようなものがあります<br>
`value`<br>
![value](/images/valueblock.png)<br>
`statement`<br>
![statement](/images/statementblock.png)<br>
`hat`<br>
![alt text](/images/hatblock.png)

### createRubyExpressionBlock

```js
converter.createRubyExpressionBlock(インスタンス名, node);
```

インスタンス部分を作成するときに用います。

1 つ目の引数はインスタンス名<br>
2 つ目の引数は`node`

インスタンス名は以下のような制限があります<br>

- 大文字は使えない
- `=`、`.`、`[]`、`()`などの記号は使えない

`node`は`params`から分割代入します

### changeRubyExpressionBlock

```js
converter.changeRubyExpressionBlock(receiver, メソッド名, ブロックの形);
```

インスタンスメソッドを作成するときに用います。

1 つ目の引数は`receiver`<br>
2 つ目の引数はメソッド名<br>
3 つ目の引数はブロックの形<br>

`receiver`は`params`から分割代入します<br>
メソッド名とブロックの形は`createBlock`を参照

### addTextInput

```js
converter.addTextInput(block, 引数名, 渡す値, デフォルト値？);
```

ブロックに引数(文字列もしくはブロック)を渡します。

1 つ目の引数は 引数を渡すブロック。基本的に`block`のままで問題ない<br>
2 つ目の引数は vm 側の`arguments`で決めた引数名<br>
3 つ目の引数は 実際に渡す値。基本的には`args[n]`という形になる<br>
4 つ目の引数は 不明。デフォルト値?<br>

### addNumberInput

```js
converter.addNumberInput(block, 引数名, 'math_number', 渡す値, デフォルト値？);
```

ブロックに引数(数字もしくはブロック)を渡します。

1 つ目の引数は 引数を渡すブロック。基本的に`block`のままで問題ない<br>
2 つ目の引数は vm 側の`arguments`で決めた引数名<br>
3 つ目の引数は 不明。`math_number`しか見たことがない<br>
4 つ目の引数は 実際に渡す値。基本的には`args[n]`という形になる<br>
5 つ目の引数は 不明。デフォルト値?<br>

### addFieldInput

```js
converter.addFieldInput(block, 引数名, gui側メニュー名, vm側メニュー名, 渡す値, デフォルト値？);
```

ブロックに引数(メニューもしくはブロック)を渡します。<br>
ブロックを引数に持てるタイプのメニューに引数を渡すことが可能です。<br>
![menu](/images/menu-block.png)

1 つ目の引数は 引数を渡すブロック。基本的に`block`のままで問題ない<br>
2 つ目の引数は vm 側の`arguments`で決めた引数名<br>
3 つ目の引数は gui 側で定義したメニューの名前<br>
4 つ目の引数は vm 側の`menus`で定義したメニュー名<br>
4 つ目の引数は 実際に渡す値。基本的には`args[n]`という形になる<br>
5 つ目の引数は 不明。デフォルト値?<br>

### addField

```js
converter.addField(block, 引数名, 渡す値, デフォルト値？);
```

ブロックに引数(メニュー)を渡します。<br>
ブロックを引数に持てないタイプのメニューに引数を渡すことが可能です。<br>
![menu](/images/menu.png)

1 つ目の引数は 引数を渡すブロック。基本的に`block`のままで問題ない<br>
2 つ目の引数は vm 側の`arguments`で決めた引数名<br>
3 つ目の引数は 実際に渡す値。基本的には`args[n]`という形になる<br>
4 つ目の引数は 不明。デフォルト値? なくても問題ない<br>

### addInput

```js
converter.addInput(block, 引数名, 渡す値, デフォルト値？);
```

不明
