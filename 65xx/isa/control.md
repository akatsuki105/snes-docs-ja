# 制御命令

## 制御命令

オペコード | フラグ | サイクル | Native | Nocash | 内容
-- | -- | -- | -- | -- | -- 
18    | --0--- | 2 | CLC     | `CLC`      | `C=0`
58    | ---0-- | 2 | CLI     | `EI`       | `I=0`
D8    | ----0- | 2 | CLD     | `CLD`      | `D=0`
B8    | -----0 | 2 | CLV     | `CL?`      | `V=0`
38    | --1--- | 2 | SEC     | `STC`      | `C=1`
78    | ---1-- | 2 | SEI     | `DI`       | `I=1`
F8    | ----1- | 2 | SED     | `STD`      | `D=1`
C2 nn | xxxxxx | 3 | REP #nn | `CLR P,nn` | `P=P AND NOT nn`
E2 nn | xxxxxx | 3 | SEP #nn | `SET P,nn` | `P=P OR nn`
FB    | --c--- | 2 | XCE     | `XCE`      | `C=E, E=C`

## 特殊命令

オペコード | フラグ | サイクル | Native | Nocash | 内容
-- | -- | -- | -- | -- | --
DB    | ------ | -  | STP     | KILL   | STOP/KILL
EB    | nz---- | 3  | XBA     | SWAP A | `A=B, B=A, NZ=LSB`
CB    | ------ | 3x | WAI     | HALT   | HALT命令
42 nn |        | 2  | WDM #nn | NUL nn | 何もしない
EA    | ------ | 2  | NOP     | NOP    | 何もしない

`WAI/HALT`命令は、(IRQ、NMIのような)例外リクエストが起きるまでCPUを停止させます。 IRQが起きた場合、`I=1`でIRQが無効になっていたとしても、これは機能します。

## 割り込み、例外、ブレーク

```
  Opcode                                                      6502     65C816
  00   BRK    Break      B=1 [S]=$+2,[S]=P,D=0 I=1, PB=00, PC=[00FFFE] [00FFE6]
  02   COP    ;65C816    B=1 [S]=$+2,[S]=P,D=0 I=1, PB=00, PC=[00FFF4] [00FFE4]
  --   /ABORT ;65C816                               PB=00, PC=[00FFF8] [00FFE8]
  --   /IRQ   Interrupt  B=0 [S]=PC, [S]=P,D=0 I=1, PB=00, PC=[00FFFE] [00FFEE]
  --   /NMI   NMI        B=0 [S]=PC, [S]=P,D=0 I=1, PB=00, PC=[00FFFA] [00FFEA]
  --   /RESET Reset      D=0 E=1 I=1 D=0000, DB=00  PB=00, PC=[00FFFC] N/A
```

例外(Exception)時は、まずBフラグが変更され（6502モードのとき）、次に`P`をスタックに書き込んだあと、Iフラグをセットし、Dフラグがクリアされます（オリジナルの6502とは異なります）。

ソフトウェアまたはハードウェアは、`/IRQ`または`/NMI`信号を処理した後、アクノリッジまたはリセットを行うよう注意する必要があります。

```
IRQs are executed whenever "/IRQ=LOW AND I=0".
NMIs are executed whenever "/NMI changes from HIGH to LOW".
```

`/IRQ`をLOWにした場合、`I=0`にするとすぐに同じ（古い）割り込みが再度実行されます。 `/NMI`をLOWにした場合、それ以上NMIを実行することはできません。