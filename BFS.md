#                                         											BFS 广度优先搜索

## 1.bfs介绍：

学习 BFS（广度优先搜索），我们同样遵循“**理解物理意义 →→ 背下万能模板 →→ 掌握实战技法**”的路线。

如果说 DFS 是一个“不撞南墙不回头”的探险家，那么 **BFS 就是“丢进水池里泛起的涟漪”**，它是一圈一圈往外扩散的。

在蓝桥杯中，**只要题目问的是：“求最少步数”、“求最短距离”、“最快几秒能到达”**（且每走一步的代价相同），**100% 要用 BFS！**

广度优先搜索算法（Breadth First Search），又称为“宽度优先搜索”，BFS 是用于图的查找算法(要求能用图表示出问题的关联性)。

## 2.解决问题类型：

1.从 A 出发是否存在到达 B 的路径；DFS 也可求
		2.从 A 出发到达 B 的最短路径；数据小 20 以内的话，DFS 也不是不可以

## 3.bfs的思路：

### 1. BFS 的核心思想：“水波扩散”与“排队”

想象你在迷宫起点，你会分身术：

1. 第一秒，你分裂成几个分身，走到起点周围所有相邻的格子上（第 1 圈）。
2. 第二秒，这些分身再次分裂，走到它们周围相邻的格子上（第 2 圈）。
3. ……一直扩散。

**重要结论**：因为是一圈圈往外扩的，**最先碰到终点的那个分身，走过的步数绝对是最短的！**

在 C++ 中，这种“按轮次扩散”的任务，我们借用数据结构 **队列 (`queue`)** 来完成：**先排队的先处理（先进先出）。**

### 2. C++ 万能 BFS 模板（必须背熟）

写 BFS 不需要递归！全靠一个 `while` 循环和一个 `queue`。遇到各种迷宫、跳点、推箱子的题，直接死磕这个模板。

```c++
#include <iostream>
#include <queue>
using namespace std;

// 1. 定义一个结构体，用来打包当前的状态（比如坐标 x, y 和 走到这里花了多少步 step）
struct Node {
    int x, y;
    int step; 
};

int dir[4][2] = {{-1,0}, {1,0}, {0,-1}, {0,1}}; // 上下左右四个方向
bool vis[100][100]; // 已经被水波覆盖过的地方，千万不能再走（重点！）
int end_x, end_y;   // 终点

int bfs(int start_x, int start_y) {
    queue<Node> q; // 准备一个排队用的队列
    
    // 2. 把起点扔进队列，并【立刻标记为已访问】
    q.push({start_x, start_y, 0});
    vis[start_x][start_y] = true;
    
    // 3. 只要队列里还有人在排队，就一直扩散
    while (!q.empty()) {
        
        // 4. 请队伍最前面的人出列，看看他走到哪了
        Node cur = q.front();
        q.pop(); // 记得把他从队伍里踢掉
        
        // 5. 【出口条件】判断这个人是不是已经踩到终点了？
        if (cur.x == end_x && cur.y == end_y) {
            return cur.step; // 找到了！立刻返回步数，这就是最短步数！
        }
        
        // 6. 没到终点？那就往上下左右四个方向扩散（分身）把下一步丢进队列
        for (int i = 0; i < 4; i++) {
            int nx = cur.x + dir[i][0];
            int ny = cur.y + dir[i][1];
            
            // 如果新位置不越界、不是墙、而且【以前没来过】
            if (nx 合法 && ny 合法 && 不是障碍物 && !vis[nx][ny]) {
                
                // 【极其重要】：只要决定把这个新位置扔进队列，必须【立刻标记vis】！
                // 否则同心圆交叉的地方会被重复排队，直接内存爆炸/TLE
                vis[nx][ny] = true; 
                
                // 把新位置扔去排队，步数是现在的步数 + 1
                q.push({nx, ny, cur.step + 1});
            }
        }
    }
    
    return -1; // 队列空了都没找到终点，说明死路一条，走不到
}
```

### 3. BFS 在蓝桥杯里的实战“套路”

在比赛中遇到 BFS 的题，你只要搞清楚 **三个问题**，往模板里一填，分数就到手了：

1. `Node` 里装什么？（状态定义）
   - *二维迷宫迷宫*：装 `x, y, step`。
   - *倒水问题*（三个杯子倒来倒去）：装三个杯子的水量 `v1, v2, v3, step`。
   - *密码锁拨盘*（每次转动一位数字）：装当前的密码字符串 `string s` 和步数 `step`。
2. 终点长什么样？（出口条件）
   - 比如迷宫是 `cur.x == end_x`；密码锁是 `cur.s == "123456"`。
3. 下一步怎么变？（扩散规则）
   - 二维地图就是 `for` 循环加上下左右的偏移量。
   - 密码锁就是用一个循环，把每一位数字 +1 或 -1，产生出新的字符串。

### 4. 终极对比：如果做新题，我选 DFS 还是 BFS？

这是很多新手最头疼的问题。记住这个铁律：

- **求“最短”、“最少”** ⇒⇒ 绝对用 **BFS**。因为水波碰到的第一下肯定是最短距离。
- **求“有多少种方案”、“打印所有路径”、“组合选数”** ⇒⇒ 绝对用 **DFS**。一条道走到黑方便记录完整的路线。
- **图太大，或者限制很深** ⇒⇒ 用 **DFS** 骗分。因为 BFS 一扩散起来非常占内存（队列里塞满了周围的点），极易导致 Memory Limit Exceeded (MLE) 爆内存。DFS 只需要存一条线，省空间。

## 4.为什么需要队列：

- **需求**：BFS 的核心是“按层遍历”，这就要求**先被扩展到的节点，必须先被处理**。
- **实现**：我们需要一个容器，能在处理当前层时，将下一层的新节点按顺序暂存。等当前层结束，再按存入的顺序依次取出这些新节点。
- **结论**：这种要求高度一致于数据结构中的**“先进先出（FIFO）”**特性。因此，队列是实现 BFS 最完美的数据结构。

## 5.bfs的缺点：

​			**在无权图（或边权相等的图，如网格地图）中，BFS 采用层级扩展的策略，优先遍历步数少的节点。因此，BFS 首次到达目标点时，所求得的绝对是“经过边数最少”的路径，此时边数最少等价于实际距离最短。**

​			**但在带权图中（边长不一），这一逻辑便会失效。“经过边数最少”不再等同于“总权重跑得最短”。例如：一条包含 2 段长边（如权值为 1000+1000）的路径，BFS 会比包含 4 段短边（如权值为 1+1+1+1）的路径更早遍历到，从而错误地将总长 2000 的解当成最优解。这也是为什么在处理带权图最短路问题时，必须放弃普通 BFS，转而采用以贪心思想为核心的 Dijkstra 算法。**

## 6.总结：

**多源 BFS = 一开始把所有起点入队，其余跟单源完全一样**

**队列两种写法完全等价，只是风格不同，推荐用 hh=0,tt=0**

**BFS/DFS 都不走回头路**

**回溯 ≠ 回头路，回溯是退回去试别的路**

**恢复现场 = 防止路径之间干扰**

# 题目：

## 第一题：入门题：[2.走迷宫 - 蓝桥云课](https://www.lanqiao.cn/problems/1216/learning/?page=1&first_category_id=1&tags=BFS&tag_relation=intersection&name=走迷宫)

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1003; 
// q用来存储当前水波扩散到的点，存放(x, y)坐标
//queue <pair<int,int>> q; 
// n行m列的地图; (x2,y2)是起点坐标; (x3,y3)是终点坐标
int n,x2,y2,x3,y3;
// g数组存地图的地形 (例如0表示墙，1表示路)

char g[N][N]; 
/* 
 * dist数组极其核心！它有两个作用：
 * 1. 记录从起点走到 (i, j) 这个点所需的最短步数。
 * 2. 如果 dist[i][j] 是 -1，说明这格子还没走过（完美充当了 vis 数组）。
 */
int dist[N][N]; 

// 方向数组：上、右、下、左 (行和列怎样平移)
int dx[] = {-1,0,1,0}; 
int dy[] = {0,1,0,-1};

// bfs函数：从起点找最短路到底能不能行
int bfs(int x,int y)
{
	// 改进2：把队列放在内部！每次调用都是全新的，不会继承上次运行残留的数据
    queue <pair<int,int>> q; 

    // 改进3：特判：如果起点恰好也是终点，那一步也不用走
    if (x == x3 && y == y3) {
        dist[x][y] = 0;
        return true; 
    }
    // 1. 初始化：把记录距离的数组全部变成 1 个不可能的数值 (-1)，表示全图未探索
    memset(dist,-1,sizeof(dist));
    
    // 2. 将起点推入排队的队伍，并立刻标记距离为 0 (我在起点当然不用走路)
    q.push({x,y});
    dist[x][y] = 0; 
    
    // 3. 核心大循环：只要还有分身在扩散，就不停下来
    while(!q.empty())
    {
        // 每次把位于排队伍头的那个点掏出来
        auto t = q.front();
        q.pop(); // 看完就要把它移出排队的行列
        
        // 4. 对这个当前点，向上下左右 4 个方向投射"探测波"
        for(int i=0;i<4;i++)
        {
            int a = t.first + dx[i];
            int b = t.second + dy[i];
            
            // a. 越界直接取消
            if(a<1 || a>n || b<1 || b>n) continue;
            // b. 有墙挡着直接取消
            if(g[a][b]!='0') continue;
            // c. 这步极其重要：检查是否走过了。>=0 说明这格子有步数了，别人比我先到，肯定它走得短
            if(dist[a][b]>=0) continue;		
            
            // 到这里说明是个风水宝地，能走，且别人没走过
            q.push({a,b}); // 扔回去排队，给之后的扩张创造条件
            
            // 到这里(a,b)花的步数，就是上一格的步数加上一步之遥
            dist[a][b] = dist[t.first][t.second] + 1; 
            
            // 改进 1 (最核心的提速)：【遇终点即停】！
            // 因为BFS先到的一定是最短的，不需要等它从队头出队，压入队尾的一瞬间就可以宣布夺冠了。
            if(a == x3 && b == y3) return true; 
        } 
    }
    // 队伍跑干了前面还没return true，说明根本过不去
    return false; 
    
    // 5. 等队列空了之后，我们去查一下终点格子的数据更新了没有
    //if(dist[x3][y3] == -1) return false; // 根本走不到，无路可走
    //else return true; // 说明途中遇到了，修改过这里的步数值
}

int main()
{
    cin>>n; 
    // 用 1 为首项输入全图
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=n;j++)
        {
            cin>>g[i][j]; 
        }
    }
    cin>>x2>>y2>>x3>>y3; // 输 起点 终点的坐标
    
    // 如果 bfs 返回了有活路走， 那就打印此时被步数记录好的答案
    if(bfs(x2,y2)) cout<<dist[x3][y3];
    else cout<<-1<<endl; // 被堵死了输出 -1
    return 0;
}
```

## 第二题：细节题：[P2895 [USACO08FEB\] Meteor Shower S - 洛谷](https://www.luogu.com.cn/problem/P2895)

```c++
#include<bits/stdc++.h>
using namespace std;
const  int  N = 310; 
int dist[N][N];
int fire[N][N];
pair<int,int> q[N*N];
int m; 
int dx[] = {-1,0,1,0};
int dy[] = {0,1,0,-1};
int bfs()
{
	memset(dist,-1,sizeof(dist));
	int hh = 0,tt = 0;
	q[0] = {0,0};
	dist[0][0] = 0;
	if(fire[0][0] == 0) return -1;
	if(fire[0][0] > 1e9) return dist[0][0];
	while(hh<=tt)
	{
		auto t = q[hh++];
		for(int i=0;i<4;i++)
		{
			int a = t.first + dx[i];
			int b = t.second + dy[i];
			if(a<0||b<0) continue;
			if(dist[a][b]>=0) continue;
			if(dist[t.first][t.second] + 1 >= fire[a][b]) continue;
			dist[a][b] = dist[t.first][t.second] + 1;
			q[++tt] = {a,b};
			if(fire[a][b] > 1e9) return dist[a][b];
		}
	}
	return -1;
}
int main()
{
	cin>>m;
	memset(fire,0x3f,sizeof(fire));
	while(m--)
	{
		int x,y,t;
		cin>>x>>y>>t;
		fire[x][y] = min(t,fire[x][y]); 
		for(int i=0;i<4;i++)
		{
			int a = x + dx[i];
			int b = y + dy[i];
			if(a<0||a>301||b<0||b>301) continue;
			fire[a][b] = min(t,fire[a][b]);
		}
	}
	int res = bfs();
	cout<<res<<endl;
	return 0;
}
```

## 第三题：双端队列：[P4554 小明的游戏 - 洛谷](https://www.luogu.com.cn/problem/P4554)

### bfs:

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 503;
deque<pair<int,int>> d;
char g[N][N];
int dist[N][N];
int n,m;
int x2,y2,x3,y3;
int dx[] = {-1,0,1,0};
int dy[] = {0,1,0,-1};
//双端队列，deque
//因为有两个不同的边权，bfs具有二段型，用双端数列可以很好的来表示0和1 
//符合的头插，不符的尾插，满足的返回
//由于bfs的性质和队列的性质，先到达目标路径的一定是最小值，即花费最少
int bfs()
{
	d.push_back({x2,y2});
	dist[x2][y2] = 0;
	while(!d.empty())		//注意感叹号
	{
		auto t = d.front();
		d.pop_front();
		for(int i=0;i<4;i++)
		{
			int a = t.first + dx[i];
			int b = t.second + dy[i];
			if(a<0||a>=n||b<0||b>=m) continue;
			if(dist[a][b]>=0) continue;
			if(g[a][b] != g[t.first][t.second])
			{
				dist[a][b] = dist[t.first][t.second] + 1; 
				d.push_back({a,b});
			}
			if(g[a][b] == g[t.first][t.second])
			{
				dist[a][b] = dist[t.first][t.second];
				d.push_front({a,b});
			}
			if(a == x3 && b == y3)
			{ 
				return dist[a][b];
			}
		}
	}
	return -1;
}
int main()
{
	while(cin>>n>>m,n||m)
	{
		for(int i = 0;i < n;i++)
		{
			cin>>g[i];
		}
		memset(dist,-1,sizeof(dist));
		d.clear();
		cin>>x2>>y2>>x3>>y3;
		int res = bfs();
		cout<<res<<endl;
	}
	return 0;
}
```

### df s练习：（但会超时）

```c++
·	#include<bits/stdc++.h>
using namespace std;
const int N = 503;
char g[N][N];
bool st[N][N];
int min_dist[N][N]; 
int n,m;
int x2,y2,x3,y3;
int dx[] = {-1,0,1,0};
int dy[] = {0,1,0,-1};
int min_ans = 1e9;
//双端队列，deque
//因为有两个不同的边权，bfs具有二段型，用双端数列可以很好的来表示0和1 
void dfs(int x,int y,int sum)
{
	 // 【核心最优性剪枝 1】：如果当前花销已经比我们找到过的全局最小花销还要大，直接放弃 (因为再走只会增加花销)
	if(sum >= min_ans) return;
	// 【核心最优性剪枝 2】：可能有别的路线也绕到了格子(x,y)，并且花销更少！
	//就是判断当前的路线走的x,y和之前的路线走到x，y的花费 
	if(sum>= min_dist[x][y]) return ;
	//如果前两个都没有return掉，说明当前到达x，y是最小花费 
	min_dist[x][y] = sum; 
	 
	if(x == x3 && y ==y3)
	{
		min_ans = min(min_ans,sum); 
		return;
	}
	
	for(int i=0;i<4;i++)
	{
		int a = x + dx[i];
		int b = y + dy[i];
		if(a<0||a>=n||b<0||b>=m) continue;
		if(st[a][b]) continue;
		st[a][b] = true;
		if(g[x][y] != g[a][b])
		{
			dfs(a,b,sum+1);
		}
		else 
		{
			dfs(a,b,sum);
		}
		st[a][b] = false;
	}
}
int main()
{
	while(cin>>n>>m,n||m)
	{
		min_ans = 1e9;
		for(int i = 0;i < n;i++)
		{
			cin>>g[i];
		}
		cin>>x2>>y2>>x3>>y3;
		memset(min_dist,0x3f,sizeof(min_dist));
		memset(st,false,sizeof(st));
		st[x2][y2] = true;
		dfs(x2,y2,0);
		cout<<min_ans<<endl; 
	}
	return 0;
}
```

## 第四题：[P1135 奇怪的电梯 - 洛谷](https://www.luogu.com.cn/problem/P1135)

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 2e2 + 10;
int dir[N];
int arr[N];
int n,a,b;
queue<int> q;
int bfs()
{
	q.push(a);
	dir[a] = 0;
	while(!q.empty())
	{
		auto t = q.front();
		q.pop();
		if(t==b)
		{
			return dir[b];
		}
		int up = t + arr[t];
		int down = t - arr[t];
		if(up<=n && dir[up] == -1) 
		{
			q.push(up);
			dir[up] = dir[t] + 1;
		} 
		if(down>=1 && dir[down] == -1) 
		{
			q.push(down);
			dir[down] = dir[t] + 1;
		}
	}
	return -1;
}
int main()
{
	cin>>n>>a>>b;
	for(int i=1;i<=n;i++)
	{
		cin>>arr[i];
	}
	memset(dir,-1,sizeof(dir));
	cout<<bfs();
	return 0;
}
```

### 笔记总结：

**“地图边界（如 `N` 或楼高 `n`）决定你能跑多远，目标点（如 `b`）只决定你什么时候停；绝不要用目标点去限制搜寻空间，‘超车再倒退’往往才是破局的最短路径！”**
