# ジャンプ命令

## 無条件ジャンプ

オペコード | フラグ | サイクル | Native | Nocash | 内容
-- | -- | -- | -- | -- | -- 
80 dd       | ------ | 3xx | BRA disp8    | JMP disp      | PC=PC+/-disp8
82 dd dd    | ------ | 4   | BRL disp16   | JMP disp      | PC=PC+/-disp16
4C nn nn    | ------ | 3   | JMP nnnn     | JMP nnnn      | PC=nnnn
5C nn nn nn | ------ | 4   | JMP nnnnnn   | JMP nnnnnn    | PB:PC=nnnnnn
6C nn nn    | ------ | 5   | JMP (nnnn)   | JMP \[nnnn\]    | PC=WORD\[00:nnnn\]
7C nn nn    | ------ | 6   | JMP (nnnn,X) | JMP \[nnnn+X\]  | PC=WORD\[PB:nnnn+X\]
DC nn nn    | ------ | 6   | JML ...      | JMP FAR\[nnnn\] | PB:PC=\[00:nnnn\]
20 nn nn    | ------ | 6   | JSR nnnn     | CALL nnnn     | \[S\]=PC+2,PC=nnnn
22 nn nn nn | ------ | 4   | JSL nnnnnn   | CALL nnnnnn   | PB:PC=nnnnnn \[S\]=PB:PC+3
FC nn nn    | ------ | 6   | JSR (nnnn,X) | CALL \[nnnn+X\] | PC=WORD\[PB:nnnn+X\] \[S\]=PC
40          | nzcidv | 6   | RTI          | RETI          | P=\[S+1\],PB:PC=\[S+2\],S=S+4
6B          | ------ | ?   | RTL          | RETF          | PB:PC=\[S+1\]+1, S=S+3
60          | ------ | 6   | RTS          | RET           | PC=\[S+1\]+1, S=S+2


>**Note**  
> RTI命令は、BRK/IRQ/NMI からリターンする際に用います。BRKから戻るときのリターン先のアドレスは、BRK命令の2バイト後であることに注意してください。(つまりBRK命令後の1バイトは無視される)  
> RTIは、Bフラグおよび不使用フラグを変更することはできません。

Glitch: For `JMP [nnnn]` the operand word cannot cross page boundaries, ie. `JMP [03FFh]` would fetch the MSB from `[0300h]` instead of `[0400h]`. Very simple workaround would be to place a ALIGN 2 before the data word.

## 条件付きジャンプ

オペコード | フラグ | サイクル | Native | Nocash | 条件
-- | -- | -- | -- | -- | -- 
10 dd | ------ | 2<sup>[1](#cycle)</sup> | BPL     | JNS     disp  | `N=0` (plus/positive)
30 dd | ------ | 2<sup>[1](#cycle)</sup> | BMI     | JS      disp  | `N=1` (minus/negative/signed)
50 dd | ------ | 2<sup>[1](#cycle)</sup> | BVC     | JNO     disp  | `V=0` (no overflow)
70 dd | ------ | 2<sup>[1](#cycle)</sup> | BVS     | JO      disp  | `V=1` (overflow)
90 dd | ------ | 2<sup>[1](#cycle)</sup> | BCC/BLT | JNC/JB  disp  | `C=0` (less/below/no carry)
B0 dd | ------ | 2<sup>[1](#cycle)</sup> | BCS/BGE | JC/JAE  disp  | `C=1` (above/greater/equal/carry)
D0 dd | ------ | 2<sup>[1](#cycle)</sup> | BNE/BZC | JNZ/JNE disp  | `Z=0` (not zero/not equal)
F0 dd | ------ | 2<sup>[1](#cycle)</sup> | BEQ/BZS | JZ/JE   disp  | `Z=1` (zero/equal)

<sup id="cycle">1: 実行時間は、条件が偽の場合（分岐が実行されない）、2サイクルです。それ以外の場合は、ジャンプ先が同じメモリページ内にある場合は3サイクル、ページ境界を越える場合は4サイクルです。</sup>

>**Note**  
> x86やZ80のCPUとは異なり、減算（SBCやCMP）は以下(`A-B`として、`A >= B`)のときに`carry=set`となります。

## Conditional Branch Page Crossing

The branch opcode with parameter takes up two bytes, causing the PC to get incremented twice (`PC=PC+2`), without any extra boundary cycle.

The signed parameter is then added to the PC (`PC+disp`), the extra clock cycle occurs if the addition crosses a page boundary (next or previous 100h-page).

