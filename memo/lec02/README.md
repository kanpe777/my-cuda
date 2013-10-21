## Compile

```
$ nvcc --compiler-bindir /usr/bin/llvm-g++4.2 hoge.cu
```

## Using Device Properties

double-precision は CUDA 1.3 以上

### Block IDs and Thread IDs
基本は thread

Gridの中にBlock

 * Block ID : 1D or 2D

 * Thread ID : 1D , 2D or 3D

```
kernel <<<1,1>>>
```

の "<<<" ">>>"超重要

> Grid
>> Block
>>> thread

```
<<<block_num,thread_num>>>
```

### example_1
```
<<<3,1>>>
```
> Grid
>> Block_1
>>> thread_0

>> Block_2
>>> thread_0

>> Block_3
>>> thread_0

### example_2
```
<<<3,2>>>
```
> Grid
>> Block_1
>>> thread_0

>>> thread_1

>> Block_2
>>> thread_0

>>> thread_1

>> Block_3
>>> thread_0

>>> thread_1


 * 同じ`Thread`の数でも、`Block`個数が違うことがありえる
 * ハードによって制約も異なる
 * 共有メモリを使用したいら1ブロックが良い

```
add<<<N,1> (dev_a, dev_b, dev_c)
```

基本的にSISDなので、みんな同じ処理をする

最悪なのはif文でこのthreadは〇〇、このthreadは△△ってのがつらい



