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

   

