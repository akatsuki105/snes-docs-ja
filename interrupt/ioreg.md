# 割り込み関連のレジスタ

## 4200h - NMITIMEN - 割り込み有効化レジスタ (W)

```
  Bit 0     自動Joypad読み出し有効化フラグ    (0=無効, 1=有効)
  Bit 1-3   不使用
  Bit 4-5   IRQフラグ
              0: 無効
              1: HIRQ  (H, V) = (HTIME, any)
              2: VIRQ  (H, V) = (0, VTIME)
              3: HVIRQ (H, V) = (HTIME, VTIME)
  Bit 6     不使用
  Bit 7     VBlank NMI 有効化フラグ  (0=無効, 1=有効)
              NMI = マスク不可割り込み
              リセット時は0
```

bit4-5を0にしてIRQを無効にすると、IRQが追加でアクノリッジされます。bit7でNMIを無効にした場合は、そのような効果はありません。

## 4210h - RDNMI - NMIフラグ (R)

```
  Bit 0-3   CPU 5A22のバージョン (version 2 exists)
  Bit 4-6   不使用
  Bit 7     NMIフラグ  (0=None, 1=Interrupt Request) (VBlank開始時にセットされる)
```

NMIフラグ(bit7)は、VBlank開始時にセットされます。（NMITIMEN.7 で NMIが無効になっている場合でもNMIフラグはセットされます）

NMIフラグは VBlank終了時に自動的にクリアされます。またこのレジスタ(RDNMI)を読み出した後にもクリアされます。

SNES の NMI は VBlank でしか発生せず、さらに NMIフラグはVBlankの終わりに自動的にクリアされるので、基本的にはこのフラグは必要ありません。

しかし、NMITIMEN.7 を `0` にして NMI を無効化し、また有効化する(`1`にする)と古いNMIが再び実行されてしまいます。このレジスタにアクノリッジ信号を送ればこれを防ぐことができます。

CPUは内部NMIフラグを RDNMI.7 とは別に持っていて、`NMITIMEN.7 & RDNMI.7` が 0 から 1 に変わった時にこの内部NMIフラグがセットされます。この内部フラグはNMIが実行されるとクリアされます。

## 4212h - HVBJOY - H/VBlankフラグ & 自動Joypadビジーフラグ (R)

```
  Bit 0     自動Joypad読み出し処理中か (1=Busy) (see 4200h, and 4218h..421Fh)
  Bit 1-5   不使用
  Bit 6     HBlankフラグ (1=HBlank中)
  Bit 7     VBlankフラグ (1=VBlank中)
```

HBlankフラグはVBlank/VSync中でもトグルします。

HBlankフラグとVBlankフラグは（FBlank中でも、IRQやNMIが有効であっても）常にトグルします。
