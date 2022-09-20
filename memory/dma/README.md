# DMA転送

DMA転送は Direct Memory Access転送 の略で、CPUを介さずに周辺機器やメモリ間で直接データ転送を行う転送方式です。

スーファミのDMAは、Aバスの指すアドレス(任意のアドレス)とBバスの指すアドレス(`0x21xx`)間でデータを転送します。([Aバス、Bバスについて](README.md#aバスとbバス))

通常のDMA転送は、HDMAと違いどんな用途でも利用可能なので GDMA(General DMA) と呼ばれることも多いです。

>**Note** 以後、通常のDMAはGDMAと呼び、DMAと呼ぶときはGDMAとHDMAの両方を指します。

[HDMA(HBlank DMA)](hdma.md) は 各スキャンラインの HBlank でちょっとしたデータ転送を行い、PPUレジスタを書き換えて画面効果を実現するためのDMAです。

スーファミには、8つのDMAチャネルがあり、GDMA と HDMA で共有しています。優先度は 0が最高、7が最低 となっています。HDMA は GDMA よりも優先度が高いです。優先度の低いDMAチャネルは、優先度の高いチャネルが完了するまで一時停止されます。

## 転送速度

```
  CPU: 336~413 KB/s
  DMA: 2680 KB/s
```

このようにDMA転送はCPUと比べて非常に高速なため、VBlank中 に急いでグラフィックデータのようなサイズの大きいデータ転送を行いたいときによく使われています。

## 参考

- [DMA & HDMA - Super Nintendo Entertainment System Features Pt. 07](https://youtu.be/K7gWmdgXPgk)
- [SNES on FPGA](https://pgate1.at-ninja.jp/SNES_on_FPGA/): HDMAの説明部分
