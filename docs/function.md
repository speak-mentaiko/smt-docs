# 関数

逆変換に用いられる関数の使い方を簡単に上げておきます。
間違っていたり、無いものがありますが許してください。

## Ruby -> Block で使う関数

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
  下の引数の数, // たぶん
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
引数には基本的に`args[n]`を入れます。

### isNumber

```js
converter.isNumber(確認する値);
```

引数が数字か確認します。
引数には基本的に`args[n]`を入れます。

### isBlock

```js
converter.isBlock(確認する値);
```

引数がブロックか確認します。
引数には基本的に`args[n]`を入れます。

### isStringOrBlock

```js
converter.isStringOrBlock(確認する値);
```

引数が文字列もしくはブロックか確認します。
引数には基本的に`args[n]`を入れます。

### isNumberOrBlock

```js
converter.isNumberOrBlock(確認する値);
```

引数が数字もしくはブロックか確認します。
引数には基本的に`args[n]`を入れます。

### createBlock

```js
const block = converter.createBlock(メソッド名, ブロックの形);
```

変換するブロックを指定します。

1 つ目の引数は`ruby-generator`側で定義したメソッド名<br>
2 つ目の引数はブロックの形

ブロックの形は以下のようなものがあります<br>

- `value`<br>
  ![value](/images/valueblock.png)<br>
- `value_boolean`<br>
  ![alt text](/images/value-booleanblock.png)<br>
- `statement`<br>
  ![statement](/images/statement-block.png)<br>
- `hat`<br>
  ![alt text](/images/hatblock.png)

### createRubyExpressionBlock

```js
converter.createRubyExpressionBlock(インスタンス名, node);
```

インスタンス部分を作成するときに用います。

1 つ目の引数はインスタンス名<br>
2 つ目の引数は`node`

インスタンス名は以下のような制限があります<br>

- 大文字は使えない(違うかも)
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
メソッド名とブロックの形は[`createBlock`](#createblock)を参照

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
3 つ目は不明、~~`math_number`以外見たことない~~

- `math_number`
  - 実数?
- `math_positive_number`
  - 正の実数？負の数も渡せるため不明
- `math_whole_number`
  - 整数？整数以外も渡せるため不明
- `math_integer`
  - 整数？整数以外も渡せるため不明
- `math_angle`
  - 0~360(たぶん)<br>`% 360`の処理が掛かる？

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

特殊<br>
![alt text](/images/color-block.png)<br>
カラーパレットに変換も可能

```js
converter.addFieldInput(
  block,
  "COLOR",
  "colour_picker",
  "COLOUR",
  args[0],
  "#43066f"
);
```

2 つ目の引数は vm 側で定義した変数の名前<br>
3 つ目の引数は `colour_picker`固定<br>
4 つ目の引数は `COLOUR`固定？<br>
5 つ目の引数は実際に渡す値<br>
6 つ目の引数は不明、デフォルト値?<br>

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

## Block -> Ruby で使う関数,変数

ドキュメントがないため一部は[Blockly](https://developers.google.com/blockly/guides/create-custom-blocks/code-generation/overview?hl=ja)のドキュメントを参考に書いています。

### prepares\_

```js
Generator.prepares_[`適当な名前`] = Generator.別の定義;
```

定義した別関数を呼び出すことができます。<br>
`prepares_["適当な名前"]`は block->Ruby に変換した際 1 度だけ呼び出されます。

### valueToCode

```js
Generator.valueToCode(block, 引数名, 評価順?);
```

ブロックから引数を取得することができます。<br>
四角いタイプのメニュー以外から値を得ることができます


1 つ目の引数は 引数を取得するブロック。基本的に`block`のままで問題ない<br>
2 つ目の引数は vm 側の`arguments`で決めた引数名<br>
3 つ目の引数は 評価順的なもの?。基本的には`ORDER_ATOMIC`,`ORDER_NONE`を使う。

### getFieldValue

```js
Generator.getFieldValue(block, 引数名)
```

ブロックから引数を取得することができます。<br>
四角いタイプのメニューから値を得ることができます

1 つ目の引数は 引数を取得するブロック。基本的に`block`のままで問題ない<br>
2 つ目の引数は vm 側の`arguments`で決めた引数名<br>


## ORDER シリーズ

評価順?のマジックナンバー<br>
基本的に上から順番に評価順が高い

### Generator.ORDER_ATOMIC

- `0` `""`
- 一番初めに評価される

```js
return ["value0", Generator.ORDER_ATOMIC];
```

基本的に値ブロックはこの変数を使う。<br>
返す文字列にかっこを含む(メソッド)を返す場合は[`ORDER_FUNCTION_CALL`](#generatororder_function_call)を使う。

### Generator.ORDER_COLLECTION

- tuples, lists, dictionaries
- scratch の list にのみ使われている

### Generator.ORDER_STRING_CONVERSION

- `expression...`
- 使われていないので不明

### Generator.ORDER_MEMBER

- `::`
- 使われていないため不明

### Generator.ORDER_INDEX

- `[]`
- 配列の要素

### Generator.ORDER_FUNCTION_CALL

- `()`
- 通常ブロックの形以外でメソッドを呼び出す際に使う<br>
  ![menu](/images/functioncall.png)

### Generator.ORDER_UNARY_SIGN

- `+(単項)` `!` `~`
- 否定演算子などに使われる?

### Generator.ORDER_EXPONENTIATION

- `**`
- 使われていないため不明

### Generator.ORDER_UNARY_MINUS_SIGN

- `-(単項)`
- 使われていないため不明

### Generator.ORDER_MULTIPLICATIVE

- `*` `/` `%`
- 積,商,余り

### Generator.ORDER_ADDITIVE

- `+` `-`
- 和,差

### Generator.ORDER_BITWISE_SHIFT

- `<<` `>>`
- 使われていないため不明
- ビット操作?

### Generator.ORDER_BITWISE_AND

- `&`
- 使われていないため不明
- ビット操作?

### Generator.ORDER_BITWISE_XOR

- `^`
- 使われていないため不明
- ビット操作?

### Generator.ORDER_BITWISE_OR

- `|`
- 使われていないため不明
- ビット操作?

### Generator.ORDER_RELATIONAL

- `>` `>=` `<` `<=`
- 比較

### Generator.ORDER_EQUALS

- `<=>` `==` `===` `!=` `=~` `!~`
- 比較

### Generator.ORDER_LOGICAL_AND

- `&&`
- かつ

### Generator.ORDER_LOGICAL_OR

- `||`
- または

### Generator.ORDER_RANGE

- `..` `...`
- range

### Generator.ORDER_CONDITIONAL

- `?:(条件演算子)`
- 使われていないため不明
- 三項演算子?

### Generator.ORDER_ASSIGNMENT

- `=(+=, -= ... )`
- 使われていないため不明
- 代入演算子?

### Generator.ORDER_NOT

- `not`
- 使われていないため不明

### Generator.ORDER_AND_OR

- `and` `or`
- 使われていないため不明

### Generator.ORDER_NONE

- `(...)`
- 通常ブロックの形でメソッドを呼び出す際に使う

```js
Generator.sample_command1 = function (block) {
  const text =
    Generator.valueToCode(block, "TEXT", Generator.ORDER_NONE) || null;
  const num = Generator.valueToCode(block, "NUM", Generator.ORDER_NONE) || 0;
  return `puts(command1, ${text}, ${num})\n`;
};
```
