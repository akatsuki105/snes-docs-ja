# DMA転送

DMA転送はDirect Memory Access転送の略で、CPUを介さずに周辺機器やメモリ間で直接データ転送を行う転送方式です。

スーファミのDMAは、Aバスの指すアドレス(任意のアドレス)とBバスの指すアドレス(`0x21xx`)間でデータを転送します。([Aバス、Bバスについて](README.md#aバスとbバス))

通常のDMA転送は、HDMAと違いどんな用途でも利用可能なのでGDMA(General DMA)と呼ばれることも多いです。

>**Note** 以後、通常のDMAはGDMAと呼び、DMAと呼ぶときはGDMAとHDMAの両方を指します。

HDMA(HBlank DMA) は 各スキャンラインのHBlankでちょっとしたデータ転送を行い、PPUレジスタを書き換えて画面効果を実現するためのDMAです。

スーファミには、8つのDMAチャネルがあり、GDMAとHDMAで共有しています。優先度は 0が最高、7が最低 となっています。HDMAはGDMAよりも優先度が高いです。

優先度の低いDMAチャネルは、優先度の高いチャネルが完了するまで一時停止されます。

## 転送速度

```
  CPU: 336~413 KB/s
  DMA: 2680 KB/s
```

このようにDMA転送はCPUと比べて非常に高速なため、VBlank中のような急いでグラフィックデータのようなサイズの大きいデータ転送を行いたいときによく使われています。

## 420Bh/420Ch - MDMAEN/HDMAEN - GDMA/HDMAチャネルレジスタ (W)

```
  Bit 0 DMAチャネル0 (0=無効, 1=有効)
  Bit 1 DMAチャネル1 (0=無効, 1=有効)
  ..
  Bit 7 DMAチャネル7 (0=無効, 1=有効)
```

このレジスタに0以外の値を書き込むと、DMAが直ちに（数clkサイクル後に）開始されます。

CPUは転送中、一時停止します。GDMA転送はHDMA転送により中断されることがあります。複数のbitがセットされている場合、別々の転送がチャネル0=先頭～7=末尾の順に実行されます。

各bitは、チャネルの転送が完了すると自動的にクリアされます。

HDMAENでHDMAとして使用しているチャネルは、GDMAに使用しないでください。

## 43x0h - DMAPx - GDMA/HDMA設定レジスタ (R/W)

```
  GDMA:
    Bit 0-2   転送モード(転送単位を決める 詳細は後述)
                0(0b000): 1x1 (xx)
                1(0b001): 2x1 (xx -> xx+1)
                2(0b010): 1x2 (xx -> xx)
                3(0b011): 2x2 (xx -> xx -> xx+1 -> xx+1)
                4(0b100): 4x1 (xx -> xx+1 -> xx+2 -> xx+3)
                5(0b101): ??? (xx -> xx+1 -> xx -> xx+1)
                6(0b110): 1x2 (2と同じ)
                7(0b111): 2x2 (3と同じ)
    Bit 3-4   Aバスのアドレス増加
                0: インクリメント
                1: 固定
                2: デクリメント
                3: 固定
    Bit 5-6   不使用
    Bit 7     転送方向 (0=Aバス->Bバス, 1=Bバス->Aバス)

  HDMA:
    Bit 0-2   転送モード (内容はGDMAと同じ)
    Bit 3-5   不使用
    Bit 6     テーブルモード (0=直接, 1=間接)
    Bit 7     転送方向 (0=Aバス->Bバス, 1=Bバス->Aバス)
```

転送モード(bit0-2)の `NxM` は 同じBバスアドレスに対して`M`回処理を行ったのちアドレスをインクリメント、これを`N`回繰り返す ということを表しています。

擬似コードで表すと次のようになります。

```c
  b = bus.b;

  for (n = 0; n < N; n++) {
    for (m = 0; m < M; m++) {
      val = read(bus.a);
      write(0x2100+b, val);
      bus.a += INCREMENT;
    }

    b++;
  }
```

## 43x1h - BBADx - Bバスアドレス (R/W)

```
  Bit 0-7 Bバスアドレス (0xNN = 0x21NN)
```

GDMAでは基本的に `04h=OAM, 18h=VRAM, 22h=CGRAM, 80h=WRAM`　のどれかになります。

HDMAでは大抵の場合、PPUレジスタのどれかを指します。

## 43x2h/43x3h/43x4h - A1Tx - Aバスアドレス (R/W)

このレジスタではAバスの指すアドレスを指定します。

AバスはGDMAの場合はソースアドレスを、HDMAの場合はHDMAテーブル(後述)の開始アドレス(エントリ0)を指します。

```
  GDMA:
    Bit 0-15   ソースアドレス (bit0-15, DMAPx.3-4の影響を受ける)
    Bit 16-23  ソースアドレス (bit16-23, DMAPx.3-4の影響を受けず、ずっと固定)

  HDMA:
    Bit 0-23   HDMAテーブル開始アドレス (bit0-15, ずっと固定)
```

## 43x5h/43x6h/43x7h - DASx - Indirect HDMA Address / DMA Byte-Counter (R/W)

```
  GDMA:
    Bit 0-15   残りの転送サイズ(バイト単位) (0のときは0x10000)
                 転送が進むと共に減っていき、0になると転送終了
    Bit 16-23  不使用

  HDMA(直接):
    Bit 0-23   不使用 (in this mode, the Data is read directly from the Table)

  HDMA(間接):
    Bit 0-15   Current CPU-Bus Data Address (automatically loaded from the Table)
    Bit 16-23  Current CPU-Bus Data Address Bank   (this must be set by software)
```

## 43x8h/43x9h - A2Ax - HDMAテーブルアドレス (R/W)

現在のHDMAテーブルアドレスを示します。転送の進行によってインクリメントされていきます。

テーブルアドレスの bit16-23(バンク) は A1Tx の bit16-23 になります。

```
  GDMA:
    Bit 0-15 不使用

  HDMA:
    Bit 0-15 現在のHDMAテーブルアドレス(bit0-15)
```

## 43xAh - NTRLx - HDMAテーブルヘッダ (R/W)

HDMAテーブルの現在のエントリのヘッダが入っています。

```
  GDMA:
    Bit 0-7 不使用
  HDMA:
    Bit 0-7 ヘッダ (後述)
```

## 43xBh - UNUSEDx - 用途なし (R/W)

```
  Bit 0-7 用途なし
```

使われていませんが、値を自由に読み書きできます。そのため、高速RAMとして使用可能です。(ただし、memfillの固定DMAソースアドレスとしては使用不可)

このレジスタに値を格納しても、転送には何の影響もないようです。（値はそのまま残され、DMAや直接・間接HDMAによって変更されることはありません）

## 43xCh..43xEh - 不使用 (W)

使われていません。書き込んでも何も起こらないようです。

読み取ると、オープンバスからのゴミ値が返ってきます。

## 43xFh - MIRRx - 43xBhミラー (R/W)

`43xBh`のミラーです。

## HDMAテーブル

HDMAテーブルは、どのデータをいつ転送する必要があるかについての指示を記したテーブルです。

```
  直接モード:
    1 byte   ヘッダ(リピートフラグ(bit7) & 転送行数)
      00h       HDMAテーブル終了 (until it restarts in next frame)
      01h..80h  1ユニット転送して、後の"X-01h"行だけ停止
      81h..FFh  "X-80h"行かけて、"X-80h"ユニット転送を行う (1行、1ユニット)
    N bytes  転送データ
      nを転送ユニットとすると、
        bit7=1: N = (X-80h) * n バイト
             0: N = n バイト

    例:
      $11           ; エントリ0
      $0000, $0000  ; 転送(y=n)

      $02           ; エントリ1
      $0100, $0040  ; 転送(y=n+17)

      $82           ; エントリ2
      $0104, $0041  ; 転送(y=n+19)
      $0108, $0042  ; 転送(y=n+20)

      $64           ; エントリ3
      $0114, $0045  ; 転送(y=n+21)

      $00           ; 終了(y=n+121)
```

間接モードのHDMAテーブルは、生データの代わりに、データへのポインタを含むようにします。

```
  間接モード:
    1 byte   ヘッダ (直接モードと同じ)
    2 bytes  Nバイトの転送データへのポインタ (ポインタが指すデータの内容は直接モードと同じ)

    例:
      $11
      $E502 --> $0000, $0000 (y=n)

      $02
      $E506 --> $0100, $0040 (y=n+17)

      $82
      $E60E --> $0104, $0041 (y=n+19)
                $0108, $0042 (y=n+20)

      $64
      $E50A --> $0114, $0045 (y=n+21)

      $00                    (y=n+121)
```

The "count" and "pointer" values are always READ from the table. The "data" values are READ or WRITTEN depending on the transfer direction. The transfer step is always INCREMENTING for HDMA (for both the table itself, as well as for any indirectly addressed data blocks).

## 参考

- [DMA & HDMA - Super Nintendo Entertainment System Features Pt. 07](https://youtu.be/K7gWmdgXPgk)
- [SNES on FPGA](https://pgate1.at-ninja.jp/SNES_on_FPGA/): HDMAの説明部分
