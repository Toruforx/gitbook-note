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

