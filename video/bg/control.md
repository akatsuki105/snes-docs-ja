# BG制御レジスタ

## 2105h - BGMODE - BG制御レジスタ (W)

```
  Bit 0-2  BGモード (0..7)
  Bit 3    BG3優先度 (Mode1用、0=通常, 1=高)
  Bit 4    BG1タイルサイズ (0=8x8, 1=16x16)
  Bit 5    BG2タイルサイズ (0=8x8, 1=16x16)
  Bit 6    BG3タイルサイズ (0=8x8, 1=16x16)
  Bit 7    BG4タイルサイズ (0=8x8, 1=16x16)
```

>**Note**  
> Mode5では 8x8 は 16x8 として、Mode6ではタイルサイズは常に 16x8 として、Mode7ではタイルサイズは常に 8x8 として扱われます。

BGモード0~7の詳細は[こちら](mode.md)を参照してください。

## 210xh - BGnSC - BGマップ設定 (W)

```
  2107h: BG1SC
  2108h: BG2SC
  2109h: BG3SC
  210Ah: BG4SC
```

```
  Bit 0-1  仮想画面サイズ (0=1枚, 1=Yミラー, 2=Xミラー, 3=4枚)
             0: 32x32 タイル
                 (SC0 SC0)
                 (SC0 SC0)
             1: 64x32 タイル
                 (SC0 SC1)
                 (SC0 SC1)
             2: 32x64 タイル
                 (SC0 SC0)
                 (SC1 SC1)
             3: 64x64 タイル
                 (SC0 SC1)
                 (SC2 SC3)
  Bit 2-7  BGマップのベースアドレス (2KB単位)
```

このレジスタで、VRAMのBGマップアドレスを指定します。

画面`SCn`は 32x32枚のタイルで構成されています。

Mode7ではこのレジスタは無視されます。(ベースアドレスは0で、サイズは128x128で固定)

## 210Bh/210Ch - BG12NBA/BG34NBA - BGタイルデータアドレス (W)

```
  BG12NBA:
    Bit 0-3   BG1タイルデータのベースアドレス (8KB単位)
    Bit 4-7   BG2タイルデータのベースアドレス (8KB単位)

  BG34NBA:
    Bit 0-3  BG3タイルデータのベースアドレス (8KB単位)
    Bit 4-7  BG4タイルデータのベースアドレス (8KB単位)
```

Mode7ではこのレジスタは無視されます。(タイルデータのベースアドレスは0となります)
