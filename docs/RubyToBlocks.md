# Ruby -> Block 逆変換マニュアル

> [SmT](https://github.com/gfd-dennou-club/smt-gui/wiki/SmT-developer)のブロックから Ruby の変換の続きとして作成しています。<br>
> また現状では複数行の逆変換には対応していません

## ファイルの作製

`smt-gui/src/lib/ruby-to-blocks-converter/`以下にファイルを作成します。<br>
その際の名前はブロックから Ruby への変換で作ったファイルと同じにしておきます。
名前に関しては制限はありませんが、正変換との対応を分かりやすくするため同じにします。<br>
変換の詳細については[別ページ](./RubyToBlocksConverter.md)にまとめます。<br>

sample.js

```js
const SampleConverter = {
  register: function (converter) {
    // 変換したいブロックとの対応
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
// 略
import SampleConverter from "./sample.js";
// 略
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
  SampleConverter, // 追加
].forEach((x) => x.register(this));
// 略
```