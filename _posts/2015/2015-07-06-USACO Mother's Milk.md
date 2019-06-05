---
time: 2015-07-06 23:58
permalink: USACO-milk3
---
<!--excerpt-->
[PPCA Mother's Milk][1]


  [1]: http://train.usaco.org/usacoprob2?a=nmTv3aKmrIY&S=milk3


----------

## 题意 ##
三个容器 A,B,C。互相倒牛奶。
初始状态只有C中装满牛奶。问倒若干次后，当A中没有牛奶时C中可能的情况有多少种。
数据范围 1到20

## 思路 ##
看到这样的题目我的第一反应居然是……
数学推导……也就是找规律……
如果这样的话要计算机有何用啊专业做repetitive work的计算机不是要失业了吗

so
上DFS

    void dfs() {
        if(milkSta[milk[0]][milk[1]][milk[2]]) return;
        milkSta[milk[0]][milk[1]][milk[2]] = true;
        if(milk[0] == 0) leftover[loP++] = milk[2];

        for(int i = 0;i < 3;++i) {
            for(int j = 0;j < 3;++j) {
                if(i == j) continue;
                int tmp[3] = { 0 };
                for(int k = 0;k < 3;++k) {
                    tmp[k] = milk[k];
                }
                if(milk[i] <= (capacity[j] - milk[j])) {
                    tmp[j] = milk[j] + milk[i];
                    tmp[i] = 0;
                }
                else {
                    tmp[j] = capacity[j];
                    tmp[i] = milk[i] - (capacity[j] - milk[j]);
                }

                for(int k = 0;k < 3;++k) {
                    std::swap(milk[k],tmp[k]);
                }
                dfs();
                for(int k = 0;k < 3;++k) {
                    std::swap(milk[k],tmp[k]);
                }
            }
        }
    }

### 存状态判重的正确姿势 ###
    const int maxMilk = 20 + 5;
    bool milkSta[maxMilk][maxMilk][maxMilk];

而不是开三个数组存状态然后每一次都扫一遍啊

### 全局变量自动赋值为零，so ###

    for(int i = 0;i < maxMilk;++i) {
        for(int j = 0;j < maxMilk;++j) {
            for(int k = 0;k < maxMilk;++k) {
                milkSta[i][j][k] = false;
            }
        }
    }
因为这个三维数组定义在main()函数外面所以其实这个赋值是不必要的

### 关闭文件…… ###
    fclose(stdin); fclose(stdout);



