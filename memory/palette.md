# 🎨 パレットへのアクセス

> **Note** CGRAM = パレットメモリ

## 🔰 概要

```
  Write:    2122h (2回アクセス)
  Read:     213Bh (2回アクセス)
  Address:  2121h
```

## 📮 2121h - CGADD - パレットアドレス (W)

0..255で色番号を指定します。(CGRAMは 1色=2バイト で256エントリ)

このレジスタへの書き込みは CGDATA(`2122h`) と RDCGRAM(`213Bh`) へのアクセスをリセットします。(つまり、次のアクセスが2回目だったとしても、1回目にリセットされます)

## 🖌 2122h - CGDATA - パレット書き込み (W)

```
  1回目のアクセス: 下位8bit (偶数アドレス)
  2回目のアクセス: 上位7bit (奇数アドレス) (upper 1bit = PPU2 open bus)
```

```
  偶数アドレスへの書き込み:    set Cgram_Lsb = Data    ;memorize value
  奇数アドレスへの書き込み:    set WORD[addr-1] = Data*256 + Cgram_Lsb
```

書き込みのたびにアドレスが自動的にインクリメントされます。

## 🔍 213Bh - RDCGRAM - パレット読み込み (R)

```
  1回目のアクセス: 下位8bit (偶数アドレス)
  2回目のアクセス: 上位7bit (奇数アドレス) (upper 1bit = PPU2 open bus)
```

読み出しのたびにアドレスが自動的にインクリメントされます。
