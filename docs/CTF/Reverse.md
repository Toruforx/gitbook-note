# Reverse

## Bugku

### 入门逆向

题目链接：https://ctf.bugku.com/challenges/detail/id/99.html

思路：附件解压是一个exe，先放exeinfope查看文件脱壳和位数，再选对应ida找main函数找flag。

1. exeinfope显示文件无壳，是32位程序，因此需要使用ida32位进行分析。

   ![image-20221114134210345](C:\Users\35947\AppData\Roaming\Typora\typora-user-images\image-20221114134210345.png)

2. ida定位main函数，发现有一串mov指令，猜测可以将后面的十六进制数转为字符组成字符串。

   ![image-20221114134446187](C:\Users\35947\AppData\Roaming\Typora\typora-user-images\image-20221114134446187.png)

3. 写python脚本输出字符串。（当然ida直接R也行

   ```python
   a=['66','6C','61','67','7B','52','65','5F','31','73','5F','53','30','5F','43','30','4F','4C','7D']
   b=""
   for i in a:
       b=b+chr(int(i,16))
   print(b)
   ```

4. 答案

   ```
   flag{Re_1s_S0_C0OL}
   ```


### 游戏过关

题目链接：https://ctf.bugku.com/challenges/detail/id/116.html

思路：程序是一个游戏，如果成功解决游戏问题会返回flag值，考虑ida找flag值或者ollydbg动态调试。（也可以自给自足把游戏通关^^

1. exeinfope显示文件无壳，是32位程序，因此需要使用ida32位进行分析。

2. ida View-String查找包含flag提示的字符串，找到之后返回至该变量汇编地址，跳转到引用该变量的函数，F5生成该函数C伪代码，其实是两个数组的运算最后得到字符串。

3. 写python脚本输出字符串。

4. 答案

   ```
   zsctf{T9is_tOpic_1s_v5ry_int7resting_b6t_others_are_n0t}
   ```

5. 推荐使用ollydbg动态调试，需要掌握ASCII智能搜索，寻找flag前地址对应函数，汇编改代码并且运行直接输出flag。

### signin

题目链接：https://ctf.bugku.com/challenges/detail/id/136

思路：附件解压得到一个apk文件，所以是一道安卓逆向的题目，使用jbe进行apk逆向，查看源码大概逻辑寻找flag值。

1. jbe进行逆向分析，考虑到附件是一个账号登陆的软件，flag值的出现应该与登陆功能有关，找到MainActivity中onClick部分代码进行解析，查看java代码。
2. 分析代码发现函数是通过读入输入栏字符串与（内置字符串常量的逆的base64解码）是否一致来判断登陆成功与否，所以在resources-string.xml中查找字符串即可。
3. 找到字符串，进行翻转和base64解密。

### mountain climbing

题目链接：https://ctf.bugku.com/challenges/detail/id/125.html

关键点：脱壳、ollydbg单步调试、设计C语言程序

1. 脱壳

   - 一开始使用upx脱壳，但是由于不知名原因一直失败

   - 尝试手动脱壳，方法如下图（图源BV1aZ4y1z7zd），结果是脱了但没完全脱= =但是用来进行ida分析是够用的

     ![image-20221209135221719](../../../Program Files/Typora/img/image-20221209135221719.png)

   - 最后找了一个新工具成功脱壳（还是不理解为什么有的工具行有的不行）

2. 逆向分析

   - ida中F5生成伪代码，阅读代码发现实际上是检测一个长度为19的由L和R组成的字符串。但是实际运行exe是发现输入这样的字符串会返回error，说明我们输入的字符串最后传入程序可能出现了变更。

   - 重新阅读代码，发现存在一个无参函数，函数意思看不大懂，放入ollydbg进行单步调试看看到底干了啥（

     ![image-20221209135854651](../../../Program Files/Typora/img/image-20221209135854651.png)

   - 在ollydbg中分别给读入字符串前和读入字符串后设置断点，之后一步步单步运行，关注存储字符串的寄存器和堆栈中值的变化。

   - 经过多次单步运行，发现字符串偶数位的R会变成V，L会变成H，说明传入的字符串在经过sub_41114F()函数后会发生改变，根据规律猜测应该是存在异或之类的映射关系。

   - 解决这个问题之后继续阅读接下来的伪代码。考虑字符串每一位应该选取L还是R，这个地方巧妙地match了mountain climbing的概念，L和R指的是接下来爬的方向，而生成的数组也是山的形状，结合题目需要求最大值，说明我们需要选择爬的方向使得最后的总分最大，考虑使用深度优先搜索。

3. 代码展示

   ```c
   #include <stdio.h>
   #include <time.h>
   #include <stdlib.h>
   #include <windows.h>
   int a[2500], maxx = -1;
   char path[25], bestpath[25];
   void dfs(int x ,int y, int ans) {
       if(x == 20)
       {
           if(ans > maxx)
           {
               for(int i = 1; i < 20; i ++)
                   bestpath[i] = path[i];
               maxx = ans;
           }
           return;
       }
       path[x] = 'L';
       dfs(x+1, y, ans+a[100*x+100+y]);
       path[x] = 'R';
       dfs(x+1, y+1, ans+a[100*x+101+y]);
       return;
   }
   int main() {
       int i,j,t;
       srand(0xCu);
       for(i=1;i<=20;i++)
           for(j=1;j<=i;j++)
               a[100*i+j]=rand()%100000;
       dfs(1,1,a[101]);
       printf("max score: %d\n",maxx);
       for(i=1;i<20;i++)
           printf("%c",bestpath[i]);
       return 0;
   }
   //max score: 444740
   //RRRRRLLRRRLRLRRRLRL
   ```

4. 答案

   ```
   zsctf{RVRVRHLVRVLVLVRVLVL}
   ```

   