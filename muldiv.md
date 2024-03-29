# [乗算・除算](https://problemkaputt.de/fullsnes.htm#snesmathsmultiplydivide)

>**Note**  
> ここでは サイクル = CPUサイクル とします。

## 4202h - WRMPYA - 被乗数レジスタ (W)

8bitの符号無し整数

WRMPYB(`4203h`) 参照

## 4203h - WRMPYB - 乗数レジスタ (W)

8bitの符号無し整数

ここに書き込みを行うと、8サイクルを使って WRMPYB(`4202h`) と掛け算を行います。

掛け算の結果は RDMPY(`4216h-4217h`) に 16bitの符号無し整数として格納されます。

掛け算を行うと、副作用として

```c
  RDDIVL = WRMPYB;
  RDDIVH = 0x00;
```

となることに注意してください。

また、本来8サイクル必要ですが、WPMPYA(`4202h`)の上位 `N`ビットがすべて0であれば、`N`サイクルだけ処理時間が減るようです。

## 4204h/4205h - WRDIVL/WRDIVH - 被除数レジスタ (W)

16bitの符号無し整数

```
  4204h = 下位8bit
  4205h = 上位8bit
```

WRDIVB(`4206h`) 参照

## 4206h - WRDIVB - 除数レジスタ (W)

8bitの符号無し整数

ここに書き込みを行うと、16サイクルを使って WRDIV(`4204h-4205h`) と割り算を行います。

割り算の結果は、商が RDDIVL(`4214-4215h`) に、余りが RDMPY(`4216-4217h`) に 16bitの符号無し整数として格納されます。

```c
  RDDIV = WRDIV / WRDIVB;
  RDMPY = WRDIV % WRDIVB;
```

ゼロ除算を行う、つまり WRDIVB に 0 を書き込んだ場合は、

```c
  RDDIV = 0xFFFF;
  RDMPY = WRDIV;
```

となります。

Note: ほとんどのゲームは初期化時にI/Oポートをゼロクリアするため、ゼロ除算が生じます。

## 4214h/4215h - RDDIVL/RDDIVH - 商レジスタ (R)

割り算の商が格納されます。WRDIVB(`4206h`) 参照

掛け算によって、このレジスタの中身は破壊されることに注意してください。

## 4216h/4217h - RDMPYL/RDMPYH - 積/余りレジスタ (R)

掛け算の積 か 割り算の余りが格納されます。

掛け算については、WRMPYB(`4203h`) 参照

割り算については、WRDIVB(`4206h`) 参照

## 待ち時間の必要性

掛け算/割り算の結果を読み出す際には、待ち時間が必要なことに注意してください。

例えば、`MOV r,[421xh]`で結果を読み出す場合、`MOV r,[421xh]`は3サイクル消費します。そのため、MUL(処理に8サイクル)では5サイクル、DIV(処理に16サイクル)では13サイクルだけ待ち時間が必要になります。

42xxh PortsはCPUクロックで動作するため、CPUクロックが3.5MHzでも2.6MHzでも関係ありません。


