# 🗺 BGマップ

BGマップは、タイルデータを画面上にどのように配置していくかを表すデータです。

背景マップや、タイルマップとも呼ばれます。

1つのBGマップで 32x32タイル(256×256px) を表します。(1タイル = 8x8 or 16x16)

各BGマップエントリ(タイルに対応)は、次のような16bitの値で構成されます。

```
  Bit 0-9    タイル番号 (000h-3FFh)
  Bit 10-12  パレット番号 (0-7)
  Bit 13     BG優先度 (0=低, 1=高)
  Bit 14     X反転 (0=反転しない、 1=水平方向に反転)
  Bit 15     Y反転 (0=反転しない、 1=垂直方向に反転)
```

Mode 2,4,6 の `Offset-per-Tile`モードでは、BG3のBGマップのみエントリの内容が変わります。

```
  Bit 0-9   スクロール量
  Bit 10-12 不使用
  Bit 13    スクロールをBG1に適用するか
  Bit 14    スクロールをBG2に適用するか
  Bit 15    スクロール方向 (0=H, 1=V, Mode4のみ)
```

Bit15が0、つまり、X方向(H)オフセットの場合、スクロール量(Bit0-9)の下位3bitは無視されます。

Mode7では、BGマップは128x128であり、下位8bitのみをBGマップエントリとして使用します。

```
  Bit 0-7   タイル番号 (00h-FFh) (without XYflip or other attributes)
  Bit 8-15  不使用 (contains tile-data; no relation to the BG-Map entry)
```

## BGマップのアドレス

各BGレイヤのBGマップがVRAM上でどこに存在するかは、[BGnSC](./control.md)で指定します。
