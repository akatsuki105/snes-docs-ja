# 🎨 パレット

SNESには、パレット用のメモリ(CGRAM)が 512B あります。

パレットはアドレス空間にマッピングされておらず、[I/Oレジスタを通してアクセスする](../memory/palette.md)必要があります。

パレットのエントリは2バイトで256個あります。エントリ1つが1色に対応しています。

<table>
    <thead>
        <tr>
            <th>bit</th>
            <th>15</th>
            <th>14</th>
            <th>13</th>
            <th>12</th>
            <th>11</th>
            <th>10</th>
            <th>9</th>
            <th>8</th>
            <th>7</th>
            <th>6</th>
            <th>5</th>
            <th>4</th>
            <th>3</th>
            <th>2</th>
            <th>1</th>
            <th>0</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>color</td>
            <td colspan=1 class="td-colspan">--</td>
            <td colspan=5 class="td-colspan">Blue(0..31)</td>
            <td colspan=5 class="td-colspan">Green(0..31)</td>
            <td colspan=5 class="td-colspan">Red(0..31)</td>
        </tr>
    </tbody>
</table>

## パレットの内訳

エントリ | 内容
-- | --
00h | 背景色(BG/OBJが全て透明色の時に適用)
01h-FFh | 256色BGパレット
01h-7Fh | 128色BGパレット (Mode7のBG2)
01h-7Fh | 16色BGパレット x 8
01h-1Fh | 4色BGパレット x 8 (Mode0のBG2-4は除く)
21h-3Fh | 4色BGパレット x 8 (Mode0のBG2のみ)
41h-5Fh | 4色BGパレット x 8 (Mode0のBG3のみ)
61h-7Fh | 4色BGパレット x 8 (Mode0のBG4のみ)
81h-FFh | 16色BGパレット x 8 (このうち半分はColor-Mathは無効)
N/A     | サブ背景色 (CGRAM中にはなく、COLDATAを通して設定)

## ダイレクトカラーモード

背景が256色利用な場合(Mode3,4,7 の BG1)は、ダイレクトカラーモードが選択可能でした。(via 2130h.Bit0; this bit hides in one of the Color-Math registers, although it isn't related to Color-Math) 

本来色番号だったはずの8bitは `BBGGGRRR` として解釈されます。また[BGマップ](vram.md)のパレット番号は `bgr` として解釈され、結果として RGB555 の `BBb00:RRRr0:GGGg0` が色として使われます。

ただし、BGマップの`bgr`はタイルごとに適用されることと、`bgr`はMode3,4でしか使えない(Mode7のBGマップにはパレット番号を指定するbitがない)ことに注意してください。

```
  Color Bit7-0 all zero --> Transparent    ;-Color "Black" is Transparent!
  Color Bit7-6   Blue Bit4-3               ;\
  Palette Bit 2  Blue Bit2                 ; 5bit Blue
  N/A            Blue Bit1-0 (always zero) ;/
  Color Bit5-3   Green Bit4-2              ;\
  Palette Bit 1  Green Bit1                ; 5bit Green
  N/A            Green Bit0 (always zero)  ;/
  Color Bit2-0   Red Bit4-2                ;\
  Palette Bit 0  Red Bit1                  ; 5bit Red
  N/A            Red Bit0 (always zero)    ;/
```

ダイレクトカラーモードで8bitをすべて0にした場合は透明色として扱われてしまうため、黒(`#000000`)を定義するには、背景色を黒に設定するか（BG1の後ろにレイヤーがない場合のみ有効）、黒に近い色(例: 01h=赤黒)で妥協する必要があります。

またダイレクトカラーモードと対比して、通常のパレットを使った色指定はインデックスカラーモードと呼ばれることもあります。

## Screen Border Color

The Screen Border is always Black (no matter of CGRAM settings and Sub Screen Backdrop Color). NTSC 256x224 images are (more or less) fullscreen, so there should be no visible screen border (however, a 2-3 pixel border may appear at the screen edges if the screen isn't properly centered). Both PAL 256x224 and PAL 256x239 images images will have black upper and lower screen borders (a fullscreen PAL picture would be around 256x264, which isn't supported by the SNES).

## Forced Blank Color

In Forced Blank, the whole screen is Black (no matter of CGRAM settings, Sub Screen Backdrop Color, and Master Brightness settings). Vsync/Hsync are kept generated (sending a black picture with valid Sync signals to the TV set).
