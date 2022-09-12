# 🗺 メモリマップ

SNESは24bit幅(`000000h-FFFFFFh`)のアドレスバスを持っています。

この24bitのアドレスは、8bitのバンク番号(`00h-FFh`)と16bitのオフセット(`0000h-FFFFh`)によく分けて説明されることが多く、このドキュメントでもそのように説明していきます。

## メモリマップ全体

```
              0x00..3F              0x40..7D         0x7E..7F        0x80..BF           0xC0..FF
0x0000 ┌─────────────────────┬────────────────────┬────────────┬───────────────────┬─────────────────┐
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │       System        │                    │            │      System       │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │     WS1 HiROM      │    WRAM    │                   │    WS2 HiROM    │
0x8000 ├─────────────────────┤                    │            ├───────────────────┤                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │      WS1 LoROM      │                    │            │     WS2 LoROM     │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       │                     │                    │            │                   │                 │
       └─────────────────────┴────────────────────┴────────────┴───────────────────┴─────────────────┘
```

バンク | オフセット | 内容 | 動作速度 | サイズ | その他
-- | -- | -- | -- | -- | -- 
00h-3Fh | 0000h-7FFFh | システム領域 | 後述 | 後述 | --
00h-3Fh | 8000h-FFFFh  | WS1 LoROM | 2.68MHz | 2048KB(64x32KB) | --
00h     | FFE0h-FFFFh  | 例外ベクタ  | 2.68MHz | -- | --
40h-7Dh | 0000h-FFFFh  | WS1 HiROM | 2.68MHz | 3968KB(62x64KB) | --
7Eh-7Fh | 0000h-FFFFh  | WRAM | 2.68MHz | 128KB(2x64KB) | --
80h-BFh | 0000h-7FFFh  | システム領域 (8K WRAM, I/O Ports, Expansion)  | 後述 | -- | バンク00-3Fhのミラー
80h-BFh | 8000h-FFFFh  | WS2 LoROM | max 3.58MHz | 2048KB(64x32KB) | バンク00-3Fhのミラー
C0h-FFh | 0000h-FFFFh  | WS2 HiROM | max 3.58MHz | 4096KB(64x64KB) |  バンク40-7Fhのミラー(?)

基本的に、バンク`80h-FFh`はバンク`00h-7Fh`のミラーです。(WRAM部分もミラーかは不明)

内部メモリ領域は、WRAMとメモリマップドI/Oポートです。

外部メモリ領域には、LoROM、HiROM、Expansion の各領域があります。

CPUのアドレス空間にマッピングされていない、I/Oを通してアクセス可能な、追加メモリ領域は以下の通りです。

```
  OAM          (512+32 B) (256+16 words)
  VRAM         (64 KB)    (32 Kwords)
  Palette      (512 B)    (256 words)
  Sound RAM    (64 KB)
  Sound ROM    (64 B BIOS Boot ROM)
```

## システム領域(バンク: 00-3Fh, 80-BFh)

`0x00xxxx..3Fxxxx`部分です。(`xxxx=0000..7FFFh`)

`0x80xxxx..BFxxxx`は`0x00xxxx..3Fxxxx`のミラーです。

オフセット | 内容 | 動作速度
-- | -- | --
0000h-1FFFh | 7E0000h-7E1FFFh(WRAMの先頭8KB)のミラー | 2.68MHz
2000h-20FFh | 不使用                                | 3.58MHz
2100h-21FFh | I/Oポート (B-Bus)                     | 3.58MHz
2200h-3FFFh | 不使用                                | 3.58MHz
4000h-41FFh | I/Oポート (manual joypad access)      | 1.78MHz
4200h-5FFFh | I/Oポート                             | 3.58MHz
6000h-7FFFh | Expansion                            | 2.68MHz

## カートリッジ容量

24bitのアドレスバスなので理論上は16MBのアドレスが可能ですが、かなりの部分がWRAMやI/Oのミラー領域で占められており、WS1/WS2のLoROM/HiROM領域のカートリッジROMは約11.9MBしか残っていません。

ほとんどのカートリッジで、WS1とWS2はミラーの関係です。さらに、ほとんどのゲームは LoROM か HiROM の どちらかのみを使用するため、以下のような容量になります。

```
LoROMゲーム:
  最大 2MB ROM
    banks 00h-3Fh, with mirror at 80h-BFh
    0x8000(Bytes) x 64(Banks) = 2MB

HiROMゲーム:
  最大 4MB ROM
    banks 40h-7Dh, with mirror at C0h-FFh
    0x10000(Bytes) x 64(Banks) = 4MB
```

カートリッジ容量の限界を克服する方法はいくつかあります。

LoROMゲームの中には、追加の "LoROM"バンクをHiROMエリアにマッピングするもの(`BigLoROM`)やWS2エリアにマッピングするもの(`SpecialLoROM`)があります。

HiROMゲームの中には、HiROMの追加バンクをWS2領域にマッピングするものがあります(`ExHiROM`)。

また、SA-1、S-DD1、SPC7110、X-in-1などのマルチカートでは、バンク切り替えを採用しているカートリッジがあります。

## 32KB LoROM (システム領域を同一バンクに持つ32KBのROMバンク)

ROMは非連続の32KBブロックに分割されています。

CPUのDB、PBレジスタの設定を変更することなく、ROMやシステム領域（I/OポートやWRAM）にアクセスできるというメリットがあります。

## HiROM (64KBのROMバンク)

HiROMマッピングは、連続したROMアドレスを提供しますが、ROMバンク内のI/OやWRAM領域は含まれません。

HiROMバンク(64KB)の上半分は、通常、対応するLoROMバンク(32KB)とミラーリングされています。これは、`40FFE0h-40FFFFh`から`00FFE0h-00FFFFh`への割り込みベクタおよびリセットベクタのマッピングに重要です。

HiROMのLoROMバンク領域へのミラーの例:

```
  0x00FFE0: 0x40FFE0 のミラー
  0x3D8000: 0x7D8000 のミラー
```

## SRAM

電池式のSRAMは、多くのゲームでセーブデータの保存に使用されています。

SRAMのサイズは通常 2KB、8KB、32KB のどれかです。ゲームでは32KB以上使っているものも一部あります。

SRAMのアドレスマッピングには、LoROMとHiROMの2つの基本方式があります。

```
  HiROM:
    SRAM: 30h-3Fh,B0h-BFh:6000h-7FFFh ; バンク1つ当たり8KB
  
  LoROM:
    SRAM: 70h-7Dh,F0h-FFh:0000h-7FFFh ; バンク1つ当たり32KB
```

SRAMは通常、WS1とWS2の両エリアにもミラーリングされます。

バンク`30h-3Fh`のSRAMは、しばしば`20h-2Fh`（場合によっては`10h-1Fh`）にもミラーリングされます。

バンク`70h-7Dh`のSRAMは、`70h-71h`または`70h-77h`にcrippleされたり、`60h-7Dh`に拡張されたり、また、オフセット`8000h-FFFFh`にミラーリングされることもあります。

## AバスとBバス

スーファミはAバスとBバスという、2種類のアドレスバス<sup>[1](#addrbus)</sup>を持っています。

Aバスは24bit幅で任意のアドレスにアクセス可能です。CPUバス、CPU空間とも呼ばれます。

Bバスは8bit幅で、アドレス空間`2100h..21FFh`にアクセス可能です。PPUバス、PPU空間とも呼ばれます。

2つのアドレスバスは同じデータバス<sup>[2](#databus)</sup>を共有していますが、それぞれのバスは独自のリード/ライト信号を持っています。

DMAコントローラは、AバスとBバスの両方に同時にアクセスすることができます。DMA転送のソースアドレスとデスティネーションアドレスを同時に2つのアドレスバスに出力することができるので、これまでのように`Read-and-Write`ではなく、`Read-then-Write`を1つのステップで行うことができます。

## バンク切り替え

ほとんどのスーファミゲームは、24bitのアドレス空間で満足しています。バンク切り替えは、特殊なチップを搭載した一部のゲームでのみ使用されています。

```
  S-DD1, SA-1, and SPC7110 chips (with mappable 1MByte-banks)
  Satellaview FLASH carts (can enable/disable ROM, PSRAM, FLASH)
  Nintendo Power FLASH carts (can map FLASH and SRAM to desired address)
  Pirate X-in-1 multicart mappers (mappable offset in 256Kbyte units)
  Cheat devices (and X-Band modem) can map their BIOS and can patch ROM bytes
  Copiers can map internal BIOS/DRAM/SRAM and external Cartridge memory
  Hotel Boxes (eg. SFC-Box) can map multiple games/cartridges
```

また、APU側では、64BのブートROMを有効/無効にすることができます。

<sup id="addrbus">1: ROMチップなどからデータを読み取る際に対象のアドレスを伝達するためのバス</sup>

<sup id="databus">2: アドレスバスで指定したアドレスから読み取ったデータを伝達させるためのバス</sup>
