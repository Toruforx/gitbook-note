# ACM

## Codeforces

### Codeforces Round #832（Div.2）

#### <u>A.Two Groups</u>

一句话题意：给了一个含n个数的数组，要求将n个数分为两个子数组s1和s2，使s1的和的绝对值与s2的和的绝对值的差最大化，给出最大值结果。

思路：贪心算法，对数组中的每个数进行遍历，分别计算放入s1和s2得到的差值，选择更优的，经过n次遍历后得到最优解。时间复杂度为O(n)。

```c++
//
// Created by Toru on 2022/11/4.
//
#include <cstdio>
#include <iostream>
#include <cmath>
using namespace std;
int main()
{
    long long t,i,j,k,n,s1,s2,x,ans;
    cin>>t;
    while(t--)
    {
        s1=0;s2=0;
        cin>>n;
        for(i=1;i<=n;i++)
        {
            cin>>x;
            if(abs(abs(s1+x)-abs(s2))>=abs(abs(s1)-abs(s2+x)))
            {
                s1+=x;
            }
            else
            {
                s2+=x;
            }
        }
        ans=abs(abs(s1)-abs(s2));
        cout<<ans<<endl;
    }
}
```

#### <u>B.BAN BAN</u>

一句话题意：给一个数n，要求将n个"BAN"依序组成的字符串通过有限次的任选两个字符swap使得字符串不存在"BAN"子串。要求给出最少操作次数，以及每次操作选择的两个字符位置。

思路：规律题。题目的样例已经给出了n=1和2的情况，自己推一下n=3就可以发现只需要每次将字符串的第一个BAN的A和最后一个BAN的N交换使不存在N在A后面即可。因此可以将n个BAN的串转化为n-2个BAN的串进行处理，不断递归直至n=1或2退出递归。

```c++
//
// Created by Toru on 2022/11/4.
//
#include <cstdio>
#include <iostream>
using namespace std;
void handle(int pre,int len)
{
    if(len == 0)
        return;
    if(len==1)
    {
        cout<<pre+2<<" "<<pre+3<<endl;
        return;
    }
    cout<<pre+2<<" "<<pre+3*len<<endl;
    handle(pre+3,len-2);
    return;
}
int main()
{
    int t,i,j,k,n;
    cin>>t;
    while(t--)
    {
        cin>>n;
        cout<<(n+1)/2<<endl;
        handle(0,n);
    }
}
```

#### <u>C.Swap Game</u>

一句话题意：给了一个含n个数的数组，AB双方轮流进行操作，操作是将数组最前面的数减1，选择后面一个数放到最前面。当玩家回合开始时，最前面的数为0，则判定为该玩家输。A先手，假设AB都是最优策略，判断最后谁会赢。

思路：经典博弈论，由必败状态（最前面的数为0）往上推，发现A/B的最优策略就是选择后面最小的数放到最前面让对方减1，由于A先手，A选到的数一定比B小，所以B的一线生机就是最前面的数减1后放到后面，然后选择这个数！所以比较初始局面最前面的数和后面最小数就可以判断胜负了。

```c++
//
// Created by Toru on 2022/11/4.
//
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
int main()
{
    int t,i,j,k,n,a[100005],mina,minb;
    cin>>t;
    while(t--)
    {
        mina=1000000005,minb=1000000005;
        cin>>n;
        cin>>a[0];
        for(i=1;i<n;i++)
        {
            cin>>a[i];
            if(a[i]<mina)
                mina=a[i];
        }
        minb=a[0]-1;
        if(mina<=minb)
        {
            cout<<"Alice\n";
        }
        else
        {
            cout<<"Bob\n";
        }
    }
}
```

#### <u>D.Yet Another Problem</u>

一句话题意：给了一个含n个数的数组，进行q次独立查询，每次查询会给出一个区间的左右界限l和r，每次操作可以选取一个长度为奇数的子区间，将子区间的异或和赋值给子区间每个数，要求经过有限次操作可以使查询区间内所有数均为0，如果可以做到输出最少操作次数，不能则输出-1。

思路：分类讨论+找规律。在推样例的时候得出了几个结论：

1. 如果查询区间的异或和不为0，不可能实现
2. 如果查询区间所有数相等且不为0，不可能实现
3. 如果查询区间为奇数长且异或和为0，必然1次就可以实现

所以难点就在于查询区间为偶数长且异或和为0该如何判断，已知偶数长的区间肯定可以划分为2个奇数长的子区间，所以只需要找是否存在从l开始奇数长的子区间的异或和为0即可，如果存在则可以实现。另外还需要判断偶数长的查询区间前后是否存在0，如果存在可以转换成奇数长区间来看待。另外要注意的是n和q都是2e5范围，很容易TLE，需要快读和O(n)复杂度的算法，预处理需要多花一些心思。

```c++
//
// Created by Toru on 2022/11/4.
//
#include <iostream>
#include <cstdio>
#include <vector>
#include <map>
#define MAXN 200005
using namespace std;
int main()
{
    ios::sync_with_stdio(false);
    int n,q,i,j,l,r,a[MAXN],sum[MAXN]={0},cnt=0,flag,tmp,same[MAXN]={0},canl[MAXN]={0};
    map<int,int> m;
    vector<int> vec[MAXN];
    cin>>n>>q;
    m[0]=++cnt;
    vec[cnt].push_back(0);
    a[0]=-1;
    for(i=1;i<=n;i++)
    {
        cin>>a[i];
        sum[i]=sum[i-1]^a[i];
        if(m[sum[i]]==0)
        {
            m[sum[i]]=++cnt;
            vec[cnt].push_back(i);
        }
        else
        {
            vec[m[sum[i]]].push_back(i);
        }
        if(a[i]==a[i-1])
        {
            same[i]=same[i-1]+1;
        }
        else
        {
            same[i]=1;
        }
    }
    for(i=1;i<=cnt;i++)
    {
        canl[vec[i][vec[i].size()-1]]=-1;
        for(j=vec[i].size()-2;j>=0;j--)
        {
            tmp=vec[i][j+1];
            if((tmp-vec[i][j])%2==0)
            {
                canl[vec[i][j]]=canl[tmp];
            }
            else
            {
                canl[vec[i][j]]=tmp;
            }
        }
    }
    for(j=1;j<=q;j++)
    {
        cin>>l>>r;
        if((sum[r]^sum[l-1])!=0)
        {
            cout<<"-1\n";
            continue;
        }
        flag=0;
        if(r-l+1>same[r])
            flag=1;
        if(flag==0)
        {
            if(a[l]==0&&a[r]==0)
            {
                cout<<"0\n";
                continue;
            }
            else
            {
                if((r-l)%2==1)
                {
                    cout<<"-1\n";
                    continue;
                }
                else
                {
                    cout<<"-1\n";
                    continue;
                }
            }
        }
        if((r-l)%2==0)
        {
            cout<<"1\n";
            continue;
        }
        flag=0;
        if(canl[l-1]!=-1&&canl[l-1]<=r)
            flag=1;
        if(flag)
        {
            if(a[l]==0||a[r]==0)
                cout<<"1\n";
            else
                cout<<"2\n";
            continue;
        }
        else
        {
            cout<<"-1\n";
        }
    }
}
```

### Codeforces Round #833（Div.2）

#### <u>A.The Ultimate Square</u>

一句话题意：略。

思路：思维题，略。（下次要学习用python过签到

```c++
//
// Created by Toru on 2022/11/12.
//
#include <cstdio>
#include <iostream>
using namespace std;
int main()
{
    int t,n,a;
    cin>>t;
    while(t--)
    {
        cin>>n;
        a=(n+1)/2;
        cout<<a<<endl;
    }
}
```

#### <u>B.Diverse Substrings</u>

一句话题意：给一个数字（0-9）字符串，问满足题目条件的子串有几个，条件是该串中各数字出现次数均不超过串中数字种类。

思路：二维循环+枚举优化。字符串的长度范围为1e5，因此n^2的枚举肯定超时，考虑对枚举范围进行优化。考虑最极限情况，串中字符种类最多为10种（0-9），每种数字均出现10次，此时不管下一个数字是什么新串都会不成立，因此串最长为100。一层枚举子串右边下标，二层枚举子串长度，最坏时间复杂度为O(100*n)。

```c++
//
// Created by Toru on 2022/11/12.
//
#include <cstdio>
#include <iostream>
#include <cstring>
using namespace std;
int main()
{
    int t,n,i,j,k,cnt,ans,tmp,sum[10],maxx;
    string s;
    cin>>t;
    while(t--)
    {
        cin>>n;
        cin>>s;
        ans=0;
        for(i=0;i<s.length();i++)
        {
            cnt=0;
            maxx=-1;
            memset(sum,0,sizeof(sum));
            for(j=0;j<100&&j<=i;j++)
            {
                tmp=s[i-j]-'0';
                if(sum[tmp]==0)
                    cnt++;
                sum[tmp]++;
                if(sum[tmp]>maxx)
                    maxx=sum[tmp];
                if(maxx<=cnt)
                    ans++;
            }
            //cout<<ans<<endl;
        }
        cout<<ans<<endl;
    }
}
```

#### <u>C.Zero-Sum Prefixes</u>

一句话题意：给n个数组成的数列，可以将数列中的0替换成任意数，问最多存在多少个为0的连续子集前缀和。

思路：枚举数列中的0，对于数列中的每个0，假设该0为字符串最后一位，则考虑的是前面的前缀和里面出现次数最多的一个，此时将0取该前缀和的相反数即可。不断向后枚举0，数量不断累加，得到最后的答案。要点在于对各种数列情况的特判。

```c++
//
// Created by Toru on 2022/11/12.
//
#include <cstdio>
#include <iostream>
#include <cstring>
#include <map>
#define MAXN 200005
using namespace std;
int main()
{
    int t,n,i,k,maxx,maxans,flag;
    long long a[MAXN],sum;
    map<long long,int> m;
    cin>>t;
    for(k=1;k<=t;k++)
    {
        maxans=0;
        sum=0;
        maxx=0;
        flag=0;
        m.clear();
        cin>>n;
        for(i=1;i<=n;i++)
        {
            cin>>a[i];
            if(a[i]==0)
            {
                if(flag==1)
                    maxans+=maxx;
                else
                    maxans+=m[0];
                sum=0;
                maxx=1;
                m.clear();
                m[0]++;
                flag=1;
            }
            else
            {
                sum+=a[i];
                m[sum]++;
                if(m[sum]>maxx)
                    maxx=m[sum];
            }
        }
        if(flag==1)
            maxans+=maxx;
        else
            maxans+=m[0];
        cout<<maxans<<endl;
    }
}
```

### Educational Codeforces Round 140 (Rated for Div. 2)

#### <u>A. Cut the Triangle</u>

一句话题意：给了t组三角形三个点的坐标，让你判断这个三角形能否用垂直线或者水平线切割成两个三角形。

思路：如果三角形有一条竖直边和一条水平边则说明绝对无法切割，判断这个即可。

```c++
//
// Created by Toru on 2022/12/16.
// Ugly codeQAQ
#include <cstdio>
#include <iostream>
using namespace std;
int main() {
    int t,x[3],y[3],dx[3],dy[3],i,j,k,flag;
    cin>>t;
    while(t--) {
        flag=0;
        for(i=0;i<3;i++)
            cin>>x[i]>>y[i];
        dx[0]=x[0]-x[1];
        dx[1]=x[0]-x[2];
        dx[2]=x[1]-x[2];
        dy[0]=y[0]-y[1];
        dy[1]=y[0]-y[2];
        dy[2]=y[1]-y[2];
        for(i=0;i<3;i++)
        {
            if(dx[i]==0)
                flag++;
            if(dy[i]==0)
                flag++;}
        if(flag>=2)
            cout<<"NO\n";
        else
            cout<<"YES\n";
    }
}
```

#### <u>B. Block Towers</u>

一句话题意：n个元素的数组，操作为选择两个元素，数字大的-1，数字小的+1，问你经过无限制次这样的操作后，最前面的元素最大值为多少。

思路：贪心做法。将除开最前面元素的数组进行排序，从前往后遍历，如果大于最前面的元素就进行有限次操作直到小于等于最前面的元素（直接平分即可）。

```c++
//
// Created by Toru on 2022/12/16.
//
#include <cstdio>
#include <iostream>
#include <algorithm>
using namespace std;
int main() {
    int t,i,j,k,n,a[200005],b[200005],cnt,ans;
    cin>>t;
    while(t--) {
        cnt=0;
        cin>>n;
        cin>>a[1];
        for(i=2;i<=n;i++)
        {
            cin>>a[i];
            if(a[i]>a[1])
                b[++cnt]=a[i];
        }
        sort(b+1,b+cnt+1);
        for(i=1;i<=cnt;i++)
            if(a[1]<b[i])
                a[1]=(a[1]+b[i]+1)/2;
        cout<<a[1]<<endl;
    }
}
```

#### <u>C. Count Binary Strings</u>

一句话题意：给出串的长度以及每个子串需要满足的条件（有三种条件：0-无条件、1-子串全1or全0、2-子串有1有0），问你能存在多少指定长度满足条件的01串，结果取模。

思路：比赛的时候想的是将确定全1或全0的子串转成单个元素看待，记录元素和每个元素最近的不同数字的元素形成pair对，然后分开考虑，再用快速幂等方法进行连乘，结果发现pair对的关系太复杂，没办法实现（寄寄寄）。比赛之后考虑用动态规划的方式来解决，dp[i] [j]表示从头开始长度为i的子串满足条件并且第i位的值为j。对i前面的每一位进行判断其是否可以是离i最近的不同数字，如果可以，将对dp[i] [0/1]做出贡献。假设第j位是离第i位最近的不同数字，则应该满足以下两个条件：

- 不能存在包含第j位且第j位不是末尾的子串要求为全0or全1
- 不能存在j和i之间且不包括j的子串要求为串中有1有0

```c++
#include <cstdio>
#include <iostream>
#define mod 998244353
using namespace std;
int s[105][105], dp[105][2], n;
int check(int l, int r) {
    int i, j;
    for(i = r;i > l;i --) {
        for(j = i;j > l;j --) if(s[j][i] == 2) return 0;
        for(j = l;j >= 0;j --) if(s[j][i] == 1) return 0;
    }
    return 1;
}
int main() {
    int i, j, flag=0;
    cin>>n;
    for(i = 1;i <= n;i ++) {
        for(j = i;j <= n;j ++) {
            cin>>s[i][j];
            if(i == j && s[i][j] == 2)
                flag = 1;
        }
    }
    if(flag) {
        cout<<"0";
        return 0;
    }
    dp[0][1] = 1;
    dp[0][0] = 1;
    for(i = 1;i <= n;i ++) {
        for(j = i-1;j >= 0;j --) {
            if(check(j, i)) {
                dp[i][0] = (dp[i][0] + dp[j][1]) % mod;
                dp[i][1] = (dp[i][1] + dp[j][0]) % mod;
            }
        }
    }
    cout<<(dp[n][0] + dp[n][1]) % mod;
}
```

#### <u>D. Playoff</u>

一句话题意：有2<sup>n</sup>个选手（1~2<sup>n</sup>），以1v1形式进行比赛（每组选手随机分配），总共进行n轮比赛，给出一个长度为n的01串，表示各轮比赛的规则（0为数字小的获胜，1反之），让你找出最后可能获胜的数字。

思路：0代表当前一些数字最大的无法获胜，1代表当前一些数字最小的无法获胜，计算0和1的次数，找规律发现无法获胜的数量需要进行连乘，然后排除无法获胜的，确定可能获胜的范围输出即可。

```c++
//
// Created by Toru on 2022/12/17.
//
#include <cstdio>
#include <iostream>
using namespace std;
int main() {
    int n,i,j,k,s0=0,s1=0,res=1,cnt0=1,cnt1=1;
    string s;
    cin>>n;
    cin>>s;
    for(i=1;i<=n;i++)
        res*=2;
    for(i=0;i<s.length();i++) {
        if(s[i]=='0')
            s0++;
        if(s[i]=='1')
            s1++;
    }
    if(s0==n)
    {
        cout<<1;
        return 0;
    }
    if(s1==n)
    {
        cout<<res;
        return 0;
    }
    for(i=1;i<=s0;i++)
        cnt0*=2;
    for(i=1;i<=s1;i++)
        cnt1*=2;
    for(i=cnt1;i<=res-cnt0+1;i++)
        cout<<i<<" ";
}
```

### Codeforces Round #839 (Div. 3)

#### <u>D. Absolute Sorting</u>

一句话题意：给一组数列，问你是否存在一个x，使得数列中各元素值减去x的绝对值组成的数列为一个不严格递增序列。如果存在输出x的值，不存在输出-1。

思路：首先，初始数列为递增或递减将一定满足，那么考虑的就是递增递减交替出现是否满足。对每个交替点进行考虑，计算满足所有交替点要求的x的最小值（即能使交替处恢复正常的x的值），然后将x带入原数列进行计算得到新数列，若新数列满足要求输出x的值，反之不可能。

```c++
//
// Created by Toru on 2022/12/18.
//
#include <cstdio>
#include <iostream>
#include <cmath>
using namespace std;
int main() {
    int x,t,i,j,k,n,a[200005],b[200005],flag1,flag2,flag,maxx,minn,res;
    cin>>t;
    while(t--){
        cin>>n;
        flag1=0;
        flag2=0;
        flag=0;
        res=0;
        cin>>a[1];
        minn=a[1];
        maxx=a[1];
        for(i=2;i<=n;i++)
        {
            cin>>a[i];
            if(a[i]<=a[i-1])
                flag2++;
            if(a[i]>=a[i-1])
                flag1++;
            maxx=max(maxx,a[i]);
            minn=min(minn,a[i]);
        }
        if(flag1==n-1)
        {
            cout<<minn-1<<endl;
            continue;
        }
        if(flag2==n-1)
        {
            cout<<maxx+1<<endl;
            continue;
        }
        for(i=2;i<=n;i++)
            if(a[i]<a[i-1])
                res=max(res,(a[i-1]+a[i]+1)/2);
        b[1]=abs(a[1]-res);
        for(i=2;i<=n;i++)
        {
            b[i]=abs(a[i]-res);
            if(b[i]<b[i-1])
            {
                flag=1;
                break;
            }
        }
        if(flag==0)
        {
            cout<<res<<endl;
            continue;
        }
        else
        {
            cout<<"-1\n";
        }
    }
}
```

#### <u>E. Permutation Game</u>

一句话题意：略。

思路：博弈论，关键在于考虑平局情况，不赘述。

```c++
//
// Created by Toru on 2022/12/18.
//
#include <cstdio>
#include <iostream>
using namespace std;
int main() {
    int t,i,j,k,n,p[500005],a,b,s;
    cin>>t;
    while(t--)
    {
        cin>>n;
        a=0;b=0;s=0;
        for(i=1;i<=n;i++)
        {
            cin>>p[i];
            if(p[i]!=i&&p[i]!=n-i+1)
            {
                s++;
                continue;
            }
            if(p[i]!=i)
                a++;
            if(p[i]!=n-i+1)
                b++;
        }
        if(b>=a+s)
        {
            cout<<"First\n";
            continue;
        }
        if(a>b+s)
        {
            cout<<"Second\n";
            continue;
        }
        cout<<"Tie\n";
        continue;
    }
}
```

#### <u>F. Copy of a Copy of a Copy</u>

一句话题意：给出q+1张01串组成的n*m大小的图片，玩家可以进行以下两种操作：

- 当一个格子四周的格子都存在且这五个格子数字相同时，将中间的格子变成另一个数字
- 将当前的状态记录为图片

要求你找出最开始的图片，并根据这q+1张图片列出具体的操作序列。

思路：已知第一种操作会减少“+”型大格子的数量，这种操作是不可逆的，所以可以记录每张图片"+"型格子的数量并进行降序排序，就是图片拍摄的顺序，然后每两张图片之间进行对比找出第一种操作的具体位置即可。

```c++
//
// Created by Toru on 2022/12/19.
//
#include <cstdio>
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;
struct noid{
    int square;
    int id;
}e[105];
bool cmp(noid a,noid b) {
    return a.square<b.square;
}
int main() {
    int n,m,k,i,j,t,x,y,a[105][35][35]={0},res,flag;
    string str;
    cin>>n>>m>>k;
    for(i=1;i<=k+1;i++)
    {
        e[i].id=i;
        e[i].square=0;
        for(j=1;j<=n;j++)
        {
            cin>>str;
            for(t=0;t<str.length();t++)
                a[i][j][t+1]=str[t]-'0';
        }
        for(j=2;j<n;j++)
        {
            for(t=2;t<m;t++)
            {
                x=j;y=t;
                res=a[i][x][y]+a[i][x-1][y]+a[i][x+1][y]+a[i][x][y-1]+a[i][x][y+1];
                if(res%5==0)
                    e[i].square++;
            }
        }
    }
    sort(e+1,e+k+2,cmp);
    cout<<e[1].id<<endl;
    res=k;
    for(i=2;i<=k+1;i++)
    {
        for(j=2;j<n;j++)
            for(t=2;t<m;t++)
                if(a[e[i].id][j][t]!=a[e[i-1].id][j][t])
                    res++;
    }
    cout<<res<<endl;
    for(i=2;i<=k+1;i++)
    {
        for(j=2;j<n;j++)
            for(t=2;t<m;t++)
                if(a[e[i].id][j][t]!=a[e[i-1].id][j][t])
                    cout<<"1 "<<j<<" "<<t<<endl;
        cout<<"2 "<<e[i].id<<endl;
    }
}

```

#### <u>G. Gaining Rating</u>

一句话题意：给出一个含有n个元素的数列，用户初始值x和用户目标值y。每次挑选数列中的元素进行比较，若用户值大于元素值则用户值+1，反之-1。要求数列中每个元素的选取要雨露均沾（即在选择某个元素第i+1次的时候，其他元素至少都要被选择过i次），问最少经过多少次的选择，用户值能到达目标值y。

思路：数列中插入一个元素值为y方便计算。因为元素选取需要雨露均沾，干脆定义为一组数列全部选取完i次为i轮，然后计算用户最先大于每一个元素的轮数与步数，数列需要进行排序。时间复杂度大概是O(nlogn)。

```c++
//
// Created by Toru on 2022/12/22.
//
#include <cstdio>
#include <iostream>
#include <algorithm>
#define MAXN 200005
using namespace std;
int main() {
    int t, n, i, j, k;
    long long x, y, a[MAXN], round[MAXN], sum, p, np, bef, flag;
    cin>>t;
    a[0] = -1;
    while(t --) {
        cin>>n>>x>>y;
        for(i = 1;i <= n;i ++) {
            cin>>a[i];
            a[i] -= x;
        }
        a[n+1] = y - x;
        sort(a+1, a+n+2);
        flag = 0;
        for(i = 1; i <= n;i ++)
            if(i - 1 < a[i]) {
                if(2 * i - 2 - n <= 0 && y > x + i - 1) flag = 1;
                break;
            }
        if(flag) {
            cout<<"-1\n";
            continue;
        }
        p = 1;
        sum = 0;
        np = 0;
        bef = 0;
        for(i = 1;i <= n + 1;i ++) {
            if((bef + i - 1) < a[i]) {
                sum = 2 * i - 2 - n;
                np = p;
                p = (a[i] - i + sum - bef) / sum + np;
                bef = bef + (p - np) * sum;
            }
            round[i] =max((p - 1) * n + a[i] - bef, 0LL);
            if(a[i] == y - x)
                break;
        }
        cout<<round[i]<<endl;
        continue;
    }
}
```

