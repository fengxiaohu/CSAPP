
## Lab1：datalab

参照README，我们可以得知此lab主要目的是修改bit.c文件来通过测试

```makefile
***********************
The CS:APP Data Lab
Directions to Students
***********************

Your goal is to modify your copy of bits.c so that it passes all the
tests in btest without violating any of the coding guidelines.


*********
0. Files:
*********

Makefile	- Makes btest, fshow, and ishow
README		- This file
bits.c		- The file you will be modifying and handing in
bits.h		- Header file
btest.c		- The main btest program
  btest.h	- Used to build btest
  decl.c	- Used to build btest
  tests.c       - Used to build btest
  tests-header.c- Used to build btest
dlc*		- Rule checking compiler binary (data lab compiler)	 
driver.pl*	- Driver program that uses btest and dlc to autograde bits.c
Driverhdrs.pm   - Header file for optional "Beat the Prof" contest
fshow.c		- Utility for examining floating-point representations
ishow.c		- Utility for examining integer representations

***********************************************************
1. Modifying bits.c and checking it for compliance with dlc
***********************************************************

IMPORTANT: Carefully read the instructions in the bits.c file before
you start. These give the coding rules that you will need to follow if
you want full credit.

Use the dlc compiler (./dlc) to automatically check your version of
bits.c for compliance with the coding guidelines:

       unix> ./dlc bits.c

dlc returns silently if there are no problems with your code.
Otherwise it prints messages that flag any problems.  Running dlc with
the -e switch:

    	unix> ./dlc -e bits.c  

causes dlc to print counts of the number of operators used by each function.

Once you have a legal solution, you can test it for correctness using
the ./btest program.

*********************
2. Testing with btest
*********************

The Makefile in this directory compiles your version of bits.c with
additional code to create a program (or test harness) named btest.

To compile and run the btest program, type:

    unix> make btest
    unix> ./btest [optional cmd line args]

You will need to recompile btest each time you change your bits.c
program. When moving from one platform to another, you will want to
get rid of the old version of btest and generate a new one.  Use the
commands:

    unix> make clean
    unix> make btest

Btest tests your code for correctness by running millions of test
cases on each function.  It tests wide swaths around well known corner
cases such as Tmin and zero for integer puzzles, and zero, inf, and
the boundary between denormalized and normalized numbers for floating
point puzzles. When btest detects an error in one of your functions,
it prints out the test that failed, the incorrect result, and the
expected result, and then terminates the testing for that function.

Here are the command line options for btest:

  unix> ./btest -h
  Usage: ./btest [-hg] [-r <n>] [-f <name> [-1|-2|-3 <val>]*] [-T <time limit>]
    -1 <val>  Specify first function argument
    -2 <val>  Specify second function argument
    -3 <val>  Specify third function argument
    -f <name> Test only the named function
    -g        Format output for autograding with no error messages
    -h        Print this message
    -r <n>    Give uniform weight of n for all problems
    -T <lim>  Set timeout limit to lim

Examples:

  Test all functions for correctness and print out error messages:
  unix> ./btest

  Test all functions in a compact form with no error messages:
  unix> ./btest -g

  Test function foo for correctness:
  unix> ./btest -f foo

  Test function foo for correctness with specific arguments:
  unix> ./btest -f foo -1 27 -2 0xf

Btest does not check your code for compliance with the coding
guidelines.  Use dlc to do that.

*******************
3. Helper Programs
*******************

We have included the ishow and fshow programs to help you decipher
integer and floating point representations respectively. Each takes a
single decimal or hex number as an argument. To build them type:

    unix> make

Example usages:

    unix> ./ishow 0x27
    Hex = 0x00000027,	Signed = 39,	Unsigned = 39

    unix> ./ishow 27
    Hex = 0x0000001b,	Signed = 27,	Unsigned = 27

    unix> ./fshow 0x15213243
    Floating point value 3.255334057e-26
    Bit Representation 0x15213243, sign = 0, exponent = 0x2a, fraction = 0x213243
    Normalized.  +1.2593463659 X 2^(-85)

    linux> ./fshow 15213243
    Floating point value 2.131829405e-38
    Bit Representation 0x00e822bb, sign = 0, exponent = 0x01, fraction = 0x6822bb
    Normalized.  +1.8135598898 X 2^(-126)


```

同时我们在阅读bit.c的开头中可以明显注意到对于编程的规则有了更为严格的界定

### 整数编码规则

- 允许使用

  ```
    1. Integer constants 0 through 255 (0xFF), inclusive. You are
        not allowed to use big constants such as 0xffffffff.
    2. Function arguments and local variables (no global variables).
    3. Unary integer operations ! ~
    4. Binary integer operations & ^ | + << >>
  ```

- 禁止使用

  ```
    You are expressly forbidden to:
    1. Use any control constructs such as if, do, while, for, switch, etc.
    2. Define or use any macros.
    3. Define any additional functions in this file.
    4. Call any functions.
    5. Use any other operations, such as &&, ||, -, or ?:
    6. Use any form of casting.
    7. Use any data type other than int.  This implies that you
       cannot use arrays, structs, or unions.
  ```

  

### 浮点数编码规则

浮点数编码规则与整数编码规则相比较而言限制更少。

```
For the problems that require you to implement floating-point operations,

the coding rules are less strict.  You are allowed to use looping and

conditional control.  You are allowed to use both ints and unsigneds.

You can use arbitrary integer and unsigned constants. You can use any arithmetic,

logical, or comparison operations on int or unsigned data.
```

禁止使用

```
 1. Define or use any macros.
  2. Define any additional functions in this file.
  3. Call any functions.
  4. Use any form of casting.
  5. Use any data type other than int or unsigned.  This means that you
     cannot use arrays, structs, or unions.
  6. Use any floating point data types, operations, or constants.
```


Lab 文件详情参考http://csapp.cs.cmu.edu/3e/datalab.pdf 

lab代码下载参考http://csapp.cs.cmu.edu/3e/labs.html。

## lab 详解

 实现XOR操作，显然根据XOR的逻辑表达式就可以推出，在使用德摩根定律进行推导
$$
\mathbf{A} \oplus \mathbf{B} = A B^{\prime}+A^{\prime} B
$$


```c
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
return ~(~x&y) & ~(~x&y);
}
```

实现int类型下最小的数值

```c
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
// Integer(32 bit) range [-2^31,2^31-1]
  return 1<<31;
}
```

判断是否为最大数值(0x7fff ffff)，去掉了位移运算符。最终我们在x=2^31-1的情况下才能返回1，0位是other。那么我们需要经过一系列的运算来判断。这里恰好可以发现x + (x+1) 恰好等于0xffff ffff的时候，题目成立。再看看还有哪些值满足以上条件但不是0x7fff ffff。0xffff ffff 排除即可。

```c
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  int y = x+1;
  x = x + (x+1);
  x = ~x;
  y = !y; // consider with the special case x: 0xffffffff
  x = x+y;
   // if x is 0xffffffff,~x must be 0
  return !(x);
}
```

检查是否所有偶数位置上的bits 等于1。

题目已经提示借助特殊的 *0xaaaaaaaa & x* 来提取出我们想要的结果。剩下的工作就是利用

```c
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
/*
hint:use 0xaaaaaaaa & x to extract the odd-numbered bits in x
*/
int allOddBits(int x) {
  int mask = (0xaa << 8) + 0xaa;
  mask = (mask<<8) + 0xaa;
  mask = (mask<<8) + 0xaa;
  int res  = mask & x;
  return res == mask;
}
```

取反操作

```c
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  
  
  return (~x+1);
}
```

判断x在'0' - '9'字符的ascii值之间。通过观察两个字符的ascii值可以看到

 *// 0x30 == 0b01100000*

 *// 0x39 == 0b01101001*

我们可以根据高低四位来分别进行判断。详细请看代码注释



```c
//3
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
 // 0x30 == 0b01100000
 // 0x39 == 0b01101001
int isAsciiDigit(int x) {

  int is_high_4digit_0x3 =!((x >>4 )^ 0x3);// high 4bit must be 0b 0011
  int low_4digit = x & 0x0f; // low bit can be 0000~1001
  int target = (low_4digit>>3)^1;// x in [0x30,0x37] and target must be 0
  int is_0x38 = !(low_4digit ^ 0x8);
  int is_0x39 = !(low_4digit ^ 0x9);
  return (is_high_4digit_0x3 &((target) | is_0x38 | is_0x39));
}
```



实现条件运算符？

```c
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
// x != 0: mask should be 0xffffffff,return mask & y
// x == 0: mask should be 0x00000000,return ~mask & y
int conditional(int x, int y, int z) {
  
  int mask = !!x;
  
  mask = mask + (mask<<1);
  mask = mask + (mask <<2);
  mask = mask + (mask <<4);//mask == 0xff  
  mask = mask + (mask <<8);// mask == 0xffff
  mask = mask + (mask << 16);// mask ==0xffffffff
  
  return (mask&y) | ((~mask)&z);
}
```

实现小于等于的逻辑判断运算符。需要注意先判断符号位，接着判断数值

```c
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  /*
  x = 0x7fffffff;
  y = 0x80000000;
  */
  int res = x + (~y+1);
  int sign_bit_x = !!(x>> 31)^0;
  int sign_bit_y = !!(y >> 31)^0;
  int flag1 =  sign_bit_x & (!sign_bit_y);
  int t = x^y;
  int flag2 = (~t) & res;
  return (flag1 | ((flag2)>>31) & 1) | (!t);
  

}
```

实现逻辑非（!）操作。

```c
//4
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  int minus_x = ~x+1;
  int sign_bit_x = (x>> 31)^0;
  int sign_bit_minus_x = (minus_x>> 31)^0;
  //int is_t_min =  ((sign_bit_x ^ sign_bit_minus_x)&(x>>31))^0;
  
  
  return (!sign_bit_minus_x &(!sign_bit_x));
}
```

求值：“一个数用补码表示最少需要几位

```c
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
  int mask = x >>31;
  if(x) mask = x >>31;
  x = (~x & mask) | (x & (~mask));
  int bit_16 = (!!(x>>16)) << 4;
  x = x>>bit_16;
  int bit_8 = (!!(x>>8)^0) << 3;
  x = x>>bit_8;
  int bit_4 = (!!(x>>4)^0) << 2;
  x = x >> bit_4;
  int bit_2 = (!!(x>>2)^0) << 1;
  x = x >>bit_2;
  int bit_1 = (!!(x>>1)^0);
  x = x>>bit_1;
  int bit_0 = x;
  int sig_bit = 1;
  return bit_16+bit_8+bit_4+bit_2+bit_1+bit_0+sig_bit;

  
}
```

求浮点数的乘法。2*f。我们需要review课本来观察浮点数存储结构。

浮点数以32bit形式存储。分为三个部分

sign符号位1bit 30-31。

指数位8bit 23-30。

mantissa 尾数位 23bit。

不考虑特殊情况下我们只需要考虑对于对于8bit的指数为+1就可以。特殊情况包括正负无穷以及小数的情况。

```c
//float
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
  int sign = uf >>31;
  int exp = (uf >> 23) & 0xff;
  int frac = uf & 0x7fffff;
  int res;
  if(exp == 0xff)//NAN case 
  {
    return uf;
  }
  else if(exp == 0)
  {
    frac <<=1;//corner case:frac == 1...0;
    res = sign<<31 | exp<<23 | frac;
  }
  else
  {
    ++exp;
    res = sign<<31 | exp<<23 | frac;
  }
  return res;

}
```

 如何进行取整操作。也就是将一个float 型转换为int型。显然通过提示我们可以知道有以下几种情况。

1. 超出了int类型的最大范围2^31-1，也就是exp大于31的情况下是很常见的。浮点数的核心思想就是通过牺牲一定的精度来换取更大的数字表示范围。 Return NAN

2. 负数直接return 0。

   

   

```c
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
  //uf = 0x0;
  int sign = uf >>31;
  int exp = ((uf >> 23) & 0xff);
  int frac = uf & 0x7fffff;
  int E = exp-127;
  if(E > 31) return 0x80000000u;
  else if(E<0) return 0;
  else
  {
    frac = frac | (1<<23); // 对于尾数进行+1操作，默认浮点数表示法中1.X中的1默认被忽略。
    if(E<23)//1.M * 2^E 不足23位需要补齐
    {
      frac >>= (23-E);
    }
    else
    {
      frac = frac <<(E-23);// 尾数超出了23位，需要进行截断
    }
  } 
  if(sign>0) frac = 0-frac; 
  return frac;
}
```



https://th.bing.com/th/id/OIP.aRI8F4p0-emtfYUzECwW0AHaA8?w=365&h=53&c=7&r=0&o=5&dpr=2&pid=1.7

求解2.0^x，也就是幂的操作。

```c
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
  if (x > 127) //too large, return +INF
  {
    return (0xFF << 23);
  }
  else if (x < -148) //too small, return 0
  {
    return 0;
  }
  else if (x >= -126) //norm，计算exp
  {
    int exp = x + 127;
    return (exp << 23);
  }
  else //denorm，令frac中某一位为1
  {
    int t = 148 + x;
    return (1 << t);
  }

}
```



## 测试代码review-如何写好测试框架

测试代码参考 datalab-handout/btest.c

main 处理不同种类的输入，通过两个系统函数getopt()与strup()来处理。

https://man7.org/linux/man-pages/man3/getopt.3.html



[getopt()]: https://man7.org/linux/man-pages/man3/getopt.3.html
[strup()]: https://man7.org/linux/man-pages/man3/strdup.3.html

基本的测试结构体(btest.h).

函数指针

```c
/* Declare different function types */
typedef int (*funct_t) (void);
typedef int (*funct1_t)(int);
typedef int (*funct2_t)(int, int); 
typedef int (*funct3_t)(int, int, int); 
```

结构体声明

```c
typedef struct {
    char *name;             /* String name */
    funct_t solution_funct; /* Function */
    funct_t test_funct;     /* Test function */
    int args;               /* Number of function arguments */
    char *ops;              /* List of legal operators. Special case: "$" for floating point */
    int op_limit;           /* Max number of ops allowed in solution */
    int rating;             /* Problem rating (1 -- 4) */
    int arg_ranges[3][2];   /* Argument ranges. Always defined for 3 args, even if */
                            /* the function takes fewer. Special case: First arg */
			    /* must be set to {1,1} for f.p. puzzles */
} test_rec, *test_ptr;

```



```c
 
int main(int argc, char *argv[])
{
    char c;

    /* parse command line args */
    while ((c = getopt(argc, argv, "hgf:r:T:1:2:3:")) != -1)
        switch (c) {
        case 'h': /* help */
	    usage(argv[0]);
	    break;
	case 'g': /* grading option for autograder */
	    grade = 1;
	    break;
	case 'f': /* test only one function */
	    test_fname = strdup(optarg);
	    break;
	case 'r': /* set global rating for each problem */
	    global_rating = atoi(optarg);
	    if (global_rating < 0)
		usage(argv[0]);
	    break;
	case '1': /* Get first argument */
	    has_arg[0] = get_num_val(optarg, &argval[0]);
	    if (!has_arg[0]) {
		printf("Bad argument '%s'\n", optarg);
		exit(0);
	    }
	    break;
	case '2': /* Get first argument */
	    has_arg[1] = get_num_val(optarg, &argval[1]);
	    if (!has_arg[1]) {
		printf("Bad argument '%s'\n", optarg);
		exit(0);
	    }
	    break;
	case '3': /* Get first argument */
	    has_arg[2] = get_num_val(optarg, &argval[2]);
	    if (!has_arg[2]) {
		printf("Bad argument '%s'\n", optarg);
		exit(0);
	    }
	    break;
	case 'T': /* Set timeout limit */
	    timeout_limit = atoi(optarg);
	    break;
	default:
	    usage(argv[0]);
	}

    if (timeout_limit > 0) {
	Signal(SIGALRM, timeout_handler);
    }

    /* test each function */
    run_tests();

    return 0;
}

```

通过run_tests() 来跑测试函数。我们可以注意到一些事先编写好的测试用例是定义在del.c里面的。同样这也是符合test_rec 所声明的上述结构体。

```c
test_rec test_set[] = {
//1




 {"bitXor", (funct_t) bitXor, (funct_t) test_bitXor, 2, "& ~", 14, 1,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
 {"tmin", (funct_t) tmin, (funct_t) test_tmin, 0, "! ~ & ^ | + << >>", 4, 1,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
//2
 {"isTmax", (funct_t) isTmax, (funct_t) test_isTmax, 1, "! ~ & ^ | +", 10, 1,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
 {"allOddBits", (funct_t) allOddBits, (funct_t) test_allOddBits, 1,
    "! ~ & ^ | + << >>", 12, 2,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
 {"negate", (funct_t) negate, (funct_t) test_negate, 1,
    "! ~ & ^ | + << >>", 5, 2,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
//3
 {"isAsciiDigit", (funct_t) isAsciiDigit, (funct_t) test_isAsciiDigit, 1,
    "! ~ & ^ | + << >>", 15, 3,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
 {"conditional", (funct_t) conditional, (funct_t) test_conditional, 3, "! ~ & ^ | << >>", 16, 3,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
 {"isLessOrEqual", (funct_t) isLessOrEqual, (funct_t) test_isLessOrEqual, 2,
    "! ~ & ^ | + << >>", 24, 3,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
//4
 {"logicalNeg", (funct_t) logicalNeg, (funct_t) test_logicalNeg, 1,
    "~ & ^ | + << >>", 12, 4,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
 {"howManyBits", (funct_t) howManyBits, (funct_t) test_howManyBits, 1, "! ~ & ^ | + << >>", 90, 4,
  {{TMin, TMax},{TMin,TMax},{TMin,TMax}}},
//float
 {"floatScale2", (funct_t) floatScale2, (funct_t) test_floatScale2, 1,
    "$", 30, 4,
     {{1, 1},{1,1},{1,1}}},
 {"floatFloat2Int", (funct_t) floatFloat2Int, (funct_t) test_floatFloat2Int, 1,
    "$", 30, 4,
     {{1, 1},{1,1},{1,1}}},
 {"floatPower2", (funct_t) floatPower2, (funct_t) test_floatPower2, 1,
    "$", 30, 4,
     {{1, 1},{1,1},{1,1}}},
  {"", NULL, NULL, 0, "", 0, 0,
   {{0, 0},{0,0},{0,0}}}
};

```

通过判断test_set[i].name 与我们通过-f输入的函数名做对比来

```c
/* 
 * run_tests - Run series of tests.  Return number of errors 
 */ 
static int run_tests() 
{
    int i;
    int errors = 0;
    double points = 0.0;
    double max_points = 0.0;

    printf("Score\tRating\tErrors\tFunction\n");

    for (i = 0; test_set[i].solution_funct; i++) {
	int terrors;
	double tscore;
	double tpoints;
	if (!test_fname || strcmp(test_set[i].name,test_fname) == 0) {
	    int rating = global_rating ? global_rating : test_set[i].rating;
	    terrors = test_function(&test_set[i]);/*select test function */
	    errors += terrors;
	    tscore = terrors == 0 ? 1.0 : 0.0;
	    tpoints = rating * tscore;
	    points += tpoints;
	    max_points += rating;

	    if (grade || terrors < 1)
		printf(" %.0f\t%d\t%d\t%s\n", 
		       tpoints, rating, terrors, test_set[i].name);

	}
    }

    printf("Total points: %.0f/%.0f\n", points, max_points);
    return errors;
}
```

