# FPGA学习： Verilog刷题记录（5）

### 刷题网站 ： HDLBits

 ### 第2章 ： Verilog Language

  #### 第5节 ：More Verilog Feature

+ **Conditional ternary operator**
  + 题目描述：Given four unsigned numbers, find the minimum. Unsigned numbers can be compared with standard comparison operators (a < b). Use the conditional operator to make two-way *min* circuits, then compose a few of them to create a 4-way *min* circuit. You'll probably want some wire vectors for the intermediate results.
  
  + 题目分析：题目中给了我们4个无符号的数字，需要我们找出其中最小的数，这里定义三个中间变量（注意位宽），用三目运算符进行两两的比较，最后赋值给输出min
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input [7:0] a, b, c, d,
        output [7:0] min);//
    
        wire [7:0]	min_1,min_2,min_3;
        assign min_1 = (a>b) ? b : a;
        assign min_2 = (min_1 > c) ? c : min_1;
        assign min_3 = (min_2 > d) ? d : min_2;
        assign min = min_3;
        
    endmodule
    /*官网解答*/
    无
    ```
  
+ **Reduction operators**
  + 题目描述：Parity checking is often used as a simple method of detecting errors when transmitting data through an imperfect channel. Create a circuit that will compute a parity bit for a 8-bit byte (which will add a 9th bit to the byte). We will use "even" parity, where the parity bit is just the XOR of all 8 data bits.
  
    The *reduction* operators can do AND, OR, and XOR of the bits of a vector, producing one bit of output:
  
    ```verilog
    & a[3:0]     // AND: a[3]&a[2]&a[1]&a[0]. Equivalent to (a[3:0] == 4'hf)
    | b[3:0]     // OR:  b[3]|b[2]|b[1]|b[0]. Equivalent to (b[3:0] != 4'h0)
    ^ c[2:0]     // XOR: c[2]^c[1]^c[0]
    ```
  
    These are *unary* operators that have only one operand (similar to the NOT operators ! and ~). You can also invert the outputs of these to create NAND, NOR, and XNOR gates, e.g., `(~& d[7:0])`.
  
  + 题目分析：利用新的方式进行异或
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input [7:0] in,
        output parity); 
    
        assign parity = ^in;
        
    endmodule
    
    /*官网解答*/
    无
    ```
  
+ **Reduction : Even wider gates**
  + 题目描述：Build a combinational circuit with 100 inputs, `in[99:0]`.
  
    There are 3 outputs:
  
    - out_and: output of a 100-input AND gate.
    - out_or: output of a 100-input OR gate.
    - out_xor: output of a 100-input XOR gate.
  
  + 题目分析：接上题，利用类似的语法进行与，或，异或门的创建
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [99:0] in,
        output out_and,
        output out_or,
        output out_xor 
    );
        assign out_and  = &in;
        assign out_or   = |in;
        assign out_xor  = ^in;
    
    endmodule
    /*官网解答*/
    无
    ```
  
+ **Combinational for-loop:Vector reversal 2**
  + 题目描述：Given a 100-bit input vector [99:0], reverse its bit ordering.

  + 题目分析：给你100位的输入信号in，让你转置后赋值给out，即

    + out[99] = in[0] ,out[98] = in[1] ....,out[0] = in[99]
    + 利用一个for循环进行转置的操作

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input [99:0] in,
        output [99:0] out
    );
        
        always@(*)
            begin
                for(int i=0;i<100;++i)
                    out[i] = in[100-i-1];
            end
    
    endmodule
    /*官网解答*/
    module top_module (
    	input [99:0] in,
    	output reg [99:0] out
    );
    	
    	always @(*) begin
    		for (int i=0;i<$bits(out);i++)		// $bits() is a system function that returns the width of a signal.
    			out[i] = in[$bits(out)-i-1];	// $bits(out) is 100 because out is 100 bits wide.
    	end
    	
    endmodule
    
    ```

+ **Combinational for-loop:255-bit population count**
  + 题目描述：A "population count" circuit counts the number of '1's in an input vector. Build a population count circuit for a 255-bit input vector.
  
  + 题目分析：让你统计在in中**1**的个数，然后将结果赋值给out
  
  + 解答：
  
    ```verilog
    /*
    	看了答案，觉得自己的做法有点多此一举，并且也有警告
    	"truncated value with size 32 to match size of target"
    	原因是位宽过大给截断了，对应代码in[i] & 1这里，尽量不要只写一个数字上去
    	把位宽也写上去，否则系统会默认是32位的，所以给出了警告
    */
    /*我的解答*/
    module top_module( 
        input [254:0] in,
        output [7:0] out );
    
        always@(*) begin
            out = 0;
            for(int i=0;i<$bits(in);++i)
                out = out + (in[i] & 1);  // out = out + (in[i] & 1'b1);
        end     
    endmodule
    /*官网解答*/
    module top_module (
    	input [254:0] in,
    	output reg [7:0] out
    );
    
    	always @(*) begin	// Combinational always block
    		out = 0;
    		for (int i=0;i<255;i++)
    			out = out + in[i];
    	end
    	
    endmodule
    ```
  
+ **Generate for-loop:100-bit binary adder 2**
  + 题目描述：Create a 100-bit binary ripple-carry adder by instantiating 100 [full adders](https://hdlbits.01xz.net/wiki/Fadd). The adder adds two 100-bit numbers and a carry-in to produce a 100-bit sum and carry out. To encourage you to actually instantiate full adders, also output the carry-out from *each* full adder in the ripple-carry adder. cout[99] is the final carry-out from the last full adder, and is the carry-out you usually see.
  
  + 题目分析：让我们实例化100个全加器，然后相互连接创建一个100位的bcd纹波进位加法器
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [99:0] a, b,
        input cin,
        output [99:0] cout,
        output [99:0] sum );
        
        
        wire	[99:0] cout_var;
        assign {cout[0],sum[0]}=a[0]+b[0]+cin;
        generate
            genvar i;
            for(i=1;i<100;++i)
                begin : full
                    full_adder full_adder_inst(
        				.a(a[i]),
      				    .b(b[i]),
     			        .cin(cout[i-1]),
                        .cout(cout[i]),
                        .sum(sum[i])
                    );
                    
                end
        endgenerate
    
    endmodule
    
    module	full_adder(
    	input	wire	a,
        input	wire	b,
        input	wire	cin,
        
        output	wire	cout,
        output	wire	sum
    ); 
        assign {cout,sum} = a + b + cin;
        
    endmodule
    /*官网解答*/
    无
    ```
  
+ **Generate for-loop:100-digit BCD adder**
  + 题目描述：You are provided with a BCD one-digit adder named `bcd_fadd` that adds two BCD digits and carry-in, and produces a sum and carry-out.
  
    ```
    module bcd_fadd {
        input [3:0] a,
        input [3:0] b,
        input     cin,
        output   cout,
        output [3:0] sum );
    ```
  
    Instantiate 100 copies of `bcd_fadd` to create a 100-digit BCD ripple-carry adder. Your adder should add two 100-digit BCD numbers (packed into 400-bit vectors) and a carry-in to produce a 100-digit sum and carry out.
  
  + 题目分析：题目中给了我们一个4位的bcd全加器，我们需要实例化100个bcd全加器，然后将它们连接一起，利用generate 和for生成，完成相连操作。如下框图(以4个为例)所示
  
    + 其中绿色的模块不是由generate生成的
    + generate生成的模块如蓝色所示，其原理就是将其“展开”就像数组一样
    + 每个模块的cin都由前一个的cout进行提供，定义一个4位宽的连接线cc用于连接cin和cout，如图
  
    ![](D:\fpga\srceenshoot\HDLBits\Arithmetic Circuits\最后一题.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [399:0] a, b,
        input cin,
        output cout,
        output [399:0] sum );
        
        wire  [99:0]  cc;		//连接cin和前一个cout
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
            for (i=1;i<100;++i) begin : bcd_fadd1
                bcd_fadd bcd_fadd_inst(
                    .a	  (a[(7+4*(i-1)):(4+4*(i-1))]),
                    .b	  (b[(7+4*(i-1)):(4+4*(i-1))]),
                    .cin  (cc[i-1]),
                    .cout (cc[i]),
                    .sum  (sum[(7+4*(i-1)):(4+4*(i-1))]) 
                ); 
            end
        endgenerate
        
        assign cout = cc[99];
    
    endmodule
    
    ```
  
    

### 总结 

1. **三目运算符**

   ```verilog
   Verilog has a ternary conditional operator ( ? : ) much like C:
   
   (condition ? if_true : if_false)
   
   This can be used to choose one of two values based on condition (a mux!) on one line, without using an if-then inside a combinational always block.
   
   Examples:
   
   (0 ? 3 : 5)     // This is 5 because the condition is false.
   (sel ? b : a)   // A 2-to-1 multiplexer between a and b selected by sel.
   
   always @(posedge clk)         // A T-flip-flop.
     q <= toggle ? ~q : q;
   
   always @(*)                   // State transition logic for a one-input FSM
     case (state)
       A: next = w ? B : A;
       B: next = w ? A : B;
     endcase
   
   assign out = ena ? q : 1'bz;  // A tri-state buffer
   
   ((sel[1:0] == 2'h0) ? a :     // A 3-to-1 mux
    (sel[1:0] == 2'h1) ? b :
                         c )
   ```

   

2.  **$bits()**

   ```verilog
   $bits() 是系统里的一个函数，用来获取信号的位宽，类似于len()用来获取数组的长度
   ```

3. 

   ```verilog
   当需要每一位都进行相同的逻辑运算时，可以采用
   &[3:0],|[3:0],^[3:0]等简写
   并且可以叠加使用，比如需要实现每一位的与非，则可以这样写
   out = ~& a[2:0] 等价于 a[2] ~& a[1] ~& a[0] 
   
   & a[3:0] 等价于 a[3]&a[2]&a[1]&a[0] 等价于 (a[3:0] == 4'hf)
   相当于每一位都和1作比较，看是否相等，因为&是有0就为0，只要有一个为0，整个算式就为0，如果全为1结果为真就是1了
   
   | b[3:0] 等价于 b[3]|b[2]|b[1]|b[0] 等价于 (b[3:0] != 4'h0)
   因为“或”是有1为1，全0才为0. 所以相当于每一位都和0作比较，看是否相等，如果全为0，则结果为假，就是0了
   
   ```

   

   