# FPGA学习： Verilog刷题记录（3）

### 刷题网站 ： HDLBits

 ### 第二章 ： Verilog Language

  #### 第三节 ：Module:Hierarchy

+ **Module**
  + 题目描述：实例化模块a，并将模块a和顶层模块进行连接，其中mod_a的定义如下
  
  + ```
    module mod_a ( input in1, input in2, output out );
        // Module body
    endmodule
    ```
  
  ![image-20210209121950113](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209121950113.png)
  
  + 题目分析：
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module ( input a, input b, output out );
    /*通过名字进行连接*/ 
    mod_a mod_a_inst
    (
        .in1 (a),
        .in2 (b),
            
        .out (out)
    );
    
        // 或通过位置进行连接 mod_a mod_a_inst(a,b,out); 不过需要注意顺序
        
    endmodule
    
    /*官网解答*/
    module top_module (
    	input a,
    	input b,
    	output out
    );
    
    	// Create an instance of "mod_a" named "inst1", and connect ports by name:
    	mod_a inst1 ( 
    		.in1(a), 	// Port"in1"connects to wire "a"
    		.in2(b),	// Port "in2" connects to wire "b"
    		.out(out)	// Port "out" connects to wire "out" 
    				// (Note: mod_a's port "out" is not related to top_module's wire "out". 
    				// It is simply coincidence that they have the same name)
    	);
    
    /*
    	// Create an instance of "mod_a" named "inst2", and connect ports by position:
    	mod_a inst2 ( a, b, out );	// The three wires are connected to ports in1, in2, and out, respectively.
    */
    	
    endmodule
    ```
  
    
  
+ **Connecting ports by position**
  + 题目描述：This problem is similar to the previous one ([module](https://hdlbits.01xz.net/wiki/module)). You are given a module named `mod_a` that has 2 outputs and 4 inputs, in that order. You must connect the 6 ports *by position* to your top-level module's ports `out1`, `out2`, `a`, `b`, `c`, and `d`, in that order.
  
    You are given the following module:
  
    ```verilog
    module mod_a ( output, output, input, input, input, input );
    ```
  
    ![image-20210209124314932](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209124314932.png)
  
  + 题目分析：需要我们通过位置来连接顶层模块和实例化模块，如图所示，将abcd按顺序连接即可
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module ( 
        input a, 
        input b, 
        input c,
        input d,
        output out1,
        output out2
    );
    
        mod_a mod_a_inst(out1,out2,a,b,c,d);
        
    endmodule
    /*官网解答*/
    	无
    ```
  
    
  
+ **Connecting ports by name**

  + 题目描述：This problem is similar to [module](https://hdlbits.01xz.net/wiki/module). You are given a module named `mod_a` that has 2 outputs and 4 inputs, in some order. You must connect the 6 ports *by name* to your top-level module's ports:

    | Port in `**mod_a**` | Port in `**top_module**` |
    | :------------------ | :----------------------- |
    | `output out1`       | `out1`                   |
    | `output out2`       | `out2`                   |
    | `input in1`         | `a`                      |
    | `input in2`         | `b`                      |
    | `input in3`         | `c`                      |
    | `input in4`         | `d`                      |

    You are given the following module:

    ```verilog
    module mod_a ( output out1, output out2, input in1, input in2, input in3, input in4);
    ```

    ![image-20210209124314932](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209124314932.png)

  + 题目分析：通过端口的名字进行连接，看题目中给的列表进行对应的连接即可 

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module ( 
        input a, 
        input b, 
        input c,
        input d,
        output out1,
        output out2
    );
    mod_a mod_a_inst
    (
        .in1	(a)	,
        .in2	(b)	,
        .in3	(c) ,
        .in4	(d)	,
        
        .out1	(out1),
        .out2	(out2)	
    );
    
    endmodule
    
    /*官网解答*/
    	无
    ```

    

+ **Three module**
  + 题目描述：You are given a module `my_dff` with two inputs and one output (that implements a D flip-flop). Instantiate three of them, then chain them together to make a shift register of length 3. The `clk` port needs to be connected to all instances.
  
    The module provided to you is: `module my_dff ( input clk, input d, output q );`
  
    ![image-20210209125413976](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209125413976.png)
  
  + 题目分析：题目要求就是实例化三个模块并且将它们连接起来，需要注意的是实例化模块之间的连接，中图里可以看出，两两由一条线连接，因此可以定义两个wire变量，用于内部的连线，接着按着模块连接的规则进行连接，这里我选择的是通过端口的名字进行相互的连接。
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module ( input clk, input d, output q );
    
    wire	dff12;	//定义12之间的连接线
    wire	dff23;	//定义23之间的连接线
     
    my_dff dff1_inst
    (
        .clk	    (clk)	,
        .d		    (d)		,
        .q			(dff12)
    );
    
    my_dff dff2_inst
    (
        .clk	(clk)	,
        .d		(dff12) ,		
        .q		(dff23)
    );
        
    my_dff dff3_inst
    (
        .clk	(clk)	,
        .d		(dff23) ,	
        .q		(q)	
    );
    endmodule
    /*官网解答*/
    module top_module (
    	input clk,
    	input d,
    	output q
    );
    
    	wire a, b;	// Create two wires. I called them a and b.
    
    	// Create three instances of my_dff, with three different instance names (d1, d2, and d3).
    	// Connect ports by position: ( input clk, input d, output q)
    	my_dff d1 ( clk, d, a );
    	my_dff d2 ( clk, a, b );
    	my_dff d3 ( clk, b, q );
    
    endmodule
    ```
  
    
  
+ **Modules and vectors**
  + 题目描述：You are given a module `my_dff8` with two inputs and one output (that implements a set of 8 D flip-flops). Instantiate three of them, then chain them together to make a 8-bit wide shift register of length 3. In addition, create a 4-to-1 multiplexer (not provided) that chooses what to output depending on `sel[1:0]`: The value at the input d, after the first, after the second, or after the third D flip-flop. (Essentially, `sel` selects how many cycles to delay the input, from zero to three clock cycles.)
  
    The module provided to you is:
  
     `module my_dff8 ( input clk, input [7:0] d, output [7:0] q );`
  
    
  
    ![Module_shift8](D:\fpga\srceenshoot\HDLBits\Module_shift8.png)
  
  + ![image-20210209140305685](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209140305685.png)
  
  + 题目分析：和上一个题目类似，需要我们实例化三个模块并将它们连接起来，并且要求我们写一个多路复用器，当sel为00时输出d，当sel为01时输出经过第一个触发器后的q值，以此类推。这里选择用case语句来实现。需要注意的是定义新的连接线时，要注意线位宽问题，如果没有定义则会给出警告，我一开始因为没有定义位宽一直找不到错误的原因
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module ( 
        input clk, 
        input [7:0] d, 
        input [1:0] sel, 
        output [7:0] q 
    );
        wire	[7:0] dff12;	//定义12之间的连接线  
        wire	[7:0] dff23;	//定义23之间的连接线
        wire	[7:0] dff3m;	//定义3和复用器的连接线
     
    my_dff8 dff1_inst(.clk(clk), .d(d), .q(dff12));
    my_dff8 dff2_inst(.clk(clk), .d(dff12),.q(dff23));
    my_dff8 dff3_inst(.clk(clk), .d(dff23),.q(dff3m));
    
        always@(*)	
            case(sel)
                2'b00 :	q =	d;
                2'b01 :	q = dff12;
                2'b10 : q = dff23;
                2'b11 : q = dff3m;
            endcase
    endmodule
    
    /*官网解答*/
    module top_module (
    	input clk,
    	input [7:0] d,
    	input [1:0] sel,
    	output reg [7:0] q
    );
    
    	wire [7:0] o1, o2, o3;		// output of each my_dff8
    	
    	// Instantiate three my_dff8s
    	my_dff8 d1 ( clk, d, o1 );
    	my_dff8 d2 ( clk, o1, o2 );
    	my_dff8 d3 ( clk, o2, o3 );
    
    	// This is one way to make a 4-to-1 multiplexer
    	always @(*)		// Combinational always block
    		case(sel)
    			2'h0: q = d;
    			2'h1: q = o1;
    			2'h2: q = o2;
    			2'h3: q = o3;
    		endcase
    
    endmodule
    
    ```
  
    
  
+ **Adder1**
  + 题目描述：You are given a module `add16` that performs a 16-bit addition. Instantiate two of them to create a 32-bit adder. One add16 module computes the lower 16 bits of the addition result, while the second add16 module computes the upper 16 bits of the result, after receiving the carry-out from the first adder. Your 32-bit adder does not need to handle carry-in (assume 0) or carry-out (ignored), but the internal modules need to in order to function correctly. (In other words, the `add16` module performs 16-bit a + b + cin, while your module performs 32-bit a + b).
  
    Connect the modules together as shown in the diagram below. The provided module `add16` has the following declaration:
  
    ```verilog
    module add16 ( input[15:0] a, input[15:0] b, input cin, output[15:0] sum, output cout );
    ```
  
    ![image-20210209145315274](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209145315274.png)
  
  + 题目分析：同样是加深实例化模块的题目，要求我们实例化两个加法器，加法器1对输入的低位进行运算，加法器2对输入的高位进行运算，我们需要将它们在内部进行连接，并将最后的结果赋值给顶层模块的sum
  
  + 解答：
  
    ```verilog
    module top_module(
        input [31:0] a,
        input [31:0] b,
        output [31:0] sum
    );
        wire	[0:0]	add12; //定义第一个加法器与第二个加法器的连接线
        wire	[15:0]	sum1;  //定义第一个加法器的输出线
        wire	[31:16]	sum2;  //定义第二个加法器的输出线
        add16 add16_inst1(
            .a(a[15:0])		,
            .b(b[15:0])		,
            .cout(add12)    ,
            .sum(sum1)	
        );
        add16 add16_inst2(
            .a(a[31:16])	,
            .b(b[31:16])	,
            .cin(add12)	    ,
            .sum(sum2)
        );
        assign sum   = {sum2,sum1}; //将两个输出用位拼接接起来，赋值给顶层模块的输出
       
    endmodule
    
    ```
  
    
  
+ **Adder2**
  + 题目描述：In this exercise, you will create a circuit with two levels of hierarchy. Your `top_module` will instantiate two copies of `add16` (provided), each of which will instantiate 16 copies of `add1` (which you must write). Thus, you must write *two* modules: `top_module` and `add1`. Connect the `add16` modules together as shown in the diagram below. The provided module `add16` has the following declaration：

  ![image-20210209152841180](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209152841180.png)

  In summary, there are three modules in this design:

  ` top_module` — Your top-level module that contains two of...

  ` add16`, provided — A 16-bit adder module that is composed of 16 of...

  ` add1` — A 1-bit full adder module.

  + 题目分析：emmmm似乎和上道题差不多，题目的意思大概是想让我们完成1位全加器的设计，16位的加法器是直接给出来的我们可以直接调用，实例化模块的操作和上题一样

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input [31:0] a,
        input [31:0] b,
        output [31:0] sum
    );
        
        wire	[0:0]	add12; //定义第一个加法器与第二个加法器的连接线
        wire	[15:0]	sum1;  //定义第一个加法器的输出线
        wire	[31:16]	sum2;  //定义第二个加法器的输出线
        add16 add16_inst1(
            .a(a[15:0])		,
            .b(b[15:0])		,
            .cout(add12)    ,
            .sum(sum1)	
        );
        add16 add16_inst2(
            .a(a[31:16])	,
            .b(b[31:16])	,
            .cin(add12)	    ,
            .sum(sum2)
        );
        assign sum   = {sum2,sum1}; //将两个输出用位拼接接起来，赋值给顶层模块的输出
    
    endmodule
    
    module add1 ( input a, input b, input cin,   output sum, output cout );
        assign {cout,sum} = a + b + cin;
    endmodule
    
    /*官网解答*/
  无 
    ```
    
    

+ **Carry-select adder**
  
  + 题目描述：In this exercise, you are provided with the same module `add16` as the previous exercise, which adds two 16-bit numbers with carry-in and produces a carry-out and 16-bit sum. You must instantiate *three* of these to build the carry-select adder, using your own 16-bit 2-to-1 multiplexer.
  
    Connect the modules together as shown in the diagram below. The provided module `add16` has the following declaration:
  
    ```verilog
    module add16 ( input[15:0] **a**, input[15:0] **b**, input **cin**, output[15:0] **sum**, output **cout** );
    ```
  
    ![image-20210209161132432](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209161132432.png)
  
  + 题目分析：该题同样也是实例化的应用的题目，大概意思就是让我们实例化三个加法器，然后根据图示进行连接，加入选择器而已用case语句可以完成，基本思路和前几题一样，主要需要注意连接线的位宽，看清楚图中线路是如何连接的
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input [31:0] a,
        input [31:0] b,
        output [31:0] sum
    );
        wire	[0:0]	sel  ; //定义一位的选择信号
        wire	[15:0]	sum1;  //定义第一个加法器的输出线
        wire	[31:16]	sum2;  //定义第二个加法器的输出线
        wire	[31:16]	sum3;  //定义第三个加法器的输出线
    
        add16 add16_inst1(
            .a(a[15:0])		,
            .b(b[15:0])		,
            .cout(sel)      ,
            .cin(1'b0)		,
            .sum(sum1)	
        );
        add16 add16_inst2(
            .a(a[31:16])	,
            .b(b[31:16])	,
            .cin(1'b0)	    ,
            .cout( )		,
            .sum(sum2)
        );
        add16 add16_inst3(
            .a(a[31:16])	,
            .b(b[31:16])	,
            .cout( )		,
            .cin(1'b1)		,
            .sum(sum3)		,
        );
     
        always@(*)
            case(sel)
                1'b0 : sum = {sum2,sum1}; 
                1'b1 : sum = {sum3,sum1};
            endcase
        
    endmodule
    ```
  
    
  
+ **Adder-subtractor**
  + 题目描述：Build the adder-subtractor below.
  
    You are provided with a 16-bit adder module, which you need to instantiate twice:
  
    ```verilog
    module add16 ( input[15:0] **a**, input[15:0] **b**, input **cin**, output[15:0] **sum**, output **cout** );
    ```
  
    Use a 32-bit wide XOR gate to invert the `b` input whenever `sub` is 1. (This can also be viewed as `b[31:0]` XORed with sub replicated 32 times，Also connect the `sub` input to the carry-in of the adder.
  
    ![image-20210209162718682](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210209162718682.png)
  
  + 题目分析：也是一道具体应用的题目，实例化两个加法器，然后根据图示进行编写代码即可，需要注意的是异或时位宽需要一样，也就是sub也需要是32位的，否则最后的结果是错误的，可以利用{}进行复制的操作
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input [31:0] a,
        input [31:0] b,
        input sub,
        output [31:0] sum
    );
        wire	[31:0] 	xor_wire; //定义经过xor门后的输出线   
        wire	[0:0]	add12; //定义第一个加法器与第二个加法器的连接线
        wire	[15:0]	sum1;  //定义第一个加法器的输出线
        wire	[31:16]	sum2;  //定义第二个加法器的输出线
     
        assign xor_wire = {32{sub}} ^ b;
     
        add16 add16_inst1(
            .a(a[15:0])			,
            .b(xor_wire[15:0])	,
            .cin(sub)			,
            .cout(add12)  		,
            .sum(sum1)	
        );
        add16 add16_inst2(
            .a(a[31:16])		,
            .b(xor_wire[31:16])	,
            .cin(add12)	   	    ,
            .cout( )			,
            .sum(sum2)
        );
    
        assign sum = {sum2,sum1};
    endmodule
    
    ```
  
    

### 总结 

1. **如何连接顶层模块和实例化模块**

   ```verilog
   共有两种方式：
   module mod_a ( input in1, input in2, output out );
       // Module body
   endmodule
   
   1.通过端口的位置
   这个连接方式类似与C语言函数的调用，当实例化完一个模块后，端口的连接就是从左向右按照顺序连接，例如有下面的实例化模块instance1
   
   mod_a instance1 ( wa, wb, wc );
   
   它实例化了mod_a并给了它instance1的名字，接着它连接了信号wa，对应于外边的新的一个模块的第一个端口即in1，wb对应mod_a的第二个端口in2，wc对应mod_a的输出端口。
   通过位置来连接的方式有一个缺点，就是你必须按照mod_a端口的顺序进行连接，如果mod_a后期进行了更改，比如将in1与in2对换，则对应连接也就发生了改变，这样就必须将实例化模块中的信号进行更改，不方便移植调用
   
   
   2.通过端口的名字
   ".模块端口的名字(你要连接的地方)"
   mod_a instance2
   (
       .out(wc), 
       .in1(wa), 
       .in2(wb) 
   );
   它实例化了mod_a并给了它instance2的名字，通过端口的名字进行了对应的连接，这种连接方式，不用管端口的顺序是怎么样的，方便移植。即使后期发生了更改也不会改变实例化后的连接方式
   
   ```


2. 一些需要注意的点

   ```verilog
   1) 定义连接线的时候，一定要注意位宽的问题，尤其是有矢量的情况下
   2）在命名导线和模块实例时要小心：名称必须是唯一的
   3）进行按位运算的时候，没有规定的情况两个操作数的位宽要相同，不然会得到错误的结果
   ```

   

