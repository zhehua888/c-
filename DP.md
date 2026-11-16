#                                                 DP

## 1.规则：

写任何 DP（动态规划）代码，都必须遵守一条绝对的铁律：
        **等号左边 `dp[...]` 是“未知”的空白位置，等号右边的 `dp[...]` 必须是“已经算出来”的已知数值**

**不管正序倒序，核心永远是拿已知的推未知的；之所以正序找前边，就是因为前边已知**

## 2.思路：

### DP 的通用核心四步法（实战心法）

1. **下定义（状态表示）**：明确 `dp` 数组（几维？）每个格子到底代表什么。
   - **常见思维误区**：定义太宏观（如“前 `i` 个元素最优解”）。
   - **破局套路**：强行加上限制条件。比如**“必须以此元素结尾”**、或者在二维里加状态**“第 `i` 轮时，手里提着重量为 `j` 的物品时的最大价值”**。定义越具体，越好转移。
2. **找关系（状态转移方程）**：寻找“当前步”是怎么从“之前某一步”变过来的。
   - **思考角度**：把自己当成第 `i` 个阶段的决策者。
   - **万能句式**：“我现在的最优结果，要么是**我什么都不做**（继承上一次的结果 `dp[i-1]`），要么是**我做出了某种选择**（前面某个状态 `dp[j]` 加上了我现在的收益）。”比较这两者，要 `max` 还是 `min`。
3. **定边界（Base Case）**：填好大楼的地基。
   - 必须非常仔细地思考：在一切选择还没开始之前（比如长度为 0，重量为 0 第一个人），状态该是多少？
   - 求最大值，其余位置通常初始化为 0。求最小值，其余位置往往需要初始化为正无穷大（如 `INF`）。
4. **找答案（最终输出）**：
   - 答案是最后一个格子 `dp[n]`（如走到最后一个台阶）？
   - 还是数组里的最大值 `max(dp[...])`（如前面的合唱队形求最高峰）？

# 题目：

## 第一题：

[P1216 [IOI 1994 / USACO1.5\] 数字三角形 Number Triangles - 洛谷](https://www.luogu.com.cn/problem/P1216)

dp学习思路：

dfs暴力-->记忆化搜索-->逆序递推-->正序递推-->状态压缩dp

### 暴力dfs：

```c++
#include<bits/stdc++.h>
using namespace std;
int a[1005][1005];
int n;
int dfs(int x,int y)
{
	if(x>n||y>x) return 0; // 越界问题
	return max(dfs(x+1,y),dfs(x+1,y+1)) + a[x][y]; 		//最常规的思路，从上往下走，分头行动，不是贪心，贪心是局部最优达到全局最优，这个显然不能
}
int main()
{
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			cin>>a[i][j];
		}
	}
	int res = dfs(1,1);
	cout<<res;
	return 0;
} 
```

### 暴力会超时，用记忆化来剪枝：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int a[N][N];
int n;
int mem[N][N]; 
//正常来说记忆化数组mem初始化为零，但在这个题要初始化为-1
//不要把合法结果值当成“未计算”的标记。用不可达的哨兵或单独的已计算标志
//不然会重复递归
int dfs(int x,int y)
{
	int sum = 0; 
	if(mem[x][y]>=0) return mem[x][y];
	if(x>n||y>x) return mem[x][y] = 0;
	return mem[x][y] = max(dfs(x+1,y),dfs(x+1,y+1)) + a[x][y];
}
int main()
{
	cin>>n;
	memset(mem,-1,sizeof(mem)); 
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			cin>>a[i][j];
		}
	}
	int res = dfs(1,1);
	cout<<res;
	return 0;
} 
```

### 逆序递推：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int a[N][N];
int n;
int dp[N][N]; 
//int dfs(int x,int y)
//{
//	int sum = 0; 
//	if(mem[x][y]>=0) return mem[x][y];
//	if(x>n||y>x) return mem[x][y] = 0;
//	return mem[x][y] = max(dfs(x+1,y),dfs(x+1,y+1)) + a[x][y];
//}
//由上边的dfs可推出dp
//都知道递归递归，归是结果诞生的行为，是从下到上的，所以在用递推的时候，也是从下到上的 
int main()
{
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			cin>>a[i][j];
		}
	}
	for(int i = 1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			dp[i][j] = max(dp[i+1][j],dp[i+1][j+1]) + a[i][j];
		}
	}
	cout<<dp[1][1];
	return 0;
} 
```



### 正序递推：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int a[N][N];
int n;
int dp[N][N]; 
//int dfs(int x,int y)
//{
//	int sum = 0; 
//	if(mem[x][y]>=0) return mem[x][y];
//	if(x>n||y>x) return mem[x][y] = 0;
//	return mem[x][y] = max(dfs(x+1,y),dfs(x+1,y+1)) + a[x][y];
//}
//由上边的dfs可推出dp
//都知道递归递归，归是结果诞生的行为，是从下到上的，所以在用递推的时候，也是从下到上的 
int main()
{
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			cin>>a[i][j];
		}
	}
    //正序递推
    //这层的结果是由上一层得来的，为什么是max(dp[i-1][j],dp[i-1][j-1])？
    //因为题目要求当前的点可以走正下方和右下方
    //当正序遍历时，这层的点只与左上和正上的点有关系
	for(int i = 1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			dp[i][j] = max(dp[i-1][j],dp[i-1][j-1]) + a[i][j];
		}
	}
    //结果需要遍历，是因为从上到下，结果全集中到下边的点上，必须进行遍历
	int res = 0;
	for(int j = 1;j<=n;j++)
	{
		res = max(dp[n][j],res);
	}
	cout<<res;
	return 0;
} 
```

### 滚动数组：

![image-20260510165921483](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20260510165921483.png)

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int a[N][N];
int n;
int f[N];
int main()
{
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			cin>>a[i][j];
		}
	}
    //逆序
	for(int i=n;i>=1;i--)
	{
		for(int j=1;j<=i;j++)
		{
			f[j] = max(f[j],f[j+1]) + a[i][j];
		}
	} 
	cout<<f[1];
	return 0;
} 
```

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int a[N][N];
int n;
int f[N];
int main()
{
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=i;j++)
		{
			cin>>a[i][j];
		}
	}
	for(int i=1;i<=n;i++)
	{
		for(int j=i;j>=1;j--)
		{
			f[j] = max(f[j],f[j-1]) + a[i][j];
		}
	}
	int res = 0;
	for(int j=1;j<=n;j++)
	{
		res = max(res,f[j]);
	} 
	cout<<res;
	return 0;
} 
```

## 第二题：

[2. 01背包问题 - AcWing题库](https://www.acwing.com/problem/content/2/)

### 暴力dfs：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int v[N],w[N];
int n,cv;
int dfs(int x,int spV)
{
	if(x>n) return 0;
	//空间不足，不能选 
	if(spV<v[x]) return dfs(x+1,spV);
	//空间足够，分两条路走，看那条路价值最大 
	else if(spV>=v[x])
	{
		return max(dfs(x+1,spV),dfs(x+1,spV-v[x]) + w[x]);
	}
}
int main()
{
	cin>>n>>cv;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>w[i];
	}
	int res = dfs(1,cv);
	cout<<res;
	return 0;
} 
```

### 记忆化剪枝：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int v[N],w[N];
int n,cv;
int mem[N][N]; 
int dfs(int x,int spV)
{
	if(x>n) return 0;
	if(mem[x][spV] != -1) return mem[x][spV]; 
	//空间不足，不能选 
	if(spV<v[x]) return mem[x][spV] = dfs(x+1,spV);
	//空间足够，分两条路走，看那条路价值最大 
	else if(spV>=v[x])
	{
		return mem[x][spV] = max(dfs(x+1,spV),dfs(x+1,spV-v[x]) + w[x]);
	}
}
int main()
{
	memset(mem,-1,sizeof(mem));
	cin>>n>>cv;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>w[i];
	}
	int res = dfs(1,cv);
	cout<<res;
	return 0;
} 
```

### 逆向递推：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int v[N], w[N];  // v数组存体积(volume)，w数组存价值(weight)
int n, cv;       // n个物品，cv是背包总容量(capacity volume)

//正序 dp[i][j] 的核心含义（极其重要）：
// 代表面对“第 i 个到第 n 个”物品时，给我的背包容量为 j，我能装出的最大价值。
// 注意这是逆序DP，所以 i 越小，管辖的物品范围越大，dp[1][cv]就是统管全局的终极答案。
int dp[N][N]; 

int main()
{
    cin >> n >> cv;
    for(int i = 1; i <= n; i++)
    {
        cin >> v[i] >> w[i];
    }
    
    // 【外层循环：物品倒着来】
    // 为什么从 n 开始倒着推？因为根据我们的定义，第 i 层的答案，必须去问第 i+1 层要。
    // 所以我们只能从底层的 n 出发，提前把下面层的答案铺好（此时系统默认 dp[n+1][j] 全部为 0，相当于 dfs 的出界底层）。
    for(int i = n; i >= 1; i--)
    {
        // 【内层循环：枚举当前可能剩下来的各种空间 j】
        // j 代表当前我手里还剩下多少背包空间
        for(int j = 0; j <= cv; j++)
        {
            // 场景 1：空间不足（剩下的空间 j 连当前这 1 个物品都塞不下）
            if(j < v[i]) 
            {
                // 对应 DFS 中的：return dfs(x+1, spV);
                // 既然装不下第 i 个，那这层我的最大收益，就直接等于“面对下一层(i+1)同样大空间(j)时”的最大收益。
                dp[i][j] = dp[i+1][j];
            } 
            // 场景 2：空间充足（能塞下当前这个物品）
            else if(j >= v[i]) // 其实直接写 else 也可以
            {
                // 对应 DFS 中的：return max(dfs(x+1, spV), dfs(x+1, spV-v[x]) + w[x]);
                // 开启平行宇宙大比拼：
                // 1. 不拿！收益等于“从下一层(i+1)带同样空间(j)出发的收益” -> dp[i+1][j]
                // 2. 拿！收益等于“当前物品价值 w[i]” + “从下一层(i+1)带剩下的空间(j-v[i])出发的收益” -> dp[i+1][j-v[i]] + w[i]
                // 取两者的最大值，落子无悔，记录在案！
                dp[i][j] = max(dp[i+1][j], dp[i+1][j-v[i]] + w[i]);
            }
        }
    } 
    
    // 最后输出山顶的答案：面对第 1 到第 n 个所有物品，带着初始的满血容量 cv，算出的终极最大价值
    cout << dp[1][cv];
    
    return 0;
} 
```

### 正序递推：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int v[N],w[N];
int n,cv;
int dp[N][N];
//dfs的暴力和逆序递推一个思路
int main()
{
	cin>>n>>cv;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>w[i];
	}
	for(int i = 1;i <= n;i++)
	{
		for(int j = 0;j <= cv;j++)
		{
			if(j<v[i]) 
			{
				dp[i][j] = dp[i-1][j];
			} 
			else if(j>=v[i])
			{
				dp[i][j] = max(dp[i-1][j],dp[i-1][j-v[i]] + w[i]);
			}
		}
	} 
	int res = 0;
	for(int j=0;j<=cv;j++)
	{
		res = max(res,dp[n][j]);
	}
	cout<<res;
	return 0;
} 
```

### 滚动数组逆序：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int v[N],w[N];
int n,cv;
int f[N]; 
//dfs的暴力和逆序递推一个思路
int main()
{
	cin>>n>>cv;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>w[i];
	}
	for(int i=n;i>=1;i--)
	{
		for(int j=cv;j>=0;j--)
		{
			if(j>=v[i])
			{
				f[j] = max(f[j],f[j-v[i]] + w[i]);
			}
		}
	} 
	cout<<f[cv];
	return 0;
} 
```

### 正序：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1005;
int v[N],w[N];
int n,cv;
int f[N]; 
//dfs的暴力和逆序递推一个思路
int main()
{
	cin>>n>>cv;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>w[i];
	}
	for(int i=1;i<=n;i++)
	{
		for(int j=cv;j>=0;j--) 
		{
			if(j>=v[i])
			{
				f[j] = max(f[j],f[j-v[i]] + w[i]);	
			}
		}
	}
	cout<<f[cv];
	return 0;
} 
```

### 解释：

这两者内层循环都是从大到小，是因为**只要你的状态转移是往左侧要数据（`j - v[i]`），那不管你是从顶到底推，还是从底到顶推，你一维数组滚动的方向都必须是“从右往左”！**

## 第三题：

[8. 二维费用的背包问题 - AcWing题库](https://www.acwing.com/problem/content/8/)

跟01背包相似，只是多了一个限制条件，那么再加一个维度数组即可

### 暴力dfs：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int v[N],m[N],w[N];
int n,V,M;
int dfs(int x,int spV,int spM)
{
	if(x>n) return 0;
	if(spV<v[x] || spM < m[x]) return dfs(x+1,spV,spM);
	else
	{
		return max(dfs(x+1,spV,spM),dfs(x+1,spV-v[x],spM-m[x]) + w[x]);
	}
}
int main()
{
	cin>>n>>V>>M;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>m[i]>>w[i];
	}
	int res = dfs(1,V,M);
	cout<<res;
	return 0;
}
```

### 记忆化：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int v[N],m[N],w[N];
int n,V,M;
int mem[N][110][110]; 
int dfs(int x,int spV,int spM)
{
	if(x>n) return 0;
	if(mem[x][spV][spM] != -1) return mem[x][spV][spM];
	if(spV<v[x] || spM < m[x]) return mem[x][spV][spM] =  dfs(x+1,spV,spM);
	else
	{
		return mem[x][spV][spM] = max(dfs(x+1,spV,spM),dfs(x+1,spV-v[x],spM-m[x]) + w[x]);
	}
}
int main()
{
	memset(mem,-1,sizeof(mem));
	cin>>n>>V>>M;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>m[i]>>w[i];
	}
	int res = dfs(1,V,M);
	cout<<res;
	return 0;
}
```

### 正序递推：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int v[N],m[N],w[N];
int n,V,M;
int dp[N][110][110]; 

int main()
{
	cin>>n>>V>>M;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>m[i]>>w[i];
	}
	for(int i=1;i<=n;i++)
	{
		for(int j = 0;j<=V;j++)
		{
			for(int k=0;k<=M;k++)
			{
				if(j < v[i] || k < m[i]) 
				{
					dp[i][j][k] = dp[i-1][j][k];
				}
				else
				{
					dp[i][j][k] = max(dp[i-1][j][k],dp[i-1][j-v[i]][k-m[i]] + w[i]);
				}
			}
		}
	}
	cout<<dp[n][V][M];
	return 0;
}
```

### 滚动数组：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int v[N],m[N],w[N];
int n,V,M;
int f[110][110]; 
int main()
{
	cin>>n>>V>>M;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>m[i]>>w[i];
	}
	for(int i=1;i<=n;i++)
	{
		for(int j = V;j>=v[i];j--)
		{
			for(int k = M;k>=m[i];k--)
			{
				f[j][k] = max(f[j][k],f[j-v[i]][k-m[i]] + w[i]);
			}
		}
	}
	cout<<f[V][M];
	return 0;
}
```

## 第四题：

[3. 完全背包问题 - AcWing题库](https://www.acwing.com/problem/content/3/)

完全背包跟01背包基本上没差距，01背包是只能选一次，而完全背包是可以选多次，区别只有一个

### 暴力dfs：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int n,V;
int v[N],w[N];
int dfs(int x,int spV)
{
if(x>n) return 0;
if(spV<v[x]) return dfs(x+1,spV);
else
{
return max(dfs(x+1,spV),dfs(x,spV-v[x]) + w[x]);
}
}
int main()
{
cin>>n>>V;
for(int i=1;i<=n;i++)
{
cin>>v[i]>>w[i];
}
int res = dfs(1,V);
cout<<res;
return 0;
}
```

### 记忆化：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int n,V;
int v[N],w[N];
int mem[N][N];
int dfs(int x,int spV)
{
if(x>n) return 0;
if(mem[x][spV] != -1) return mem[x][spV];
if(spV<v[x]) return mem[x][spV] = dfs(x+1,spV);
else
{
return mem[x][spV] = max(dfs(x+1,spV),dfs(x,spV-v[x]) + w[x]);
}
}
int main()
{
memset(mem,-1,sizeof(mem));
cin>>n>>V;
for(int i=1;i<=n;i++)
{
cin>>v[i]>>w[i];
}
int res = dfs(1,V);
cout<<res;
return 0;
}
```

### 正序递推：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int n,V;
int v[N],w[N];
int dp[N][N]; 
int main()
{
	cin>>n>>V;
	for(int i=1;i<=n;i++)
	{
		cin>>v[i]>>w[i];
	}
	for(int i=1;i<=n;i++)
	{
		for(int j = 0;j<=V;j++)
		{
			if(j<v[i]) dp[i][j] = dp[i-1][j];
			else
			{
				dp[i][j] = max(dp[i-1][j],dp[i][j-v[i]] + w[i]);
			} 
		}
	}
	cout<<dp[n][V];
	return 0;
} 
```

### 滚动数组：

```c++
#include<bits/stdc++.h>
using namespace std;
const int N = 1010;
int n,V;
int v[N],w[N];
int f[N];
int main()
{
    cin>>n>>V;
    for(int i=1;i<=n;i++)
    {
    	cin>>v[i]>>w[i];
    }
    for(int i=1;i<=n;i++)
    {
    	for(int j=v[i];j<=V;j++)
    	{
    		f[j] = max(f[j],f[j-v[i]] + w[i]);
   	 	}
    }
    cout<<f[V];
	return 0;
}
```

## 第五题：

[LCR 088. 使用最小花费爬楼梯 - 力扣（LeetCode）](https://leetcode.cn/problems/GzCJIP/)

这种dp题的思路：

![image-20260512223308847](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20260512223308847.png)

### 记忆化：

```c++
const int N = 1010; 

class Solution {
public:
	int mem[N];
	int dfs(vector<int>& cost,int x)
	{
		if(x==0 || x==1) return 0;
		if(mem[x] != -1) return mem[x];
		else
		{
			return mem[x] = min(dfs(cost,x-1) + cost[x-1],dfs(cost,x-2) + cost[x-2]);
		}
	}
    int minCostClimbingStairs(vector<int>& cost) 
	{
		memset(mem,-1,sizeof(mem));
		int res = 0;
		res = dfs(cost,cost.size());
		return res; 
    }
};
```

### 正序递推：

```c++
const int N = 1010; 

class Solution {
public:
	int dp[N]; 		//dp[i]含义： 从起点跑到第 i 层，已经花掉的最小费用。
    int minCostClimbingStairs(vector<int>& cost) 
	{
		int n = cost.size();
		for(int i=2;i<=n;i++)
		{
			dp[i] = min(dp[i-1] + cost[i-1],dp[i-2] + cost[i-2]);
		}
		return dp[n];
    }
};dp的含义是代表n层台阶的最小花费，这个是正序递推，按照递归搜索树，正序是从0到n
```

### 逆序递推：

```c++
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int n = cost.size();
        vector<int> dp(n + 2, 0); 
        
        // 逆序推导：从最顶端楼梯往下退
        for(int i = n - 1; i >= 0; i--) {
            // 向前（上）看已知数据 i+1 和 i+2
            dp[i] = cost[i] + min(dp[i+1], dp[i+2]);
        }
        
        // 答案就是比较从哪格起步更便宜
        return min(dp[0], dp[1]);
    }
};
```

## 第六题：[P1091 [NOIP 2004 提高组\] 合唱队形 - 洛谷](https://www.luogu.com.cn/problem/P1091)

```c++
#include <iostream>
#include <vector>
using namespace std;

int main() {
    int n;
    cin >> n;
    vector<int> arr(n + 1);
    for (int i = 1; i <= n; i++) cin >> arr[i];

    // 初始化 DP 数组，每个人自己本身就是一个长度为 1 的子序列
    vector<int> dp1(n + 1, 1);
    vector<int> dp2(n + 1, 1);

    // 1. 从左往右求 LIS
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j < i; j++) {
            if (arr[j] < arr[i]) {
                dp1[i] = max(dp1[i], dp1[j] + 1);
            }
        }
    }

    // 2. 从右往左求 LIS
    for (int i = n; i >= 1; i--) {
        for (int j = n; j > i; j--) {
            if (arr[j] < arr[i]) {
                dp2[i] = max(dp2[i], dp2[j] + 1);
            }
        }
    }

    // 3. 寻找最优的“山峰”
    int max_len = 0;
    for (int i = 1; i <= n; i++) {
        max_len = max(max_len, dp1[i] + dp2[i] - 1);
    }

    // 4. 打印最少需要踢掉的人数
    cout << n - max_len << endl;

    return 0;
}
```

