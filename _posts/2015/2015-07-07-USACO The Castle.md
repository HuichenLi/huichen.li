---
time: 2015-07-07 22:02
permalink: USACO-castle
---
<!--excerpt-->
[USACO The Castle][1]


----------

## 题意 ##
城堡。有若干个房间，每个房间由若干个格子组成，部分格子间由墙壁隔开以形成封闭的房间。
求房间数。最大房间格子数。敲掉一堵墙壁后最大房间格子数。敲掉的墙壁。

### 原思路 ###
第一次写的时候就开了一个结构体准备存相关的信息…

    struct room{
        int x,y;
        int num;
        room *nextDoor[4];
        int biDoor[4];
        int d;
    }castle[50+5][50+5];

于此相应的是一大堆麻烦的预处理：

    void decide_nextDoor(int i,int j) {
        int num = castle[i][j].num;
        int d = castle[i][j].d = 0;
        for(int k = 3;k >= 0;--k) {
            castle[i][j].biDoor[k] = num % 2;
            num /= 2;
        }
        if(castle[i][j].biDoor[0]) castle[i][j].nextDoor[0] = castle[i][j-1];
        if(castle[i][j].biDoor[1]) castle[i][j].nextDoor[1] = castle[i-1][j];
        if(castle[i][j].biDoor[2]) castle[i][j].nextDoor[2] = castle[i][j+1];
        if(castle[i][j].biDoor[3]) castle[i][j].nextDoor[3] = castle[i+1][j];
    }
……
这当然是太奇怪的方法=，=

### Flood Fill ###
[USACO text Flood Fill][2]


  [1]: http://train.usaco.org/usacoprob2?a=PXoc4D3qPnu&S=castle
  [2]: http://train.usaco.org/usacotext2?a=0GieFnEVcjU&S=flood


搜索。染色。

#### 判断是否联通——墙的处理：位运算 ####

题目中墙方位的表示：
 - 1: wall to the west
 - 2: wall to the north
 - 4: wall to the east
 - 8: wall to the south
则用  **&  运算符**就可以得到墙的位置：num & 2^i 为零则没有墙，反之有墙

#### 判断是否已访问：染色 ####

    int visit[50+5][50+5];
用visit数组记录，所存的非零值为**当前联通块的编号**。
这样一来后面判断拆墙后房间大小时比较方便：可以根据编号到数组的相应下标中找到原先房间的大小。
   
     int roomSize[250];

于是乎代码如下：
   
     int searchRoom(int x,int y,int ct) {
        int num = room[x][y];
        if(visit[x][y]) return 0;
        int tmp = 1;
        visit[x][y] = ct;
        for(int i = 0;i < 4;++i) {
            if(!(num & expo[i])) tmp += searchRoom(x + xdif[i],y + ydif[i],ct);
        }
        return tmp;
    }

以下三个数组是为了减少重复代码而加入的
    
    int expo[4] = {1,2,4,8};
    int xdif[4] = {0,-1,0,1};
    int ydif[4] = {-1,0,1,0};






