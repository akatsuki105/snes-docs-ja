# OAM

スーファミには、544B のOAMがあります。OAMはアドレス空間にマッピングされておらず、[I/Oレジスタを通してアクセスする](../../memory/oam.md)必要があります。

OAM は 128個のOBJ(スプライト)の情報を表しています。

このメモリ領域には、128個のOBJについて、各OBJの座標、サイズ、タイル番号などの属性(アトリビュート)が格納されています。

OAM は 0..511バイト と 512..544バイト で内容が異なっています。<sup>[1](#table)</sup>

0..511バイトは 4バイトのデータが128個並んでいて、各OBJに対応しています。

```
  0..3:     OBJ0
  4..7:     OBJ1
  8..11:    OBJ2
  ...
  507..511: OBJ127
```

512..544バイトは 2bit のデータが128個並んでいて、同じく各OBJに対応しています。

```
  512:
    bit0-1: OBJ0
    bit2-3: OBJ1
    bit4-5: OBJ2
    bit6-7: OBJ3
  513:
    bit0-1: OBJ4
    bit2-3: OBJ5
    bit4-5: OBJ6
    bit6-7: OBJ7
  ...
  543:
    bit0-1: OBJ124
    bit2-3: OBJ125
    bit4-5: OBJ126
    bit6-7: OBJ127
```

つまり、1つのOBJあたり、4バイト と 2bit の属性データがあります。

```
  0..511バイト
    Byte 0 X座標(下位8bit)
    Byte 1 Y座標
    Byte 2 タイル番号(下位8bit)
    Byte 3 属性
             Bit0:   タイル番号(上位1bit)
             Bit1-3: パレット番号
             Bit4-5: 優先度(0=最低, 3=最大)
             Bit6:   X反転
             Bit7:   Y反転

  512..544バイト
    Bit 0  X座標(上位1bit)
    Bit 1  サイズ (0=小, 1=大 具体的なサイズはOBSELに依存)
```

<sup id="table">1: 0..511バイトをOAMの下位テーブル, 512..543バイトをOAMの上位テーブルと呼ぶこともあります</sup>