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

```js
converter.isNumberOrBlock(args[0]);
```

- `isString`
  - 文字列かどうかを確認
- `isNumber`
  - 数字かどうかを確認
- `isBlock`
  - 不明
- `isStringOrBlock`
  - 文字列もしくはブロックか確認
- `isNumberOrBlock`
  - 数字もしくはブロックか確認

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
  - 3 つ目は不明、`math_number`以外見たことない
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
- `converter.addInput`
  - 特殊
