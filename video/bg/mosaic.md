# モザイク

モザイク機能は、BGレイヤに対して、画像を縦×横の一定サイズ(ブロック)で区切り、その部分を左上の色で塗りつぶすというものです。結果として、画面の解像度が下がります。

**モザイク前 -> モザイク後**

(これはGBAのモザイク機能ですが原理は全く同じです)

<img src="https://github.com/akatsuki105/gba-docs-ja/blob/main/images/bitmap.png?raw=true" />  <img src="https://github.com/akatsuki105/gba-docs-ja/blob/main/images/mosaic.png?raw=true" />

## 2106h - MOSAIC - モザイク (W)

```
  Bit 0    BG1モザイク有効化フラグ  (0=無効, 1=有効)
  Bit 1    BG2モザイク有効化フラグ  (0=無効, 1=有効)
  Bit 2    BG3モザイク有効化フラグ  (0=無効, 1=有効)
  Bit 3    BG4モザイク有効化フラグ  (0=無効, 1=有効)
  Bit 4-7  モザイクサイズ          (0=最小/1x1, 0Fh=最大/16x16)
```

X方向では、最初のブロックは常にテレビ画面の左端に配置されます。Y方向では、最初のブロックはテレビ画面の上部に配置されます。

When changing the mosaic size mid-frame, the hardware does first finish current block (using the old vertical size) before applying the new vertical size. Technically, vertical mosaic is implemented as so: subtract the veritical index (within the current block) from the vertical scroll register (BGnVOFS).


