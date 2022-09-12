# [アドレッシング](https://problemkaputt.de/fullsnes.htm#cpumemoryaddressing)

## アドレッシングモード

```
nn = 命令直後の1バイト
nnnn = 命令直後の2バイト
LOAD(addr) = addr から 8bit or 16bit をロード
LOAD8(addr) = addr から 8bit をロード
LOAD16(addr) = addr から 16bit をロード
```

名前 | 記号 | 内容
-- | -- | --
Zero Page | `[nn]` | `LOAD(D + nn)`
Zero Page,X | `[nn+X]` | `LOAD(D + nn + X)`
Zero Page,Y | `[nn+Y]` | `LOAD(D + nn + Y)`
Absolute | `[nnnn]` | `LOAD((DB*0x10000) + nnnn)`
Absolute,X | `[nnnn+X]` | `LOAD((DB*0x10000) + nnnn + X)`
Absolute,Y | `[nnnn+Y]` | `LOAD((DB*0x10000) + nnnn + Y)`
(Indirect,X) | `[[nn+X]]` | `LOAD((DB*0x10000) + LOAD16(nn + X))`
(Indirect),Y | `[[nn]+Y]` | `LOAD((DB*0x10000) + LOAD16(nn) + Y)`

### ゼロページ

```
[nn], [nn+X], [nn+Y]
```

8bitのパラメータ(`nn`, 1バイト)を使って、メモリの最初の256バイト`0000h...00FFh`(=ゼロページ)内のアドレスを指定します。

この範囲は、`nn+X` や `nn+Y` のアドレス指定でも使用されます。例えば、`C0h+60h`は、`0120h`となりますが、ゼロページの範囲外であり、実際には`0020h`(`=0x0120&0xff`)にアクセスすることになります。

### 絶対アドレス

```
[nnnn],[nnnn+X],[nnnn+Y]
```

16bitのパラメータ（`nnnn`, 2バイト）を使って、64KBのメモリ空間`0000h...FFFFh`のアドレスのどこかを指定します。ゼロページと違ってパラメータとして余計に1バイト追加されるため、ゼロページアドレッシングを使ったアクセスより少し遅くなります。

### 間接アドレッシング

```
[[nn+X]],[[nn]+Y]
```

ページゼロの16bitパラメータを指し示す8bitパラメータを使用します。

SNESは（プログラムカウンタを除いて）CPUが16bitレジスタをサポートしていないですが、この（二重）間接アドレッシングモードによって、可変16bitポインタを使用することができます。

### On-Chip Bi-directional I/O port

Addresses (00)00h and (00)01h are occupied by an I/O port which is built-in into 6510, 8500, 7501, 8501 CPUs (eg. used in C64 and C16), be sure not to use the addresses as normal memory. For description read chapter about I/O ports.

### Caution

Because of the identical format, assemblers will be more or less unable to separate between `[XXh+r]` and `[00XXh+r]`, the assembler will most likely produce `[XXh+r]` when address is already known to be located in page 0, and `[00XXh+r]` in case of forward references.
Beside for different opcode size/time, `[XXh+r]` will always access page 0 memory (even when XXh+r>FFh), while `[00XXh+r]` may direct to memory in page 0 or 1, to avoid unpredictable results be sure not to use (00)XXh+r>FFh if possible.
