# 制御レジスタ

## 2100h - INIDISP - ディスプレイ制御レジスタ1 (W)

```
  Bit 0-3   明るさ (0=真っ黒, or N=1..15: Brightness*(N+1)/16)
  Bit 4-6   不使用
  Bit 7     FBlankフラグ (FBlank = Forced blank, 0=何もしない, 1=画面を真っ黒に)
```

通常時は VRAM,OAM,CGRAM には VBlank中 のみアクセスできますが、FBlank中 は自由にアクセスできます。

FBlankであったとしても、TVは Vsync/Hsync の信号を受け取り続けます。(結果として、真っ黒な画面を描画し続けます) 

同様にCPUも FBlank であったとしても、HBlank/VBlank の信号を受け取り続け、(有効化されている場合は)NMIs, IRQs, HDMAs も生成され続けます。

```
  Forced blank doesn't apply immediately... so one must wait whatever
  (maybe a scanline) before VRAM can be freely accessed... or is it only
  vice-versa: disabling forced blank doesn't apply immediately/shows garbage
  pixels?
```

## 212Ch/212Dh - TM/TS - メイン/サブ画面レイヤ制御 (W)

各画面レイヤをメイン画面に映すか、サブ画面に映すか、両方に映すかを制御するレジスタです。

```
  Bit 0    BG1 (0=無効, 1=有効)
  Bit 1    BG2 (0=無効, 1=有効)
  Bit 2    BG3 (0=無効, 1=有効)
  Bit 3    BG4 (0=無効, 1=有効)
  Bit 4    OBJ (0=無効, 1=有効)
  Bit 5-7  不使用
  Bit -    Backdrop (メイン/サブ両方で常に有効)
```

Backdrop(背景色)は、メイン画面とサブ画面の両方で常に有効になっています。そのうち、サブ画面の背景は、その色を黒に設定することで効果的に無効化（完全透過化）することができます。

## 2133h - SETINI - ディスプレイ制御レジスタ2 (W)

```
  Bit 0     V-Scanning         (0=Non Interlace, 1=Interlace) (See Port 2105h)
  Bit 1     OBJ V-Direction Display (0=Low, 1=High Resolution/Smaller OBJs)
             IN THE INTERLACE MODE, SELECT EITHER OF 1-DOT PER LINE OR 1-DOT
             REPEATED EVERY 2-LINES. IF "1" IS WRITTEN, THE OBJ SEEMS REDUCED
             HALF VERTICALLY IN APPEARANCE.
  Bit 2     オーバースキャンフラグ (0=無効(224), 1=有効(239))
  Bit 3     擬似512モード (0=無効, 1=有効) (SHIFT SUBSCREEN HALF DOT TO THE LEFT)
  Bit 4-5   不使用
  Bit 6     EXTBGモード  (0=無効, 1=有効)
  Bit 7     External Synchronization (0=Normal, 1=Super Impose and etc.)
```

## 213Eh - STAT77 - PPU1ステータス (R)

```
  Bit 0-3  PPU1バージョン番号 (確認されてる限り 1 のみ)
  Bit 4    不使用 (PPU1 open bus) (same as last value read from PPU1)
  Bit 5    マスター/スレーブモード (PPU1.Pin25) (0=Normal=Master)
  Bit 6    OBJ Range overflow (0=Okay, 1=More than 32 OBJs per scanline)
  Bit 7    OBJ Time overflow  (0=Okay, 1=More than 8x34 OBJ pixels per scanline)
```

The overflow flags are cleared at end of V-Blank, but NOT during forced blank!

The overflow flags are set (regardless of OBJ enable/disable in 212Ch), at following times: Bit6 when V=OBJ.YLOC/H=OAM.INDEX*2, bit7 when V=OBJ.YLOC+1/H=0.

## 213Fh - STAT78 - PPU2ステータス (R)

```
  Bit 0-3  PPU2バージョン番号 (確認されてる限り 1,2,3 が存在)
  Bit 4    フレームレート (PPU2.Pin30)  (0=NTSC/60Hz, 1=PAL/50Hz)
  Bit 5    不使用 (PPU2 open bus) (same as last value read from PPU2)
  Bit 6    H/V-Counter/Lightgun/Joypad2.Pin6 Latch Flag (0=No, 1=New Data Latched)
  Bit 7    現在のインターレース (0=1stフレーム, 1=2ndフレーム)
```

このレジスタを読み出すと、ラッチフラグ（bit6）がリセットされ、OPHCT/OPVCT の1st/2ndリードフリップフロップがリセットされます。


