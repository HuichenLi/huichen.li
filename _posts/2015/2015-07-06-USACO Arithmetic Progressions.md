---
time: 2015-07-06 23:29
permalink: USACO-ariprog
---
<!--excerpt-->
# PPCA new week #
[USACO Arithmetic Progressions][1]


  [1]: http://train.usaco.org/usacoprob2?a=nmTv3aKmrIY&S=ariprog


----------
section1.4 两道题纠结了大半个上午 =，=
## 题意 ##
Arithmetic Progressions。
从给定范围M的两个数的平方和序列中挑出指定长度N的等差数列。
3 <= N <= 25
1 <= M <= 250

## 思路 ##
拿到这样的题目完全没有“估计一下时间复杂度看能不能过”的概念= =于是一上来直接从1开始枚举公差，与根据M处理好的平方和数列进行对比看有没有符合要求的等差数列。

然而这样的思路显然是暴力得有点夸张了= =

比较接近正轨的思路是枚举等差数列的前两项，计算它们的差作为数列的公差，与数列比对看是否会有足够长的等差数列。

### 首先：数组大小不是随便开的 ###
1e5就可以了么？不，像下面这样的才是正确的姿势：
    
    const int MAXN = 250;
    int bisquare[MAXN*MAXN+1] = { 0 },bis[MAXN*MAXN*2+1] = { 0 };

### 处理平方和数列：桶排序 ###

    scanf("%d%d",&N,&M);
    for(int i = 0;i <= M;++i) {
        for(int j = i;j <= M;++j) {
            int bb = i * i + j * j;
            bis[bb] = 1;
        }
    }
    for(int i = 0;i <= 2 * M * M; ++i) {
        if(bis[i]) {
            bisquare[bisq++] = i;
        }
    }

### 枚举前两项 && 计算公差 ###

    bool flag = false;
    for(int j = 0;j < bisq;++j) {
        for(int i = j + 1;i < bisq;++i) {
            int b = bisquare[i] - bisquare[j];
            if (bisquare[j] + b*(N-1) > bisquare[bisq-1]) break; （~1~）
            if(is_bisquare(b,i)) {
                path[p].a = bisquare[j];
                path[p++].b = b;
                flag = true;
            }
        }

（~1~）处有一个小优化。当平方和数列中的最大值小于形成当前等差数列所需的末位元素时，必然不存在合法情形，break。

### 判断是否构成合法等差数列：函数is_bisquare() ###

原本……我是这样写的：

    if (N == 2) return true;
    int val = bisquare[now];
    int cnt = 0;
    for(int i = now + 1;i < bisq;++i) {
        if(bisquare[i] > val + b) return false;
        if(bisquare[i] == val + b) {
            ++cnt;
            if(cnt == N - 2) return true;
            val = val + b;
        }
    }
    return false;

然而这样一来复杂度达到了250*250  

> 整个人都二百五的平方了

这样一来再怎么想要优化常数都没有明显的作用咯……

> 你这个螳臂当车的歹徒

而这样写……
就变成25了（N最大为25）

    for (int cnt = 2, val = bisquare[now]; cnt < N; ++cnt) {
        val += b;
        if (!bis[val]) return false;
    }
    return true;


T T







