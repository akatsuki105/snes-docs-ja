# DMAの実行タイミング

TODO([Anomie's Docs](../../others/anomie/timing.txt)を参考に書く予定)

## メモ(GDMA)

```
DMA is activated by writing a 1 to any bit of CPU register $420b. The CPU is halted during the DMA transfer.

DMAはCPUレジスタ$420bのいずれかのビットに1を書き込むことで起動します。
DMA転送中、CPUは停止します。
```

```
DMA takes 8 master cycles per byte transferred, regardless of the memory regions accessed. It is unknown what happens if you attempt DMA to or from $4000-$41ff, since that memory region requires 12 master cycles for access. There is also 8 master cycles overhead per channel.

DMAは、アクセスするメモリ領域に関係なく、1バイトの転送に8マスターサイクルを要します。
4000-$41ffのメモリ領域へのアクセスには12マスターサイクルを要するため、この領域との間でDMAを試みた場合どうなるかは不明です。
また、1チャネルあたり8マスターサイクルのオーバーヘッドが発生します。
```

```
The exact DMA timing works as follows. After $420b is written, the CPU gets one more CPU cycle before the pause. For example, on a standard "STA $420b", this would be the opcode fetch for the next instruction The speed of the next CPU cycle to execute after the DMA determines the CPU Clock speed for the DMA transfer.

正確なDMAのタイミングは次のように動作します。
$420bが書き込まれた後、CPUは一時停止の前にもう1つのCPUサイクルを取得します。
例えば、標準的な`STA $420b`の場合、これは次の命令のオペコードフェッチになります DMAの次に実行するCPUサイクルの速度によって、DMA転送のためのCPUクロック速度が決まります。
```

```
Now, after the pause, wait 2-8 master cycles to reach a whole multiple of 8 master cycles since reset.
The perform the DMA. 8 master cycles overhead and 8 master cycles per byte per channel, and an extra 8 master cycles overhead for the whole thing.
Then wait 2-8 master cycles to reach a whole number of CPU Clock cycles since the pause, and only then resume S-CPU execution.

ここで、一時停止後、リセットから8回の全倍数になるように2～8回のマスタサイクルを待ちます。
DMAを実行します。8マスターサイクルのオーバーヘッドと、チャンネルごとにバイトあたり8マスターサイクル、そして全体で8マスターサイクルのオーバーヘッドを追加します。
その後、一時停止からCPUクロックサイクルの整数倍に達するまで2～8マスタサイクルを待ち、その後のみS-CPUの実行を再開します。
```

```
The exact timing of the read within the DMA period is not known. 
Best guess at this point is that 2-4 of the "whole DMA overhead" is before the transfer and the rest after.

DMA期間内の読み出しの正確なタイミングはわかっていない。
現時点での最善の推測は、「DMA全体のオーバーヘッド」の2～4が転送前に、残りが転送後になることだ。
```

```
An example: "STA $420b : NOP", one channel active for a 3 byte transfer.

The pause occurs after the NOP opcode is fetched, so the CPU Clock Speed is 6 master cycles due to the following IO cycle in the NOP. 

Total = 0
  After the pause, say we need 2 master cycles to reach an even multiple of 8 master cycles since reset.
Total = 2
  Then wait 8 for DMA init.
Total = 10
  8 for channel init.
Total = 18
  8*3 for the actual transfer.
Total = 42
  To reach a whole number of CPU Clock cycles since the pause, we must wait 6 master cycles (remember, 0 is not
an option).
Total = 48

命令
  STA $420b;
  NOP;
で3バイトのDMA転送をアクティベートした場合を考えましょう。

NOPオペコードのフェッチ後にポーズが発生するため、NOPの次のIOサイクルの影響でCPUクロックスピードは6マスターサイクルになります。

Total: 0
  一時停止後、リセット後8サイクルの倍数まで2サイクルが必要だとします。
Total: 2
  DMAの初期化処理で8サイクル待ちます
Total: 10
  DMAチャネルの初期化処理で8サイクル待ちます
Total: 18
  実際の転送に 8x3 サイクルかかります
Total: 42
  CPUクロックが一時停止から6の倍数になるには、6サイクルの待ちが必要です。(42は6の倍数だが、ここでは1サイクル以上の待ちが必要)
Total: 48
```

```
上記の例と同じことを、2サイクルはやく始めた場合を考えましょう。

Total = 0
  リセットから8の倍数になるには、4サイクルを待たなければなりません。
Total = 4
  DMA転送に合計40サイクルかかります
Total = 44
  6の倍数まで4サイクル待つ必要があります
Total = 48
```
