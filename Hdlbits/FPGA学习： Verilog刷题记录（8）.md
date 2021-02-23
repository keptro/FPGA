# FPGA学习： Verilog刷题记录（8）

### 刷题网站 ： HDLBits

 ### 第3章 ： Circuits


  #### 第1大节 ：Combinational Logic

##### 第3小节 : Arithmetic Circuits

### 

+ **Half adder**
  + 题目描述：Create a half adder. A half adder adds two bits (with no carry-in) and produces a sum and carry-out.
  
  + 题目分析：实现半加器，可以用位拼接符直接赋值，或者由真值表得到 sum与a,b是异或的关系，cout与a，b是与的关系
  
    | a    | b    | sum  | cout |
    | ---- | ---- | ---- | ---- |
    | 0    | 0    | 0    | 0    |
    | 0    | 1    | 1    | 0    |
    | 1    | 0    | 1    | 0    |
    | 1    | 1    | 0    | 1    |
  
    
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input a, b,
        output cout, sum );
        
        assign {cout,sum} = a + b;
        /*
        	或
        	assign cout = a & b;
        	assign sum  = a ^ b;
        */
    
    endmodule
    
    /*官网解答*/
    无
    ```
  
+ **Full adder**
  + 题目描述：Create a full adder. A full adder adds three bits (including carry-in) and produces a sum and carry-out.

  + 题目分析：实现一个全加器，目前知道三种做法

    + 由位拼接符，直接进行赋值
    + 又真值表得出输出与输入的关系(可以看出sum与三个输入是三者异或的关系，根据卡诺图化简后cout与三个输入的关系是两两相与最后相或的关系)

    | a    | b    | cin  | sum  | cout |
    | ---- | ---- | ---- | ---- | ---- |
    | 0    | 0    | 0    | 0    | 0    |
    | 0    | 0    | 1    | 1    | 0    |
    | 0    | 1    | 0    | 1    | 0    |
    | 0    | 1    | 1    | 0    | 1    |
    | 1    | 0    | 0    | 1    | 0    |
    | 1    | 0    | 1    | 0    | 1    |
    | 1    | 1    | 0    | 0    | 1    |
    | 1    | 1    | 1    | 1    | 1    |

    + 实例化两个半加器，进行连接，变成全加器

      ![image-20210219180915428](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210219180915428.png)

  + 解答：

    ```verilog
    /*我的解答*/
    /*解法1*/
    module top_module(
    	input	a,
        input	b,
        input	cin,
        
        output	sum,
        output	cout
    );
        assign {cout,sum} = a + b + cin;
        
    endmodule
    
    /*解法2*/
    module top_module(
    	input	a,
        input	b,
        input	cin,
        
        output	sum,
        output	cout
    );
        
        assign sum  = a ^ b ^ cin;
        assign cout = a&b | a&cin | b&cin; 
        
    endmodule
    
    /*解法3*/
    
    //顶层模块
    module top_module(
    	input 	a,
        input	b,
        input 	cin,
        
        output  sum,
        output  cout
    );
        wire	h0_sum;
        wire	h0_cout;
        wire	h1_cout;
        //实例化两个半加器
        half_module half0(
            .a    (a)		,
            .b	  (b)		,
            .sum  (h0_sum)	,
            .cout (h0_cout)	
        );
        half_module half1(
            .a	  (h0_sum)	,
            .b	  (cin)		,
            .sum  (sum)		,
            .cout (h1_cout)
        );
        
        assign cout = h1_cout | h0_cout;
        
    endmodule
    
    //半加器模块
    module half_module(
    	input 	a,
        input	b,
    
        output  sum,
        output  cout
    );
        
        assign {cout,sum} = a + b;
        
    endmodule
    ```

+ **3-bit binary adder**
  + 题目描述：Now that you know how to build a [full adder](https://hdlbits.01xz.net/wiki/Fadd), make 3 instances of it to create a 3-bit binary ripple-carry adder. The adder adds two 3-bit numbers and a carry-in to produce a 3-bit sum and carry out. To encourage you to actually instantiate full adders, also output the carry-out from *each* full adder in the ripple-carry adder. cout[2] is the final carry-out from the last full adder, and is the carry-out you usually see.
  
  + 题目分析：题目要求实例化3个全加器来综合成一个行波进位加法器
  
    ![full_adder1](D:\fpga\srceenshoot\HDLBits\Arithmetic Circuits\full_adder1.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [2:0] a, b,
        input cin,
        output [2:0] cout,
        output [2:0] sum );
        
        wire	wire_cout0;
        wire	wire_cout1;
        wire	wire_cout2;
        
        full_adder full0(
            .a		(a[0])		 ,
            .b		(b[0])		 ,
            .cin	(cin)		 ,
            .cout	(wire_cout0) ,
            .sum	(sum[0])
        );
        
        full_adder full1(
            .a		(a[1])		 ,
            .b		(b[1])		 ,
            .cin	(wire_cout0) ,
            .cout	(wire_cout1) ,
            .sum	(sum[1])
        );
        
         full_adder full2(
            .a		(a[2])		 ,
            .b		(b[2])		 ,
            .cin	(wire_cout1) ,
            .cout	(wire_cout2) ,
            .sum	(sum[2])
        );
        
        assign cout = {wire_cout2,wire_cout1,wire_cout0} ;
    
    endmodule
    
    module full_adder(
    	input	a	 ,
        input	b	 ,
        input	cin  ,
        
        output	sum  ,
        output  cout
    );
        assign {cout,sum} = a + b + cin;
        
    endmodule
    
    /*官网解答*/
    无
    ```
  
+ **Adder**
  
  + 题目描述：Implement the following circuit:
  
    ![Exams m2014q4j.png](https://hdlbits.01xz.net/mw/images/d/d2/Exams_m2014q4j.png)
  
  + 题目分析：图中给了4个全加器，先实例化一个全加器后，定义3两条连接线，用于接收cout然后与下一个cin进行连接，和上题的思路差不多，不过官网答案更加简洁些
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input [3:0] x,
        input [3:0] y, 
        output [4:0] sum);
        
        wire fa1,fa2,fa3;
        full_adder FA0(
            .a	  (x[0])	,
            .b	  (y[0])    ,
            .sum  (sum[0])  ,
            .cout (fa1)		,
            .cin  ( )		//没有接任何东西，也尽量写上
        );
        
        assign {fa2,sum[1]} = x[1] + y[1] + fa1;
        assign {fa3,sum[2]} = x[2] + y[2] + fa2;
        assign {sum[4],sum[3]} = x[3] + y[3] + fa3;
        
        assign sum = {sum[4],sum[3],sum[2],sum[1],sum[0]};
        
    
    endmodule
    
    module full_adder(
    	input	a	 ,
        input	b	 ,
        input	cin  ,
        
        output  sum  ,
        output  cout 
    );
        assign {cout,sum} = a + b + cin;
        
    endmodule
    
    /*官网解答*/
    module top_module (
    	input [3:0] x,
    	input [3:0] y,
    	output [4:0] sum
    );
    
    	// This circuit is a 4-bit ripple-carry adder with carry-out.
    	assign sum = x+y;	// Verilog addition automatically produces the carry-out bit.
    
    	// Verilog quirk: Even though the value of (x+y) includes the carry-out, (x+y) is still considered to be a 4-bit number (The max width of the two operands).
    	// This is correct:
    	// assign sum = (x+y);
    	// But this is incorrect:
    	// assign sum = {x+y};	// Concatenation operator: This discards the carry-out
    endmodule
    ```
  
+ **Signed addition overflow**
  + 题目描述：Assume that you have two 8-bit 2's complement numbers, a[7:0] and b[7:0]. These numbers are added to produce s[7:0]. Also compute whether a (signed) overflow has occurred
  
  + 题目分析：这道题是考虑有符号数溢出的问题，题目中给了两个8位的有符号数，要求我们计算它们的和，以及判断是否有溢出。溢出的判断主要的方式有两种
  
    + 通过从第n位和第n-1位的进位得到。即利用符号位和数值部分的最高位的进位状态来判断结果是否溢出,通过该两位进位状态的异或结果来判断,结果为1时，表示溢出，结果为0时，表示没有溢出，下面用真值表来表示，以数值部分的最高位来说明，因为符号位的情况和数值部分的最高位相同
      + 可以这么理解 
      + 当两个加数均为0时，结果如果为0说明没有进位，如果为1只是表明后一位向前进了一位，加到了第6位，因此也没有进位。
      + 当两个加数均为1时，不管结果是什么都说明进位了。
      + 当两个加数不同时，如果结果为0则说明进位了，如果结果为1则没有进位，因为如果进位了则必须加2才能满足，而实际上只能加1，因为不可能说明进位了。
  
    | a[6] | b[6] | s[6] | df   |
    | ---- | ---- | ---- | ---- |
    | 0    | 0    | 0    | 0    |
    | 0    | 0    | 1    | 0    |
    | 0    | 1    | 0    | 1    |
    | 0    | 1    | 1    | 0    |
    | 1    | 0    | 0    | 1    |
    | 1    | 0    | 1    | 0    |
    | 1    | 1    | 0    | 1    |
    | 1    | 1    | 1    | 1    |
  
    
  
    + 通过比较输入和输出数字的符号来计算，即比较输入和输出的符号位来判断，当两个正数相加产生负结果，或两个负数相加产生正结果时，会发生有符号溢出，下面用真值表来表示
    | a[7] | b[7] | s[7] | overflow |
    | ---- | ---- | ---- | -------- |
    | 0    | 0    | 0    | 0        |
    | 0    | 0    | 1    | 1        |
    | 0    | 1    | 0    | 0        |
    | 0    | 1    | 1    | 0        |
    | 1    | 0    | 0    | 0        |
    | 1    | 0    | 1    | 0        |
    | 1    | 1    | 0    | 0        |
    | 1    | 1    | 1    | 1        |
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    
    //第一种方式
    module top_module (
        input [7:0] a,
        input [7:0] b,
        output [7:0] s,
        output overflow
    ); 
     
    	wire   df,cf;
        assign df = (((~s[6])&b[6] | (~s[6]) & a[6] | a[6] & b[6]));
        assign cf = (((~s[7])&b[7] | (~s[7]) & a[7] | a[7] & b[7]));
        assign s = a + b;
        assign overflow =  df ^ cf ; 
    
    endmodule
    
    //第二种方式
    module top_module (
        input [7:0] a,
        input [7:0] b,
        output [7:0] s,
        output overflow
    ); 
     
        assign s = a + b;
        assign overflow = (~a[7] & ~b[7] & s[7]) | (a[7] & b[7] & ~s[7]) ; 
    
    endmodule
    /*官网解答*/
    无
    ```
  
+ **100-bit binary adder**
  + 题目描述：Create a 100-bit binary adder. The adder adds two 100-bit numbers and a carry-in to produce a 100-bit sum and carry out.
  
  + 题目分析：由位拼接符，直接进行赋值
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [99:0] a, b,
        input cin,
        output cout,
        output [99:0] sum );
        
        assign {cout,sum} = a + b + cin;
    
    endmodule
    /*官网解答*/
    module top_module (
    	input [99:0] a,
    	input [99:0] b,
    	input cin,
    	output cout,
    	output [99:0] sum
    );
    
    	// The concatenation {cout, sum} is a 101-bit vector.
    	assign {cout, sum} = a+b+cin;
    
    endmodule
    
    ```
  
+ **4-digit BCD adder**
  + 题目描述：You are provided with a BCD (binary-coded decimal) one-digit adder named `bcd_fadd` that adds two BCD digits and carry-in, and produces a sum and carry-out.
  
    ```
    module bcd_fadd {
        input [3:0] a,
        input [3:0] b,
        input     cin,
        output   cout,
        output [3:0] sum );
    ```
  
    Instantiate 4 copies of `bcd_fadd` to create a 4-digit BCD ripple-carry adder. Your adder should add two 4-digit BCD numbers (packed into 16-bit vectors) and a carry-in to produce a 4-digit sum and carry out.
  
  + 题目分析：题目中给了我们一个4位的bcd全加器，我们需要实例化4个bcd全加器，然后将它们连接一起，我的想法是利用generate 和for生成，完成相连操作。如下框图所示
  
    + 其中绿色的模块不是由generate生成的
    + generate生成的模块如蓝色所示，其原理就是将其“展开”就像数组一样
    + 每个模块的cin都由前一个的cout进行提供，定义一个4位宽的连接线cc用于连接cin和cout，如图
  
    ![](D:\fpga\srceenshoot\HDLBits\Arithmetic Circuits\最后一题.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [15:0] a, b,
        input cin,
        output cout,
        output [15:0] sum );
        
        wire  [3:0]  cc;		//连接cin和前一个cout
        //实例化第一个模块
        bcd_fadd bcd_fadd_inst(
            .a 		(a[3:0]),
            .b 		(b[3:0]),
            .cin	(cin)	  ,
            .cout	(cc[0])	  ,
            .sum	(sum[3:0])
        );
        
        //由generate生成的模块
        generate 
            genvar i;
            for (i=1;i<4;++i) begin : bcd_fadd1
                bcd_fadd bcd_fadd_inst(
                    .a	  (a[(7+4*(i-1)):(4+4*(i-1))]),
                    .b	  (b[(7+4*(i-1)):(4+4*(i-1))]),
                    .cin  (cc[i-1]),
                    .cout (cc[i]),
                    .sum  (sum[(7+4*(i-1)):(4+4*(i-1))]) 
                ); 
            end
        endgenerate
        
        assign cout = cc[3];
    
    endmodule
    /*官网解答*/
    无
    ```
  
    



## 总结

+ 补码与溢出及其判断方式

  ```verilog
  1.补码加法
  符号位和数值位一起参与运算，并且结果就是最终结果(包括符号位与数值位)
  [x]补 + [y]补 = [x+y]补  两数补码的和等于两数和的补码
  2.补码减法
  [x]补 - [y]补 = [x]补 + [-y]补 = [x-y]补
  3.溢出与进位
  1)溢出指带符号数的补码运算溢出，用来判断带符号数补码运算结果是否超出补码所能表示的范围。当两个正数相加产生负结果，或两个负数相加产生正结果时，会发生有符号溢出
  	2)进位指运算结果的最高位向更高位的进位，用来判断无符号数运算结果是否超出了计算机所能表示的最大无符号数的范围
  4.判断溢出的方法
  	1)利用符号位和数值部分的最高位的进位状态来判断结果是否溢出,通过该两位进位状态的异或结果来判断,结果为1时，表示溢出，结果为0时，表示没有溢出
  	2)通过参与运算的两个数的符号及运算结果的符号进行判断
  ```

+ 加法器的生成

  ```verilog
  1. 可以直接由简单的逻辑门生成
  	1)	sum 与输入的关系是异或
  	2)  cout与输入的关系是逻辑与
  2. 可以由位拼接符+assign语句的方式直接得到(半加器，全加器)
      比如{cout,sum} = a+b 
      需要注意的是cout在高位，假设a,b,sum都是2位宽的
      a = 011,b=110 则a+b = 1 001
      当使用{}时，cout=1,sum=001
  3. 对于多位的加法器，可以通过实例化半加器或者全加器，根据需要进行连线
      
  ```

  

