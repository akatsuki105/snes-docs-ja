# SPC700

APUは、ソニーのSPC700命令セットを使用した8bitCPUであるS-SMPチップ(以後、SPC700と呼びます)で制御されています。

SPC700 のクロック周波数は 1.024MHz です。<sup>[1](#clock)</sup>

SPC700 のオペコードと3つのメインレジスタ（A,X,Y）は、オペコードの番号が異なるものの、明らかに6502命令セットからインスピレーションを受けており、いくつかの新しい/変更された命令を持っています。

## レジスタ

```
  A     アキュムレータ (8bit)
  X     インデックスレジスタX (8bit)
  Y     インデックスレジスタY (8bit)
  SP    スタックポインタ (8bit, nn => 0x1nn)
  YA    16bit combination of Y=MSB, and A=LSB
  PC    プログラムカウンタ (16bit)
  PSW   ステータスレジスタ (8bit)
    bit0  C  キャリー (0=No Carry, 1=Carry)
    bit1  Z  ゼロ (0=(前の計算結果が)非ゼロ, 1=ゼロ)
    bit2  I  割り込み無効 (0=有効, 1=無効)
    bit3  H  Half-carry (0=Borrow, or no-carry, 1=Carry, or no-borrow)
    bit4  B  ブレークフラグ (0=RESET, 1=BRK)
    bit5  P  Zero Page Location (0=00xxh, 1=01xxh)
    bit6  V  オーバーフロー
    bit7  N  ネガティブ (0=(前の計算結果が)正, 1=負)
```

<sup id="clock">1: ウィキペディアには 2.048MHz とあるが、真偽不明</sup>
