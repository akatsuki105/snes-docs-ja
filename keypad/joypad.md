# [キー入力](https://problemkaputt.de/fullsnes.htm#snescontrollersioportsautomaticreading)

このレジスタは、[NMITIMEN.0](../interrupt/ioreg.md) で自動Joypad読み出し(`Auto Joypad Read`)を有効にした場合に有効です。

## 421xh - JOYnL/JOYnH - Joypad n レジスタ (R)

```
  4218h/4219h - JOY1L/JOY1H - Joypad 1 (gameport 1, pin 4) (R)
  421Ah/421Bh - JOY2L/JOY2H - Joypad 2 (gameport 2, pin 4) (R)
  421Ch/421Dh - JOY3L/JOY3H - Joypad 3 (gameport 1, pin 5) (R)
  421Eh/421Fh - JOY4L/JOY4H - Joypad 4 (gameport 2, pin 5) (R)
```

ビット | 15 | 14 | 13 | 12 | 11 | 10 | 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 
-- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- 
ボタン | B | Y | SELECT | START | ↑ | ↓ | ← | → | A | X | L | R | 0 | 0 | 0 | 0

ボタンが押されているときに`1`になります。

シリアル転送の順番は`Bit15 -> Bit0`の順です。

Before reading above ports, set Bit 0 in port 4200h to request automatic reading, then wait until Bit 0 of port 4212h gets set-or-cleared? Once 4200h enabled, seems to be automatically read on every retrace?

Be sure that Out0 in Port 4016h is zero (otherwise the shift register gets stuck on the first bit, ie. all 16bit will be equal to the B-button state.

```
 AUTO JOYPAD READ
 ----------------
  この機能を有効にすると、4つのコントローラポートデータラインのそれぞれから16bitをレジスタ$4218-fに読み込みます。
  これは最初のVBlankスキャンラインのH=32.5とH=95.5の間で始まり、4224マスターサイクル後に終了します。
  この間，レジスタ$4212のbit0がセットされます。具体的には，1フレーム目のH=74.5から始まり，観測範囲に入る前回の読み出し開始後，256サイクルの倍数で終了します。
 When enabled, the SNES will read 16 bits from each of the 4 controller port
 data lines into registers $4218-f. This begins between H=32.5 and H=95.5 of
 the first V-Blank scanline, and ends 4224 master cycles later. Register $4212
 bit 0 is set during this time. Specifically, it begins at H=74.5 on the first
 frame, and thereafter some multiple of 256 cycles after the start of the
 previous read that falls within the observed range.

 Reading $4218-f during this time will read back incorrect values. The only
 reliable value is that no buttons pressed will return 0 (however, if buttons
 are pressed 0 could still be returned incorrectly). Presumably reading $4016/7
 or writing $4016 during this time will also screw things up.
```

