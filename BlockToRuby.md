# Block -> Ruby 変換マニュアル

## ファイルの作製

`smt-gui/src/lib/ruby-generator/`以下にファイルを作成します。<br>
ファイル名は拡張機能と同じ名前を付けるとよいです。<br>
変換の詳細については[別ページ](./RubyGenerator.md)にまとめます。<br>

sample.js

```js
export default function (Generator) {
    Generator.sample_init = function() {
        return `Class.new\n`;
    };
    Generator.sample_command0 = function () {
        Generator.prepares_[`sample`] = Generator.sample_init(null);
        return `puts "command0"\n`;
    };
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
SampleBlocks(RubyGenerator);

export default RubyGenerator;
```