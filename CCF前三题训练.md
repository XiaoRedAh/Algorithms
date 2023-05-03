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

得分：70，运行超时

思路

这次是想着用优先级队列，每次都拿出时间最长的来给资源，等到循环结束后，直接取优先级队列里的最长时间就行。

结果还是超时了。。。

代码

```c++
#include<iostream>
# include<queue>
using namespace std;
#define ll long long
struct s {
	int t;
	int c;
};
//自定义<规则
bool operator < (s a, s b) {
	return a.t < b.t;
}
priority_queue<s> que;
void solve(int n, ll m, int k) {
	while (m >= que.top().c) {
		int tempt = que.top().t - 1;
		int tempc = que.top().c;
		if (tempt < k)break;
		else {
			m -= tempc;
			que.pop();
			que.push({ tempt,tempc });
		}
	}
	cout << que.top().t;
	return;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n,k;
	ll m;
	int t, c;
	cin >> n >> m >> k;
	for (int i = 1; i <= n; i++) {
		cin >> t >> c;
		s a = { t,c };
		que.push(a);
	}
	solve(n, m, k);
	return 0;
}
```

**第三次提交**

### 第3题

题目：http://118.190.20.162/view.page?gpid=T163

得分：100

思路：写在代码上了，参考 https://www.cnblogs.com/AlexHoring/p/17246861.html

```c++
#include<iostream>
#include <vector>
#include <algorithm>//使用了其中的sort(),unique()
#include<string>//使用stio()函数将字符串转换为数字
using namespace std;
vector<int> d;//d[i]表示第i行的DN
//a[i][j]表示第i行的第j个属性，first是属性编号，second是属性值
vector <vector<pair<int, int>>> a;
//当前待分析的集合（比如说有{1，2}，就是分析DA=d[1],d[2]的用户）
vector<int> anlPtr;
string s;//输入的表达式
int ptr;//遍历当前表达式的指针

//获取当前表达式，当前指针元素开始的一个连续语义
string getStr() {
	string str = "";
	//如果当前指针指向数字，那就获取这个连续的数字返回
	if (isdigit(s[ptr])) {
		while (isdigit(s[ptr])) {
			str += s[ptr];
			ptr++;
		}
	}
	//如果当前指针指向的是符号，则直接返回符号
	else {
		str += s[ptr];
		ptr++;
	}
	return str;
}

/*对于当前的表达式，在传入的待分析用户中进行匹配分析，
返回满足表达式的用户在d[i]中对应的下标*/
vector<int> solve(vector<int> &anlPtr) {
	vector<int> rel;//返回结果
	string str = getStr();
	//如果获取到运算符
	if (!isdigit(str[0])) {
		string op = str;//保存获取到的运算符
		//求解运算符后的第一个原子表达式
		getStr();//跳过(
		vector<int> r1 = solve(anlPtr);
		getStr();//跳过)
		getStr();//跳过(
		//&取交集。因此下一个原子表达式的待分析用户是上一个表达式的结果集
		if (op == "&") rel = solve(r1);
		/*|取并集。得到满足第二个原子表达式的集合后，与前一个原子表达式的解
		合并，排序，去重，然后添加到总体的要返回的解的集合中*/
		if (op == "|") {
			vector<int> r2 = solve(anlPtr);
			r1.insert(r1.end(), r2.begin(), r2.end());//合并
			sort(r1.begin(), r1.end());//排序
			auto last = unique(r1.begin(), r1.end());//去重
			r1.erase(last, r1.end());//去重
			rel.insert(rel.end(), r1.begin(), r1.end());//添加到解集
		}
		getStr();//跳过)
	}
	//如果获取到数字，则继续获取完整的一个原子表达式
	else {
		int key = stoi(str);//转换为数字，即属性编号
		string op = getStr();//获取这个原子表达式的运算符
		str = getStr();
		int value = stoi(str);//转换为数字，即属性值
		//遍历待分析集合
		for (int i : anlPtr) {
			//二分查找当前用户是否有属性编号为key的属性
			auto it = lower_bound(a[i].begin(), a[i].end(), make_pair(key, 0));
			if (it == a[i].end() || it->first != key)continue;//该用户没有这个属性
			else {//该用户有这个属性
				if (op == ":" && it->second == value)rel.push_back(i);
				else if (op == "~" && it->second != value)rel.push_back(i);
			}
		}
	}
	return rel;//返回满足表达式的集合
}

int main() {
	ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL);
	int n, q, m;
	cin >> n;
	//根据输入的规模创造对应大小的存储空间
	d.resize(n + 1);
	a.resize(n + 1);
	for (int i = 1; i <= n; i++) {
		cin >> d[i];
		cin >> q;
		a[i].resize(q + 1);
		for (int j = 1; j <= q; j++) {
			cin >> a[i][j].first >> a[i][j].second;
		}
		anlPtr.push_back(i);
	}
	//输入m条表达式
	cin >> m;
	while (m--) {
		cin >> s;
		ptr = 0;//每分析完一个表达式后，指针都要归0
		vector<int> ansPtr = solve(anlPtr);//保存匹配结果的DN对应在d[i]中的下标
		vector<int> ans;
		for (int i : ansPtr)
			ans.push_back(d[i]);
		sort(ans.begin(), ans.end());
		for (int i : ans)cout << i << " ";
		cout << '\n';
	}
	return 0;
}
```

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

### 第3题

题目：

得分：100

思路：

分为三个阶段：

1. 填表：找规律，按对角线填，分为填第偶数条和填第奇数条对角线这两种情况
2. 量化：如果需要量化，在填表的同时进行量化
3. 解码：如果需要解码，直接套题目给的公式，四层循环就行（一开始以为四层循环过不了，但实在想不到其他方法）

```c++
#include<iostream>
#include <vector>
#include<cmath>
using namespace std;
vector<vector<int>> Q(8, vector<int>(8, 0));//量化矩阵Q
vector<int> d(70);//存储要填充的数据
vector<vector<int>> M(8, vector<int>(8, 0));
vector <vector<int>> M_utl(8, vector<int>(8, 0));//T==2,用来存放最终解码得到的M
void solve(int n, int T, vector<vector<int>> M) {
	int i = 0;
	int j = 0;
	int t = 0;//记录现在填充了几个数
	//填充矩阵M:按对角线填充，从第一条到第15条
	for (int k = 1; k <= 15; k++) {
		if (t == n)break;//带填充数填完后就不用填了
		while (t < n) {
			//当前位置填入数据，然后再分析下一步怎么填
			M[i][j] = d[t+1];
			if (T == 1 || T == 2)
				M[i][j] = M[i][j] * Q[i][j];
			t++;
			//当前是第偶数条对角线
			if (k % 2 == 0) {
				if (k < 8 && j == 0) {
					i = i + 1;
					break;
				}
				else if (k >= 8 && i == 7) {
					j = j + 1;
					break;
				}
				i = i + 1;
				j = j - 1;
			}
			//当前是第奇数条对角线
			else {
				if (k <= 8 && i == 0) {
					j = j + 1;
					break;
				}
				else if (k > 8 && j == 7) {
					i = i + 1;
					break;
				}
				i = i - 1;
				j = j + 1;
			}
		}
	}
	if (T == 2) {
		double sum = 0;
		for(int i =0 ;i<=7;i++)
			for (int j = 0; j <= 7; j++) {
				sum = 0;
				for (int u = 0; u <= 7; u++)
					for (int v = 0; v <= 7; v++) {
						double num1 = acos(-1)/8 * (i + 0.5) * u;
						double num2 = acos(-1)/8 * (j + 0.5) * v;
						double a = sqrt(0.5);
						if (u == 0 && v == 0)a = 0.5;
						else if (u != 0 && v != 0)a = 1;
						sum += a * M[u][v]* cos(num1) * cos(num2);
					}
				int temp = (int)round((sum / 4) + 128);
				M_utl[i][j] = temp;
				if (temp < 0)M_utl[i][j] = 0;
				if (temp > 255)M_utl[i][j] = 255;
			}
	}
	if (T != 2) {
		for (int i = 0; i <= 7; i++)
			for (int j = 0; j <= 7; j++) {
				cout << M[i][j] << " ";
				if (j == 7)cout << '\n';
			}
	}
	else if (T == 2) {
		for (int i = 0; i <= 7; i++)
			for (int j = 0; j <= 7; j++) {
				cout << M_utl[i][j] << " ";
				if (j == 7)cout << '\n';
			}
	}
}
int main() {
	ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL);
	int n, T;
	for(int i=0;i<=7;i++)
		for (int j = 0; j <= 7; j++) {
			cin >> Q[i][j];
		}
	cin >> n >> T;
	for (int i = 1; i <= n; i++)
		cin >> d[i];
	solve(n, T, M);
	return 0;
}
```

## 202209


### 第1题

**第一次提交**

题目：http://118.190.20.162/view.page?gpid=T153

分数: 100

思路

看懂题，直接做就行

代码

```c++
#include<iostream>
using namespace std;
#define ll long long
ll a[21];
ll b[21];
ll c[21];
void solve(int n, ll m) {
	c[0] = 1;
	for (int i = 1; i <= n; i++)
		c[i] = c[i - 1] * a[i];
	ll sum = 0;
	for (int i = 1; i <= n; i++) {
		b[i] = (m % c[i] - sum) / c[i - 1];
		sum += c[i - 1] * b[i];
		cout << b[i] << " ";
	}
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n;
	ll m;
	cin >> n >> m;
	for (int i = 1; i <= n; i++)
		cin >> a[i];
	solve(n, m);
	return 0;
}
```

### 第2题

**第一次提交**

题目：http://118.190.20.162/view.page?gpid=T152

分数：100

思路

先计算出总价和包邮的差值m，问题转换为在不超过m的前提下，选出一个总价值最大的组合，这个组合就是要丢掉的物品，那其实就是一个01背包问题

代码

```c++
#include<iostream>
#include <vector>
using namespace std;
#define ll long long
ll a[31];
void solve(int n, ll m, ll sum) {
	//创建n+1行m+1列二维数组（一开始还以为第一个参数是列）
	vector<vector<ll>> ans(n+1, vector<ll>(m + 1, 0));
	for(int i=1;i<=n;i++)
		for (int j = 1; j <= m; j++) {
			if (j >= a[i]) {
				ans[i][j] = max(ans[i - 1][j], ans[i - 1][j - a[i]] + a[i]);
			}
			else ans[i][j] = ans[i - 1][j];
		}
	cout << sum - ans[n][m];
	return;
}

int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n;
	ll x;
	ll sum = 0;
	cin >> n >> x;
	for (int i = 1; i <= n; i++) {
		cin >> a[i];
		sum += a[i];
	}
	//m：物品总价和包邮界限x的差值
	ll m = sum - x;
	//选择删除的物品，价值总和应该在不超过m的条件下尽可能最大
	//实际上就是一个01背包问题
	solve(n, m, sum);
	return 0;
}
```

### 第3题

题目：http://118.190.20.162/view.page?gpid=T151

得分：第一次10分，第二次100分（结果集要用set装起来，不然会有重复）

思路：写在代码上	

代码：

```c++
#include<iostream>
#include <vector>
#include<set>
#define ll long long
using namespace std;
//一个漫游数据对应的结构体
struct da {
	ll d;
	ll u;
	ll r;
};
//v[i]存放第i天的漫游数据
vector<da> v[1005];
//dangerR[i]表示第i天时，处于风险的所有地区
set<ll> dangerR[1005];
//存放风险名单（不能直接输出，而是要用set先存起来，不然有重复）
set<ll> ans;
int main() {
	ios::sync_with_stdio(false),cin.tie(NULL),cout.tie(NULL);
	ll n, ri, mi, p;
	ll d, u, r;
	cin >> n;
	for (int i = 0; i < n; i++) {//输出第i天的风险名单
		cin >> ri >> mi;
		//输入第i天的风险地区列表.同时更新i~i+6的dangerR
		for (int j = 0; j < ri; j++) {
			cin >> p;
			for (int k = 0; k <= 6; k++) 
				dangerR[i + k].insert(p);
		}
		//输入第i天收到的漫游数据
		for (int k = 0; k < mi; k++) {
			cin >> d >> u >> r;
			if (d < 0)continue;
			//到访当天，该地区不是风险区，那以后也不可能成为风险 。因此直接不保存这个数据
			if (!dangerR[i].count(r))continue;
			v[i].push_back({ d,u,r }); 
		}
		//分析最近七天的所有漫游数据，得到第i天的风险名单并输出
		cout << i << " ";
		for (int s = max(0, i - 6); s <= i; s++) {
			for (int t = 0; t < v[s].size(); t++) {
				d = v[s][t].d;
				u = v[s][t].u;
				r = v[s][t].r;
				/*进入风险名单需要同时满足以下三点条件：
				1. 这条数据是最近7天内的
				2. 在到访的那一日，到访处于风险状态；
				3. 自到访日至生成名单当日，到访地区持续处于风险状态。*/
				if (d <= i - 7)continue;//条件1
				//条件2，3
				bool flag = true;
				for (int z = d; z <= i; z++) {
					if (!dangerR[z].count(r)) {
						flag = false;
						break;
					}
				}
				if (flag)ans.insert(u);
			}
		}
		for (auto it : ans)cout << it << " ";
		cout << '\n';
		ans.clear();//每次用完后还要清空
	}
	return 0;
}
```

## 202206

### 第1题

题目：http://118.190.20.162/submitlist.page?gpid=T148

分数：100

思路

看懂题，直接做就行。注意：虽然不加`#include<math.h>`在vs中也可以使用sqrt()函数，但ccf里需要添加这个头文件，不然编译错误

代码

```c++
#include<iostream>
#include<math.h>
using namespace std;
double a[1001];
void solve(int n, double sum) {
	double avg = sum / n;
	double temp = 0;
	double d;
	for (int i = 1; i <= n; i++) {
		temp += (a[i] - avg) * (a[i] - avg);
		d = temp / n;
	}
	for (int i = 1; i <= n; i++)
		cout << (a[i] - avg) / sqrt(d) << "\n";
		
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n;
	double sum = 0;
	cin >> n;
	for (int i = 1; i <= n; i++) {
		cin >> a[i];
		sum += a[i];
	}
	solve(n, sum);
	return 0;
}
```

### 第2题

题目：http://118.190.20.162/view.page?gpid=T147

**第一次提交**

得分：100

思路

主要思路已经写在代码注释上了

代码

```c++
#include<iostream>
#include <vector>
using namespace std;
#define ll long long
//定义一个结构体，记录绿化图A中值为1的坐标
struct point {
	ll x;
	ll y;
};
point p[1001];//存放所有绿化图值为1的坐标
int B[51][51];
int c;//记录藏宝图B中值为1的个数
void solve(int n, int s, int l) {
	int ans = 0;
	for (int i = 1; i <= n; i++) {//第一层循环：遍历p[i]与B[0][0]对应
		//若以p[i]为左下角会越界，则后面都不用讨论了
		if (p[i].x + s > l || p[i].y + s > l)continue;
		int k = c - 1;//k：目前藏宝图中还未与绿化图匹配上的树的个数
		for (int j = 1; j <= n; j++) {
			//如果这个点都不在B[0][0]的右上方，则直接跳过这个点
			if (i == j)continue;
			if (p[j].x < p[i].x || p[j].y < p[i].y)continue;
			//如果这个点位于藏宝图中
			int xs = p[j].x - p[i].x;
			int ys = p[j].y - p[i].y;
			if (xs <= s && ys <= s) {
				//情况1：对应于藏宝图的值也为1，那么k--
				if (B[xs][ys] == 1)k--;
				//情况2：对应于藏宝图的值不为1，说明不匹配，直接跳出
				if (B[xs][ys] != 1) {
					k = 100;
					break;
				}
			}
		}
		if (k == 0)ans++;
	}
	cout << ans;
	return;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n, s, temp;
	ll l;
	ll x, y;
	cin >> n >> l >> s;
	for (int i = 1; i <= n; i++) {
		cin >> x >> y;
		p[i] = { x,y };
	}
	for(int i=s;i>=0;i--)
		for (int j = 0; j <=s; j++) {
			cin >> temp;
			if (temp == 1) c++;//初始化B的时候顺便记录值为1的个数
			B[i][j] = temp;
		}
	solve(n, s, l);
	return 0;
}
```

## 202203

### 第1题

题目：http://118.190.20.162/view.page?gpid=T143

**第一次提交**

得分：80，运行超时

思路

以为第一题不会很严格，直接用双层循环遍历两个数组，没想到超时了

代码

```c++
#include<iostream>
using namespace std;
int x[100001];
int y[100001];
void solve(int n, int k) {
	int ans = 0;
	int flag = 0;
	for (int i = 1; i <= k; i++) {
		flag = 0;
		if (y[i] == 0) continue;
		for (int j = i - 1; j >= 1; j--) {
			if (y[i] == x[j]) {
				flag = 1;
				break;
			}
		}
		if (flag == 0)ans++;
	}
	cout << ans;
	return;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n, k;
	cin >> n >> k;
	for (int i = 1; i <= k; i++) 
		cin >> x[i] >> y[i];
	solve(n, k);
	return 0;
}
```

**第二次提交**

得分：100

思路

用一个f[]数组，记录下标对应的变量是否被初始化过，0未初始化，1已初始化

代码

```c++
#include<iostream>
using namespace std;
int x[100001];
int y[100001];
int f[100001];
void solve(int k) {
	int ans = 0;
	for (int i = 1; i <= k; i++) {
		if (f[y[i]] != 1) ans++;
		f[x[i]] = 1;
	}
	cout << ans;
	return;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n, k;
	cin >> n >> k;
	for (int i = 1; i <= k; i++) 
		cin >> x[i] >> y[i];
	f[0] = 1;
	solve(k);
	return 0;
}
```

### 第2题

题目：http://118.190.20.162/submitlist.page?gpid=T142

**第一次提交**

得分：70，运行超时

思路

看上去挺简单，就是满足t[j] - s <= c[j] - 1公式就可以，但数据量给的多，直接双层循环做会超时

代码

```c++
#include<iostream>
using namespace std;
int t[100001];
int c[100001];
int q[100001];
void solve(int n, int m, int k) {
	int ans = 0;
	for (int i = 1; i <= m; i++) {
		ans = 0;
		for (int j = 1; j <= n; j++) {
			int s = q[i] + k;
			if (s > t[j])continue;
			if (t[j] - s <= c[j] - 1) ans++;
		}
		cout << ans << "\n";
	}
	return;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n, m, k;
	cin >> n >> m >> k;
	for (int i = 1; i <= n; i++)
		cin >> t[i] >> c[i];
	for (int i = 1; i <= m; i++)
		cin >> q[i];
	solve(n, m, k);
	return 0;
}
```

**第二次提交**

得分：100

思路

**差分**

首先对于每一个计划，计算出应在哪个时间段内拿到核酸证明，使得该计划能成功通行。然后让该时间段上的通行数都加1

当遍历完所有通行计划后，对差分数组进行前缀和还原成操作过后的原始数组（A[i]表示在时刻 i 拿到核酸证明，可以使计划通行的数量）

利用数组下标直接得到询问的结果

代码

```c++
#include<iostream>
using namespace std;
const int N = 4e5 + 10;//t+q最大为2e5+2e5
int b[N];//差分数组
//对区间[left,right]中的每一个元素+c
void insert(int left, int right, int c) {
	b[left] += c;
	b[right + 1] -= c;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n, m, k;
	cin >> n >> m >> k;
	for (int i = 1; i <= n; i++) {//对于每一个计划
		int t, c;
		cin >> t >> c;
		//确定符合通过条件的最早拿到核酸证明时间
		int left = t - c + 1;
		left = left > 0 ? left : 1;
		//确定符合通过条件的最晚拿到核酸证明时间
		int right = t;
		//在这个区间内拿到核酸证明的可通行数全部加1
		insert(left, right, 1);
	}
	//对差分数组做前缀和，还原原始数组
	for (int i = 1; i < N; i++)
		b[i] += b[i - 1];
	while (m--) {
		int s;
		cin >> s;
		//直接通过数组下标查
		cout << b[s + k] << "\n";
	}
	return 0;
}
```

## 202112

### 第1题

题目：http://118.190.20.162/view.page?gpid=T138

**第一次提交**

得分：100

思路

* 题目提示说如果有多个f(x)的值一样，用乘法比加法快，因此想到记录f(x)值的个数，那么定义一个f[]数组，含义是f(x)=i的有f[i]个
* 由于序列有序，求f(x)可以从下标=f(x-1)处开始，不用每次都从头开始遍历

代码

```c++
#include<iostream>
using namespace std;
int A[200];//记录A序列
int f[200];//f(x)=i的有f(i)个
void solve(int n, int N) {
	int pre = 0;//pre：f(x-1)的值
	long long ans = 0;
	for (int x = 1; x <= N - 1; x++) {
		//特例：大于序列中所有数，直接f(x)=n
		if (x >= A[n]) {
			f[n]++;
			continue;
		}
		//求f(x),以f(x-1)为起点，不用从头开始遍历
		for (int i = pre; i <= n; i++) {
			if (x < A[i + 1]) {
				f[i]++;
				pre = i;
				break;
			}
		}
	}
	//计算求和
	for (int i = 1; i <= n; i++)
		if (f[i] != 0)ans += i * f[i];
	cout << ans;
	return;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n, N;
	cin >> n >> N;
	for (int i = 1; i <= n; i++)
		cin >> A[i];
	solve(n, N);
	return 0;
}
```

### 第2题

题目：http://118.190.20.162/view.page?gpid=T137

**第一次提交**

得分：70，运行超时

思路

用二分查找求得f(x),用公式求得g(x),按题目给的公式求出答案

代码

```c++
#include<iostream>
using namespace std;
int A[100005];
int f[100005];
int g[100005];
int getF(int x, int ld, int ud) {
	int mid = 0;
	while (ld < ud) {
		mid = (ld + ud) / 2;
		if (x == A[mid])return mid;
		if (x > A[mid])ld = mid + 1;
		if (x < A[mid])ud = mid;
	}
	return ld - 1;
}
void solve(int n, int N) {
	int r = N / (n + 1);
	int ans = 0;
	for (int i = 1; i < N; i++) {
		int ld = f[i - 1];
		if (ld == n)f[i] = ld;
		else f[i] = getF(i, ld, n + 1);
		g[i] = i / r;
		int temp = f[i] >= g[i] ? f[i] - g[i] : g[i] - f[i];
		ans += temp;
	}
	cout << ans;
}
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n, N;
	cin >> n>> N;
	for (int i = 1; i <= n; i++)
		cin >> A[i];
	solve(n, N);
	return 0;
}
```

## 202109

### 第1题

题目：http://118.190.20.162/view.page?gpid=T129

**第一次提交**

得分：100

思路 

直接做就行

代码

```c++
#include<iostream>
using namespace std;
int B[100001];
long long sum_max;
long long sum_min;
int main() {
	ios::sync_with_stdio(false), cin.tie(NULL), cout.tie(NULL);
	int n;
	cin >> n;
	for (int i = 1; i <= n; i++) {
		cin >> B[i];
		sum_max += B[i];
		if (B[i] > B[i - 1])sum_min += B[i];
	}
	cout << sum_max << "\n" << sum_min;
	return 0;
}
```

### 第2题

题目：http://118.190.20.162/view.page?gpid=T130

**第一次提交**

得分：

思路 


代码

```c++


```