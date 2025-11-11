# 補完機能

## 補完機能とは

![snippet](/images/snippets.png)

ruby タブでコードを補完してくれる機能<br>
メソッドの簡単な説明がついている

## 実装方法

`smt-gui/src/containers/ruby-tab/`に`sample-snippets.json`を作成します。

```jsonc
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

```jsonc
{
  "メソッド名": {
    "snippet": "簡単なメソッドの例",
    "description": "ブロックの文言"
  }
}
```

### 例

![snippets](/images/snippets-case.png)

```jsonc
"snippet": "microbit.display_text(\"こんにちは!\")",
```

![code](/images/snippets-code.png)

```jsonc
"description": "(こんにちは!)を表示する"
```

![block](/images/snippets-block.png)
