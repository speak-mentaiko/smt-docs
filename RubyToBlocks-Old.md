# Ruby -> Block 逆変換マニュアル(旧版)

[旧版資料](https://github.com/gfd-dennou-club/smt-gui/wiki/SmT-gui-blockgen)

上記の資料では変換できないので新たに資料を作成する。<br>
またメモ程度なのでそこまで期待しないでもらいたい。

## 実装方法

基本的にファイル作成と index.js をいじる部分に関してはそのままで大丈夫そう

### 「インスタンス.メソッド()」の場合

```js
onSend: function (receiver, name, args, rubyBlockArgs, rubyBlock, variable) {
        let block;
        switch (name) {
            case 'メソッド名': // この case 文で生成したいブロックに対応する Rubyのメソッドか判定
                if (receiver.toString() === '::インスタンス名') {// 対応するインスタンスか判定
                    // ブロック生成
                }
                break;
        }
        return block;
    },
```

となる。

`tools.puts()`を逆変換する場合は以下のようになる。

```js
onSend: function (receiver, name, args, rubyBlockArgs, rubyBlock, variable) {
    let block;
    switch (name) {
        case 'puts': // この case 文で生成したいブロックに対応する Rubyのメソッドか判定
            if (receiver.toString() === '::tools') {// 対応するインスタンスか判定
                    // ブロック生成
            }
            break;
    }
    return block;
},
```
