# CCF前三题训练

## 202303

### 第1题

题目：http://118.190.20.162/view.page?gpid=T165

**第一次提交**

分数：100

思路：

1. 先找到有交集的时候，x1x2y1y2满足什么条件。这个好解决：想想无交集的样子，反着来就行
2. 然后算每个交集的面积，加起来就是答案
   * 刚开始是想穷举每种相交的方式，然后给出对应的求面积公式。但在打草稿的时候发现，相交方式有太多种了，不仅条件判断会特别多和细，而且还不能保证覆盖了所有情况，可能会遗漏
   * 之后再想，其实面积=长*宽，与其穷举面积公式，不如穷举长度和宽度来的方便，这样不用去想相交是啥样子，只要能求出长和宽自然就能求面积。求长宽很简单，就是看这个线段和这个田地的位置关系。

代码

```c++
#include<iostream>
using namespace std;
#define ll long long
ll x1[100];
ll y_1[100];
ll x2[100];
ll y2[100];
void solve(ll n,ll a,ll b) {
	ll sum = 0;
	ll length = 0;
	ll width = 0;
	for (int i = 0; i < n; i++) {
		if (x1[i] < a && x2[i]>0 && y_1[i] < b && y2[i]>0) {
			if (x1[i] >= 0 && x2[i] <= a)length = x2[i] - x1[i];
			if (x1[i] <= 0 && x2[i] <= a)length = x2[i];
			if (x1[i] >= 0 && x2[i] >= a)length = a - x1[i];
			if (x1[i] <= 0 && x2[i] >= a)length = a;
			if (y_1[i] >= 0 && y2[i] >= b)width = b - y_1[i];
			if (y_1[i] <= 0 && y2[i] <= b)width = y2[i];
			if (y_1[i] >= 0 && y2[i] <= b)width = y2[i] - y_1[i];
			if (y_1[i] <= 0 && y2[i] >= b)width = b;
			sum += length * width;
		}
	}
	cout << sum;
}
int main() {
	ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL);
	ll n, a, b;
	cin >> n >> a >> b;
	for (int i = 0; i < n; i++) {
		cin >> x1[i] >> y_1[i] >> x2[i] >> y2[i];
	}
	solve(n, a, b);
	return 0;
}
```

### 第2题

题目：http://118.190.20.162/view.page?gpid=T164

**第一次提交**

得分：70，运行超时

思路：

最短耗时取决于耗时最长的那块田。那就每次分配都把资源给到耗时最长的田（如果资源够的话），分配完后再判断现在耗时最长的是哪块田。

需要找到耗时最长的田t[f]和耗时第二长的田t[s]，在每次分配资源后

* 如果t[f]还是耗时最长，那就继续给它分配
* 如果t[s]>t[f]，那t[s]就成为新的t[f],然后再去找到一个新的t[s]

从上述分析，可以用递归来进行，终结条件：剩余资源无法再分给t[f]的时候

并且要注意，更新的时候也要和最小天数k去作比较

代码

```c++
#include<iostream>
using namespace std;
#define ll long long
ll t[100001];
ll c[100001];
int findSecond(int f,int n) {
	int temp = -1;
	int sIndex = -1;
	for (int i = 0; i < n; i++) {
		if (i == f)continue;
		if (t[i] > temp) {
			temp = t[i];
			sIndex = i;
		}
	}
	return sIndex;
}
int solve(int n, ll m,int k, int f, int s) {
	//终结条件：无法分配资源给t[f]
	if (m < c[f])return t[f];
	//只要t[f]还是耗时最长的田，并且有资源能给它，那就给它资源（要注意更新后不能小于k）
	while (m>=c[f] && t[f] - 1 >= t[s] - 1 && t[f]-1 >= k) {
		t[f]--;
		m -= c[f];
	}
	//这两种情况就可以直接返回了
	if (t[f] >= t[s]|| t[f] == k)return t[f];
	//更新f,s,继续递归地解决
	else {
		f = s;
		s = findSecond(f, n);
		solve(n, m,k, f, s);
	}
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n,k;
	ll m;
	cin >> n >> m >> k;
	for (int i = 0; i < n; i++) {
		cin >> t[i] >> c[i];
	}
	int f = findSecond(100000, n);//找到耗时最长的田
	int s = findSecond(f, n);//找到耗时第二长的田
	int rel = solve(n, m,k, f, s);
	cout << rel;
	return 0;
}
```

**第二次提交**

## 202212

### 第1题

题目：http://118.190.20.162/view.page?gpid=T160

没啥好说的，看懂题，直接做就行

代码

```c++
#include<iostream>
using namespace std;
double s[51];
void solve(int n, double i) {
	double sum = 0;
	double t = 1 + i;
	for (int k = 1; k <= n; k++) {
		sum += s[k] / t;
		t = t * (1 + i);
	}
	printf("%f", sum+s[0]);
	return;
}
int main() {
	int n;
	double i;
	scanf_s("%d", &n);
	scanf_s("%lf", &i);
	for (int k = 0; k <= n; k++) {
		scanf_s("%lf", &s[k]);
	}
	solve(n, i);
	return 0;
}
```

### 第2题

题目：http://118.190.20.162/view.page?gpid=T159

**第一次提交**

分数：100

思路

把这些科目想象成有前后项。后项指向前项，表示后项开始于前项结束之后。

① 最早开始时间

这个很简单，紧接着前项结束后开始即可：s1[i] = s1[p[i]] + t[p[i]]。用s1[0]=1和t[0]=0就可以将无前项的科目也包含在式子中

② 最晚开始时间

一开始想得很复杂，总是难以去划分，直到再审一次题才发现一个关键信息：**依赖科目的编号小于自己**，这样的话问题就简单很多。应该从编号最大的科目开始分配开始时间，这样从大到小逆序回去，考虑当前科目的时候，它的最晚截止时间已经被确定（因为所有必须发生在它之后的科目的编号都比它大，而这些大编号的科目的开始时间已经在之前的循环里被定下来了），就很好讨论了。

具体的操作是：
* 如果科目i没有被依赖，那就按期限n来计算最晚开始时间
* 如果科目i被依赖，则找出所有依赖项中最早开始的那个，以它为期限计算最晚开始时间

代码

```c++
#include<iostream>
#include <vector>
using namespace std;
int p[101];//p[i]:科目i依赖的科目
int t[101];//t[i]:科目i所需的时间
int s1[101];//s1[i]:科目i最早开始时间
int s2[101];//s2[i]:科目i最晚开始时间
//将依赖于科目i的科目下标封装为数组返回
vector<int> hasPost(int m, int i) {
	vector<int> arr;
	for (int k = 1; k <= m; k++) {
		if (k == i)continue;
		if (p[k] == i)arr.push_back(k);
	}
	return arr;
}
void solve(int n, int m) {
	int flag = 0;//判断是否能按时完成训练
	//计算最早开始时间
	for (int i = 1; i <= m; i++) {
		s1[i] = s1[p[i]] + t[p[i]];
		cout << s1[i] << " ";
		if (s1[i] + t[i] -1 > n)flag = 1;
	}
	if (flag == 1)return;//不能按时完成训练，直接返回
	//可以按时返回，计算最晚开始时间
	else {
		for (int i = m; i >= 1; i--) {//注意是倒序
			vector<int> arr = hasPost(m, i);
			//如果科目i没有被依赖，那就按期限n来计算最晚开始时间
			if (arr.size()==0)s2[i] = n - t[i] + 1;
			//如果科目i被依赖，则找出所有依赖项中最早开始的那个，以它为期限计算最晚开始时间
			else {
				s2[i] = 366;
				for (int k = 0; k < arr.size(); k++) {
					int temp = s2[arr[k]] - t[i];
					s2[i] = s2[i] > temp ? temp : s2[i];
				}
			}
		}
		cout << "\n";
		for (int i = 1; i <= m; i++) cout << s2[i] << " ";
	}
}
int main() {
	ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL);
	int n, m;
	cin >> n >> m;
	for (int i = 1; i <= m; i++)
		cin >> p[i];
	for (int i = 1; i <= m; i++) 
		cin >> t[i];
	s1[0] = s2[0] = 1;//边界初始化：最早从第一天开始
	solve(n, m);
	return 0;
}
```

