---
title: 算法导论期末考资料
layout: post
tags:
  - algorithm
---

为了应付考试整理了一些算法机考时参考。

**大数加减乘除乘方**

    #define SWAP(a,b) string c; c=a; a=b; b=c     //交換字符串
    int compare(string a, string b) {              //大數比較
      if (a.length() < b.length()) {
          return 0;
      } else if (a.length() == b.length()) {
          if (a < b)
              return 0;
      }
      return 1;
    }
    string add(string a, string b) {              //大數加法
      if (!compare(a, b)) {
          SWAP(a, b);
      }            //保證a的長度大於b
      int *ad = new int[a.length() + 1];
      memset(ad, 0, sizeof(int) * (a.length() + 1));  //初始化

      int i, j;
      for (i = 0; i < a.length() - b.length(); i++)    //將a長度大於b的部分傳過去
          ad[i + 1] = a[i] - '0';
      for (j = 0; i < a.length(); i++, j++)          //將a與b匹配的部分相加再傳
          ad[i + 1] = a[i] - '0' + b[j] - '0';
      for (i = a.length(); i > 0; i--) {             //進位操作
          if (ad[i] > 9) {
              ad[i - 1] += ad[i] / 10;
              ad[i] %= 10;
          }
      }
      char *c = new char[a.length() + 1];         //定義一個字符數組保存結果
      i = 0, j = 0;
      while (ad[i] == 0)
          i++;                      //去除前導0
      while (i < a.length() + 1)
          c[j++] = ad[i++] + '0';
      c[j] = '\0';
      if (c[0] == '\0') {
          c[1] = '\0';
          c[0] = '0';
      }     //如果結果为0
      free(ad);                                 //釋放ad數組
      string s(c);                              //將c轉換成string類型
      return s;
    }
    string subtract(string a, string b) {
      int flag = 0, i, j;
      if (!compare(a, b)) {                        //如果a<b，交換a、b並標識結果为負
          flag = 1;
          SWAP(a, b);
      }
      int *su = new int[a.length()];
      memset(su, 0, sizeof(int) * a.length());      //初始化
      for (i = 0; i < a.length() - b.length(); i++)    //將a長度大於b的部分傳過去
          su[i] = a[i] - '0';
      for (j = 0; i < a.length(); i++, j++)          //將a與b匹配的部分相減再傳
          su[i] = a[i] - b[j];
      for (i = a.length() - 1; i > 0; i--) {           //進位操作
          if (su[i] < 0) {
              su[i - 1]--;
              su[i] += 10;
          }
      }
      char *c = new char[a.length() + 2];
      i = 0, j = 0;
      if (flag)
          c[j++] = '-';                    //如果是負數，c[0]='-'
      while (su[i] == 0)
          i++;                      //後面同加法
      while (i < a.length())
          c[j++] = su[i++] + '0';
      c[j] = '\0';
      if (c[0] == '\0') {
          c[1] = '\0';
          c[0] = '0';
      }
      free(su);
      string s(c);
      return s;
    }
    string multiply(string a, string b) {
      int *mu = new int[a.length() + b.length()];
      memset(mu, 0, sizeof(int) * (a.length() + b.length()));
      for (int i = 0; i < a.length(); i++)                 //將數字兩兩相乘
          for (int j = 0; j < b.length(); j++)
              mu[i + j + 1] += (a[i] - '0') * (b[j] - '0');
      for (int i = a.length() + b.length() - 1; i > 0; i--) {  //進位操作
          if (mu[i] > 9) {
              mu[i - 1] += mu[i] / 10;
              mu[i] %= 10;
          }
      }
      char *c = new char[a.length() + b.length()];
      int i = 0, j = 0;

      while (mu[i] == 0)
          i++;
      while (i < a.length() + b.length())
          c[j++] = mu[i++] + '0';
      c[j] = '\0';
      if (c[0] == '\0') {
          c[1] = '\0';
          c[0] = '0';
      }
      free(mu);
      string s(c);
      return s;
    }
    string *devide(string a, string b) {
      string *de = new string[2];
      de[0] = "0";
      de[1] = b;
      string one = "1", ten = "10";
      if (!compare(a, b))
          return de;
      while (a[0] != '-') {
          de[1] = a;
          a = subtract(a, b);
          de[0] = add(de[0], one);
      }
      de[0] = subtract(de[0], one);
      return de;
    }
    string power(string a, string b) {
      string c = "1", one = "1";
      while (b[0] != '0') {
          c = multiply(c, a);
          b = subtract(b, one);
      }
      return c;
    }

**大数求模**

    int BigNumMod(char bigNum[], int b) {
      int mod = 0;
      for (int j = 0; j < strlen(bigNum); j++) {
          mod = (bigNum[j] - '0' + 10 * mod) % b;
      }
      return mod;
    }

**快速取模(a pow b mod c)**

    int PowerMod(int a, int b, int c)
    {
      int ans = 1;
      a = a % c;
      while(b>0) {
          if(b % 2 = = 1)
          ans = (ans * a) % c;
          b = b/2;
          a = (a * a) % c;
      }
      return ans;
    }

**筛法求素数**

    #define MAXPRIME 1000000
    int prime[MAXPRIME];
    int testPrime(int n, int i) {
        int k = n;
        for (int j = 0; j < i; j++) {
            if (prime[j] > k)
                break;
            if (n % prime[j] == 0)
                return 0;
            k = n / prime[j];
        }
        return 1;
    }
    void getPrime() {
        int i = 1;
        prime[0] = 2;
        for (int n = 3; n < MAXPRIME; n++) {
            if (testPrime(n, i)) {
                prime[i] = n;
                i++;
                if (i >= MAXPRIME)
                    break;
            }
        }
    }

**全排列**

    while (next_permutation(str.begin(), str.end()))
    {
        cout << str << endl;
    }

**康拓展开**

排列与整数之间的映射

    int fac[] = { 1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880 }; //阶乘
    int cantor(int *s, int n) {
        int num = 0;
        for (int i = 0; i < n - 1; i++) {
            int tmp = 0;
            for (int j = i + 1; j < n; j++)
                if (s[j] < s[i])
                    tmp++;
            num += fac[n - i - 1] * tmp;
        }
        return num;
    }

    void _cantor(int *s, int n, int x) {
        bool tmp[n + 1];
        memset(tmp, 0, sizeof(tmp));
        for (int i = n - 1; i >= 0; i--) {
            int k = x / fac[i];
            x %= fac[i];
            int j = 1;
            for (int sum = 0; sum < k || tmp[j]; j++)
                if (!tmp[j])
                    sum++;
            s[n - 1 - i] = j;
            tmp[j] = 1;
        }
    }

**最大公约数**

    int gcd(int m, int n) {
        if (m == 0) return n;
        if (n == 0) return m;
        if (m < n) {
            int tmp = m;
            m = n;
            n = tmp;
        }
        while (n != 0) {
            int tmp = m % n;
            m = n;
            n = tmp;
        }
        return m;
    }

**逆序对**

    double num;//逆序对个数

    void MergeArray(int a[], int begin, int mid, int end, int temp[]) {
        int i = begin, j = mid + 1, k = 0;
        while (i <= mid && j <= end) {
            if (a[i] <= a[j]) {
                temp[k] = a[i];
                k++;
                i++;
            } else {
                temp[k] = a[j];
                k++;
                j++;
                num += mid - i + 1;
            }
        }
        while (i <= mid) {
            temp[k] = a[i];
            k++;
            i++;
        }
        while (j <= end) {
            temp[k] = a[j];
            k++;
            j++;
        }
        for (i = 0; i < k; i++)
            a[begin + i] = temp[i];
    }

    void MergeSort(int a[], int begin, int end, int temp[]) {//a为要求逆序对个数的数组,begin=0，end=n-1
        if (begin < end) {
            int mid = (begin + end) / 2;
            MergeSort(a, begin, mid, temp);
            MergeSort(a, mid + 1, end, temp);
            MergeArray(a, begin, mid, end, temp);
        }
    }

**二分图最大匹配**

    #define M 20
    #define inf 0x3f3f3f3f

    int n, nx, ny;
    int link_[M], lx[M], ly[M], slack[M];    //lx,ly为顶标，nx,ny分别为x点集y点集的个数
    int visx[M], visy[M], w[M][M];//w保存图的信息

    int DFS(int x) {
        visx[x] = 1;
        for (int y = 1; y <= ny; y++) {
            if (visy[y])
                continue;
            int t = lx[x] + ly[y] - w[x][y];
            if (t == 0)       //
                    {
                visy[y] = 1;
                if (link_[y] == -1 || DFS(link_[y])) {
                    link_[y] = x;
                    return 1;
                }
            } else if (slack[y] > t)  //不在相等子图中slack 取最小的
                slack[y] = t;
        }
        return 0;
    }
    int KM() {
        int i, j;
        memset(link_, -1, sizeof(link_));
        memset(ly, 0, sizeof(ly));
        for (i = 1; i <= nx; i++)            //lx初始化为与它关联边中最大的
            for (j = 1, lx[i] = -inf; j <= ny; j++)
                if (w[i][j] > lx[i])
                    lx[i] = w[i][j];

        for (int x = 1; x <= nx; x++) {
            for (i = 1; i <= ny; i++)
                slack[i] = inf;
            while (1) {
                memset(visx, 0, sizeof(visx));
                memset(visy, 0, sizeof(visy));
                if (DFS(x))     //若成功（找到了增广轨），则该点增广完成，进入下一个点的增广
                    break;  //若失败（没有找到增广轨），则需要改变一些点的标号，使得图中可行边的数量增加。
                            //方法为：将所有在增广轨中（就是在增广过程中遍历到）的X方点的标号全部减去一个常数d，
                            //所有在增广轨中的Y方点的标号全部加上一个常数d
                int d = inf;
                for (i = 1; i <= ny; i++)
                    if (!visy[i] && d > slack[i])
                        d = slack[i];
                for (i = 1; i <= nx; i++)
                    if (visx[i])
                        lx[i] -= d;
                for (i = 1; i <= ny; i++)  //修改顶标后，要把所有不在交错树中的Y顶点的slack值都减去d
                    if (visy[i])
                        ly[i] += d;
                    else
                        slack[i] -= d;
            }
        }
        int res = 0;
        for (i = 1; i <= ny; i++)
            if (link_[i] > -1)
                res += w[link_[i]][i];
        return res;
    }
    int main() {
        int i, j, t;
        scanf("%d", &t);
        while (t--) {
            scanf("%d", &n);
            nx = ny = n;
            //  memset (w,0,sizeof(w));
            for (i = 1; i <= n; i++)
                for (j = 1; j <= n; j++)
                    scanf("%d", &w[i][j]);
            int ans = KM();
            printf("%d\n", ans);
        }
        return 0;
    }     

**并查集**

    int p[555], r[555];//p[i]=j表示i的父节点是j，r[i]表示根节点为i的树的高度，初始p[i]=i，r[i]=0
    int root(int x) {//返回x的根节点
      if (p[x] == x)
          return x;
      else
          return p[x] = root(p[x]);
    }

    void merge(int x, int y) {
      x = root(x);
      y = root(y);
      if (r[x] > r[y])
          p[y] = x;
      else if (r[x] < r[y])
          p[x] = y;
      else {
          p[y] = x;
          r[x]++;
      }
    }

**最大连续子序列**

    int maxsum(int a[n])    
    {  
        int max=a[0];       //全负情况，返回最大数  
        int sum=0;  
        for(int j=0;j<n;j++)  
        {  
            if(sum>=0)     //如果加上某个元素，sum>=0的话，就加  
                sum+=a[j];  
            else     
                sum=a[j];  //如果加上某个元素，sum<0了，就不加  
            if(sum>max)  
                max=sum;  
        }  
        return max;  
    }  

**最长公共子序列**

c[i,j]=0 if i=0 or j=0

c[i,j]=c[i-1,j-1]+1 if a[i]=b[j]

c[i,j]=max(c[i,j-1],c[i-1,j]) if a[i]!=b[j]

**最长递增子序列**

    int BinarySearch(int *array, int value, int nLength)
    {
        int begin = 0;
        int end = nLength - 1;
        while(begin <= end)
        {
            int mid = begin + (end - begin) / 2;
            if(array[mid] == value)
                return mid;
            else if(array[mid] > value)
                end = mid - 1;
            else
                begin = mid + 1;
        }
        return begin;
    }

    int LIS_DP_NlogN(int *array, int nLength)
    {
        int B[nLength];
        int nLISLen = 1;
        B[0] = array[0];

        for(int i = 1; i < nLength; i++)
        {
            if(array[i] > B[nLISLen - 1])
            {
                B[nLISLen] = array[i];
                nLISLen++;
            }
            else
            {
                int pos = BinarySearch(B, array[i], nLISLen);
                B[pos] = array[i];
            }
        }
        return nLISLen;
    }

**0-1背包**

每种物品仅有一件，可以选择放或不放。

f[i][v]表示前i件物品恰放入一个容量为v的背包可以获得的最大价值，c[i]表示第i件物品的容量，w[i]表示第i件物品价值。则其状态转移方程便是：

f[i][v]=max{f[i-1][v],f[i-1][v-c[i]]+w[i]}

**完全背包**

这个问题非常类似于01背包问题，所不同的是每种物品有无限件。

f[i][v]=max{f[i-1][v],f[i][v-c[i]]+w[i]}

**多重背包**

有N种物品和一个容量为V的背包。第i种物品最多有n[i]件可用。

f[i][v]=max{f[i-1][v-k*c[i]]+k*w[i]|0<=k<=n[i]}

**BFS**

略

**DFS**

略

**最小生成树**

每次选能把一个不在树中的节点合并起来的边(利用并查集)中最小的一条加在图中。

**拓扑排序**

略

**最短路**

Dijkstra，求单源、无负权的最短路。

    q.push(0);
    while (!q.empty()) {
        int temp = q.front();
        q.pop();
        for (i = 0; i < n; i++) {
          //w[i]表示第i点到起始点的距离
            if (w[i] > w[temp] + dis[temp][i]) {
                w[i] = w[temp] + dis[temp][i];
                q.push(i);
            }
        }
    }
    shortest = w[n - 1];

**无向图判断并输出环**

    #define INT_MAX -1 //表示node间没有路
    void dfsVisit(vector<vector<int> >&graph, int node, vector<int>&visit,
                 vector<int>&father)
    {
      int n = graph.size();
      visit[node] = 1;
      for(int i = 0; i < n; i++)
          if(i != node && graph[node][i] != INT_MAX)
          {
              if(visit[i] == 1 && i != father[node])//找到一个环
              {
                  int tmp = node;
                  cout<<"cycle: ";
                  while(tmp != i)
                  {
                      cout<<tmp<<"->";
                      tmp = father[tmp];
                  }
                  cout<<tmp<<endl;
              }
              else if(visit[i] == 0)
              {
                  father[i] = node;
                  dfsVisit(graph, i, visit, father);
              }
          }
      visit[node] = 2;
    }
    void dfs(vector<vector<int> >&graph)
    {
      int n = graph.size();
      vector<int> visit(n, 0); //visit按照算法导论22.3节分为三种状态
      vector<int> father(n, -1);// father[i] 记录遍历过程中i的父节点
      for(int i = 0; i < n; i++)
          if(visit[i] == 0)
              dfsVisit(graph, i, visit, father);
    }

**有向图判断并输出环**

    #define INT_MAX -1　//表示node间没有路
    stack<int> tuopu;
    void dfsVisit(vector<vector<int> >&graph, int node, vector<int>&visit,
                   vector<int>&father)
    {
        int n = graph.size();
        visit[node] = 1;
        //cout<<node<<"-\n";
        for(int i = 0; i < n; i++)
            if(i != node && graph[node][i] != INT_MAX)
            {
                if(visit[i] == 1 && i != father[node])//找到一个环
                {
                    int tmp = node;
                    cout<<"cycle: ";
                    while(tmp != i)
                    {
                        cout<<tmp<<"->";
                        tmp = father[tmp];
                    }
                    cout<<tmp<<endl;
                }
                else if(visit[i] == 0)
                {
                    father[i] = node;
                    dfsVisit(graph, i, visit, father);
                }
            }
        visit[node] = 2;
        tuopu.push(node);
    }
    void dfs(vector<vector<int> >&graph)
    {
        int n = graph.size();
        vector<int> visit(n, 0); //visit按照算法导论22.3节分为三种状态
        vector<int> father(n, -1);// father[i] 记录遍历过程中i的父节点
        for(int i = 0; i < n; i++)
            if(visit[i] == 0)
                dfsVisit(graph, i, visit, father);
    }