# 通常メソッド

## `value`を逆変換する場合

![nomal_block](/images/nomal_block.png)

基本的な形(引数入力なし)

```js
const SampleConverter = {
  register: function (converter) {
    converter.registerCallMethod("self", "value", 0, (params) => {
      const block = converter.createBlock("sample_command0", "statement");
      return block;
    });
  },
};
export default SampleConverter;
```

### 詳細

```js
converter.registerCallMethod("self", "puts", 0, (params) => {
```

`registerCallMethod`の引数は 1 つ目がインスタンスメソッドでないため`"self"`、<br>
2 つ目の引数はメソッド名なので`"value"`、<br>
3 つ目の引数は変換するメソッドの引数の数なので`0`となります。<br>
<br>

```js
const block = converter.createBlock("sample_command0", "statement");
return block;
```

実際に変換するブロックを指定します。<br>
この時インスタンスメソッドでない場合は`createBlock`を使用します。<br>
1 つ目の引数は`ruby-generator`で定義したメソッド名を入れます。<br>
2 つ目の引数はブロックの形を指定します。<br>
ブロックの形は以下のようなものがあります。<br>

- `value`<br>
  ![value](/images/valueblock.png)<br>
- `value_boolean`<br>
  ![alt text](/images/value-booleanblock.png)<br>
- `statement`<br>
  ![statement](/images/statement-block.png)<br>
- `hat`<br>
  ![alt text](/images/hatblock.png)

## `puts(${num})`を逆変換する場合

![statement-block](/images/statement-block.png)

基本的な形(引数入力あり)

```js
const SampleConverter = {
  register: function (converter) {
    converter.registerCallMethod("self", "puts", 1, (params) => {
      const { args } = params;
      if (!converter.isNumberOrBlock(args[0])) return null;

      const block = converter.createBlock("sample_command1", "statement");
      converter.addNumberInput(block, "NUM", "math_number", args[0], 2);
      return block;
    });
  },
};
export default SampleConverter;
```

### 詳細

```js
const { args } = params;
```

`params`は`args`に分割代入します。<br>

逆変換に使う`params`の値は以下のようなものがあります。

- `args`
  - 引数の情報
  - オブジェクト配列になっている
  - 一つ目の引数の値を取り出す際は`args[0].value`といった風に使う
- `receiver`
  - 不明。インスタンスの情報?
  - インスタンスメソッドを変換するときに使う
- `node`
  - 不明。ブロック全体の情報?
  - インスタンスメソッドを変換するときに使う
    <br>

<br>

```js
if (!converter.isNumberOrBlock(args[0])) return null;
```

入ってくる引数が正しいか確認します<br>

- `isNumberOrBlock`
  - ブロックもしくは数字
- `isStringOrBlock`
  - ブロックもしくは文字列
- `isNumber`
  - 数字のみ
- `isString`
  - 文字列のみ
- `isBlock`
  - ブロック

特定の文字列などのみの場合は

```js
if (!args[0].value === "command1") return null;
```

などのようにします。
<br>

```js
converter.addNumberInput(block, "NUM", "math_number", args[0], 2);
```

実際に変換するブロックを指定します。<br>
この際にブロック側に引数がある場合は引数に値を渡します。<br>
引数の渡し方は以下の通りです。<br>

- テキストを入れる関数

  ```js
  converter.addTextInput(block, "TEXT", args[0], "hello");
  ```

  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目は渡す値
  - 4 つ目は不明、デフォルト値？

- 数字を入れる関数

  ```js
  converter.addNumberInput(block, "NUM", "math_number", args[0], 2);
  ```

  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目は不明、`math_number`以外見たことない
  - 4 つ目は実際に渡す値
  - 5 つ目は不明、デフォルト値？

- ブロックの入らないメニュー

  ```js
  converter.addField(block, "TEXT", "hello");
  ```

  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目は渡す値

  ![menu](/images/menu.png)

- ブロックの入るメニュー

  ```js
  converter.addFieldInput(
    block,
    "TEXT1",
    "sample_menu_menu1",
    "menu1",
    args[1],
    "hoge"
  );
  ```

  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目の引数は`ruby-generator`で定義したメニューの名前
  - 4 つ目の引数は vm 側の`menus`で定義したメニューの名前
  - 5 つ目の引数は実際に渡す値
  - 6 つ目の引数は不明、デフォルト値？

  ![menu-block](/images/menu-block.png)

- `converter.addInput`
  - 不明
  - 特殊型?

## `args`の取り方

基本的な取り方としては`args[0]`のように取ります。

```js
swap(num1, num2);
```

というメソッドがあった場合、<br>
1 つ目の引数を取る場合は`args[0]`となります。<br>
2 つ目の引数を取る場合は`args[1]`となります。<br>

```js
swap(first: num1, second: num2, third: num3)
```

というメソッドがあった場合、<br>
1 つ目の引数を取る場合は`args[0].get('sym:first')`となります。<br>
2 つ目の引数を取る場合は`args[0].get('sym:second')`となります。<br>
3 つ目の引数を取る場合は`args[0].get('sym:third')`となります。<br>

ハッシュは 1 つの変数としてカウントされます

また引数の値を読み取る場合は`args[0].value`という風に取ります。<br>
引数に入っているのが値かブロックか分からないため、基本的に型の確認や引数を渡す際は`args[0]`といった形で渡しましょう。<br>
ただブロックを受け入れない場合などは`args[0].value`といった形で問題ありません。<br>
<br>

# インスタンスメソッド

```js
tools.puts("test");
```

というインスタンスメソッドがあった場合、逆変換は以下のようになります。

```js
register: function (converter) {
  converter.registerCallMethod("self", "tools", 0, (params) => {
    const { node } = params;
    return converter.createRubyExpressionBlock("tools", node);
  });

  converter.registerCallMethod("tools", "puts", 1, (params) => {
    const { receiver, args } = params;

    if (!converter.isStringOrBlock(args[0])) return null;

    const block = converter.changeRubyExpressionBlock(receiver,　"tools_puts", "statement");
    converter.addTextInput(block, "TEXT", args[0], "test");
    return block;
  });
},
```

## インスタンスを作成

```js
converter.registerCallMethod("self", "tools", 0, (params) => {
  const { node } = params;
  return converter.createRubyExpressionBlock("tools", node);
});
```

今回の場合は`tools`がインスタンス名なので、<br>
`registerCallMethod`と`createRubyExpressionBlock`にそれぞれ当てはます。
この時インスタンス名は

- 大文字は使えない
- `.`、`=`などの一部文字は使えない

などの制限があります。

`params`から分割代入する値は`node`のみになります。

## インスタンスメソッド

```js
converter.registerCallMethod("tools", "puts", 1, (params) => {
  const { receiver, args, node } = params;

  if (!converter.isStringOrBlock(args[0])) return null;

  const block = converter.changeRubyExpressionBlock(
    receiver,
    "tools_puts",
    "statement"
  );
  converter.addTextInput(block, "TEXT", args[0], "test");
  return block;
});
```

通常のメソッドとの違いは

- `registerCallMethod`の`self`だった部分がインスタンス名になっている。
- `params`から分割代入する値に`receiver`が追加。
- `createBlock`が`changeRubyExpressionBlock`になっている。

という部分<br>
それ以外は基本的に同じになっています。

`changeRubyExpressionBlock`では<br>
1 つ目の引数が`params`から分割代入した`receiver`<br>
2 つ目の引数が`ruby-generator`でのメソッド名<br>
3 つ目の引数がブロックの形<br>
となります。

# 代入式

## 通常

```js
converter.registerCallMethod("tools", "x=", 1, (params) => {
```

メソッド名の部分に`=`が使えるようになっています。
この場合`=`以降を引数として扱います

変換後

```js
tools.x = 10;
```

## メソッドの場合

代入する値がメソッドなどの場合は以下のようになります

変換する Ruby コード

```ruby
sample = Sample.new()
```

変換するためのコード

```js
const SampleConverter = {
  register: function (converter) {
    converter.registerCallMethod("self", "Sample", 0, (params) => {
      const { node } = params;
      return converter.createRubyExpressionBlock("Sample", node);
    });

    converter.registerCallMethod("Sample", "new", 0, (params) => {
      const { node } = params;
      return converter.createRubyExpressionBlock("Sample.new()", node);
    });
  },
  onVasgn: function (scope, variable, rh) {
    const expression = this._getRubyExpression(rh);

    if (!expression) return null;
    if (variable.name === "sample");

    return this._changeRubyExpressionBlock(rh, "sample_init", "statement");
  },
};
```

### 詳細

```js
register: function(converter) {
```

この中で`Sample.new()`を解決します<br>
内容については[インスタンス](#インスタンスメソッド)を見てください

```js
onVasgn: function (scope, variable, rh) {
```

この中で代入式`sample =`を解決します<br>
`scope`は`locak`や`global`といった変数のスコープ<br>
`variable`は左辺<br>
`rh`は右辺<br>
が入ってきます

```js
const expression = this._getRubyExpression(rh);
```

`register:function`で解決した`Sample.new()`を取り出します<br>

```js
if (!expression) return null;
if (variable.name === "sample");
```

正しく入っているか確認します

```js
return this._changeRubyExpressionBlock(rh, "sample_init", "statement");
```

内容は割愛します<br>
[インスタンス](#インスタンスメソッド)を見てください

# Block について

coming soon
