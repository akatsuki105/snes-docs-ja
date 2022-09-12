# CPUサイクル

CPUサイクルは、CPUにとっての最短時間、つまり 1/CPUクロック 秒 です。 詳細は[こちら](../cycle.md)を見てください。

各CPUサイクルには、フェーズ1 と フェーズ2 の2つのフェーズがあります。

```
  フェーズ1では、CPUがアドレスバス上で次のアドレスの準備をします。
  フェーズ2の間、CPUがメモリによってデータバスに値を入れられるのを待ちます。
```

フェーズ1は常に3マスターサイクル要します。

フェーズ2はアクセス先のメモリによって要する時間が変わります。

```
  フェーズ2:
    高速メモリ(3.5MHz): 3マスターサイクル
    低速メモリ(2.6MHz): 5マスターサイクル
```

## 参考

- https://discord.com/channels/388461081888292865/449676046736818178/713407616738656317

```
  Each CPU cycle has two phases: phase 1 and phase 2. During phase 1, the CPU gets the next address ready on the address bus. During phase 2, the CPU is waiting for the memory to put a value on the data bus. Phase 1 always lasts 3 master clocks. When the CPU is accessing fast memory, phase 2 lasts 3 master clocks; when accessing slow memory, it lasts 5.
```
