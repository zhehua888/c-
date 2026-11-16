#                                              												DFS

学习 DFS（深度优先搜索）最快的方法，就是**背诵一套万能模板**，然后深刻理解它的**“物理意义”**。

在 C++ 中，DFS 几乎全是靠**“递归（函数自己调用自己）”**来实现的。

### 1. DFS 的核心思想：“不撞南墙不回头”

想象你在走迷宫：

1. 你遇到一个岔路口，有 A、B、C 三条路。
2. 你先选 A 路，一直往前走，到底了发现是死胡同（**撞南墙**）。
3. 你沿原路退回到上一个岔路口（**回溯**）。
4. 接着选 B 路，继续一直往前走……
5. 直到找到出口。

这就是 DFS！**一条路走到黑，走不通就退回来换一条。**

### 2. C++ 万能 DFS 模板 (一定要背下来)

无论是走迷宫、全排列、还是搜图，90% 的基础 DFS 都可以套用下面这个结构：

```c++
#include <bits/stdc++.h>
using namespace std;

int path[10]; // path[i] 用来记录第 i 步选了哪个数字
bool vis[10]; // vis[i] 等于 true 表示数字 i 已经被用过了 (visited)
int n = 3;    // 我们要全排列 1~3

// step 表示当前要填第几个空 (这里从 1 开始)
void dfs(int step) {
    // 1. 出口条件：如果我们要填第 n+1 个空，说明前 n 个空已经填满了！
    if (step == n + 1) { 
        for (int i = 1; i <= n; i++) {
            cout << path[i] << " "; // 输出这条路的结果
        }
        cout << endl;
        return; // 退回到上一层
    }

    // 2. 枚举所有可能的选择 (数字 1 到 n)
    for (int i = 1; i <= n; i++) {
        // 如果数字 i 还没被用过
        if (vis[i] == false) { 
            
            // 3. 标记：我选了数字 i
            vis[i] = true;     // 标记为已使用
            path[step] = i;  // 记录到路径中
            
            // 4. 递归：去填下一个空
            dfs(step + 1); 
            
            // 5. 回溯 (恢复现场)：刚才那条路走完了，我要把数字 i 吐出来，留给别的路用
            vis[i] = false;  
            // path[step] 不需要清空，因为下次填会被直接覆盖
        }
    }
}

int main() {
    dfs(1); // 从填第 1 个空开始搜索
    return 0;
}
```

## ！DFS三大题型：

1.[B3622 枚举子集（递归实现指数型枚举） - 洛谷](https://www.luogu.com.cn/problem/B3622)

![img](https://i-blog.csdnimg.cn/direct/964732e6c3e44f79918e6858825ae989.png)

```c++
#include<bits/stdc++.h>
using namespace std;
int st[20];    //0代表不选，1代表选，2代表考虑中 
int n;
void dfs(int i)  // i 的含义代表当前的位置，从第一开始 
{
	//递归的出口
	if(i>n)
	{
		for(int j=1;j<=n;j++)
		{
			if(st[j] == 1)
			{
				cout<<j<<" ";
			}
		}
		cout<<endl;
		return;
	} 
	//不选的情况 
	st[i] = 0;
	dfs(i+1);
	//回溯
	st[i] = 2;
	//选择的情况
	st[i] = 1;
	dfs(i+1);
	st[i] = 2;	
	 
} 
int main()
{
	cin>>n;
	dfs(1);
	return 0;
}
```

2.[B3623 枚举排列（递归实现排列型枚举） - 洛谷](https://www.luogu.com.cn/problem/B3623)

```c++
#include<bits/stdc++.h>
using namespace std;
int n,k;
bool st[20];
int arr[20];
void dfs(int x)
{
	if(x>k)
	{
		for(int i=1;i<=k;i++)
		{
			cout<<arr[i]<<" ";
		}
		cout<<endl;
		return ;
	}
	for(int i=1;i<=n;i++)
	{
		if(!st[i])
		{
			st[i] = true;
			arr[x] = i;
			dfs(x+1);
			st[i] = false;
			arr[x] = 0;
			
		 } 
	}
}
int main()
{
	cin>>n>>k;
	dfs(1);
	return 0;
}
```

3.[P1157 组合的输出 - 洛谷](https://www.luogu.com.cn/problem/P1157#submit)

```c++
#include<bits/stdc++.h>
using namespace std;
int n,m; 
int arr[30];
void dfs(int x,int start)		//x代表当前位置,start代表当前选的数 
{
    if(n - start + 1 < m - x + 1) return;
	if(x>m)
	{
		for(int i=1;i<=m;i++)
		{
			cout<<arr[i]<<" ";
		}
		cout<<endl;
		return; 
	}
	for(int i=start;i<=n;i++)
	{
		arr[x] = i;
		dfs(x+1,i+1);
		arr[x] = 0;
	}
	
}
int main()
{
	cin>>n>>m;
	dfs(1,1); 
	return 0;
}
```



## 第一题：

### 题目链接：

[P1036 [NOIP 2002 普及组\] 选数 - 洛谷](https://www.luogu.com.cn/problem/P1036)

### 题目描述

已知 *n* 个整数 *x*1,*x*2,⋯,*x**n*，以及 1 个整数 *k*（*k*<*n*）。从 *n* 个整数中任选 *k* 个整数相加，可分别得到一系列的和。例如当 *n*=4，*k*=3，4 个整数分别为 3,7,12,19 时，可得全部的组合与它们的和为：

3+7+12=22

3+7+19=29

7+12+19=38

3+12+19=34

现在，要求你计算出和为素数共有多少种。

例如上例，只有一种的和为素数：3+7+19=29。

### 输入格式

第一行两个空格隔开的整数 *n*,*k*（1≤*n*≤20，*k*<*n*）。

第二行 *n* 个整数，分别为 *x*1,*x*2,⋯,*x**n*（1≤*x**i*≤5×106）。

### 输出格式

输出一个整数，表示种类数。

### 输入输出样例

**输入 #1**复制

```
4 3
3 7 12 19
```

**输出 #1**复制

```
1
```

### 说明/提示

**【题目来源】**

NOIP 2002 普及组第二题

### 代码详情：

```c++
#include<bits/stdc++.h>
using namespace std;
int n,k;
int s[30];
int arr[30];
int res; 
bool is_per(int a)
{
	for(int i=2;i*i<=a;i++)
	{
		if(a%i==0)
		{
			return false;
		}
	}
	return true;
}
void dfs(int x,int start) //x:表示选择到哪个位置  start:在当前递归层中，我们只能从数组 s 的第 start 个位置开始选择数字 
{
	if((x-1) + (n-start+1)<k) //已经选的个数（x-1） + 剩余的个数（n-start+1)   剪枝 
	{
		return; 
	} 
	if(x>k)
	{
		int sum=0;
		for(int i=1;i<=k;i++)
		{
			sum+=arr[i];
		}	
		if(is_per(sum))
		{
			res++;
		}
		return; 
	}
	for(int i=start;i<=n;i++)
	{
		arr[x] = s[i];   // 选第i个数作为第x个选中的数
        dfs(x+1,i+1);    // 下一层递归：从i+1开始选（不能回头选）
        arr[x] = 0;      // 回溯：恢复状态
	}
}
int main()
{
	cin>>n>>k;
	for(int i=1;i<=n;i++)
	{
		cin>>s[i];
	}
	dfs(1,1);
	cout<<res;
	return 0;
}
```

## 第二题：

### 题目链接：

[P2089 烤鸡 - 洛谷](https://www.luogu.com.cn/problem/P2089)

### 题目背景

猪猪 Hanke 得到了一只鸡。

### 题目描述

猪猪 Hanke 特别喜欢吃烤鸡（本是同畜牲，相煎何太急！）Hanke 吃鸡很特别，为什么特别呢？因为他有 10 种配料（芥末、孜然等），每种配料可以放 1 到 3 克，任意烤鸡的美味程度为所有配料质量之和。

现在， Hanke 想要知道，如果给你一个美味程度 *n* ，请输出这 10 种配料的所有搭配方案。

### 输入格式

一个正整数 *n*，表示美味程度。

### 输出格式

第一行，方案总数。

第二行至结束，10 个数，表示每种配料所放的质量，按字典序排列。

如果没有符合要求的方法，就只要在第一行输出一个 0。

### 输入输出样例

**输入 #1**复制

```
11
```

**输出 #1**复制

```
10
1 1 1 1 1 1 1 1 1 2 
1 1 1 1 1 1 1 1 2 1 
1 1 1 1 1 1 1 2 1 1 
1 1 1 1 1 1 2 1 1 1 
1 1 1 1 1 2 1 1 1 1 
1 1 1 1 2 1 1 1 1 1 
1 1 1 2 1 1 1 1 1 1 
1 1 2 1 1 1 1 1 1 1 
1 2 1 1 1 1 1 1 1 1 
2 1 1 1 1 1 1 1 1 1 
```

### 说明/提示

对于 100% 的数据，*n*≤10000。

```c++
#include<bits/stdc++.h>
using namespace std;
int n; 
int arr[30];//方案的数组 
int mem[100000][30];  // 由题意要求，先输出方案数 
int res; //方案数 
//可以看出这题是一个指数型枚举，美味程度的范围为10~30，可以用来剪枝 
void dfs(int x,int sum) 
{
	//if(sum>30) return;   // 剪枝：总重量超过30（10份最多3*10=30），直接返回
	// 优化剪枝：
    // 1. 总重量超过30（上限），直接返回
    // 2. 剩余材料都取1克，总重量仍超过n，返回
    // 3. 剩余材料都取3克，总重量仍不够n，返回
    if(sum > 30 || sum + (10 - x + 1)*1 > n || sum + (10 - x + 1)*3 < n) 
	{
		return; 
	}  
	if(x>10)		// 递归终止：已经枚举完10份材料
	{
		if(sum==n)
		{
			res++;
			// 把当前方案存入mem数组
			for(int i=1;i<=10;i++)
			{
				mem[res][i] = arr[i];
			}
		}
		return ;
	}
	for(int i=1;i<=3;i++)  //一至三克分别枚举 
	{
		arr[x] = i;          // 第x份材料取i克
        dfs(x+1,sum+i);      // 递归处理第x+1份，总重量累加i
        arr[x] = 0;          // 回溯：恢复状态（不影响下一轮枚举）
	}
}
int main()
{
	cin>>n;
	dfs(1,0);
	cout<<res<<endl;
	for(int i=1;i<=res;i++)
	{
		for(int j=1;j<=10;j++)
		{
			cout<<mem[i][j]<<" ";
		}
		cout<<endl;
	}
	return 0;
}
```

## 第三题：

### 题目链接：

[P1088 [NOIP 2004 普及组\] 火星人 - 洛谷](https://www.luogu.com.cn/problem/P1088)

### 题目描述

人类终于登上了火星的土地并且见到了神秘的火星人。人类和火星人都无法理解对方的语言，但是我们的科学家发明了一种用数字交流的方法。这种交流方法是这样的，首先，火星人把一个非常大的数字告诉人类科学家，科学家破解这个数字的含义后，再把一个很小的数字加到这个大数上面，把结果告诉火星人，作为人类的回答。

火星人用一种非常简单的方式来表示数字――掰手指。火星人只有一只手，但这只手上有成千上万的手指，这些手指排成一列，分别编号为 1,2,3,⋯。火星人的任意两根手指都能随意交换位置，他们就是通过这方法计数的。

一个火星人用一个人类的手演示了如何用手指计数。如果把五根手指――拇指、食指、中指、无名指和小指分别编号为 1,2,3,4 和 5，当它们按正常顺序排列时，形成了 5 位数 12345，当你交换无名指和小指的位置时，会形成 5 位数 12354，当你把五个手指的顺序完全颠倒时，会形成 54321，在所有能够形成的 120 个 5 位数中，12345 最小，它表示 1；12354 第二小，它表示 2；54321 最大，它表示 120。下表展示了只有 3 根手指时能够形成的 6 个 3 位数和它们代表的数字：

| 三位数 | 代表的数字 |
| :----: | :--------: |
|  123   |     1      |
|  132   |     2      |
|  213   |     3      |
|  231   |     4      |
|  312   |     5      |
|  321   |     6      |

现在你有幸成为了第一个和火星人交流的地球人。一个火星人会让你看他的手指，科学家会告诉你要加上去的很小的数。你的任务是，把火星人用手指表示的数与科学家告诉你的数相加，并根据相加的结果改变火星人手指的排列顺序。输入数据保证这个结果不会超出火星人手指能表示的范围。

### 输入格式

共三行。
第一行一个正整数 *N*，表示火星人手指的数目（1≤*N*≤10000）。
第二行是一个正整数 *M*，表示要加上去的小整数（1≤*M*≤100）。
下一行是 1 到 *N* 这 *N* 个整数的一个排列，用空格隔开，表示火星人手指的排列顺序。

### 输出格式

*N* 个整数，表示改变后的火星人手指的排列顺序。每两个相邻的数中间用一个空格分开，不能有多余的空格。

### 输入输出样例

**输入 #1**复制

```
5
3
1 2 3 4 5
```

**输出 #1**复制

```
1 2 4 5 3
```

### 说明/提示

对于 30% 的数据，*N*≤15。

对于 60% 的数据，*N*≤50。

对于 100% 的数据，*N*≤10000。

noip2004 普及组第 4 题

## 第四题：

[P3956 [NOIP 2017 普及组\] 棋盘 - 洛谷](https://www.luogu.com.cn/problem/P3956)

可以用爆搜和剪枝，

也可以用dijkstra。

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 103;
int dist[N][N];
int st[N][N];
int m,n;
int dx[] = {-1,0,1,0};
int dy[] = {0,1,0,-1};
int min_ans = 1e9;
void dfs(int x,int y,int sum_cost,bool mf)		//当前坐标，当前花费，当前魔法是否使用 
{
	
	if(sum_cost>=min_ans) return;
	if(sum_cost>=dist[x][y]) return; 
	dist[x][y] = sum_cost; 
	if(x == m && y == m)
	{
		min_ans = min(min_ans,sum_cost);
		return;
	} 
	for(int i=0;i<4;i++)
	{
		int a = x + dx[i];
		int b = y + dy[i];
		if(a<0||a>m||b<0||b>m) continue;
		//情况1：如果有颜色
		if(st[a][b] != -1)
		{
			if(st[x][y] == st[a][b])
			{
				dfs(a,b,sum_cost,false);
			}
			else
			{
				dfs(a,b,sum_cost+1,false);
			}
		} 
		else if(st[a][b] == -1 && !mf)		//无颜色且可用魔法 
		{
			st[a][b] = st[x][y];
			dfs(a,b,sum_cost+2,true);
			st[a][b] = -1;
		}  	
	}
	
}
int main()
{
	memset(st,-1,sizeof(st));
	memset(dist,0x3f,sizeof(dist));
	cin>>m>>n;
	while(n--)
	{
		int x,y,c;
		cin>>x>>y>>c;
		st[x][y] = c;
	}
	dist[0][0] = 0;
	dfs(1,1,0,false);
	cout<<(min_ans == 1e9 ? -1 : min_ans)<<endl;
	return 0;
}
```

