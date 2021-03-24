## 实验一 搜索

### 1、实验内容

- 求解罗马尼亚度假问题，找到从`Arad`到`Bucharest`的一条路径

- 实现两种搜索算法求解该问题

  - 一种无信息搜索算法：任选一种
  - 有信息搜索算法：$A*$搜索

  \* 地图信息可用邻接矩阵或邻接表等存储

  <div align=center >
      <img width="700" src = ../../imgs/其他/城市网络.png>
  </div>
  
  
  

<div align=center>
    图1 城市网络图
</div>


<div align=center >
    <img width = "700" src = ../../imgs/其他/直线距离.png>
</div>


<div align=center>
    图2 每个城市到Bucharest的直线距离
</div>

### 2、编程语言

`C++`

### 3、算法

- $dijkstra$算法
- $A*$算法

### 4、思路

- **无信息搜索方法**

  图的存储结构采用邻接矩阵。

  首先将城市网络图输入到边表中$($为了方便，直接将边的信息写入构建的边表中$)$。

  然后利用$dijkstra$算法，直接对问题进行求解。

  ​	$dijkstra$函数中，`prev[]`数组存放节点 $i$ 的前驱顶点

  ​	`dist[]` 数组存放 $start$ 节点到节点 $i$ 的最短路径长度

  ​	算法中，首先将 $start$ 节点加入最短路径中，然后查找其邻接点，找到离$start$最近的顶点	$k$，将其加入到最短路径中。然后利用刚纳入最短路径的顶点 $k$ 更新未纳入最短路径的顶点的最短路径以及前驱结点。

  ​	重复`顶点数-1`次上述操作，结束算法。

  根据`prev[]`数组中的信息，利用递归，从前到后打印路径。

- **有信息的搜索方法**

  题目中给出的信息为每个节点到目标节点的直线距离，我们采用$A*$算法求解。

  给定方程：$F(i) = G(i) + H(i)$， 其中，$G(i)$ = 从起点 $start$ 移动到顶点i的移动代价,$H(i)$ = 从顶点 $i$ 移动到终点 $target$ 的估算成本。

  $AStar$算法中，$pq$ 为优先队列，用来存放$F[i]$ 的最小值

  首先，我们将$start$顶点加入优先队列。

  然后，每次从优先队列中取出一个顶点$s$，将其加入最短路径序列中，遍历其邻接点，找到所有临接点中$F[i]$最下的顶点$k$，将$s - k$的距离加入最短路径长度中，将$k$加入优先队列，重复操作，当最后找到的$F[i]$最小的临接点为$target$顶点时，结束算法。

  

### 5、程序运行结果

<div align=center width="500">
    <img src = ../../imgs/其他/运行结果.png>
</div>
### 6、算法分析

虽然$dijkstra$算法和$A*$算法都能得到正确结果，但是相比来说，$A*$算法利用了提供的信息，能够计算出当前顶点到目标顶点的期望代价，是一种启发式的算法，少了很多不必要的搜索。$dijkstra$算法实质是广度优先搜索，是一种发散式的搜索，时间复杂度和空间复杂度较高

另外，$dijkstra$算法可以计算一个点到所有顶点的最短路径，而$A*$算法只能计算一个点到另一个点的最短路径，所以，当目标点很多时，$A*$算法会带入大量重复数据，所以如果不要求获得具体路径而只比较路径长度时，$dijkstra$算法更优。

### 7、代码

#### Dijkstra算法

```cpp
#include <iostream>
#include <vector>

using namespace std;

struct edge{
    int begin, end, weight;
};
//存放城市名字
string city[20]{"Arad", "Bucharest", "Craiova", "Drobeta", "Eforie", "Fagaras", "Giurgiu",
                "Hirsova", "Iasi", "lugoj", "Mehadia", "Neamt", "Oradea", "Pitesti",
                "Rimnica_Vilcea", "Sibiu", "Timisoara", "Urziceni", "Vaslui", "erind"};

//打印路径
void printPath( int pre[], int tar, int start){
    if( tar == start ){
        cout << city[tar]<<endl;
        return ;
    }
    printPath(pre, pre[tar], start);
    cout << city[tar] << endl;
}
/**
 *
 * @param graph 邻接矩阵
 * @param start 从start出发
 * @param prev  前驱顶点
 * @param dist  最短路径
 */
void dijkstra(vector<vector<int> >&graph, int start, int prev[], int dist[]) {
    int i, j, k;
    int min;
    int tmp;
    int flag[20] ;      // flag[i]=1表示"顶点start"到"顶点i"的最短路径已成功获取。

    // 初始化
    for (i = 0; i < 20; i++) {
        flag[i] = 0;              // 顶点i的最短路径还没获取到。
        prev[i] = 0;              // 顶点i的前驱顶点为0
        dist[i] = graph[start][i];    // 顶点i的最短路径为"顶点start"到"顶点i"的权。
    }

    // 对"顶点start"自身进行初始化
    flag[start] = 1;
    dist[start] = 0;

    // 每次找出一个顶点的最短路径。
    for (i = 1; i < 20; i++) {
        // 寻找当前最小的路径；
        // 即，在未获取最短路径的顶点中，找到离start最近的顶点k。
        min = INT_MAX;
        for (j = 0; j < 20; j++) {
            if (flag[j] == 0 && dist[j] < min) {
                min = dist[j];
                k = j;
            }
        }
        // 标记"顶点k"为已经获取到最短路径
        flag[k] = 1;

        // 修正当前最短路径和前驱顶点
        // 即，当已经"顶点k的最短路径"之后，更新"未获取最短路径的顶点的最短路径和前驱顶点"。
        for (j = 0; j < 20; j++) {
            tmp = (graph[k][j] == INT_MAX ? INT_MAX : (min + graph[k][j])); //防止溢出
            if (flag[j] == 0 && (tmp < dist[j])) {
                dist[j] = tmp;
                prev[j] = k;
            }
        }
    }
}

int main() {
    //邻接矩阵
    vector<vector<int>> graph(20, vector<int>(20));
    //边表
    edge e[16] = {
            {0, 19, 75},
            {0, 16, 118},
            {0, 15, 140},
            {1, 5, 211},
            {1, 13, 101},
            {2,3, 120},
            {2,14, 146},
            {2, 13, 138},
            {3,10, 75},
            {5, 15, 99},
            {9, 16, 111},
            {9, 10, 70},
            {12, 19, 71},
            {12, 15, 151},
            {13, 14, 97},
            {14, 15,80},
    };
    //初始化邻接矩阵
    for(int i = 0; i < 20; i++){
        for(int j  =0; j < 20; j++){
            graph[i][j] = INT_MAX;
            if( i == j ){
                graph[i][j] = 0;
            }
        }
    }
    for (auto & i : e) {
        graph[i.begin][i.end] = i.weight;
        graph[i.end][i.begin] = i.weight;
    }
    int prev[20];//存放前驱结点
    int dist[20];//存放最短路径长度
    //计算最短路径
    dijkstra(graph, 0, prev, dist);

    // 打印dijkstra最短路径的结果
    cout << "从" << city[0] << "到" << city[1] << "的最短路径为：" << dist[1] << endl;
    cout << "依次经过的路径为:" <<endl;
    printPath(prev, 1, 0);

    return 0;
}
```

#### A*算法

```cpp
#include <iostream>
#include <queue>
#include <stack>
#include <algorithm>

using namespace std;

struct edge {
    int begin, end, weight;
};

//存放城市名字
string city[20]{"Arad", "Bucharest", "Craiova", "Drobeta", "Eforie", "Fagaras", "Giurgiu",
                "Hirsova", "Iasi", "lugoj", "Mehadia", "Neamt", "Oradea", "Pitesti",
                "Rimnica_Vilcea", "Sibiu", "Timisoara", "Urziceni", "Vaslui", "erind"};
//每个顶点到1的直线距离
int line_distance_to_target[20]{366, 0, 160, 242, 161, 176, 77, 151, 226, 244,
                                241, 234, 380, 100, 193, 253, 329, 80, 199, 374};

/**
 *
 * @param graph     邻接矩阵
 * @param pq        优先队列，选择代价最小
 * @param path_city 最短路径经过的城市
 * @param start     起点
 * @param target    终点
 */
//返回最短路径的长度
int AStar(vector<vector<int> > &graph, priority_queue<int> &pq, queue<int> &path_city, int start, int target) {
    pq.push(0);
    //最小代价
    int min_length;
    //最短路径的长度
    int short_path_len = 0;
    while (!pq.empty()) {
        int s = pq.top();
        //加入最短路径
        path_city.push(s);
        pq.pop();
        //到达终点，结束循环
        if (s == target) {
            break;
        }
        min_length = INT_MAX;
        int k;
        for (int i = 0; i < 20; i++) {
            if (graph[s][i] != INT_MAX && s != i) {
	//更新代价
                if (min_length > graph[s][i] + line_distance_to_target[i]) {
                    min_length = graph[s][i] + line_distance_to_target[i];
                    k = i;
                }
            }
        }
        //放入刚添加的路径的长度
        short_path_len += graph[s][k];
        pq.push(k);
    }
    return short_path_len;
}

int main() {
    //优先队列，选择代价最小的
    priority_queue<int> pq;
    //邻接矩阵
    vector<vector<int>> graph(20, vector<int>(20));
    //边表
    edge e[16] = {
            {0,  19, 75},
            {0,  16, 118},
            {0,  15, 140},
            {1,  5,  211},
            {1,  13, 101},
            {2,  3,  120},
            {2,  14, 146},
            {2,  13, 138},
            {3,  10, 75},
            {5,  15, 99},
            {9,  16, 111},
            {9,  10, 70},
            {12, 19, 71},
            {12, 15, 151},
            {13, 14, 97},
            {14, 15, 80},
    };
    //初始化邻接矩阵
    for (int i = 0; i < 20; i++) {
        for (int j = 0; j < 20; j++) {
            graph[i][j] = INT_MAX;
            if (i == j) {
                graph[i][j] = 0;
            }
        }
    }
    for (auto &i : e) {
        graph[i.begin][i.end] = i.weight;
        graph[i.end][i.begin] = i.weight;
    }

    queue<int> path_city;
    int result = AStar(graph, pq, path_city, 0, 1);
    cout << "从" << city[0] << "到" << city[1] << "的最短路径长度为" << result << "，沿途路径为：" << endl;
    while (!path_city.empty()) {
        int i = path_city.front();
        cout << city[i] << endl;
        path_city.pop();
    }
    return 0;
}

```



<div align=right>
    2021年3月21日
</div>

