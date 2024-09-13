# Ruby -> Block 逆変換マニュアル

> [SmT](https://github.com/gfd-dennou-club/smt-gui/wiki/)のブロックから Ruby の変換の続きとして作成しています。<br>
> また現状では複数行の逆変換には対応していません

## ファイルの作製

`smt-gui/src/lib/ruby-to-blocks-converter/`にファイルを作成します。<br>
その際の名前はブロックから Ruby への変換で作ったファイルと同じにしておきます。
名前に関しては制限があるなどではなく、正変換との対応を分かりやすくするため同じにします。

sample.js

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

## index.js の変更

`smt-gui/src/lib/ruby-to-blocks-converter/index.js`の内容を変更します

index.js

```js
//略
import SampleConverter from "./sample.js";
//略
[
  // EventConverter,
  ControlConverter,
  // MicroBitConverter,
  // VideoConverter,
  // Text2SpeechConverter,
  // Wedo2Converter,
  // MicrobitMoreConverter,
  // MeshConverter,
  ToolsConverter,
  Kanirobo2Converter,
  SampleConverter, //追加
].forEach((x) => x.register(this));
//略
```

## 実装方法

### `puts "command0"`を逆変換する場合

---

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

`registerCallMethod`の引数は 1 つ目がインスタンスメソッドでないため`self`、<br>
2 つ目の引数はメソッド名が`puts`なので`puts`、<br>
3 つ目の引数は変換するメソッドの引数の数なので`1`となります。<br>

```js
converter.registerCallMethod("self", "puts", 1, (params) => {
```

`params`は`args`に分割代入します。<br>

逆変換に使う`params`の値は以下のようなものがあります。

- `args`は引数の情報
  - オブジェクト配列になっている
  - 一つ目の引数の値を取り出す際は`args[0].value`といった風に使う
- `receiver`は不明
  - インスタンスメソッドを変換するときに使う
- `node`は不明　ブロック全体の情報?
  - インスタンスメソッドを変換するときに使う

```js
const { args } = params;
```

引数の値の型が正しいかを確認します。
正しくなかった場合は`null`を返します

```js
if (!args[0].value === "command0") return null;
```

実際に変換するブロックを指定する。
この時インスタンスメソッド出ない場合は`createBlock`を使用します。<br>
1 つ目の引数は`ruby-generater`でのメソッド名を入れます。<br>
2 つ目の引数はブロックの形を指定します。<br>
ブロックの形は以下のようなものがあります。<br>
`value`<br>
![value](/images/valueblock.png)<br>
`statement`<br>
![statement](/images/statementblock.png)<br>
`hat`<br>
![alt text](/images/hatblock.png)

```js
const block = converter.createBlock("sample_command0", "statement");
return block;
```

このブロックでは引数が固定なのでこれで終わりです。

### `puts(command1, ${text}, ${num})`を逆変換する場合

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

入ってくる引数が正しいか確認します<br>
1 つ目は`command1`という文字列かを確認<br>
2 つ目は文字列もしくはブロックかを確認<br>
3 つ目は数字もしくはブロックかを確認<br>
2 つ目と 3 つ目はブロックをはじきたい場合は、`isString`か`isNumber`を使います。<br>

```js
if (!args[0].value === "command1") return null;
if (!converter.isStringOrBlock(args[1])) return null;
if (!converter.isNumberOrBlock(args[2])) return null;
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
  - 特殊

```js
converter.addTextInput(block, "TEXT", args[1], "hello");
converter.addNumberInput(block, "NUM", "math_number", args[2], 2);
```

### `puts(command3, ${text1}, ${num1})`を逆変換する場合

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
      converter.addFieldInput(
        block,
        "TEXT1",
        "sample_menu_menu1",
        "menu1",
        args[1].value,
        "hoge"
      );
      converter.addFieldInput(
        block,
        "NUM1",
        "sample_menu_menu2",
        "menu2",
        args[2].value,
        "-1"
      );
      return block;
    });
  },
};
export default SampleConverter;
```

このメソッドはブロック側での入力がプルダウンメニューになっているため、
少し変数の渡し方が特殊です。
上でも少し紹介しましたがメニュー用の関数を使います
2 つ目の引数は vm 側で定義した変数の名前、<br>
3 つ目の引数は `ruby-generator`で定義したメニューの名前、<br>
4 つ目の引数は vm 側の`menus`で定義したメニューの名前、<br>
5 つ目の引数は実際に渡す値、<br>
6 つ目の引数は不明、デフォルト値?<br>
という風になります。

```js
converter.addFieldInput(
  block,
  "TEXT1",
  "sample_menu_menu1",
  "menu1",
  args[1].value,
  "hoge"
);
converter.addFieldInput(
  block,
  "NUM1",
  "sample_menu_menu2",
  "menu2",
  args[2].value,
  "-1"
);
```
