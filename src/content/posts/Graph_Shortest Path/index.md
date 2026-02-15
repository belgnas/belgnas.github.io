---
title: 图论_最短路
published: 2025-02-12
pinned: false
description: 朴素Dijkstra、堆优化Dijkstra、Bellman-Ford、SPFA、Floyd
tags: [算法, 图论, 最短路]
category: 算法与数据结构
author: qiqimora
draft: false
---

# 最短路

我们学编程不会让我们去数学证明，我们只需要知道结论，然后用代码去“实现”就好了，实际上就比的是记忆能力。

## 算法速览
分类（在每种情况下，都有各自的最优抉择）
1. 单源最短路（求一个点到其他所有点的最短距离）
    - 所有边权重为正数
      - 朴素Dijkstra：$  O(n^2)$  ，适合稠密图
      - 堆优化Dijkstra：$  O(m\log n)$  ，适合稀疏图
    - 存在负权边
      - Bellman-Ford：$  O(nm)$  ，可限制边数，无负权回路才有解
      - SPFA：平均 $  O(m)$  ，最坏 $  O(nm)$  ，队列优化的 Bellman-Ford
2. 多源汇最短路（任意两点之间最短距离）
    - Floyd：$  O(n^3)$  

> 所有算法默认针对有向图。无向图等价于双向有向边。

---

## 朴素 Dijkstra
**题目**：AcWing 849. Dijkstra求最短路 I  
**链接**：https://www.acwing.com/problem/content/851/

### 完整题面
#### 题目描述
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，所有边权均为正值。
请你求出 1 号点到 n 号点的最短距离，如果无法从 1 号点走到 n 号点，则输出 −1。

#### 输入格式
第一行包含两个整数 n 和 m。
接下来 m 行，每行包含三个整数 a,b,c，表示存在一条从 a 走到 b 的有向边，边长为 c。

#### 输出格式
输出一个整数，表示 1 号点到 n 号点的最短距离。
如果路径不存在，则输出 −1。

#### 数据范围
$  1≤n≤500$    
$  1≤m≤10^5$    
$  1≤a,b≤n$    
$  c>0$    

#### 输入样例
```
3 3
1 2 2
2 3 1
1 3 4
```

#### 输出样例
```
3
```

### 代码（含完整思考注释）
```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 510; // 这是一个稠密图，所以用邻接矩阵来存

int n, m;    // n个点，m条边
int g[N][N]; // 邻接矩阵
int dist[N]; // 距离数组：dist[i]表示1号点到i号点的最短距离
bool st[N];  // st[i]表示i号点是否已经确定了距离

int dijkstra()
{
    // 为什么要用0x3f，
    // 因为memset是针对每个字节进行操作的，
    // C++中int型变量所占的位数为4个字节，即32位
    // 4个字节均为0x3f时，0x3f3f3f3f的十进制是1061109567，也就是10^9级别的（和0x7fffffff一个数量级）
    memset(dist, 0x3f, sizeof dist); // 初始化距离数组，把所有点的距离都设置为正无穷
    dist[1] = 0;                     // 把起点的距离设置为0

    for (int i = 0; i < n - 1; i++) // 迭代n - 1次
    {
        int t = -1; // 找到不在s中的，距离最近的点t
        for (int j = 1; j <= n; j++)
            // 下面这个 if 的意思就是：在不在s中的点中（距离还没有确定距离，即st[j]为false的第一个点），找到距离最近的点t
            if (!st[j] && (t == -1 || dist[t] > dist[j])) // 如果j不在s中，并且t是第一个点，或者dist[t] > dist[j]
                t = j;                                    // 那么就把j赋值给t

        // // 可以选加的优化
        // if (t == n) break; // 如果t == n，说明1号点到n号点的最短距离已经确定了，所以就可以退出循环

        st[t] = true; // 把t加入s

        for (int j = 1; j <= n; j++)                   // 用t更新其他点的距离
            dist[j] = min(dist[j], dist[t] + g[t][j]); // dist[j] = min(dist[j], dist[t] + g[t][j])
        // 为什么是min呢？
        // 因为我们是要找到1号点到n号点的最短距离，所以我们要取最小值
    }

    if (dist[n] == 0x3f3f3f3f)
        return -1;  // 如果dist[n] == 0x3f3f3f3f，说明1号点到n号点没有路径，返回-1
        
    return dist[n]; // 返回1号点到n号点的最短距离
}

int main()
{
    scanf("%d%d", &n, &m);

    memset(g, 0x3f, sizeof g); // 初始化邻接矩阵，把所有边的权重都设置为正无穷

    while (m--)
    {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        g[a][b] = min(g[a][b], c); // 因为可能存在重边，所以要取最小值
    }

    int t = dijkstra();

    printf("%d\n", t);

    return 0;
}

// 如果想优化时间复杂度，可以使用堆优化
// 用堆优化的方式有2种：
//  1.手写堆
//  2.使用STL中的priority_queue（优先队列）

// 因为优先队列比手写堆要简单，所以我们一般使用优先队列来优化
// 具体优化方法见下一节   
```

---

## 堆优化 Dijkstra
**题目**：AcWing 850. Dijkstra求最短路 II  
**链接**：https://www.acwing.com/problem/content/852/

### 完整题面
#### 题目描述
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，所有边权均为非负值。
请你求出 1 号点到 n 号点的最短距离，如果无法从 1 号点走到 n 号点，则输出 −1。

#### 输入格式
第一行包含两个整数 n 和 m。
接下来 m 行，每行包含三个整数 a,b,c，表示存在一条从 a 走到 b 的有向边，边长为 c。

#### 输出格式
输出一个整数，表示 1 号点到 n 号点的最短距离。
如果路径不存在，则输出 −1。

#### 数据范围
$1≤n,m≤1.5×10^5$  
$1≤a,b≤n$  
$c≥0$  

#### 输入样例
```
3 3
1 2 2
2 3 1
1 3 4
```

#### 输出样例
```
3
```

### 代码
```cpp
#include <iostream>
#include <algorithm>
#include <cstring>
#include <queue>

using namespace std;

typedef pair<int, int> PII; // 定义一个pair类型，第一个元素是距离，第二个元素是点的编号

const int N = 100010;

int n, m; // n个点，m条边
int h[N], e[N], ne[N], w[N], idx; // h是邻接表的头结点，e是边的终点，ne是下一条边的编号，w是边的权重，idx是边的编号
int dist[N]; // 存储1号点到每个点的最短距离
bool st[N]; // 存储每个点是否已经确定了距离

void add(int a, int b, int c) // 添加一条边a->b，权重为c
{
    
    // 这样子想：h[a]是一个链表，链表的头结点是h[a]，头结点的下一个是起点结点是ne[idx]，即ne[h[a]]，头结点的下一个的下一个结点是ne[ne[h[a]]]，以此类推
    // 其中e[idx]是起点对应的终点，w[idx]是权重，可以看成起点的2个内容元素。
    e[idx] = b;
    // cout << "e[" << idx << "] = " << b << endl;
    w[idx] = c;
    // cout << "w[" << idx << "] = " << c << endl;
    ne[idx] = h[a];
    // cout << "ne[" << idx << "] = " << h[a] << endl;
    h[a] = idx;
    // cout << "h[" << a << "] = " << idx << endl;
    idx++;
    // cout << "idx = " << idx << endl;
}

int dijkstra()
{
    memset(dist, 0x3f, sizeof dist); 
    dist[1] = 0;

    priority_queue<PII, vector<PII>, greater<PII>> heap; // 定义一个小根堆，存储距离和点的编号
    heap.push({0, 1}); // 把距离为0的1号点加入堆中

    while (heap.size())// while堆不为空
    {
        auto t = heap.top(); // 每次找到距离最小的点，也就是堆顶元素
        heap.pop(); // 弹出堆顶元素

        int ver = t.second, distance = t.first; // ver是点的编号，distance是距离
        
        if (st[ver]) continue; // 如果这个点已经被确定了距离，就跳过
        // 比如有2条路被记录到堆中，一条是{4, 3}，{5, 3}，前者是{ 距离为4 , 起点到点3 }，后者是{ 距离为5 , 起点到点3}
        // 由于小根堆的特性，{4, 3}会被先取出，这是一切正常运行，st[3]会从false标记成true，然后堆里剩下的那个{5, 3}就没有了意义，等到{5, 4}时就会直接continue
        st[ver] = true;

        for (int i = h[ver]; i != -1; i = ne[i]) // 遍历ver的所有出边
        {
            int j = e[i]; // j是ver的出边的终点
            if (dist[j] > distance + w[i]) // 如果j的距离大于ver的距离加上ver到j的权重
            {
                dist[j] = distance + w[i]; // 更新j的距离
                heap.push({dist[j], j}); // 把j加入堆中
            }
        }
    }


    if (dist[n] == 0x3f3f3f3f) return -1;  // 如果dist[n] == 0x3f3f3f3f，说明1号点到n号点没有路径，返回-1
        
    return dist[n]; // 返回1号点到n号点的最短距离
}

int main()
{
    scanf("%d%d", &n, &m);

    memset(h, -1, sizeof h); // 初始化邻接表

    while (m--)
    {
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        add(a, b, c); // 添加一条边a->b，权重为c
    }

    int t = dijkstra();

    printf("%d\n", t);

    return 0;
}  
```

---

## Bellman-Ford
**题目**：AcWing 853. 有边数限制的最短路  
**链接**：https://www.acwing.com/problem/content/855/

### 完整题面
#### 题目描述
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，边权可能为负数。
请你求出从 1 号点到 n 号点的最多经过 k 条边的最短距离，如果无法从 1 号点走到 n 号点，输出 `impossible`。

#### 输入格式
第一行包含三个整数 n,m,k。
接下来 m 行，每行包含三个整数 a,b,c，表示存在一条从 a 走到 b 的有向边，边长为 c。

#### 输出格式
输出一个整数，表示从 1 号点到 n 号点的最多经过 k 条边的最短距离。
如果不存在满足条件的路径，则输出 `impossible`。

#### 数据范围
$1≤n,k≤500$  
$1≤m≤10^4$  
$1≤a,b≤n$  
$−10^4≤c≤10^4$  

#### 输入样例
```
3 3 1
1 2 1
2 3 1
1 3 3
```

#### 输出样例
```
3
```

### 代码
```cpp
#include<iostream>
#include<iostream>
#include<algorithm>
#include<cstring>
using namespace std;

const int N = 510, M = 10010;

int n, m, k;
int dist[N], backup[N];

struct Edge
{
    int a, b, w;
}edges[M]; // 这里edges[M]是一个数组，数组的元素是Edge类型的结构体

int bellman_ford()
{
    memset(dist, 0x3f, sizeof dist); // 初始化dist数组
    dist[1] = 0; // 1号点到1号点的距离为0

    for(int i = 0; i < k; i++)
    {
        memcpy(backup, dist, sizeof dist); // 备份dist数组
        // 为什么要备份dist数组？
        // 因为我们要更新dist数组，但是我们要保证更新dist数组的时候，dist数组中的值是上一次迭代的结果，而不是本次迭代的结果
        for(int j = 0; j < m; j++)
        {
            int a  = edges[j].a, b = edges[j].b, w = edges[j].w;
            dist[b] = min(dist[b], backup[a] + w); // 更新dist数组
        }
    }

    if(dist[n] > 0x3f3f3f3f / 2) return -1; // 如果dist[n]大于(一个量级很大的数) 0x3f3f3f3f / 2，说明最短路不存在
    return dist[n]; // 否则，返回dist[n]
    
}

int main()
{
    scanf("%d%d%d", &n, &m, &k);
    
    for(int i = 0; i < m; i++)
    {
        int a, b, w;
        scanf("%d%d%d", &a, &b, &w);
        edges[i] = {a, b, w};
    }

    int t = bellman_ford();

    if(t == -1) puts("impossible"); // 如果最短路不存在，输出impossible
    else printf("%d\n", t); // 否则，输出最短距离

    return 0;
}
```

---

## SPFA 求最短路
**题目**：AcWing 851. spfa求最短路  
**链接**：https://www.acwing.com/problem/content/853/

### 完整题面
#### 题目描述
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，边权可能为负数。
请你求出 1 号点到 n 号点的最短距离，如果无法从 1 号点走到 n 号点，则输出 `impossible`。

#### 输入格式
第一行包含两个整数 n 和 m。
接下来 m 行，每行包含三个整数 a,b,c，表示存在一条从 a 走到 b 的有向边，边长为 c。

#### 输出格式
输出一个整数，表示 1 号点到 n 号点的最短距离。
如果路径不存在，则输出 `impossible`。

#### 数据范围
$1≤n,m≤10^5$  
$1≤a,b≤n$  
$−10^9≤c≤10^9$  

#### 输入样例
```
3 3
1 2 5
2 3 -3
1 3 4
```

#### 输出样例
```
2
```

### 代码
```cpp
#include<iostream>
#include<algorithm>
#include<cstring>
#include<queue>

using namespace std;

const int N = 100010;

int n, m;
int h[N], e[N], ne[N], w[N], idx;
int dist[N];
bool st[N];

void add(int a, int b, int c)
{
    w[idx] = c;
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx;
    // 这样子想：h[a]是一个链表，链表的头结点是h[a]，头结点的下一个是起点结点是ne[idx]，即ne[h[a]]，头结点的下一个的下一个是起点结点是ne[ne[h[a]]]，以此类推
    // 其中e[idx]是起点对应的终点，w[idx]是权重，可以看成起点的2个内容元素。
    idx ++;
}

int spfa()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    queue<int> q;
    q.push(1); // 把距离为0的1号点加入队列中
    st[1] = true; // 表示1号点在队列中

    while(q.size())
    {
        int t = q.front();
        q.pop();

        st[t] = false; // 表示t号点不在队列中

        for(int i = h[t]; i != -1; i = ne[i]) // 更新t号点的所有出边
        {
            int j = e[i];
            if(dist[j] > dist[t] + w[i]) // 如果j号点的距离大于t号点的距离加上边权
            {
                dist[j] = dist[t] + w[i]; // 更新j号点的距离
                if(!st[j]) // 如果j号点不在队列中
                {
                    q.push(j); // 把j号点加入队列中
                    st[j] = true; // 表示j号点在队列中
                }
            }
        }
    }

    if(dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}

int main()
{
    cin >> n >> m;

    memset(h, -1, sizeof h);

    while(m--)
    {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }

    int t = spfa();

    if(t == -1) cout << "impossible" << endl;
    else cout << t << endl;

    return 0;
}
```

---

## SPFA 判断负环
**题目**：AcWing 852. spfa判断负环  
**链接**：https://www.acwing.com/problem/content/854/

### 完整题面
#### 题目描述
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，边权可能为负数。
请你判断图中是否存在负权回路。

#### 输入格式
第一行包含两个整数 n 和 m。
接下来 m 行，每行包含三个整数 a,b,c，表示存在一条从 a 走到 b 的有向边，边长为 c。

#### 输出格式
如果图中存在负权回路，则输出 `Yes`，否则输出 `No`。

#### 数据范围
$1≤n≤2000$  
$1≤m≤10^4$  
$1≤a,b≤n$  
$−10^4≤c≤10^4$  

#### 输入样例
```
3 3
1 2 -1
2 3 4
3 1 -4
```

#### 输出样例
```
Yes
```

### 代码
```cpp
#include<iostream>
#include<algorithm>
#include<cstring>
#include<queue>

using namespace std;

const int N = 100010;

int n, m;
int h[N], e[N], ne[N], w[N], idx;
int dist[N], cnt[N];
bool st[N];

void add(int a, int b, int c)
{
    w[idx] = c;
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx;
    // 这样子想：h[a]是一个链表，链表的头结点是h[a]，头结点的下一个是起点结点是ne[idx]，即ne[h[a]]，头结点的下一个的下一个是起点结点是ne[ne[h[a]]]，以此类推
    // 其中e[idx]是起点对应的终点，w[idx]是权重，可以看成起点的2个内容元素。
    idx ++;
}

bool spfa()
{
    queue<int> q;
    for(int i = 1; i <= n; i++) // 我们一开始需要把所有点都加入队列中，然后从每个点开始判断是否有负环
    {
        q.push(i);
        st[i] = true;
    }

    while(q.size())
    {
        int t = q.front();
        q.pop();

        st[t] = false; // 表示t号点不在队列中

        for(int i = h[t]; i != -1; i = ne[i]) // 更新t号点的所有出边
        {
            int j = e[i];
            if(dist[j] > dist[t] + w[i]) // 如果j号点的距离大于t号点的距离加上边权
            {
                dist[j] = dist[t] + w[i]; // 更新j号点的距离
                cnt[j] = cnt[t] + 1; // 更新j号点的入队次数

                if(cnt[j] >= n) return true; // 如果j号点的入队次数大于等于n，则说明存在负环

                if(!st[j]) // 如果j号点不在队列中
                {
                    q.push(j); // 把j号点加入队列中
                    st[j] = true; // 表示j号点在队列中
                }
            }
        }
    }

    return false;
}

int main()
{
    cin >> n >> m;

    memset(h, -1, sizeof h);

    while(m--)
    {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }

    if(spfa()) puts("Yes");
    else puts("No");

    return 0;
}
```

---

## Floyd 多源最短路
**题目**：AcWing 854. Floyd求最短路  
**链接**：https://www.acwing.com/problem/content/856/

### 完整题面
#### 题目描述
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，边权可能为负数。
再给定 k 个询问，每个询问包含两个整数 x 和 y，表示求从点 x 到点 y 的最短距离。
如果路径不存在，则输出 `impossible`。

#### 输入格式
第一行包含三个整数 n,m,k。
接下来 m 行，每行包含三个整数 a,b,c，表示存在一条从 a 走到 b 的有向边，边长为 c。
接下来 k 行，每行包含两个整数 x,y，表示询问从点 x 到点 y 的最短距离。

#### 输出格式
共 k 行，每行输出一个整数，表示询问的结果。
如果路径不存在，则输出 `impossible`。

#### 数据范围
$1≤n≤200$  
$1≤m≤20000$  
$1≤k≤10^4$  
$1≤a,b,x,y≤n$  
$−10^9≤c≤10^9$  

#### 输入样例
```
3 3 2
1 2 1
2 3 2
1 3 1
2 1
1 3
```

#### 输出样例
```
impossible
1
```

### 代码
```cpp
#include<iostream>
#include<algorithm>
#include<cstring>

using namespace std;

const int N = 210, INF = 1e9;

int n, m, Q; // n个点，m条边，Q个询问
int d[N][N]; // d是邻接矩阵，d[i][j]表示从i到j的最短距离

void floyd()
{
    for(int k = 1; k <= n; k++) // 枚举中间点
        for(int i = 1; i <= n; i++) // 枚举起点
            for(int j = 1; j <= n; j++) // 枚举终点
                d[i][j] = min(d[i][j], d[i][k] + d[k][j]); // 松弛操作
}

int main()
{
    scanf("%d%d%d", &n, &m, &Q);

    // 初始化邻接矩阵，将所有点到自己的距离初始化为0，其他点到自己的距离初始化为无穷大
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= n; j++)
            if(i == j) d[i][j] = 0;
            else d[i][j] = INF;

    while(m--)
    {
        int a, b, w;
        scanf("%d%d%d", &a, &b, &w);

        d[a][b] = min(d[a][b], w); // 有重边，只保留最短的边
    }

    floyd();

    // 处理询问
    while(Q--)
    {
        int a, b;
        scanf("%d%d", &a, &b);

        if(d[a][b] > INF / 2) puts("impossible"); // 如果距离大于INF/2，说明没有路径
        else printf("%d\n", d[a][b]);
    }

    return 0;
}
```

---

## 总结
### Dijkstra-朴素 O(n^2)
1. 初始化距离数组, dist[1] = 0, dist[i] = inf;
2. for n次循环 每次循环确定一个min加入S集合中，n次之后就得出所有的最短距离
3. 将不在S中dist_min的点->t
4. t->S加入最短路集合
5. 用t更新到其他点的距离

### Dijkstra-堆优化 O(mlogm)
1. 利用邻接表，优先队列
2. 在priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> heap;中将返回堆顶
3. 利用堆顶来更新其他点，并加入堆中类似宽搜的过程

### Bellman_ford O(nm)
1. 注意连锁想象需要备份, struct Edge{int a,b,c} Edge[M];
2. 初始化dist, 松弛dist[x.b] = min(dist[x.b], backup[x.a]+x.w);
3. 松弛k次，每次访问m条边

### Spfa O(n)~O(nm)
1. 利用队列优化仅加入修改过的地方
2. for k次
3. for 所有边利用宽搜模型去优化bellman_ford算法
4. 更新队列中当前点的所有出边

### Floyd O(n^3)
1. 初始化d
2. k, i, j 去更新d