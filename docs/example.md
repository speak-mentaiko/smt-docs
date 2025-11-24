# 実例 `kanirobo2`の場合

[ファイル](https://github.com/gfd-dennou-club/smt-gui/blob/develop/src/lib/ruby-to-blocks-converter/kanirobo2.js)はリンクのみ記載します

## `gpio25 = GPIO.new( 25, GPIO::OUT )`

### `GPIO.new( 25, GPIO::OUT )`

```js
const classNames = ['GPIO', 'PWM', 'ADC'];
// 省略
register: function (converter) {
    // GPIO,PWM,ADC
    classNames.forEach((className) => {
        converter.registerCallMethod('self', className, 0, (params) => {
            const { node } = params;

            return converter.createRubyExpressionBlock(className, node);
        });
    });
// 省略
```

`GPIO.new( 25, GPIO::OUT )`の`GPIO`の部分を作成<br>
同時に`PWM.new`と`ADC.new`のそれぞれも作成

```js
// GPIO.new()
converter.registerCallMethod("GPIO", "new", 2, (params) => {
  const { args, node } = params;
  if (!converter.isNumber(args[0])) return null;
  if (args[0].value !== 25 && args[0].value !== 32) return null;
  // ToDo: GPIO::OUTについてもチェックができるといい

  const expression = `GPIO.new(${args[0].value}, GPIO::OUT)`;
  return converter.createRubyExpressionBlock(expression, node);
});
```

`GPIO.new( 25, GPIO::OUT )`をいったん作成<br>
1 つ目の引数がピン番号なので数字かつ`25`,`32`であることを確認<br>
代入式が残っているので`createRubyExpressionBlock`

### `gpio25 =`

```js
// 省略
const gpioPins = ["25", "32"];
// 省略

// gpio
gpioPins.forEach((pin) => {
  converter.registerCallMethod("self", "gpio" + pin, 0, (params) => {
    const { node } = params;
    return converter.createRubyExpressionBlock("gpio" + pin, node);
  });
});
```

`gpio25 = `の`gpio25`の部分を作成<br>
`gpio`では`25`,`32`2 つの変数があるため同時に作成

```js
onVasgn: function (scope, variable, rh) {
    const expression = this._getRubyExpression(rh);
    if (!expression) return null;

    const className = expression.substring(0, expression.indexOf('.'));

    switch (className) {
        // GPIO.new
        case 'GPIO': {
            const match = expression.match(/GPIO\.new\(\s*(\d+),\s*GPIO::OUT\s*\)/);
            if (variable.name !== `gpio${match[1]}`) return null;

            const block = this._changeRubyExpressionBlock(
                rh,
                'kanirobo2_command2',
                'statement'
            );
            this._addField(block, 'TEXT', match[1]);

            return block;
        }
```

代入式には`onVasgn`を使う<br>

```js
onVasgn: function (scope, variable, rh) {
```

- `scope`
  - 代入される変数のスコープ<br>
    `local`,`global`,`instance`などがある
- `variable`
  - 左辺
- `rh`
  - 右辺

```js
const expression = this._getRubyExpression(rh);
if (!expression) return null;
```

右辺が未処理のブロックであるかを確認<br>
`_getRubyExpression`の返り値は`string`で右辺が文字列として帰ってくる

```js
const className = expression.substring(0, expression.indexOf('.'));

switch (className) {
```

`kannirobo2`では代入式が 3 つあるので右辺のクラス名を見て判断する

```js
const match = expression.match(/GPIO\.new\(\s*(\d+),\s*GPIO::OUT\s*\)/);
if (variable.name !== `gpio${match[1]}`) return null;
```

右辺が期待していたクラスのインスタンス生成か確認

> 今は正規表現で確認しているがなんかいい感じの方法があるのかも？

また左辺の変数名と合致しているかも確認<br>

```js
const block = this._changeRubyExpressionBlock(
  rh,
  "kanirobo2_command2",
  "statement"
);
this._addField(block, "TEXT", match[1]);
```

ブロックの生成<br>
引数の代入

## `gpio25.write( 1 )`

```js
onSend: function (receiver, name, args, rubyBlockArgs, rubyBlock, node) {
    const receiverName = (() => {
        if (this._isRubyArgument(receiver)) {
            return receiver.fields.VALUE.value;
        } else if (this._isRubyExpression(receiver)) {
            return this._getRubyExpression(receiver);
        } else {
            return null;
        }
    })();

    if (!receiverName) return null;

    switch (name) {
        // gpio.write
        case 'write': {
            const match = receiverName.match(/^gpio(\d+)$/);

            if (match && args.length === 1) {
                const pin = match[1];

                if (!this.isNumber(args[0])) return null;
                if (args[0].value !== 0 && args[0].value !== 1) return null;

                const block = (() => {
                    if (this._isRubyExpression(receiver)) {
                        return this._changeRubyExpressionBlock(
                            receiver,
                            'kanirobo2_command4',
                            'statement'
                        );
                    } else {
                        return this._changeBlock(receiver, 'kanirobo2_command4', 'statement');
                    }
                })();

                this._addField(block, 'TEXT1', pin);
                this._addField(block, 'TEXT2', args[0].value);
                return block;
            }
            break;
        }
```

`gpio25`の部分は`gpio25 = GPIO.new( 25, GPIO::OUT )`で作っているので割愛

```js
onSend: function (receiver, name, args, rubyBlockArgs, rubyBlock, node) {
```

- `receiver`
  - レシーバー<br>
    ここでは`gpio25`の部分
- `name`
  - メソッド名<br>
    ここでは`write`の部分
- `args`
  - 引数
- `rubyBlockArgs`
  - 不明
- `rubyBlock`
  - 不明
- `node`
  - 構文木

インスタンスメソッドのみの変換では`registerCallMethod`で良いが,
インスタンス作成の代入式と同時に変換しようとするとうまくいかないので`onSend`を使う

> [!TIP]
> 代入式があると`gpio25`がローカル変数として解釈されインスタンスメソッドとして変換ができない

```js
const receiverName = (() => {
  if (this._isRubyArgument(receiver)) {
    return receiver.fields.VALUE.value;
  } else if (this._isRubyExpression(receiver)) {
    return this._getRubyExpression(receiver);
  } else {
    return null;
  }
})();
```

`gpio25`の部分の取り出し<br>
単体だと`expression`になるが代入と同時にすると変数として扱われるため場合分けする

```js
const match = receiverName.match(/^gpio(\d+)$/);
// 省略
const pin = match[1];

if (!this.isNumber(args[0])) return null;
if (args[0].value !== 0 && args[0].value !== 1) return null;
```

`gpio25`から 25 の部分を取り出す<br>
引数が正しいかを確認

```js
const block = (() => {
  if (this._isRubyExpression(receiver)) {
    return this._changeRubyExpressionBlock(
      receiver,
      "kanirobo2_command4",
      "statement"
    );
  } else {
    return this._changeBlock(receiver, "kanirobo2_command4", "statement");
  }
})();

this._addField(block, "TEXT1", pin);
this._addField(block, "TEXT2", args[0]);
```

ブロックの生成と引数の追加<br>
`expression`だった場合とそうでない場合では作り方が違うため分岐
