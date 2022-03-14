# Data Lab

> **Abstract**
>
> 1. 整数补码表示，范围，溢出
> 2. 浮点数的IEEE标准，尤其是单精度 `float` 的 `1(s)---8-bits(exp)---23-bits(frac)` 的格式化分布
> 3. 与，或，非，异或，移位运算
> 4. u, int, float之间类型的隐式转换和显示转换，舍入(rouding)

### instructions

***

> 编程总体要求

```C++
#if 0
/*
 * Instructions to Students:
 *
 * STEP 1: Read the following instructions carefully.
 */

You will provide your solution to the Data Lab by
editing the collection of functions in this source file.

INTEGER CODING RULES:
 
  Replace the "return" statement in each function with one
  or more lines of C code that implements the function. Your code 
  must conform to the following style:
 
  int Funct(arg1, arg2, ...) {
      /* brief description of how your implementation works */
      int var1 = Expr1;
      ...
      int varM = ExprM;

      varJ = ExprJ;
      ...
      varN = ExprN;
      return ExprR;
  }

  Each "Expr" is an expression using ONLY the following:
  1. Integer constants 0 through 255 (0xFF), inclusive. You are
      not allowed to use big constants such as 0xffffffff.
  2. Function arguments and local variables (no global variables).
  3. Unary integer operations ! ~
  4. Binary integer operations & ^ | + << >>
    
  Some of the problems restrict the set of allowed operators even further.
  Each "Expr" may consist of multiple operators. You are not restricted to
  one operator per line.

  You are expressly forbidden to:
  1. Use any control constructs such as if, do, while, for, switch, etc.
  2. Define or use any macros.
  3. Define any additional functions in this file.
  4. Call any functions.
  5. Use any other operations, such as &&, ||, -, or ?:
  6. Use any form of casting.
  7. Use any data type other than int.  This implies that you
     cannot use arrays, structs, or unions.

 
  You may assume that your machine:
  1. Uses 2s complement, 32-bit representations of integers.
  2. Performs right shifts arithmetically.
  3. Has unpredictable behavior when shifting if the shift amount
     is less than 0 or greater than 31.


EXAMPLES OF ACCEPTABLE CODING STYLE:
  /*
   * pow2plus1 - returns 2^x + 1, where 0 <= x <= 31
   */
  int pow2plus1(int x) {
     /* exploit: ability of shifts to compute powers of 2 */
     return (1 << x) + 1;
  }

  /*
   * pow2plus4 - returns 2^x + 4, where 0 <= x <= 31
   */
  int pow2plus4(int x) {
     /* exploit ability of shifts to compute powers of 2 */
     int result = (1 << x);
     result += 4;
     return result;
  }

FLOATING POINT CODING RULES

For the problems that require you to implement floating-point operations,
the coding rules are less strict.  You are allowed to use looping and
conditional control.  You are allowed to use both ints and unsigneds.
You can use arbitrary integer and unsigned constants. You can use any arithmetic,
logical, or comparison operations on int or unsigned data.

You are expressly forbidden to:
  1. Define or use any macros.
  2. Define any additional functions in this file.
  3. Call any functions.
  4. Use any form of casting.
  5. Use any data type other than int or unsigned.  This means that you
     cannot use arrays, structs, or unions.
  6. Use any floating point data types, operations, or constants.


NOTES:
  1. Use the dlc (data lab checker) compiler (described in the handout) to 
     check the legality of your solutions.
  2. Each function has a maximum number of operations (integer, logical,
     or comparison) that you are allowed to use for your implementation
     of the function.  The max operator count is checked by dlc.
     Note that assignment ('=') is not counted; you may use as many of
     these as you want without penalty.
  3. Use the btest test harness to check your functions for correctness.
  4. Use the BDD checker to formally verify your functions
  5. The maximum number of ops for each function is given in the
     header comment for each function. If there are any inconsistencies 
     between the maximum ops in the writeup and in this file, consider
     this file the authoritative source.

/*
 * STEP 2: Modify the following functions according the coding rules.
 * 
 *   IMPORTANT. TO AVOID GRADING SURPRISES:
 *   1. Use the dlc compiler to check that your solutions conform
 *      to the coding rules.
 *   2. Use the BDD checker to formally verify that your solutions produce 
 *      the correct answers.
 */


#endif
```



### Experiment Steps

***

1. 阅读`bits.c`文件的注释，在函数处填入合适的代码。
2. 命令行使用`./dlc bits.c`命令，检查实验编码规范是否符合。
3. 使用`make`命令进行编译文件。
4. 使用`./btest`程序检查函数的正确性。
5. 使用`./driver.pl`获取函数正确性以及评分。**（推荐）**

### Answer

***

##### basic kownledge

* 优先级：`!` > `~` > `>> or <<` > `&(按位与)` > `^(按位异或)` > `|(按位异或)`
* gcc指令：
  * **-Wall** : 在众多的警告选项之中，最常用的就是-Wall选项。该选项能发现程序中一系列的常见错误警告
  * **-O** : -O、-O1优化编译需要更多时间，并且大型函数需要更多内存。使用-O选项，编译器会尝试减小代码尺寸减少执行时间，不执行任何需要大量编译时间的优化。 (还有O2,O3...)
  * **-o** : 生成可执行文件
  * **-lm** : `lm` 是link libm(像 sqrt(3.0); 这种调用，gcc 在编译时就可以将其替换为对应的值（gcc 优化的一部分），从而不需要链接 sqrt 所在的数学库 libm；而像 sqrt(n); 这种调用，一般无法做这种替换，因此需要链接 libm。),同理有：`lc` 是link libc，`lc` 是link libc。
  * **-m32** : sets int, long, and pointer types to 32 bits, and generates code that runs on
    any i386 system.




##### bitXor(x, y)

> 异或操作: x^y using only ~ and &

```C++
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  /* Based on the 0,1 values of x,y and its variants */
  //return x^y;
  return ~(~x&~y)&~(x&y);
}
```



##### tmin(void)

> 求int最小值 : -2^31

```C++
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  /* minimum by operation "<<" */
  return 0x1<<31;
}
```



##### isTmax(x)

> 求取int的最大值

```C++
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  int i=x+1;//(x==max)i==min;(x==-1)i==0
  x = x+i;//(x==max)x=-1;(x==-1)x==-1
  //此步得到-1那么对于-1同样会经过上面两个步骤得到-1
  x = ~x;//x==0
  i = !i;//(x==max)i==0,(x==-1)i==1;the res of ! is only 1 or 0
  x = x+i;//(x==max)x==0,(x==-1)x==1
  return !x;//(x==max)x==1,(x==-1)x==0
}
```



##### allOddBits(x)

> 奇数位 (从0记数) 均为1返回1，否则返回0

```C++
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  int standard = 0xAA + (0xAA << 8);
  standard = standard + (standard<<16);
  //int y = standard & x;
  //int z = y^standard;
  return !((standard&x)^standard);
}
```



##### negate(x)

> 求相反数

```C++
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return ~x+1;
}
```



##### isAsciiDigit(x)

> 判断是否为 ascii 码

```C++
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  /* 判断正负的方法(最高位):(x>>31)==0(+);==1(-) */
  int sign = 0x1<<31;
  int upper = ~(sign|0x39);//位运算处理减法的魅力
  int lower = ~0x30 + 1;//-48
  //判断正负,注意优先级">>">"&"
  //满足题意时upper+x与lower+x均为正,最高位为0与sign按位与后均为0
  //为负时只有一个为负,最高位为1,sign保留了最高位,则对二者作并运算
  upper = sign&(upper+x)>>31;
  lower = sign&(lower+x)>>31;
  return !(upper|lower);//同时为0时才为0
}
```



##### conditional(x,y,z)

> 实现三目运算符

```C++
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  /* 三目运算符,判断x是否为0即可 */
  x = !(!x);//化为布尔类型
  x = ~x+1;
  //x==1:y;x==0:z
  //用到特点为:-1=0xFFFFFFFF(全为1);0=0x00000000(全为0)
  //x==1:m = ~x+1=0xFFFFFFFF -> m&y
  //x==0:m = ~x+1=0x00000000 -> ~m&z
  return (x&y)|(~x&z);
}
```



##### isLessOrEqual(x,y)

>比大小

```C++
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  int neg_x = ~x+1;//-x
  int add = neg_x + y;//y-x,如果 x<0,那么两者相加可能溢出,因此其符号不能完全由此确定
  //判断符号
  int sign_1 = (add>>31)&1;//(>=0)0;(<0)1
  int m = 0x1<<31;
  int signx = m&x;
  int signy = m&y;//y<0则signy==0x10000000
  int sign_2 = (signx^signy)>>31&1;//同号:0;异号:1
  //(sign_2==1看y符号signy):(sign_2&signy>>31)
  //(sign_2==0看sign_1):(!sign_2&!sign_1)
  return (!sign_2&!sign_1)|(sign_2&(!(signy>>31)));
}
```



##### logicalNeg(x)

>求解逻辑正负 (0 : return 1; other : return 0)

```C++
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  /* 除了0,x和 ~x+1 必有一个最高位为 1 */
  /* 由于TMin无对应的相反数因此sign_1==sign_1==0x80000000,但其实该特例是可通过的*/
  //if(!(x^(1<<31))) return 0;
  int X = ~x + 1;
  return ((x | X) >> 31) + 1;
}
```



##### howManyBits(x)

>求解补码表示 x (int) 所需要的最少位 (bits)

```C++
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  /* neg:111...10...(最高位只需要一个1);nonneg:000...01...(最高位只需要一个0) */
  int b16,b8,b4,b2,b1,b0;
  //if(!(x^(1<<31))) return 32;
  int sign = x>>31;//(x>=0)0x00000000;(x<0)0xFFFFFFFF
  //注意这种里&外|的if...else...等价方式
  x = (sign&~x)|(~sign&x);//如果是负数,取反;正数,保持原状,此处要考虑TMin!!!
  /*二分法*/
  b16 = !!(x>>16)<<4;//如果高16位含1则 b16 为10000(16):即算上后16位
  x>>=b16;//处理完高16位,紧接着二分法处理后16位
  b8 = !!(x>>8)<<3;//此时的高8位
  x>>=b8;
  b4 = !!(x>>4)<<2;
  x>>=b4;
  b2 = !!(x>>2)<<1;
  x>>=b2;
  b1 = !!(x>>1);
  x>>=b1;
  b0 = x;
  return b16+b8+b4+b2+b1+b0+1;//还要加上一个符号位所以+1
}
```



##### floatScale2(uf)

>已知 x (float) 的二进制码 (unsigned int 储存) 返回 2x (float) 的二进制码

```C++
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
  /* 题意理解:
   *  参数float用unsigned的形式表现
   *  结果是该float*2之后等价的unsigned的形式表现1
   *  exp:(0000 0000)非格式化:0以及其附近的数
   *      (1111 1111)frac==0(∞),frac!=0(NaN)
   *      (else)格式化的 float
   */
  //符号位(+)0x10000000;(-)0x00000000
  int sign = uf&(1<<31);
  //0x7f800000 == 011111111000...0(得到幂的八位),最后右移23位得到exp的unsigned表示
  int exp = (uf&0x7f800000)>>23;  
  //判断
  if(exp==0)
  {
    //乘以2是左移:可能变为格式化的数
    uf = (uf<<1)|sign;//带上符号
    return uf;
  }
  if(exp==255) return uf;//NaN*2==NaN;inf*2==inf
  //格式化者,exp加一
  exp++;
  //仍然要排除NaN和inf的情况
  if(exp==255) return 0x7f800000|sign;//无穷大,而不是NaN
  return (exp<<23)|(uf&0x807fffff);//覆盖exp位:10000 00001111...1
}
```



##### floatFloat2Int(uf)

>根据 float 的二进制码的等价形式转化成 int 并对边缘值进行处理

```C++
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
  /* 取整 : V = (-1)^s * M * 2^E
   *  s即最高位值
   * 格式化值: 
   *  E = exp - Bias(偏置),Bias = 2^(k-1) - 1(for float k==8)
   *  M = 1+frac(格式化)
   * 非格式化:
   *  E = 1 - Bias
   *  M = frac
   * 特殊值:
   *  NaN,INF
   */
  int s = uf>>31;//(>=0)0x00000000;(<0)0xffffffff
  int E = ((uf&0x7f800000)>>23) - 127;//阶码值:exp-Bias
  int M = (uf&0x007fffff)|0x00800000;//尾数:1+frac
  //0的情况
  if(!(uf&0x7fffffff)) return 0;
  //E>31,溢出返回给定值
  if(E>31) return 0x80000000u;
  //E<0为小数,此时返回0
  if(E<0) return 0;
  //乘 2**M
  if(E>23) M <<= (E-23);//不舍去
  else M>>= (23-E);//舍去
  //求解
  if(!((M>>31)^s)) return M;//符号相同:未溢出
  else if(M>>31) return 0x80000000u;//原正现负,溢出返回相应值
  else return ~M+1;//原负现正,由于上述计算未考虑符号位因此需要带上符号
}
```



##### floatPower2(x)

>求 2**x ,并且用 unsigned int 返回其 float 形式的等价二进制码
>
>注意要设计到格式化，非格式化(0,小数)，NaN, INF... 分段关注全面性

```C++
/* 
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4
 */
unsigned floatPower2(int x) {
  /* 求幂 : V = (-1)^s * M * 2^E, 并用unsigned表示其等价形式
   *  s即最高位值
   *  E = x
   * 格式化值: 
   *  E = exp - Bias(偏置),Bias = 2^(k-1) - 1(for float k==8)
   * -> exp = Bias + E(x) = 127+x
   *  M = 1+frac(格式化)=1
   * -> frac == 0
   * 非格式化:
   *  E = 1 - Bias(前提是解码域为全0)
   * -> 当 x==0-127==-127时非格式化(已经不适用格式化)
   *  M = frac
   * -> 当 frac == 1
   * 特殊值(按题目要求是都输出+INF):
   *  NaN : frac!=0 && exp==255即x=E=255-127=128
   *  INF : frac==0 && exp==255
   *  min : 0 00000001 00...001 (2**(-150)) --> denorm
   */
  unsigned INF = 0xff<<23;//+INF即 0 11111111(8-bits) 000...0(23-bits)
  if(x>=128) return INF;//太大
  else if(x<=-150) return 0;//太小
  else if(x>=-126)//整数
  {
    //格式化
    int exp = x + 127;
    return (exp<<23);
  }
  else//分数: -149<=x<=-127 --> exp=x+127
  {
    //非格式化的小数
    int t = x+149;//移位次数 : t --> [0,22] --> 遍历 frac位
    return (1<<t);
  }
}

```

### Summary

***

##### Scores

![data_lab_scores](https://github.com/Ganliber/CSAPP/blob/main/images/data_lab_scores.png)

##### Reflection

* 独立思考，独立动手
* 更深刻的把握`float`的存储原理
* 对`int`的溢出以及与`float`的转换的原则要了然于胸





