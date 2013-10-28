## 3回目の授業
### やったこと
 * チーム決定(himajin, ジェイソンさん)
 * JuliaSetについて 

### 次回予告
 * 

***********************
### memo

#### Julia Set
Z = constant complex number

Z_(n+1) = (Z_n)^2 + C　-> 漸化式

 * 範囲以内ならJulia Set
 * 範囲外ならJulia Setじゃない

#### How use GPU?
```
int main(void) {
    CPUBitmap bitmap(DIM, DIM);
    ...
}
```
***********************
#### リニアライズ
DIM つなげていく

X is horizon access

Y is vertical access

***********************
#### int julia(int x, int y)
```
float jx = scale * (float)(DIM/2 - x) / (DIM/2);
float jy = scale * (float)(DIM/2 - y) / (DIM/2);
```
理由は座標調整のため

shift うんちゃら

***********************
`magnitude2()` は2乗を求めている

***********************
```
dim3 grid(DIM, DIM);
```
 * 横:DIM長さ
 * 縦:DIM長さ


```
dim3 grid(3, 4);
```
 * 横:3
 * 縦:4

kernel<<<grid, 1>>>(dev_bitmap)

kernel<<< , 各ブロックのスレッドの数>>>

dim3 -> 3なので3次元も可能!!

```
dim3 grid(DIM, DIM); = dim3 grid(DIM, DIM, 1);
```

***********************
```
int x = blockIdx.x;
int y = blockIdx.y;
int offset = x + y * gridDim.x
```

`giridDim.x` は横の長さ

offset は閾値

x,yが各スレッドが異なる

ptrで指定されたところだけ計算ができる

Program is same.

Data is different.

***********************
問題のサイズとハードウェアのサイズが異なる時どうなるか？

A.次のチャプター


***********************
`blockIdx.x` -> `threadIdx.x`

source: add_loop_blocks.cu

***********************
hardware limit は 65,535ブロックが限界

1ブロック = 512 threadsが限界

int tid = threadIdx.x + blockIdx.x * blockDim.x;

***********************
#### 128スレッド / ブロックの実現

add<<<(N+127)/128, 128>>>

割り算の答えがうんうぬんかんぬん

```
if N < 128 then
N/128 = 0;
end
```

N is size of vector.


***********************
```
if (tid < N) c[tid] = a[tid] + b[tid];
```
をかかなかったら、メモリアクセスで死ぬ？

***********************
`[0][1][2][3]...[N-1]`

N is the size of vector.

***********************
```
dim3 blocks(DIM/16, DIM/16);
dim3 threads(16,16);
kernel<<<blocks, threads>>>
```
 * ブロックも2次元配列
 * スレッドも2次元配列

**********************
#### CPU 
 * manage in software
 * スレッドを増やしてもかえって遅くなる場合がある

#### GPU 
 * manage in hardware
 * オーバーヘッドが少ない
 * スレッドはいっぱい作ったほうが良い
 * チューニング = スレッド増加 
 

***********************
#### Dot product = 内積
(x1, x2, x3, x4) * (y1, y2, y3, y4) = x1*y1 + x2*y2 + x3*y3 + x4*y4

掛け算自体は並列で!!

総和求めるのはPUで計算するのは難しい。

 * 特徴が活かせない
 * 他が活かせない

several way

パフォーマンスは数十倍違う。

 * メモリアクセスを局所化
 * とびとびのスレッドが動くのはまり良くない

