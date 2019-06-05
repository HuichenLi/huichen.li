---
time: 2015-07-08 23:15
permalink: USACO-hamming
---
<!--excerpt-->

[USACO Hamming Codes][1]


  [1]: http://train.usaco.org/usacoprob2?a=M1i5gvRqbfP&S=hamming


----------

## 题意 ##
Hamming Codes海明码。找出 N 个 B 位的编码。两两间有 D 个单位的hamming距离。
hamming距离：两数二进制表示中不同的位数。
输出字典序排列的最小答案。

## **计算hamming距离：位运算 ##

    int cnt(int x) {
        int ans = 0;
        while(x) {
            if(x % 2) ++ans;
            x /= 2;
            }
            return ans;
        }
    bool ham(int x,int step) {
        for(int i = 1;i <= step;++i) {
            if(cnt(x ^ nowcode[i]) < D) return false;
        }
        return true;
    }

## 枚举 ##

> In the case of multiple solutions, your program should output the solution which, if interpreted as a base 2^B integer, would have the least value.

一开始把这段文字理解成了编码值的和最小=，=
于是想，A… B… C… D… 这一段数字当中的ABCD四个编码假如A，D和B，C分别符合题意则A+D不一定（不知道怎么证明）小于B+C，所以不能用贪心算法得最前的编码，于是写了一个搜索：

    void dfs(int step,int now) {
    if(step > N) {
        if(nowsum < minsum) {
            for(int i = 1;i <= N;++i) {
                codewords[i] = nowcode[i];
            }
			minsum = nowsum;
        }
        return;
    }
    for(int i = now;i <= maxNum;++i) {
        if(!ham(i,step)) continue;
        nowcode[step] = i;
        nowsum += i;
        dfs(step+1,i+1);
            nowsum -= i;
        }
        return;
    }

结果超时了。

后来改成了：

    bool flag = true;
    void dfs(int step,int now) {
	    if (!flag) return;
        if(step > N) {
            if(nowsum < minsum) {
                for(int i = 1;i <= N;++i) {
                    codewords[i] = nowcode[i];
                }
	    		minsum = nowsum;
            }
		    flag = false;
            return;
        }
        for(int i = now;i <= maxNum;++i) {
            if(!ham(i,step)) continue;
            nowcode[step] = i;
            nowsum += i;
            dfs(step+1,i+1);
		    if (!flag) return;
            nowsum -= i;
        }
        return;
    }

然而这样的话跟直接while枚举并没有什么区别  而且还繁琐好多。
**不要读错题意呀！！**




