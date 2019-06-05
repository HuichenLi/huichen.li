---
time: 2015-07-02 23:19
permalink: USACOwormhole
---
<!--excerpt-->

# PPCA DAY1 #
[USACOwormholes][1]


  [1]: http://train.usaco.org/usacoprob2?a=HVa8QLfTtGo&S=wormhole


----------


## 题意 ##
虫洞。两两相连，从一个进，从另一个相同方向出。
存在一些虫洞的组合，使得以特定方式运动的奶牛被虫洞困住。
给定虫洞坐标，求它们使奶牛被困住的成对方式。


## 分析 ##
题目限制了虫洞数量（2 <= N <= 12）以及奶牛运动的方向（+x方向），因此可以枚举虫洞相连的方式并判断是否形成环路。

### 搜索算法 ###
之前虽然一直听到搜索这两个字，也了解它的算法思想但是其实并没有真正写过搜索的代码= =

搜索首先有一个基本的框架。
比如要输出n个数的全排列：

    const int tot = 100;
    int n;
    int a[tot];
    bool b[tot];
    void go(const int step) {
        if (step == n+1) {
            for (int i = 1; i <= n; ++i) printf("%d ", a[i]);
            printf("\n");
            return;
        }
        for (int i = 1; i <= n; ++i)
            if (!b[i]) {
                a[step] = i;
                b[i] = true;
                go(step+1);
                b[i] = false;
            }
    }
    int main()
    {
        scanf("%d", &n);
        go(1);
        return 0;
    }

但是在这道题中，这样的全排列会导致重复计算，比如
1 2 3 4 5 6
3 4 1 2 5 6
这是两个不同的全排列，

但是当把它们作为虫洞连接次序时
（1 2）（3 4）（5 6）
（3 4）（1 2）（5 6）
这是相同的三对虫洞。

要避免这样的重复出现，我刚开始想的是排列组合的方法，先计算全排列，然后把重复的减掉。
但是写了之后发现这个思路很难实现，要想恰好把重复的部分减掉并不容易。

### 新思路 ###
**每次固定住每一对虫洞中的第一个，搜索第二个虫洞。**
（（fixed），（to_be_searched））（（fixed），（to_be_searched））（（fixed）……

    void wormCircle(int step) {
	    if (step == N) {	
            if(is_circle()) {
	    		//for (int i = 0; i < N; ++i) printf("%d ", holeOrder[i]); printf("\n");
                ++cnt;
                return;
            }
        }
        int i;
        for(i = 0;i < N; ++i) {
            if(sel[i]) {
                sel[i] = 0;
                holeOrder[step] = i;
                break; （~1~）
            }
        }
        for(int j = 0;j < N;++j) {
            if(sel[j]) {
                holeOrder[step + 1] = j;
                sel[j] = 0;
                wormCircle(step + 2);（~2~）
                sel[j] = 1;
            }
        }
        sel[i] = 1;
    }

首先写边界条件，即当排列完成时递归终止，并调用is_circle()判断是否构成trap，维护cnt的值。
sel[]数组记录一个虫洞是否已在本次配对方案中出现。
（~1~）处，当**顺序地**找到一个还没有被加入方案的虫洞时，将它添加到holeOrder[]数组中并放在偶数序号位上（偶数序号位这个条件由（~2~）处递归参数step + 2保证）。
接着搜索剩余的虫洞中可以放在这个固定位后面一位的虫洞并把它加入到holeOrder[]数组中。
递归调用wormCircle(int step)函数，继续固定下一对的第一个虫洞。

每次将一对虫洞中的第一位固定住可以使得如（a，b）（c，d）这样排列的两对虫洞不会出现c和d同时小于a和b的情形，保证了不会出现重复的配对方案。


### 几个值得注意的小点 ###
#### 初始化结构体数组时“+x方向上的第一个虫洞”的寻找 ####

    for(int i = 0;i < N;++i) {
		int k = 0x7FFFFFFF;
        for(int j = 0;j < N;++j) {
            if(hole[i].y == hole[j].y) {
                if(hole[j].x > hole[i].x && hole[j].x < k) {
					k = hole[j].x;
                    hole[i].rightHole = j;
                }
            }
        }
    }

#### 判断是否有环路的函数的实现 ####

    bool circle(int ori, int now) {  //表示从now点走出来的状态
	    int right = hole[now].rightHole;
	    bool first = true;
	    do { 
		    int b = hole[now].rightHole;
		    if (b == right && !first) return true;
		    if (b == -1) return false;
		    now = hole[b].conHole;
		    if (now == ori) return true;
		    first = false;
	    } while (now != -1);
	    return false;
        }
    bool is_circle() {
        for(int i = 0;i < N;i += 2) {
            hole[holeOrder[i]].conHole = holeOrder[i + 1];
            hole[holeOrder[i + 1]].conHole = holeOrder[i];
        }
        for(int i = 0;i < N;++i) {
            if(circle(i,i)) return true;
        }
        return false;
    }


