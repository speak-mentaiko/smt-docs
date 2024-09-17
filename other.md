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

    const block = converter.changeRubyExpressionBlock(
      receiver,
      "tools_puts",
      "statement"
    );
    converter.addTextInput(block, "TEXT", args[0], "test");
    return block;
  });
},
```

## インスタンスを作成

今回の場合は`tools`がインスタンス名なので、<br>
`registerCallMethod`と`createRubyExpressionBlock`にそれぞれ当てはめる。
この時インスタンス名は

- 大文字は使えない
- `.`、`=`などの一部文字は使えない

などの制限がある。

`params`から分割代入する値は`node`のみになる。

```js
converter.registerCallMethod("self", "tools", 0, (params) => {
  const { node } = params;
  return converter.createRubyExpressionBlock("tools", node);
});
```

## インスタンスメソッド

通常のメソッドとの違いは

- `registerCallMethod`の`self`だった部分がインスタンス名になっている。
- `params`から分割代入する値に`receiver`が追加。
- `createBlock`が`changeRubyExpressionBlock`になっている。

という部分<br>
それ以外は基本的に同じになっている。

`changeRubyExpressionBlock`では<br>
1 つ目の引数が`params`から分割代入した`receiver`<br>
2 つ目の引数が`ruby-generater`でのメソッド名<br>
3 つ目の引数がブロックの形<br>
となります。

メソッド名の部分に`=`が使えるようになっている。
この場合`=`以降を引数として扱う

```js
converter.registerCallMethod("tools", "puts", 0, (params) => {
  const { receiver, node } = params;

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

# 補完機能

## 補完機能とは

![snippet](/images/snippets.png)

ruby タブでコードを補完してくれる機能<br>
メソッドの簡単な説明がついている

## 実装方法

`smt-gui/src/containers/ruby-tab/`に`sample-snippets.json`を作成します。

```json
{
  "command0": {
    "snippet": "puts \"command0\"",
    "description": "command0 と表示"
  },
  "command1": {
    "snippet": "puts (command1, \"hello\", 0)",
    "description": "command1 (hello) (0) を表示"
  }
}
```

`smt-gui/src/containers/ruby-tab/snippets-completer.js`に内容を追加します。

```js
//略
import SampleSnippets from "./sample-snippets.json";
//略
const snippetsList = [
  MotionSnippets,
  LooksSnippets,
  SoundSnippets,
  EventsSnippets,
  ControlSnippets,
  SensingSnippets,
  OperatorsSnippets,
  VariablesSnippets,
  ProcedureSnippets,

  MusicSnippets,
  PenSnippets,
  VideoSensingSnippets,
  TextToSpeechSnippets,
  TranslateSnippets,
  MicrobitSnippets,
  MeshSnippets,
  SmalrubotS1Snippets,
  MicrobitMoreSnippets,
  SampleSnippets, //追加
];
```

書く内容は以下のようになる

```json
{
  "メソッド名": {
    "snippet": "簡単なメソッドの例",
    "description": "ブロックの文言"
  }
}
```

### 例

![snippets](/images/snippets-case.png)

```json
"snippet": "microbit.display_text(\"こんにちは!\")",
```

![code](/images/snippets-code.png)

```json
"description": "(こんにちは!)を表示する"
```

![block](/images/snippets-block.png)

# その他

こまごまとした仕様？を書きます

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

ハッシュは 1 つとしてカウントされます

また引数の値を読み取る場合は`args[0].value`という風に取ります。<br>
引数に入っているのが値かブロックか分からないため、基本的に型の確認や引数を渡す際は`args[0]`といった形で渡しましょう。<br>
ただブロックを受け入れない場合などは`args[0].value`といった形で問題ありません。<br>

## 型の確認

引数の値が正しい型かを確認する関数たちです。

- `isString`
  - 文字列かどうかを確認

```js
converter.isString(args[0]);
```

- `isNumber`
  - 数字かどうかを確認

```js
converter.isNumber(args[0]);
```

- `isBlock`
  - 不明

```js
converter.isBlock(args[0]);
```

- `isStringOrBlock`
  - 文字列もしくはブロックか確認

```js
converter.isStringOrBlock(args[0]);
```

- `isNumberOrBlock`
  - 数字もしくはブロックか確認

```js
converter.isNumberOrBlock(args[0]);
```

## 引数を渡す

変換するメソッドに引数を渡します。

- `converter.addTextInput`
  - テキストを入れる関数
  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目は渡す値
  - 4 つ目は不明、デフォルト値？
  ```js
  converter.addTextInput(block, "TEXT", args[1], "hello");
  ```
- `converter.addNumberInput`
  - 数字を入れる関数
  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目は不明、~~`math_number`以外見たことない~~
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
  - 4 つ目は実際に渡す値
  - 5 つ目は不明、デフォルト値？
  ```js
  converter.addNumberInput(block, "NUM", "math_number", args[2], 2);
  ```
- `converter.addField`
  - ブロックの入らないメニュー<br>
    ![menu](/images/menu.png)
  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目の引数は実際に渡す値
  - 4 つ目の引数は不明。無くても問題ない
  ```js
  converter.addField(block, "TEXT1", "32");
  ```
- `converter.addFieldInput`

  - ブロックの入るメニュー<br>
    ![menu-block](/images/menu-block.png)
  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目の引数は `ruby-generator`で定義したメニューの名前
  - 4 つ目の引数は vm 側の`menus`で定義したメニューの名前
  - 5 つ目の引数は実際に渡す値
  - 6 つ目の引数は不明、デフォルト値?

  ```js
  converter.addFieldInput(
    block,
    "TEXT2",
    "kanirobo2_menu_menu1",
    "menu1",
    args[0].value,
    "0"
  );
  ```

  特殊<br>
  ![alt text](/images/color-block.png)<br>
  カラーパレットの変換も可能

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

  - 2 つ目の引数は vm 側で定義した変数の名前
  - 3 つ目の引数は `colour_picker`固定
  - 4 つ目の引数は `COLOUR`固定？
  - 5 つ目の引数は実際に渡す値
  - 6 つ目の引数は不明、デフォルト値?

- `converter.addInput`

  - 特殊

- `colour_picker` - カラーコード
