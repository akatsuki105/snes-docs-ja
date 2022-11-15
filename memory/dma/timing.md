# DMAの実行タイミング

>**Warning** 信頼性低

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