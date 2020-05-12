## 【Fibonacci数列】 斐波那契数列

> 本文均是本人一个一个字打出来的，没有复制粘贴别人的东西，手打的东西也没有直接抄书或者抄别人的内容，都是看过之后用自己的话重新书写出来的。代码也都是自己写的，经过测试的。



全文目录：

[TOC]

### Fibonacci数列定义

Fibonacci数列的通常定义

**定义一：**
$$
fib(n)=
\begin{cases} 
n\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad(若n<=1)\\ 
fib(n-1)+fib(n-2)\quad\quad\quad(若n>=2)
\end{cases}
$$
由这个定义，可以得到

| 下标n  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | .......  |
| ------ | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | -------- |
| fib(n) | 0    | 1    | 1    | 2    | 3    | 5    | 8    | 13   | 21   | 34   | ........ |

即：fib(0) = 0, fib(1) = 1, fib(2) = 1, fib(3) = 2    ............



在另外一些书上，Fibonacci数列是这样定义的

**定义二：**
$$
fib(n)=
\begin{cases} 
1\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad(若n=0,1)\\ 
fib(n-1)+fib(n-2)\quad\quad\quad(若n>=2)
\end{cases}
$$


同样由这个定义，可以得到

| 下标n  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | .......  |
| ------ | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | -------- |
| fib(n) | 1    | 1    | 2    | 3    | 5    | 8    | 13   | 21   | 34   | 55   | ........ |

定义二和定义一对  f(0)的初始值不一样，定义一中 f(0) = 0,定义二中f(0) = 1,所以定义一相比定义二要推迟1个单位。但是作为Fabonacci它们基本的核心定义是一样的也即 fib(n) = fib(n-1) + fib(n-2)



------



下面都是基于【 定义一】 来做的讨论，保持与邓俊辉版本的《数据结构》一致

### **二分递归**

根据定义可以知道，Fibonacci数列实际上是一个递归定义，要计算f(n)就要知道f(n-1)和f(n-2)，要计算f(n-1),就要知道f(n-2)和f(n-3)，以此类推，而边界就是我们一开始给定的  fib(0) = 0, fib(1) = 1,

以fib(6)为例，下面是计算fib(6)展开的递归树

```mermaid
graph TD
   C[fib6]
   C-->D[fib5]
   C -->E[fib4]
   D-->F[fib4]
   D-->G[fib3]
   E-->H[fib3]
   E-->I[fib2]
   F-->F1[fib3] 
   F-->F2[fib2]
   G-->G1[fib2]
   G-->G2[fib1]
   H-->H1[fib2]
   H-->H2[fib1]
   I-->I1[fib1]
   I-->I2[fib0]
   F1-->F11[fib2]
   F1-->F12[fib1]
   F11-->F111[fib1]
   F11-->F112[fib0]
   
   F2-->F21[fib1]
   F2-->F22[fib0]
   
   G1-->G11[fib1]
   G1-->G12[fib0]
   
   H1-->H11[fib1]
   H1-->H12[fib0]
```



由此可以得到计算Fibonacci数的递归代码：

```
int fib(int n)
{
	if(2 > n)
       return n;   //递归基
     else
        return fib(n-1) + fib(n-2);    //递推公式
}
```

这是一个二分递归，从n开始，计算每一个fib(n)时都要分支为两个计算，故 ==时间复杂度为 O（2^n）==。

空间复杂度线性正比于递归深度，故这里的==空间复杂度为O（n）==

二分递归在计算过程中所出现的递归实例的重复度极高，如上面计算fib6时，fib4重复出现了2次，fib3重复出现了3次，fib2重复出现了5次，这是致使二分递归时间复杂度达到指数级别的真正原因所在。



### **——更多优化**



#### **1.线性递归**

线性递归版本一：

Fibonacci数在计算过程中每一步并不是相互独立的，可以用到上一次的计算结果。那么考虑把中间先计算出的结果保存下来，如果当再次需要这个结果的时候先检查是否已经得到了。

```
int dp[MAXN];   //dp[n] 表示 fib(n)的结果
fill(dp,dp+MAXN,-1); //初始化dp[]的每个值为-1，dp[n] = -1表示F(n)当前还没有被计算过
int fibL(int n)
{
   if(2 > n)
      return n;
      
   if(dp[n] != -1)
   {
   	   return dp[n];
   }
    else
    {
    	dp[n] = fibL(n-1) + fibL(n-2);
        return dp[n];
    }
}
//《算法笔记》上的版本
```

这个版本的fibonacci数计算也用到了动态规划思想的方法，也即是在计算过程中记录子问题的解，来避免下次遇到相同的子问题时重复计算。这也称之为记忆搜索==时间复杂度和空间复杂度均为O(n)==

与二分递归的递归计算图相比，下面是Fibonacci数列记忆搜索示意图

```mermaid
graph TD
   C[fib6]
   C-->D[fib5]
   C -->E[fib4]
   D-->F[fib4]
   D-->G[fib3]
   F-->F1[fib3] 
   F-->F2[fib2]
   F1-->F11[fib2]
   F1-->F12[fib1]
   F11-->F111[fib1]
   F11-->F112[fib0]
```

​															图：Fibonacci数列记忆搜索示意图



线性递归版本二：

        ```
/*
函数说明：
prev作为辅助变量记录前一项，当前函数返回的是数列当前项
*/
int fibL(int n,int& prev)
{
	//递归基，直接取值
	if(0 == n)
	{
		prev = 1; //相当于初始 fib(-1) = 1 
		return 0;  // fib(0) = 1
	}
	else //深入递归计算
	{
		int prePrev;
		prev = fibL(n-1,prePrev);//递归计算前两项
		return prevPrev + prev;//其和即为正解
	}
}
//邓俊辉《数据结构》上的版本

/*
使用例子：
int prev;
fibL(6,prev);
结果：
fibL(6,prev) = 8;
prev = 5;
*/
        ```

这个版本稍微难理解点，主要是借助一个参数引用来一直记录前一项的值 。

和 fib(n) = fib(n-1) + fib(n-2) 相比，这里省略了 fib(n-2),而实际上  fib(n-2)的解答在这里借助了形式参数的机制，通过变量 prevPrev “调阅” 此前的记录直接获得。

==时间复杂度和空间复杂度均为O(n)==



#### **2.迭代（动态规划的思想）**

​      如果从 1->n  计算  fib（n）

​        fib0  +  fib1 = fib2   —>   fib1 +fib2 = fib3   —>     fib2 + fib3 = fib4    —>     fib3 + fib4 = fib5     .............

 由此可以得到迭代版代码：

```
int fibI(n)
{
	int f = 1,g =0; //相当于fib(-1) = 1,fib(0) = 0;
	while(n--)
	{
		g = g + f;  //g 变为g与f的和,也即g往前进一步
		f = g - f;  // f变为g,也即f往前进一步
	}
	return g;
}
```

不难得出==时间复杂度为O(n)，空间复杂度为 O(1)==



#### **3.快速幂矩阵求斐波那契数列**

还有一种更快的方法求斐波那契数列的方法，==时间复杂度仅为：O( logn )==，就是马上要介绍的斐波那契数列的快速幂矩阵方法

首先，由矩阵乘法，我们知道下式成立
$$
\begin{pmatrix}
\mathbf{0} & \mathbf{1}  \\
\mathbf{1} & \mathbf{1}  \\
\end{pmatrix}
\times
\begin{pmatrix}
\mathbf{fib(k-1)}   \\
\mathbf{fib(k)}  \\
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{fib(k)}   \\
\mathbf{fib(k-1)+fib(k)}  \\
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{fib(k)}   \\
\mathbf{fib(k+1)}  \\
\end{pmatrix}
$$
也即
$$
\begin{pmatrix}
\mathbf{fib(k)}   \\
\mathbf{fib(k+1)}  \\
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{0} & \mathbf{1}  \\
\mathbf{1} & \mathbf{1}  \\
\end{pmatrix}
\times
\begin{pmatrix}
\mathbf{fib(k-1)}   \\
\mathbf{fib(k)}  \\
\end{pmatrix}
$$


由此，可以进一步推导如下通项公式：
$$
\begin{pmatrix}
\mathbf{fib(n)}   \\
\mathbf{fib(n+1)}  \\
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{0} & \mathbf{1}  \\
\mathbf{1} & \mathbf{1}  \\
\end{pmatrix}
^n
\times
\begin{pmatrix}
\mathbf{fib(0)}   \\
\mathbf{fib(1)}  \\
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{0} & \mathbf{1}  \\
\mathbf{1} & \mathbf{1}  \\
\end{pmatrix}
^n
\times
\begin{pmatrix}
\mathbf{0}   \\
\mathbf{1}  \\
\end{pmatrix}
$$

$$
设：
\begin{pmatrix}
\mathbf{0} & \mathbf{1}  \\
\mathbf{1} & \mathbf{1}  \\
\end{pmatrix}
^n 
=
\begin{pmatrix}
\mathbf{a} & \mathbf{b}  \\
\mathbf{c} & \mathbf{d}  \\
\end{pmatrix}
，
则 
\begin{pmatrix} 
\mathbf{fib(n)}   \\
\mathbf{fib(n+1)}  \\ 
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{a} & \mathbf{b}  \\
\mathbf{c} & \mathbf{d}  \\
\end{pmatrix}
\times
\begin{pmatrix}
\mathbf{0}   \\
\mathbf{1}  \\
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{b}   \\
\mathbf{d}  \\
\end{pmatrix} 
$$
由此可以得到 fib(n) = b,  fib(n+1) = d, 



其中
$$
\begin{pmatrix}
\mathbf{0} & \mathbf{1}  \\
\mathbf{1} & \mathbf{1}  \\
\end{pmatrix}
^n 
\quad,矩阵乘法的计算，可以套用快速幂的算法流程,快速幂的时间复杂度仅为O(logn)
$$


将普通数的两个数的乘法运算换成两个矩阵的乘法运算，即可实现 fib(n) 的计算。更重要的是。这里仅涉及 

2 × 2 的矩阵的计算，每次同样只需要常数的时间，故==整体的运行时间也是 O ( logn )==

 

**矩阵乘法**
$$
\begin{pmatrix}
\mathbf{a}_{11} & \mathbf{a}_{12}  \\
\mathbf{a}_{21} & \mathbf{a}_{22}  \\
\end{pmatrix}
\times
\begin{pmatrix}
\mathbf{b}_{11} & \mathbf{b}_{12}  \\
\mathbf{b}_{21} & \mathbf{b}_{22}  \\
\end{pmatrix}
=
\begin{pmatrix}
\mathbf{a}_{11}\times{b}_{11}+{a}_{12}\times{b}_{21}& 
\mathbf{a}_{11}\times{b}_{12}+{a}_{12}\times{b}_{22}  \\
\mathbf{a}_{21}\times{b}_{11}+{a}_{22}\times{b}_{21} & 
\mathbf{a}_{21}\times{b}_{12}+{a}_{22}\times{b}_{22}  \\
\end{pmatrix}
$$


示例代码：

```
#include<iostream>
#include<stdio.h>
#include<string.h>
using namespace std;

//  2*2 的矩阵 
struct mat{
    int res[2][2];
    //构造函数 
    mat()
    {
        memset(res,0,sizeof(res));
    }
    //构造函数 
    mat(int a00,int a01,int a10,int a11)
    {
        res[0][0] = a00;
        res[0][1] = a01;
        res[1][0] = a10;
        res[1][1] = a11;
    }
    //深度拷贝方法 
    mat operator=(const mat& a)
    {
        for(int i=0;i<2;i++)
            for(int j=0;j<2;j++)
                res[i][j] = a.res[i][j];
        return *this;
    }
};

/*
  矩阵乘法,n维矩阵的相乘 
*/ 
mat multi(mat a,mat b,int n)
{
    mat c;
    for(int i=0;i<n;i++)//行
    {
        for(int j=0;j<n;j++)//列
        {
            for(int k=0;k<n;k++)//枚举个数
            {
                c.res[i][j]=(c.res[i][j]+a.res[i][k]*b.res[k][j]);
            }
        }
    }
    return c;
}

/*
   矩阵乘法快速幂
  ( 0 1 ) ^n
  ( 1 1 )
*/
mat matirix_pow(mat a,int n)
{
    mat pow(1,0,0,1);//单位矩阵，相当于1
    mat p = a;
    while(0 < n)
    {
        if(n&1)
            pow = multi(pow,p,2);    // pow = pow*p
        p = multi(p,p,2);   // p = p*p
        n >>= 1;   //  n = n/2
    }
    return pow;
}

/*
	用快速矩阵乘法求fibonacci数列 
	  fib(n) =  fibMat.res[0][1];
	fib(n+1) =  fibMat.res[1][1];
*/ 
int fib_matrixPow(int n)
{
	mat a(0,1,1,1);
	mat fibMat = matirix_pow(a,n);
	return fibMat.res[0][1];
} 

int main()
{
	cout<<"fib(9) = "<<fib_matrixPow(9)<<endl;
    return 0;
}
```





## 【快速幂】

快速幂就是快速算出底数的n次幂，它的复杂度为log(n),相比朴素的O(n)有了极大的提高，故因此得名

### 蛮力算法

以求 2^n^ 为例，最直接的办法就是将n个2相乘，即： 
$$
2 \times 2 \times2 \times2 \times2 \times.........{有n个2} = 2^n
$$
时间复杂度为 O(log)

```
#include<iostream>
using namespace std;

// 蛮力算法递归版 
int pow2_brute_recursive(int n)
{
	if( n == 0)
		return 1;
	return 2*pow2_brute_recursive(n-1);
	
}
// 蛮力算法迭代版 
int pow2_brute_iteration(int n)
{
	int res = 1;
	while(n--){
		res *= 2;
	}
	return res;
}

int main()
{
	cout<<"pow2_brute_recursive(8) = "<<pow2_brute_recursive(8)<<endl;
	cout<<"pow2_brute_iteration(8) = "<<pow2_brute_iteration(8)<<endl;
    return 0;
}
```



### 快速幂相乘

#### 原理：

$$
pow2(n)=
\begin{cases} 
n\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad(若n<=1)\\ 
pow2(\lfloor n/2 \rfloor)^2\times2 \quad\quad\quad\quad\quad(n>0\quad and\quad odd)\\ 
pow2(\lfloor n/2 \rfloor)^2\quad\quad\quad\quad\quad\quad\quad(n>0\quad and\quad even)
\end{cases}
$$



```
#include<iostream>
using namespace std;

/*
      快速幂，时间复杂度O(logn)
      求 2^n
*/
int square(int n)
{
    return n*n;
}
//递归版本
int pow2_recursive(int n)
{
    if(n==0)
        return 1;
    return (n&1)?square(pow2_recursive(n>>1))<<1
                 :square(pow2_recursive(n>>1));
}
//迭代版本
int pow2_iteration(int n)
{
    int pow = 1;
    int p=2;
    while(0 < n)
    {
       if(n&1)
         pow *=p;
      p *= p;
      n >>= 1;
    }
    return pow;
}

/*
    快速幂迭代版，时间复杂度O(logn)
    求 a^n
*/
int pow(int a,int n)
{
    int pow = 1;
    int p = a;
    while(0 < n)
    {
        if(n&1)
          pow *= p;
        p *= p;
        n >>= 1;
    }
    return pow;
}


int main()
{
   cout<<"pow2_recursive(5): "<<pow2_recursive(5)<<endl;
   cout<<"pow_iteration(5): "<<pow_iteration(5)<<endl;
   cout<<"pow(5,3): "<<pow(5,3)<<endl;

    return 0;
}
```





## 【栈及其应用】

栈是一种“**后进先出**”线性序列结构，和数组这种同是线性序列的结构相比，栈的一端被“封”住，元素进入和出去都只允许从某一端。

### STL 里的栈

```
#include<stack>  //要使用STL里的栈就要先引入这个头文件

template<typename T>
stack<T> sta;   //声明各种类型的栈,除了C++内置的基本类型，栈的元素也可以是自定义的结构体
具体如：
stack<int> sta;
stack<double> sta;
stack<char> sta;
stack<string> sta;
..........

stack<int> sta;
sta.push(1);  //往栈中放入一个元素,新元素成为新的栈顶元素
sta.pop();  //从栈顶弹出一个元素
sta.top();  //返回当前栈顶元素
sta.empty(); //判断栈是否为空
sta.size();  //查看当前栈中有几个元素
```





### 栈的应用

#### 1.逆序输出  —  进制转换

十进制转其他进制

如：  121~（10）~    =   1111000~(2)~

​          12345 ~(10)~   =  30071~(8)~

其他进制转十进制

如：      1111001~(2)~   =  1*2^0^ + 0 * 2^1^ + 0 * 2^2^  + 1 * 2^3^  + 1 * 2^4^ + 1 * 2^5^ + 1 * 2^6^  =  121

​                  30071~(8)~   =   1*8^0^ + 7 * 8^1^ + 0 * 8^2^  + 0 * 8^3^  + 3 * 8^4^  =  12345

推广：    任何  k 进制的数b~n~ b~n-1~ .....b~3~ b~2~ b~1~ b~0~转为十进制数

​                b~n~ b~n-1~ .....b~3~ b~2~ b~1~ b~0~   =    b~0~*k^0^ + b~1~ * k^1^ +  b~2~  * k^2^  + b~3~ * k^3^  +  .......+  b~n-1~ * k^n-1^   + b~n~ * k^n^  

十进制与其他进制相互转换示例代码如下：

```
#include<iostream>
#include<stdio.h>
#include<stack>
using namespace std;

//把十进制数转为base进制
stack<int> dCovert(int n,int base)
{
    stack<int> sta;
    while(n > 0)
    {
        sta.push(n%base);
        n /= base;
    }
    return sta;
}

//将base进制的数转为十进制数
int covertD(stack<int> sta,int base)
{
    int n = 0;
    int i;
    while(!sta.empty())
    {
        n = n*base + sta.top();
        sta.pop();
    }
    return n;
}

int main()
{
    stack<int> sta;
    sta = dCovert(120,2);
    stack<int> sta_cp = sta;
    cout<<"十进制转二进制：";
    while(!sta.empty())
    {
       cout<<sta.top();
       sta.pop();
    }
    cout<<endl;
    cout<<"二进制转十进制："<<covertD(sta_cp,2)<<endl;
    return 0;
}
```



#### 2.递归嵌套 — 栈混洗，括号匹配

##### 栈混洗

栈混洗：存在3个栈 A，B，S，其中只有栈 A 不为空，栈 B 和栈 S 都为空，现在通过某种次序将栈 A中的元素push到 B 中 ，再从 B 中push到S中，最终通过有限次的倒腾（也就是3个栈的push，pop操作），使得最后A,B栈为空，S栈不为空。我们通常称这样一个过程之为栈混洗。

先来看一个栈混洗的题目：

现有3个栈A、B、S，其中A中的元素为 [ 1 2 3 4 >  , 将A中的元素以某种次序出栈，S作为临时放的栈，最终A中元素要push到B中，请问下列哪种不可能是最终B中元素次序    （注： 符号 [ 表示栈底，> 表示栈顶）

a.  [ 1 2 3 4 >      b.  [4 3 2 1 >     c. [ 3 2 1 4 >       d. [ 2 1 4 3>

解答： a , b, c 答案都是    [ 1 2 3 4 > 可能的栈混洗；d 不可能，因为 2 1 最后位于栈底，那么 4 3 是先出栈放到临时栈S中的，即   [  4 3 > ,  从S中出栈入到B中只能是先出3，B变为[ 2 1  3 >，再出4，B变为[ 2 1  3 4 >，所以不可能是 [ 2 1 4 3>



##### 括号匹配

检查某一表达式或字符串中的括号是否匹配

如：

​			（（ 0 + （1 + 23）/4 * 5 * 67 - 8 + 9  )） 

​			 {  (  [   ]   ) } (  )

方法：从前往后扫描字符串，遇到左括号先压入栈，遇到右括号就将栈顶元素弹出，看是否与当前扫描的右括号相匹配，匹配就继续扫描，直到扫描结束，所有压入栈中的左括号都找到与之匹配的右括号而并被弹出，栈为空。否则中间任有一次不匹配都认为整个字符串括号不匹配。

括号匹配示例代码：

```
#include<stdio.h>
#include<stack>

using namespace std;
/*
	检查括号是否匹配
*/
bool braketMatch(char str[])
{
    stack<char> sta;
    char* p = str;
    while(*p != '\0')
    {
        switch(*p)
        {
           case '(':
               sta.push(*p);
            break;
            case '[':
               sta.push(*p);
            break;
            case '{':
               sta.push(*p);
            break;
            case ')':
               if(sta.top() != '(')
                  return false;  //有一次不匹配直接返回false,退出
               sta.pop();  //否则弹出当前左括号，准备进行下一轮匹配检查或者检查已结束
            break;
            case ']':
               if(sta.top() != '[')
                  return false;
               sta.pop();
            break;
            case '}':
               if(sta.top() != '{')
                  return false;
               sta.pop();
            break;
        }
        p++;
    }
    if(!sta.empty())//最后还要判断栈是否为空，不为空则有左括号没有找到匹配，则判定字符串不匹配
        return false;
    return true;//经过了层层考验终于来到这里
}

int main()
{
	char* str1 = "{ [ ()} ()";
    char* str2 = "( ( 0 + ( 1 + 23 ) /4*5*67-8+9))";
    bool match1 = braketMatch(str1);
    bool match2 = braketMatch(str2);
    if(match1)
      printf("str1: %s is matching\n",str1);
    else
         printf("str1: %s is  not matching\n",str1);

    if(match2)
      printf("str2: %s is matching\n",str2);
    else
      printf("str2: %s is not matching\n",str2);

    return 0;
}
```



#### 3.延迟缓冲 —表达式求值

中缀表达式求值

如：

​			 30 / 90 - 26 + 97 - 5 - 6 - 13 / 88 * 6 + 51 / 29 + 79 * 87 + 57 * 92

​			（（ 0 + （1 + 23）/4 * 5 * 67 - 8 + 9  ) )

不能直接从左到右直接进行计算，因为后面可能存在高优先级的运算符要比前面低优先级的运算符先进行计算。所以一可以用栈作为缓存保存前面待运算的数值和运算符，等待该它们运算的时候再取出来运算。

逻辑：

1. 运算符之间存在优先级关系，通常要事先约定好，如 * / 比 + - 要先计算
2. 从前往后扫描中缀表达式，后面的运算符因为优先级比较高可能会先计算，所以前面某些数和低优先级的运算符要先缓存，等待后面的运算完之后才能运算。
3. 一般都是有了两个数，一个运算符之后可以计算一个结果，计算的结果又可以作为等待被计算的数。
4. 由以上分析可以考虑建立两个栈，一个存放数值，另一个存放运算符。从前往后扫描，遇到数的时候直接入操作数栈，而遇到运算符先与栈顶运算符进行比较，如果栈顶运算符优先级比较高，此时栈顶运算符可以取出来运算了，同时取出操作数栈里的两个操作数一起运算，将运算结果再次存入到操作数栈中。

细节：

1. 初始时，操作数栈和运算符栈均为空，第一个运算符怎么和栈顶运算符做比较？

   答：为了实现循环的自动化，统一算法的处理流程，这里不妨初始时将先 ‘\0’也当做一个运算符 ，人为设置其第一个入栈，将 ‘\0’ 的优先级设置为最低，也就是说其他任何运算符和它比较优先级都比它高从而入栈。其实可以把‘\0’看成一个哨兵，这个哨兵在遇到其他运算符时都让它们入栈，而当扫描到表达式末尾，遇到表达式结束标志字符‘\0’时结束表达式运算。

​    2.什么时候表达式运算结束？

​		答：可以考虑扫描到字符串末尾的时候结束。或是当运算符栈为空时结束运算，前面已经把 '\0' 放入栈中，正      		常的运算符都比它优先级高，所以它不能出栈，C语言字符串都是以‘\0’结束的，所以当扫描到结束的‘\0’字符  时，让此时栈内唯一剩下的‘\0’出栈，它的使命结束了，‘\0’被出栈了，栈就为空了，也即表达式运算结束。

3. 除了‘）’，‘\0’,每个运算符都要入栈，只有入栈了，当扫描到其他运算符和它进行比较时，才有出栈运算的机会



先建立一个运算符优先级表，可以快速的找到运算符之间的关系，也避免代码中使用复杂冗余的if语句，使得代码更直观简洁高效。

运算符优先级表：

```
/*
   操作符优先级比较表
*/
                 //当前运算符   +    -    *     /   (    )  '\0'
char pri[7][7] = {
           /* 栈  +  */    {'>', '>', '<', '<', '<', '>','>'},
           /* 顶 -   */    {'>', '>', '<', '<', '<', '>','>'},
           /* 运 *   */    {'>', '>', '>', '>', '<', '>','>'},
           /* 算 /   */    {'>', '>', '>', '>', '<', '>','>'},
           /* 符 (   */    {'<', '<', '<', '<', '<', '=',' '},
           /*    )   */    {' ', ' ', ' ', ' ', ' ', ' ',' '},
           /*   '\0' */    {'<', '<', '<', '<', '<', ' ','='}

这个表什么意思？
想要这样的效果 pri[‘+’][‘*’] = ‘<’,pri[‘*’][‘/’] = ‘>’等，即栈顶运算符和当前运算符比较得到的关系，比如'+’和‘*’的到<，那么当前扫描到的运算符压栈
经过不同表达式的分析，可以得到栈顶运算符和当前运算符存在三种关系：
   关系                    操作
   <             栈顶运算符优先级低于当前运算符，当前运算符压栈
   >             栈顶运算符优先级高于当前元素运算符，栈顶运算符和对应操作数出栈 
   =             栈顶运算符优先级等同于当前元素运算符，栈顶运算符出栈
   
但是字符不能作为下标，所以先建立一个字符和数字之间的对应关系
先对  +    -    *     /   (    )  '\0' 这6个符号和 0 1 2 3 4 5 建立一个映射关系
map<char,int> opMap;
//建立运算符和下标的关系
opMap.insert(make_pair('+',0));
opMap.insert(make_pair('-',1));
opMap.insert(make_pair('*',2));
opMap.insert(make_pair('/',3));
opMap.insert(make_pair('(',4));
opMap.insert(make_pair(')',5));
opMap.insert(make_pair('\0',6));

那么 pri[opMap['+']][opMap['+']]就可以得到 '>',同理pri[opMap['+']][opMap['*']] = ‘<’

```



步骤：

1. 读入表达式，预处理去掉空格（空格位于数字和符号之间）
2. 建立运算符优先级关系表
3. 扫描表达式，分析表达式中的数字和运算符，从表达式中提取出操作数和运算符，根据运算符的优先级关系分为几种情况： ‘<’ 运算符入栈,并继续扫描； '>' 取出栈顶运算符和操作数做运算； ‘=’ 取出栈顶运算符丢弃，并继续扫描。
4. 重复步骤3直至 运算符栈为空



示例代码：

> 测试数据：
>
> 30 / 90 - 26 + 97 - 5 - 6 - 13 / 88 * 6 + 51 / 29 + 79 * 87 + 57 * 92
>
> ( (  0 + ( 1 + 23 ) / 4 * 5 * 67 - 8 + 9  )  )



中缀表达式求值示例代码：

```
#include<iostream>
#include<stack>
#include<string.h>
#include<stdio.h>
#include<iomanip>
#include<map>
#include <sstream>
using namespace std;

/*
测试用例：
30 / 90 - 26 + 97 - 5 - 6 - 13 / 88 * 6 + 51 / 29 + 79 * 87 + 57 * 92
0
*/
const int NUMOP = 7;
/*
   操作符优先级比较表
*/
              //当前运算符   +    -    *     /   (    )  '\0'
char pri[NUMOP][NUMOP] = {
               /*  +   */    {'>', '>', '<', '<', '<', '>','>'},
               /*  -   */    {'>', '>', '<', '<', '<', '>','>'},
               /*  *   */    {'>', '>', '>', '>', '<', '>','>'},
               /*  /   */    {'>', '>', '>', '>', '<', '>','>'},
               /*  (   */    {'<', '<', '<', '<', '<', '=',' '},
               /*  )   */    {' ', ' ', ' ', ' ', ' ', ' ',' '},
               /* '\0' */    {'<', '<', '<', '<', '<', ' ','='}


                        };
map<char,int> opMap;
void createOpTable()
{
    //建立运算符和下标的关系
    opMap.insert(make_pair('+',0));
    opMap.insert(make_pair('-',1));
    opMap.insert(make_pair('*',2));
    opMap.insert(make_pair('/',3));
    opMap.insert(make_pair('(',4));
    opMap.insert(make_pair(')',5));
    opMap.insert(make_pair('\0',6));
}
/*
    判断当前的字符是操作符还是数，是数则返回true,否则返回false
    并且从字符串中提取数
*/
bool readNumber(char* &sp,double &number)
{
    char num_s[9] = {0};
    if(*sp >= '0' && *sp <= '9'){
        int i=0;
        while(*sp >= '0' && *sp <= '9')
        {
            num_s[i++] = *sp;
            sp++;
        }
        sscanf(num_s,"%lf",&number);
        return true;
    }else{
        return false;
    }
}

int main()
{
    createOpTable();
    string str;
    string RPN; //逆波兰表达式

    while(getline(cin,str) && str!="0"){

        stack<double> numberStack;
        stack<char> operatorStack;
        operatorStack.push('\0');

        //删除表达式str中的空格
        for(int i=0;i<str.length();i++)
        {
            if(str[i]==' '){
                str.erase(i,1);
            }
        }
     //   cout<<"after initial:"<<str<<endl;

        char stingstr[210]={0};
        //将string转为str[]数组
        str.copy(stingstr,210);
        //设置字符指针，用来遍历表达式
        char* sp = stingstr;

        while(!operatorStack.empty())
        {
             double number;
             char op;
             if(readNumber(sp,number))
             {
                numberStack.push(number);
                cout<<"number:"<<number<<endl;
                char ss[5];
                sprintf(ss,"%d",(int)number);
                string strnumber = ss;
                RPN += strnumber;//数字直接追加到RPN后
             }
             else
             {
                op = *sp;
                char stackOp = operatorStack.top();
                cout<<"stackOp:"<<stackOp<<"  cuOp:"<<op<<endl;
                char order = pri[opMap[stackOp]][opMap[op]];
                switch(order){
                case '<':
                    operatorStack.push(op);
                    sp++;
                    break;
                case '>':{
                     double numB = numberStack.top();
                     numberStack.pop();
                     double numA = numberStack.top();
                     numberStack.pop();
                     double numSum;
                     switch(stackOp){
                        case '+':
                             numSum = numA + numB;
                             printf("%f + %f = %f\n",numA,numB,numSum);
                        break;
                        case '-':
                             numSum = numA - numB;
                             printf("%f - %f = %f\n",numA,numB,numSum);
                        break;
                        case '*':
                             numSum = numA * numB;
                             printf("%f * %f = %f\n",numA,numB,numSum);
                        break;
                        case '/':
                             numSum = numA / numB;
                             printf("%f / %f = %f\n",numA,numB,numSum);
                        break;
                     }
                     numberStack.push(numSum);
                     operatorStack.pop();
                     RPN += stackOp;//运算符该计算的时候追加到RPN后
                  }
                  break;
                case '=':
                    operatorStack.pop();
                    sp++;
                    break;
                default:
                    break;
                }
             }
        }

        if(!numberStack.empty())
        {
            cout<<"final result: "<<endl;
            cout<<fixed << setprecision(2)<<numberStack.top()<<endl;
        }
        cout<<"逆波兰表达式 RPN："<<RPN<<endl;
    }
    return 0;
}
```





#### 4.逆波兰表达式RPN

将中缀表达式转为后缀表达式即为逆波兰表达式，RPN（Reversal Polish Notation）

如：

​			中缀表达式： 30 / 90 - 26 + 97 - 5 - 6 - 13 / 88 * 6 + 51 / 29 + 79 * 87 + 57 * 92

其对应逆波兰表达式： 30 90 /  26 - 97 + 5 - 6  -13 88 / 6 * - 51 29 / + 79 87 * + 57 92 * +

​		      中缀表达式：	（（ 0 + （1 + 23）/4 * 5 * 67 - 8 + 9  ) 

其对应逆波兰表达式： 0 1 23 + 4 / 5 * 67 * + 8 - 9 +

RPN干什么的，有什么用，为什么还要有RPN？

1. RPN的好处就是每一个运算符的紧紧跟在其操作数之后，而并不需要事先定义各种运算符的优先级，只需从头到尾的一趟扫描就可以完成计算，不像中缀表达式一样需要各种条件控制逻辑，从而大大加速了计算的速度。

2. RPN中操作数的顺序和原来的中缀表达式中的顺序是一致的

   

##### 求RPN表达式计算结果

RPN计算模拟过程：

如上面所示的RPN表达式  0 1 23 + 4 / 5 * 67 * + 8 - 9 +

1. 从左往右扫描，扫描到 + ,  然后将前面两个操作数进行运算  1 + 23 = 24,结果放在原来的位置 ，即此时表达式变为
2. 0 24 4 / 5 * 67 * + 8 - 9 +
3. 继续向前扫描，扫描到 / , 然后将前面两个操作数进行运算  24 / 4 = 6 ，结果放在原来的位置 ，即此时表达式变为
4. 0 6 5 * 67 * + 8 - 9 +
5. 继续向前扫描，扫描到 *  , 然后将前面两个操作数进行运算  6 * 5 = 30 ，结果放在原来的位置 ，即此时表达式变为  0 30 67 * + 8 - 9 +
6. 继续向前扫描，扫描到 *  , 然后将前面两个操作数进行运算  6 * 5 = 30 ，结果放在原来的位置 ，即此时表达式变为  0 30 67 * + 8 - 9 +
7. 继续向前扫描，扫描到 *  , 然后将前面两个操作数进行运算  30 * 67= 2010 ，结果放在原来的位置 ，即此时表达式变为  0  2010  + 8 - 9 +
8. 继续向前扫描，扫描到 + , 然后将前面两个操作数进行运算  0 * 2010= 2010 ，结果放在原来的位置 ，即此时表达式变为    2010   8 - 9 +
9. 继续向前扫描，扫描到 - , 然后将前面两个操作数进行运算  2010 - 8 = 2002，结果放在原来的位置 ，即此时表达式变为    2002  9 +
10. 继续向前扫描，扫描到 + , 然后将前面两个操作数进行运算  2002 +  9 = 2011，结果放在原来的位置 ，即此时表达式变为    2011 ，也即最后的结果



RPN表达式求值示例代码：

```
#include<stdio.h>
#include<stack>

using namespace std;

int main()
{
    stack<double> sta;

  //  char* RPN = "0 1 23 + 4 / 5 * 67 * + 8 - 9 +";
char* RPN = "30 90 /  26 - 97 + 5 - 6  -13 88 / 6 * - 51 29 / + 79 87 * + 57 92 * +";
    char *p = RPN;
    while(*p != '\0')
    {
        if(*p >= '0' && *p <= '9'){
            double number = 0;
            while(*p != '\0' && *p >= '0' && *p <= '9')
            {
               number = number*10 + (*p - '0');
               p++;
            }
            sta.push(number);
        }else if(*p =='+' ||*p =='-'||*p =='*'||*p =='/' ){
           double tempRes;
           double numB = sta.top();
           sta.pop();
           double numA = sta.top();
           sta.pop();
           switch(*p){
            case '+':
                tempRes = numA + numB;
            break;
            case '-':
                tempRes = numA - numB;
            break;
            case '*':
                tempRes = numA * numB;
            break;
            case '/':
                tempRes = numA / numB;
            break;
           }
           sta.push(tempRes);
        }
        p++;
    }
    printf("result:%.2f\n",sta.top());
    return 0;
}
```



##### 中缀表达式转后缀表达式

在之前的表达式求值的代码中，其实也在中缀表达式求值的过程中顺便将中缀表达式转换成了后缀表达式也即RPN，详情可以见表达式求值代码注释。这里单独把中缀表达式转后缀表达式的代码拎出来。

中缀表达式转后缀表达式示例代码：

```
#include<iostream>
#include<stdio.h>
#include<stack>
#include<string>
#include<map>

using namespace std;

const int MAXN = 300;//表达式长度
const int NUMOP = 7;

/*
   操作符优先级比较表
*/
              //当前运算符   +    -    *     /   (    )  '\0'
char pri[NUMOP][NUMOP] = {
           /* 栈顶 +   */    {'>', '>', '<', '<', '<', '>','>'},
               /*  -   */    {'>', '>', '<', '<', '<', '>','>'},
               /*  *   */    {'>', '>', '>', '>', '<', '>','>'},
               /*  /   */    {'>', '>', '>', '>', '<', '>','>'},
               /*  (   */    {'<', '<', '<', '<', '<', '=',' '},
               /*  )   */    {' ', ' ', ' ', ' ', ' ', ' ',' '},
               /* '\0' */    {'<', '<', '<', '<', '<', ' ','='}


                        };
map<char,int> opMap;
void createOpTable()
{
    //建立运算符和下标的关系
    opMap.insert(make_pair('+',0));
    opMap.insert(make_pair('-',1));
    opMap.insert(make_pair('*',2));
    opMap.insert(make_pair('/',3));
    opMap.insert(make_pair('(',4));
    opMap.insert(make_pair(')',5));
    opMap.insert(make_pair('\0',6));
}
/*
    判断当前的字符是操作符还是数，是数则返回true,否则返回false
    并且从字符串中提取数
*/
bool readNumber(char* &sp,double &number)
{
    char num_s[9] = {0};
    if(*sp >= '0' && *sp <= '9'){
        int i=0;
        while(*sp >= '0' && *sp <= '9')
        {
            num_s[i++] = *sp;
            sp++;
        }
        sscanf(num_s,"%lf",&number);
        return true;
    }else{
        return false;
    }
}


/*
    中缀表达式转RPN表达式
*/
string InOrderToRPN(string str)
{
    createOpTable();
    string RPN; //逆波兰表达式

    stack<char> operatorStack;
    operatorStack.push('\0');

    //删除表达式str中的空格
    for(int i=0;i<str.length();i++)
    {
        if(str[i]==' '){
            str.erase(i,1);
        }
    }
    cout<<"str:"<<str<<endl;
    char stringstr[MAXN]={0};
    str.copy(stringstr,MAXN);

    //设置字符指针，用来遍历表达式
    char* sp = stringstr;

    while(!operatorStack.empty())
    {
         double number;
         char op;
         if(readNumber(sp,number))
         {
            char ss[5];
            sprintf(ss,"%d",(int)number);
            string strnumber = ss;
            RPN += strnumber;//数字直接追加到RPN后
         }
         else
         {
            op = *sp;
            char stackOp = operatorStack.top();
            char order = pri[opMap[stackOp]][opMap[op]];
            switch(order){
            case '<':
                operatorStack.push(op);
                sp++;
                break;
            case '>':{
                 operatorStack.pop();
                 RPN += stackOp;//运算符该计算的时候追加到RPN后
              }
              break;
            case '=':
                operatorStack.pop();
                sp++;
                break;
            default:
                break;
            }
        }
    }
    return RPN;
}

int main()
{
    string str = "( ( 0 + ( 1 + 23 ) /4*5*67-8+9))";
    cout<<"逆波兰表达式 RPN："<<InOrderToRPN(str)<<endl;
    return 0;
}
```



手工转换：

将中缀表达式：（（ 0 + （1 + 23）/4 * 5 * 67 - 8 + 9  )）  按照二叉树中序遍历还原原本的二叉树如下图所示：

```mermaid
graph TD
   C[+]
   C-->D[-]
   C -->E[9]
   D-->F[+]
   D-->G[8]
   F-->F1[0] 
   F-->F2[*]
   F2-->F11[*]
   F2-->F12[67]
   F11-->F111[/]
   F11-->F112[5]
   F111-->A1[+]
   F111-->A2[4]
   A1-->B1[1]
   A1-->B2[23]
```

有了这颗二叉树，我们就可以得到它的后序遍历： 0 1 23 + 4 / 5 * 67 * + 8 - 9 +

这个后序遍历和我们之前的逆波兰表达式是一样的，所以说给一个中序遍历的表达式，求RPN，也就是在求中序遍历对应的二叉树的后序遍历。



## 【树】



### 二叉树的创建

```
//定义二叉树结点
struct node{
    int data;
    node* lchild;
    node* rchild;
};
//创建一个二叉树结点
node* newNode(int v)
{
    node* anode = new node;
    anode->data = v;
    anode->lchild = anode->rchild = NULL;
    return anode;
}
/*
    function:往BST中插入一个结点
    这里的BST是二叉搜索树，任意结点的值大于其左孩子,小于等于其右孩子
    params:
    root为二叉树根结点
    v为要插入的二叉树结点的值
*/
void insertNode(node* &root,int v)
{
    if(root == NULL){
         root = newNode(v);
         return;
    }else if(v < root->data){
        insertNode(root->lchild,v);//递归式的往左子树搜索
    }else if(v >= root->data){
         insertNode(root->rchild,v);//递归式的往右子树搜索
    }
}

/*
    function:创建一棵二叉树
    params:
    data[]存储要插入的各个结点的值
    n为结点个数
*/
node* createBinaryTree(int data[],int n)
{
    node* root = NULL;
    for(int i=0;i<n;i++){
        insertNode(root,data[i]);
    }
    return root;
}

/*
  function:由前序遍历和中序遍历创建二叉树
  params:
  前序遍历下标:[preL,preR]
  中序遍历下标:[inL,inR]

*/
node* createByPre(int preL,int preR,int inL,int inR)
{
    node* root = new node;
    root->data = preOrder[preL];
    if(preL > preR)
        return NULL;
    int k;
    //在中序遍历中,找到前序遍历的那个节点,从而划分中序遍历为左子树和右子树
    for(k = inL;k<=inR;k++)
    {
        if(inOrder[k] == preOrder[preL])
            break;
    }
    int numLeft = k - inL;
    //左子树
    root->lchild = createByPre(preL+1,preL+numLeft,inL,k-1);
    //右子树
    root->rchild = createByPre(preL+numLeft+1,preR,k+1,inR);

    return root;
}

/*
  function:由后序遍历和中序遍历创建二叉树
  params:
  后序遍历下标:[postL,postR]
  中序遍历下标:[inL,inR]
*/
node* createByPost(int postL,int postR,int inL,int inR)
{
    node* root = new node;
    root->data = postOrder[postR];

    if(postL > postR)
        return NULL;
    int k;
    //在中序中找到后序遍历的顶点,划分左右子树
    for(k=inL;k<=inR;k++)
        if(inOrder[k] == postOrder[postR])
            break;
    int numLeft = k - inL;
    //左子树
    root->lchild = createByPost(postL,postL+numLeft-1,inL,k-1);
    //右子树
    root->rchild = createByPost(postL+numLeft,postR-1,k+1,inR);
    return root;
}
```



### 二叉树遍历

```
//前序遍历
void preTravel(node* root)
{
    if(root == NULL)
        return;
    printf(" %d",root->data);
    preTravel(root->lchild);
    preTravel(root->rchild);
}

//中序遍历
void inTravel(node* root)
{
    if(root == NULL)
        return;
    inTravel(root->lchild);
    printf(" %d",root->data);
    inTravel(root->rchild);
}

//后序遍历
void postTravel(node* root)
{
    if(root == NULL)
        return;
    postTravel(root->lchild);
    postTravel(root->rchild);
    printf(" %d",root->data);
}

//层次遍历
void layerTravel(node* root)
{
    queue<node*> treeQ;
    treeQ.push(root);
    bool first = true;
    while(!treeQ.empty())
    {
        //取出来访问
        node* now = treeQ.front();
        if(!first)
            printf(" %d",now->data);
        else{
            printf("%d",now->data);
            first =false;
        }
        treeQ.pop();
        if(now->lchild!=NULL)
            treeQ.push(now->lchild);
        if(now->rchild!=NULL)
            treeQ.push(now->rchild);
    }
    printf("\n");
}
```





### Huffman树

Huffman,中文翻译有叫赫夫曼，也有叫哈夫曼

Huffman树是以Huffman这个人名命名的，Huffman编码是Huffman树的应用

Huffman树指的是**带权路径长度(WPL)最小的二叉树**

Huffman树是Huffman为解决通讯编码压缩问题时所产生的，所以先有Huffman编码再有Huffman树



#### Huffman编码

##### 二进制编码

编码(encoding):信息被转为二进制形式的过程

解码(decoding):二进制编码恢复原始信息的过程

原始信息是由字符组成的，这些字符来自于特定的有限集合E，也称作字符集(alphbet)

而二进制形式承载的信息，都可表示为来自编码表r ={0,1}的某一特定二进制

所以可以理解为每一编码都是从字符集E到编码表r的一个单射，编码就是对信息文本中各字符逐个实施这一映射过程

如：

![image-20200324085832260](wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324085832260.png)

<img src="wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324090022013.png" alt="image-20200324090022013" style="zoom:200%;" />



**二进制编码**

编码表一旦指定好，编码就是对任意给定的文本，通过查询编码表逐一将其中的字符转译为二进制编码，这些编码依次串接起来得到了全文的编码。

比如待编码文本为 “MESSAGE”，则根据表5.1确定的编码方案,对应的二进制编码串为“ 110 01 111 111 00 10 01”

**二进制解码**

由编译器生成的二进制流经信道送到接收方后，接收方也是根据事前约定好的编码表(表5.1),依次扫描各比特位，并经匹配逐一转译出各字符，从而最终恢复出原始的文本

仍以二进制编码串“ 110 01 111 111 00 10 01”为例，其解码过程为

![image-20200324090818181](wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324090818181.png)

**解码歧义**

当编码方案确定好之后，尽管编码结果时必然确定的，但解码过程和结果却不一定唯一

如按照下述编码方案对字符集E做编码

![image-20200324091238588](wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324091238588.png)

注意，与表5.1的差别在于，字符‘M’的编码由"110"变为了“11”，此时原始文本 “MESSAGE”经编码得二进制编码串为“ 11 01 111 111 00 10 01”，但是如下图所示，解码方法却至少有两种

![image-20200324091520306](wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324091520306.png)

可以看到当遇到连续的1111.....时，既可以解码为字符M，也可以解码为字符S，所以会产生歧义(解码器就混乱了.....在风中飘) 

**前缀无歧义编码  PFC（prefix-free code）**  

解码过程之所以会出现上述歧义设置错误，**根源在于编码表指定不当**。这里的解码算法采用的是**按顺序**对信息比特流**做子串匹配**的策略，如果两个字符的编码有相同的前缀，那么就会出现匹配的歧义性，如 111111既可以匹配为MMM也可以匹配为SS。因此为消除匹配的歧义性，任何两个原始字符串所对应的二进制编码串，**相互不得是前缀。**

反过来，只要**各字符的编码串互不为前缀**，则即便是出现无法解码的错误，也绝对不致歧义。

这类编码方法即所谓的”**前缀无歧义编码”（prefix-free code），简称PFC编码**。此类编码算法可以明确地将二进制比特串，分割为一系列与各原始字符对应的片段，从而实现无歧义编码。



##### 二叉编码树

怎样对字符进行编码呢？

任一编码方案都可以描述为一颗二叉树：从根结点出发，每次向左对应一个0比特位，向右对应一个1比特位，由从根节点到每个结点的**唯一通路**，可以为各结点v赋予一个互异的二进制串，称作根通路串（root path string）,记作rps(v), |rps(v)| = depth(v) 就是结点v的深度。

![image-20200324094204871](wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324094204871.png)

如下图

rps(A) = 00,    rps(E) = 01,      rps(G) = 10,      rps(M) = 110,      rps(S) = 111

![image-20200324100602568](wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324100602568.png)



**PCF编码树**

仍以前面表5.1和表5.3所定义的编码方案分为对应于图5.9左右所示的二叉编码树

![image-20200324094445332](wrp%E7%9A%84%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B9%A6.assets/image-20200324094445332.png)

由图右所示，字符M是字符S的父亲，从而导致了解码歧义。所以，**rps(u)是rps(v)的前缀，当且仅当u是v的祖先**。

反之，只要所有字符对应与**叶结点**（没有谁是谁的祖先），歧义现象就自然消除，这也是实现PFC编码的简明策略。

**PCF解码**

依据PCF编码树可以完成对字符的编码，反过来，也可以便捷地完成编码串的解码。

操作过程：

以图5.9(a)中编码树为例，设对编码串 110 01 111 111 00 10 01 解码

起始时从树根出发，视各比特位的取值相应地向左或向右深入下一层，直到抵达叶结点。

比如，在扫描过前三位110后将抵达叶节点M，此时可以输出其对应的字符M，然后重新回到树根，并继续扫描编码串的剩余部分。再经过接下来两位01抵达叶结点E，同样输出字符E，并回到树根。如此迭代，即可无歧义地解析出原文中的所有字符。

实际上，这一解码过程甚至可以再二进制解码串接收过程中实时进行，而不必等到所有比特位都到达之后才开始，因此这类算法属于**在线算法**。

**PCF编码树的构造**

PCF编码方案可由PCF树来描述，由PCF树不仅可以快速生成编码表，而且支持高效的解码。

如何构造PCF编码树呢？

由前面的描述中，我们已经知道PCF编码树的字符位于二叉树的叶结点，其余内部结点没有明确含义

可以这样做：

例如要将 A B C D E F构成一个PCF编码树，步骤如下

1.初始时把每个叶结点都单独看成一颗只有根结点的树 ，它们属于森林集合 Forest{A,B,C,D,E}

2.选择其中的两棵进行合并为一颗树，如选择A,B合并为一颗树

3.将合并后的树重新加入Forest中，并将原A,B删除，得到  Forest{C,D,E,(A,B）}

如下图所示，对现有森林重复23步骤依次得到 Forest{D,E,F,(A,B,C)},     Forest{E,(A,B,C,D}  ,  Forest{(A,B,C,DE,)}

```mermaid
graph TD
   ora((A))
   orb((B))
   orc((C))
   ord((D))
   ore((E))
```

```mermaid
graph TD
   R((^ ))
   R-->A((A ))
   R -->B((B ))
   
   r((^ ))
   r-->A1((A ))
   r -->B1((B ))
   R1((^ ))
   R1-->r((^ ))
   R1 -->C((C ))
   
   r2((^ ))
   r2-->A2((A ))
   r2 -->B2((B ))
   R2((^ ))
   R2-->r2((^ ))
   R2 -->C2((C ))
   R3((^ ))
   R3-->R2((^ ))
   R3 -->D((D ))
   
   r3((^ ))
   r3-->A3((A ))
   r3 -->B3((B ))
   R33((^ ))
   R33-->r3((^ ))
   R33 -->C3((C ))
   R4((^ ))
   R4-->R33((^ ))
   R4 -->D2((D ))
   R5((^ ))
   R5-->R4((^ ))
   R5 -->E((E ))
   
```

实际上，上述例子之所以成立是基于这样一个事实：若字符集E1和E2之间没有公共字符，且PFC编码方案分别对应于二叉树T1和T2，则通过引入一个根结点合并T1和T2之后得到的二叉树，就是对应于E1并上E2的一种PFC编码方案。请注意，无论T1和T2的高度与规模是否相等，这一性质总是成立。



