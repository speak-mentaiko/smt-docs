# 補完機能

## 補完機能とは

![snippet](/images/snippet.png)

ruby タブでコードを補完してくれる機能

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
    "description": "command1 (hello) (0) と表示"
  }
}
```

書く内容は以下のようになる

```json
{
  "メッソド名": {
    "snippet": 簡単なメソッドの例,　//たぶん
    "description": メソッドのブロックとの対応
  }
}
```
