# 通常メソッド

## `puts "command0"`を逆変換する場合

基本的な形(引数入力なし)

```js
const SampleConverter = {
  register: function (converter) {
    converter.registerCallMethod("self", "puts", 1, (params) => {
      const { args } = params;
      if (!args[0].value === "command0") return null;

      const block = converter.createBlock("sample_command0", "statement");
      return block;
    });
  },
};
export default SampleConverter;
```

### 詳細

```js
converter.registerCallMethod("self", "puts", 1, (params) => {
```

`registerCallMethod`の引数は 1 つ目がインスタンスメソッドでないため`self`、<br>
2 つ目の引数はメソッド名が`puts`なので`puts`、<br>
3 つ目の引数は変換するメソッドの引数の数なので`1`となります。<br>
<br>

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

```js
if (!args[0].value === "command0") return null;
```

引数の値の型が正しいかを確認します。
正しくなかった場合は`null`を返します。
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
  ![statement](/images/statementblock.png)<br>
- `hat`<br>
  ![alt text](/images/hatblock.png)

<br>

## `puts(command1, ${text}, ${num})`を逆変換する場合

---

基本的な形(引数入力あり)

```js
const SampleConverter = {
  register: function (converter) {
    converter.registerCallMethod("self", "puts", 3, (params) => {
      const { args } = params;
      if (!args[0].value === "command1") return null;
      if (!converter.isStringOrBlock(args[1])) return null;
      if (!converter.isNumberOrBlock(args[2])) return null;

      const block = converter.createBlock("sample_command1", "statement");
      converter.addTextInput(block, "TEXT", args[1], "hello");
      converter.addNumberInput(block, "NUM", "math_number", args[2], 2);
      return block;
    });
  },
};
export default SampleConverter;
```

### 詳細

```js
if (!args[0].value === "command1") return null;
if (!converter.isStringOrBlock(args[1])) return null;
if (!converter.isNumberOrBlock(args[2])) return null;
```

入ってくる引数が正しいか確認します<br>
1 つ目は`command1`という文字列かを確認<br>
2 つ目は文字列もしくはブロックかを確認<br>
3 つ目は数字もしくはブロックかを確認<br>
2 つ目と 3 つ目はブロックをはじきたい場合は、`isString`か`isNumber`を使います。<br>


```js
converter.addTextInput(block, "TEXT", args[1], "hello");
converter.addNumberInput(block, "NUM", "math_number", args[2], 2);
```

実際に変換するブロックを指定します。<br>
この際にブロック側に引数がある場合は引数に値を渡します。<br>
引数の渡し方は以下の通りです。<br>

- `converter.addTextInput`
  - テキストを入れる関数
  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目は渡す値
  - 4 つ目は不明、デフォルト値？
- `converter.addNumberInput`
  - 数字を入れる関数
  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目は不明、`math_number`以外見たことない
  - 4 つ目は実際に渡す値
  - 5 つ目は不明、デフォルト値？
- `converter.addField`
  - ブロックの入らないメニュー<br>
    ![menu](/images/menu.png)
- `converter.addFieldInput`
  - ブロックの入るメニュー<br>
    ![menu-block](/images/menu-block.png)
- `converter.addInput`
  - 不明
  - 特殊型?

<br>

## `puts(command3, ${text1}, ${num1})`を逆変換する場合

---

基本的な形(メニューあり)

```js
const SampleConverter = {
  register: function (converter) {
    converter.registerCallMethod("self", "puts", 3, (params) => {
      const { args } = params;
      if (!args[0].value === "command3") return null;
      if (!converter.isStringOrBlock(args[1])) return null;
      if (!converter.isStringOrBlock(args[2])) return null;

      const block = converter.createBlock("sample_command3", "statement");
      converter.addFieldInput(block, "TEXT1", "sample_menu_menu1", "menu1", args[1].value, "hoge");
      converter.addFieldInput(block, "NUM1", "sample_menu_menu2", "menu2", args[2].value, "-1");
      return block;
    });
  },
};
export default SampleConverter;
```

### 詳細

```js
converter.addFieldInput(block, "TEXT1", "sample_menu_menu1", "menu1", args[1].value, "hoge");
converter.addFieldInput(block, "NUM1", "sample_menu_menu2", "menu2", args[2].value, "-1");
```

このメソッドはブロック側での入力がプルダウンメニューになっているため、
少し引数の渡し方が特殊です。<br>
上でも少し紹介しましたがメニュー用の関数を使います。<br>
2 つ目の引数は vm 側で定義した変数の名前<br>
3 つ目の引数は `ruby-generator`で定義したメニューの名前<br>
4 つ目の引数は vm 側の`menus`で定義したメニューの名前<br>
5 つ目の引数は実際に渡す値<br>
6 つ目の引数は不明。デフォルト値?<br>

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
converter.registerCallMethod("tools", "puts", 0, (params) => {
  const { receiver, node } = params;

  if (!converter.isStringOrBlock(args[0])) return null;

  const block = converter.changeRubyExpressionBlock(receiver, "tools_puts", "statement");
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

## その他

```js
converter.registerCallMethod("tools", "x=", 1, (params) => {
```

メソッド名の部分に`=`が使えるようになっています。
この場合`=`以降を引数として扱います

変換後
```js
tools.x = 10
```
