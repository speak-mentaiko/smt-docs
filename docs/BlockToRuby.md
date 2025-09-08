# Block -> Ruby 変換マニュアル

## ファイルの作製

`smt-gui/src/lib/ruby-generator/`以下にファイルを作成します。<br>
ファイル名は拡張機能と同じ名前を付けるとよいです。<br>
変換の詳細については[別ページ](./RubyGenerator.md)にまとめます。<br>

sample.js

```js
export default function (Generator) {
  // クラス定義など
  Generator.sample_init = function () {
    return `Class.new\n`;
  };

  // 変換したいコードとの対応
  Generator.sample_command0 = function () {
    Generator.prepares_[`sample`] = Generator.sample_init(null);
    return `puts "command0"\n`;
  };

  // メニュー定義
  Generator.sample_month_menu = function (block) {
    const menu1 = Generator.getFieldValue(block, "month") || null;
    return [menu1, Generator.ORDER_ATOMIC];
  };

  return Generator;
}
```

変換したいブロックにつき定義を増やしていく。<br>
名前は基本的に`[拡張機能名]_[メソッド名]`のようにする。

## index.js の変更

`smt-gui/src/lib/ruby-generator/index.js`の内容を変更します

index.js

```js
// 略
import SampleBlocks from "./sample.js";
// 略
M5stackBlocks(RubyGenerator);
SensorBlocks(RubyGenerator);
RboardBlocks(RubyGenerator);
SampleBlocks(RubyGenerator); // 追加
export default RubyGenerator;
```
