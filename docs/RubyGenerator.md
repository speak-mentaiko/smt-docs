# 変換方法

## 通常ブロック

```js
Generator.sample_command0 = function () {
  return `puts "command0"\n`;
};
```

![block](/images/nomal_block.png)

そのまま出したいコードを返す

## 値ブロック

```js
Generator.sample_value0 = function () {
  return ["value0", Generator.ORDER_ATOMIC];
};
```

![valueblock](/images/valueblock.png)

返したいコードと`Generator.ORDER_ATOMIC`を配列にして返す

## 通常ブロック(引数あり 丸)

```js
Generator.sample_command1 = function (block) {
  const text =
    Generator.valueToCode(block, "TEXT", Generator.ORDER_NONE) || null;
  const num = Generator.valueToCode(block, "NUM", Generator.ORDER_NONE) || 0;
  return `puts(command1, ${text}, ${num})\n`;
};
```

![menu](/images/menu-block.png)<br>
画像のようなメニューの際に使う。

引数は`Generator.valueToCode`で取得する。<br>
2 つ目の引数は vm 側で定義した変数名.この場合は`"TEXT"`<br>
3 つ目は基本的に`Generator.ORDER_NONE`<br>

## 通常ブロック(引数あり 四角)

```js
Generator.sample_command1 = function (block) {
  const text = Generator.getFieldValue(block, "TEXT") || null;
  const num = Generator.getFieldValue(block, "NUM") || null;
  return `puts(command1, ${text}, ${num})\n`;
};
```

![menu](/images/menu.png)<br>
画像のようなメニューの際に使う。

引数は`Generator.getFieldValue`で取得する。<br>
2 つ目の引数は vm 側で定義した変数名.この場合は`"TEXT"`と`"NUM"`<br>

## 値ブロック(引数あり)

```js
Generator.sample_value1 = function (block) {
  const order = Generator.ORDER_MULTIPLICATIVE;
  const num = Generator.valueToCode(block, "NUM", order) || 0;
  return [`${num} * 2`, order];
};
```

![valueblock-menu](/images/valueblock-menu.png)

返すコードによって`order`の中身を変える<br>
この場合は積を計算するため`ORDER_MULTIPLICATIVE`を使用する。<br>
このほかの`order`は`ruby-generator/index.js`に記述があるので、それを参考にする。<br>
[その他](./function.md#order-シリーズ)

## クラス定義

```js
Generator.sample_init = function () {
  return `Class.new\n`;
};
Generator.sample_command0 = function () {
  Generator.prepares_[`sample`] = Generator.sample_init(null);
  return `puts "command0"\n`;
};
```

`Generator.prepares_["sample"]`を使うことで別の関数を呼び出すことができる。<br>

## メニュー定義

```js
Generator.sample_menu_menu1 = function (block) {
  const menu1 = Generator.getFieldValue(block, "menu1") || null;
  return [menu1, Generator.ORDER_ATOMIC];
};
```

Ruby -> Block の変換の際に使います。<br>
メニューの内容は`Generator.getFieldValue`で取得します。<br>
2 つ目の引数は vm 側で定義したメニュー名<br>
