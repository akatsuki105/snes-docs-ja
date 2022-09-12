# Mode7

<img src="/images/modeprops/mode7.webp" width="248" alt="mode7 props" />

## VRAM

Mode7のVRAMは他のBGモードとは構成が異なっています。

Mode7は64KBのVRAMのうち、前半32KB(0x0000..7FFF)しか利用しません。(要調査)

タイルデータはVRAMの偶数バイトに、BGマップは奇数バイトに存在しています。

```
  M: BGマップ(1byte)
  T: タイルデータ(1byte=1pixel)

  $0000 MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT
  $0020 MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT
  $0040 MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT
  ..
  $7FFF MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT MT
```

もともとBGマップとタイルデータを格納するメモリチップが分かれていて、それをインタリーブしたためこのよう奇妙な内容になっています。

## BGマップ

BGマップでは16KBのVRAMを使用します。

BGマップの大きさは 128x128タイル つまり、1024x1024px で固定です。

BGマップの1エントリ(1タイル)は1バイトで、タイル番号を 0..255 の範囲で表しています。

## タイルデータ

Mode7のタイルデータは 1byte = 1pixel に対応しており、256色パレットの色番号となっています。

## BG2 (EXTBG)

Mode7では正確にはBG1の1つしか使えませんが、`SETINI`(0x2133) のMode7拡張フラグ(bit6)をセットすると、BG2も使えるようになります。

BG2は、完全に新しいBGレイヤというわけではなく、BG1と**BGマップとタイルデータを共有**しています。

ただし、タイルデータの最上位bitを優先bitとして扱うようになります。このため、BG2が利用できる色数は7bpp(128色)になります。
