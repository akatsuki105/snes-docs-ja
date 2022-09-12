# 割り込み

割り込み関連のレジスタについては [こちら](ioreg.md) を参照。
- [レジスタ](ioreg.md)
- [割り込み、例外、ブレーク](../65xx/isa/control.md#割り込み例外ブレーク)

## 各種割り込み

IRQはIフラグで無効にできますが、BRK，NMI，RESET はIフラグで無効にすることはできません。

ソフトウェアまたはハードウェアは、IRQ または NMI を処理した後、アクノリッジまたはリセットを行う必要があります。

### RESET

文字通り、リセット時に起こる割り込みです。

```go
  REG.P[D], REG.P[E], REG.P[I] = 0, 1, 1;
  REG.D = 0x0000;
  REG.DB = 0x00;
  REG.PB, REG.PC = 0x00, load16(0xFFFC);
```

### BRK

`BRK`命令によって引き起こされます。ソフト開発時のデバッガ向けの割り込みです。

割り込みから復帰する時のリターン先のアドレスは、`BRK`命令の2バイト後であることに注意してください。(つまりBRK命令後の1バイトは無視される)

```go
  if REG.P[E] = 1 {
    REG.P[B] = 1;
  }
  if REG.P[E] = 0 {
    push8(REG.PB);
  }
  push16(REG.PC+1);                  // PC は BRK命令の1バイト後
  push8(REG.P);
  REG.P[D], REG.P[I] = 0, 1;
  REG.PB, REG.PC = 0x00, VEC_BRK;
```

### COP

`COP`命令によって引き起こされる割り込みで、現在実行中の処理を中断し、コプロセッサによるソフトウェア割込を実行するためのものです。

```go
  if REG.P[E] = 1 {
    REG.P[B] = 1;
  }
  if REG.P[E] = 0 {
    push8(REG.PB);
  }
  push16(REG.PC+1);                 // PC は COP命令の1バイト後
  push8(REG.P);
  REG.P[D], REG.P[I] = 0, 1;
  REG.PB, REG.PC = 0x00, VEC_COP;
```

### ABORT

```go
  if REG.P[E] = 0 {
    push8(REG.PB);
  }
  push16(REG.PC);
  push8(REG.P);
  REG.P[I] = 1;
  REG.PB, REG.PC = 0x00, VEC_ABORT;
```

### IRQ

[IRQ](./irq.md) を参照してください。

### NMI

NMIピンが HighからLow になると、現在実行中の命令終了後に割り込みシーケンスが開始されます。

NMIはエッジ依存の入力<sup>[2](#edge)</sup>なので、前の割り込みの処理中にNMIピンが HighからLow になった場合は割り込みが再び発生します。

<sup id="level">2: Lowであることではなく、HighからLowに切り替わることでトリガーされる</sup>

```go
  if REG.P[E] = 1 {
    REG.P[B] = 0;
  }
  if REG.P[E] = 0 {
    push8(REG.PB);
  }
  push16(REG.PC);
  push8(REG.P);
  REG.P[D], REG.P[I] = 0, 1;
  REG.PB, REG.PC = 0x00, VEC_NMI;
```

## 例外ベクタ

例外ベクタはオフセット`FFE0h..FFFFh`に配置されています。

SNESは、CPUに割り込みが発生した時に、対応する例外ベクタにジャンプします。

リセット時は、6502モードで起動します。

### 65816モード(E=0)

名称 | ベクタ | ソフト/ハード | 発生条件 | 変化するレジスタ
--- | ----- | ------------ | ------ | -------------
COP | 0xFFE4-0xFFE5 | ソフト   | コプロセッサ割り込み | `P[I]=1, P[D]=0`
BRK | 0xFFE6-0xFFE7 | ソフト   | BRK命令実行時      | `P[I]=1, P[D]=0`
ABORT | 0xFFE8-0xFFE9 | ハード | ハードウェア特殊信号 | `P[I]=1`
NMI | 0xFFEA-0xFFEB | ハード   | V-Blank割り込み   | `P[I]=1`
(予約) | 0xFFEC-0xFFED | ハード	| -- | -- 
IRQ | 0xFFEE-0xFFEF | ハード/ソフト | ハードウェア信号 | `P[I]=1`

### 6502モード(E=1)

6502モードでは、BRKとIRQは同じベクタを共有しており、ソフトウェアはプッシュされたBフラグのみを調べることで、BRKとIRQを見分けることができます。

名称 | ベクタ | ソフト/ハード | 発生条件 | 変化するレジスタ
--- | ----- | ------------ | ------ | -------------
COP     |  0xFFF4-0xFFE5  | ソフト | コプロセッサ割り込み | `P[I]=1, P[D]=0`
(予約)  |  0xFFF6-0xFFE7  | ハード | -- | -- 
ABORT   | 0xFFF8-0xFFE9   | ハード | ハードウェア特殊信号 | `P[I]=1`
NMI     | 0xFFFA-0xFFEB   | ハード | V-Blank割り込み | `P[I]=1`
RESET   | 0xFFFC-0xFFED   | ハード | 電源投入時、リセット時 | `P[I]=1, P[D]=0`
IRQ/BRK | 0xFFFE-0xFFEF   | ハード/ソフト | ハードウェア信号/BRK命令 | `P[I]=1, P[D]=0`
