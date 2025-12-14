# 関数

逆変換に用いられる関数の使い方を簡単に上げておきます。
間違っていたり、無いものがありますが許してください。

## Ruby -> Block で使う関数

### `registerCallMethod`

```ts
registerCallMethod(receiverName: string, name: string, numArgs: number, createBlockFunc: (params: params): block):void;
```

基本的なブロック(`value`, `value_boolean`, `statement`, `hat`)が変換できます。

- `receiverName`
  - 基本的には`self`
  - インスタンスメソッドの場合はインスタンス名になる<br>
    `command.puts`の場合は`command`となる
- `name`
  - 変換するメソッド名
- `numArgs`
  - 引数の数
- `createBlockFunc`
  - `(params) => {}`
  - `params`を引数に持つ即時関数
  - `params`には以下のようなものがある
    - `receiver`
      - レシーバ
    - `name`
      - メソッド名
    - `args`
      - 引数
    - `rubyBlockArgs`
      - 不明
    - `rubyBlock`
      - 不明
    - `node`
      - 構文木

### `registerOnSend`

```ts
registerOnSend(receiverName: string, name: string, numArgs: number, createBlockFunc: (params: params): block): void;
```

[`registerCallMethod`](#registercallmethod)と同じです

### `registerCallMethodWithBlock`

> 確認しきれていません

```ts
registerOnSendWithBlock(
  receiverName: string,
  name: string,
  numArgs: number,
  numRubyBlockArgs: number,
  createBlockFunc: (params: params): block
): void;
```

`end`がつくブロックを変換可能?

- `receiverName`
  - 基本的には`self`
  - インスタンスメソッドの場合はインスタンス名になる<br>
    `command.puts`の場合は`command`となる
- `name`
  - 変換するメソッド名
- `numArgs`
  - 引数の数
- `numRubyBlockArgs`
  - 不明
  - `end`側につく引数の数？
- `createBlockFunc`
  - `(params) => {}`
  - `params`を引数に持つ即時関数
  - `params`には以下のようなものがある
    - `receiver`
      - レシーバ
    - `name`
      - メソッド名
    - `args`
      - 引数
    - `rubyBlockArgs`
      - 不明
    - `rubyBlock`
      - 不明
    - `node`
      - 構文木

### `isString`

```ts
isString(value: args): boolean;
```

引数が文字列か確認します。<br>
引数には基本的に`args[n]`を入れます。

### `isNumber`

```ts
isNumber(value: args): boolean;
```

引数が数字か確認します。<br>
引数には基本的に`args[n]`を入れます。

### `isHash`

```ts
isHash(value: args): boolean
```

引数がハッシュか確認します。<br>
引数には基本的に`args[n]`を入れます。

### `isBlock`

```ts
isBlock(value: args): boolean;
```

引数がブロックか確認します。<br>
引数には基本的に`args[n]`を入れます。

### `isStringOrBlock`

```ts
isStringOrBlock(value: args): boolean;
```

引数が文字列もしくはブロックか確認します。<br>
引数には基本的に`args[n]`を入れます。

### `isNumberOrBlock`

```ts
isNumberOrBlock(value: args): boolean;
```

引数が数字もしくはブロックか確認します。<br>
引数には基本的に`args[n]`を入れます。

### `isNumberOrStringOrBlock`

```ts
isNumberOrStringOrBlock(value: args): boolean;
```

引数が数字もしくは文字列もしくはブロックか確認します。<br>
引数には基本的に`args[n]`を入れます

### `isRubyExpression`

```ts
isRubyExpression(block: block): boolean
```

引数が式か確認します<br>

### `getSource`

```ts
getSource(node: node): string
```

ブロックから Ruby コードを取得します<br>

### `getRubyExpression`

```ts
getRubyExpression(block: block): string
```

式として登録したブロックから Ruby コードを取得します<br>

### `createBlock`

```ts
createBlock(opcode: string, type: string, attributes = {}): block;
```

変換するブロックを指定します。

- `opcode`
  - オペコード
  - vm 側で決めたもの
- `type`
  - ブロックの形
  - `value`<br>
    ![value](/images/valueBlock.png)
  - `value_boolean`<br>
    ![value_boolean](/images/valueBooleanBlock.png)
  - `statement`<br>
    ![statement](/images/statementBlock.png)
  - `hat`<br>
    ![hat](/images/hatBlock.png)
- `attributes`
  - 不明
  - 引数の値？

### `createRubyExpressionBlock`

```ts
createRubyExpressionBlock(expression: string, node: node): block;
```

与えられた文字列を 1 つの式として設定しておく<br>
インスタンス部分を作成するときなどに用います。

- `expression`
  - 式
  - 文字列としてあたえる
- `node`
  - 構文木
  - 式に対応するノード

### `changeBlock`

```ts
changeBlock(block: block, opcode: string: blockType: string): block
```

`expression`以外の式を実際のブロックに変換する<br>

- `block`
  - 変換する式
- `opcode`
  - 実際に変換するブロックの opcode
  - vm 側で決めたもの
- `blockType`
  - ブロックの形
  - [`createBlock`](#createblock)を参照

### `changeRubyExpressionBlock`

```ts
changeRubyExpressionBlock(block: block, opcode: string, blockType: string): block;
```

`createRubyExpressionBlock`などで作った式を実際のブロックに変換する<br>
インスタンスメソッドを作成するときに用います。

- `block`
  - 変換する式
- `opcode`
  - 実際に変換するブロックの opcode
  - vm 側で決めたもの
- `blockType`
  - ブロックの形
  - [`createBlock`](#createblock)を参照

### `removeBlock`

```ts
removeBlock(block: block): void
```

与えられたブロックを削除する

```ruby
puts( 123 % 100)
```

は`puts`をブロックとして引数を持ち、引数の`123 % 100`も`123`、`100`を引数に持つブロックとして認識される<br>

このうち`123 % 100`をブロックとして認識させたくないときに使う

### `addTextInput`

```ts
addTextInput(block: block, name: string, inputValue: string | args, shadowValue: string): void;
```

ブロックに引数(文字列もしくはブロック)を渡します。

- `block`
  - `createBlock`などで作ったブロック
- `name`
  - vm 側で決めた変数名
- `inputValue`
  - 実際に渡す値
  - `args`もしくは`string`で渡す
- `shadowValue`
  - デフォルト値
  - `inputValue`に問題があった場合こちらの文字列で変換される

### `addNumberInput`

```ts
addNumberInput(block: block, name: string, opcode: string, inputValue: number | args, shadowValue: number): void;
```

ブロックに引数(数字もしくはブロック)を渡します。

- `block`
  - `createBlock`などで作ったブロック
- `name`
  - vm 側で決めた変数名
- `opcode`
  - 不明　以下のようにものがある
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
- `inputValue`
  - 実際に渡す値
  - `args`もしくは`Number`で渡す
- `shadowValue`
  - デフォルト値
  - `inputValue`に問題があった場合こちらの数字で変換される

### `addNoteInput`

```ts
addNoteInput(block: block, name: string, inputValue: number | args, shadowValue: number):void
```

ブロックに引数を値(鍵盤)を渡します<br>
![note block](/images/noteBlock.png)

- `block`
  - `createBlock`などで作ったブロック
- `name`
  - vm 側で決めた変数名
- `inputValue`
  - 実際に渡す値
- `shadowValue`
  - デフォルト値

### `addFieldInput`

```ts
addFieldInput(block: block, name: string, opcode: string, fieldName: string, inputValue: string | args, shadowValue: string): void;
```

ブロックに引数(メニューもしくはブロック)を渡します。<br>
ブロックを引数に持てるタイプのメニューに引数を渡すことが可能です。<br>
![menu](/images/menuBlock.png)

- `block`
  - `createBlock`などで作ったブロック
- `name`
  - vm 側で決めた変数名
- `opcode`
  - gui 側で定義したメニュー名
- `fieldName`
  - vm 側で決めたメニュー名
- `inputValue`
  - 実際に渡す値
- `shadowValue`
  - デフォルト値
  - `inputValue`に問題があった売位こちらの値で変換される

特殊<br>
![alt text](/images/colorBlock.png)<br>
カラーパレットに変換も可能

```ts
addFieldInput(block, "COLOR", "colour_picker", "COLOUR", args[0], "#43066f");
```

### `addField`

```ts
addField(block: block, name: string, value: string | args, attributes = {}): void;
```

ブロックに引数(メニュー)を渡します。<br>
ブロックを引数に持てないタイプのメニューに引数を渡すことが可能です。<br>
![menu](/images/menu.png)

- `block`
  - `createBlock`などで作ったブロック
- `name`
  - vm 側で決めた変数名
- `value`
  - 実際に渡す値
- `attribute`
  - 不明
  - デフォルト値？

### `addInput`

```ts
addInput(block: block, name: string, inputBlock: string | block, shadowBlock: block): void;
```

不明<br>
`addFieldInput`と同じようなことをしている？

- `block`
  - `createBlock`などで作ったブロック
- `name`
  - vm 側で決めた変数名
- `inputBlock`
  - 不明
- `shadowBlock`
  - 不明

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
Generator.getFieldValue(block, 引数名);
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
