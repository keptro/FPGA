# FPGA学习： Verilog刷题记录（2）

### 刷题网站 ： HDLBits

 ### 第二章 ： Verilog Language

  #### 第二节 ：Vectors

+ Vectors

   + 题目描述：Build a circuit that has one 3-bit input, then outputs the same vector, and also splits it into three separate 1-bit outputs. Connect output `o0` to the input vector's position 0, `o1` to position 1, etc  

     ![image-20210202192138053](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210202192138053.png)

    + 题目分析：熟悉verilog中vector的操作，将输入的3位信号赋值给outv和分别给o2,o1，o0，利用assign进行赋值

    + 解答：

      ```verilog
      /*我的解答*/
      module top_module ( 
          input wire [2:0] vec,
          output wire [2:0] outv,
          output wire o2,
          output wire o1,
          output wire o0  ); // Module body starts after module declaration
          
        	assign outv = vec;
          assign {o0,o1,o2} = {vec[0],vec[1],vec[2]};
        
      endmodule
      
      
      /*官网的解答*/
      module top_module(
      	input [2:0] vec, 
      	output [2:0] outv,
      	output o2,
      	output o1,
      	output o0
      );
      	
      	assign outv = vec;
      
      	// This is ok too: assign {o2, o1, o0} = vec;
      	assign o0 = vec[0];
      	assign o1 = vec[1];
      	assign o2 = vec[2];
      	
      endmodule
      
      ```

      

+ Vectors in more detail

    + 题目描述： Build a combinational circuit that splits an input half-word (16 bits, [15:0] ) into lower [7:0] and upper [15:8] bytes.

    + 题目分析：将输入的高8位赋值给out_hi,低八位赋值给out_lo即可

    + 解答：

      ``` verilog
      /*我的解答*/
      `default_nettype none     // Disable implicit nets. Reduces some types of bugs.
      module top_module( 
          input wire [15:0] in,
          output wire [7:0] out_hi,
          output wire [7:0] out_lo );
      
          assign out_lo = in[7:0];
          assign out_hi = in[15:8];
          
      endmodule
      
      /*官网解答*/
      module top_module (
      	input [15:0] in,
      	output [7:0] out_hi,
      	output [7:0] out_lo
      );
      	
      	assign out_hi = in[15:8];
      	assign out_lo = in[7:0];
      	
      	// Concatenation operator also works: assign {out_hi, out_lo} = in;
      	
      endmodule
      ```
      
      

+ Vector part select

    + 题目描述：Build a circuit that will reverse the *byte* ordering of the 4-byte word.

      ```txt
      AaaaaaaaBbbbbbbbCcccccccDddddddd => DdddddddCcccccccBbbbbbbbAaaaaaaa
      ```

    + 题目分析：题目的意思就是将in的低位以8位vector依次赋值给out的高位，同样是以矢量的形式

    + 解答：

      ``` verilog
      /*我的解答*/
      module top_module( 
          input [31:0] in,
          output [31:0] out );//
      
          assign {out[31:24],out[23:16],out[15:8],out[7:0]} = {in[7:0],in[15:8],in[23:16],in[31:24]};
      
      endmodule
      
      /*官网解答*/
      module top_module (
      	input [31:0] in,
      	output [31:0] out
      );
      
      	assign out[31:24] = in[ 7: 0];	
      	assign out[23:16] = in[15: 8];	
      	assign out[15: 8] = in[23:16];	
      	assign out[ 7: 0] = in[31:24];	
      	
      endmodule
      
      ```

+ Bitwise operators

    + 题目描述：Build a circuit that has two 3-bit inputs that computes the bitwise-OR of the two vectors, the logical-OR of the two vectors, and the inverse (NOT) of both vectors. Place the inverse of `b` in the upper half of `out_not` (i.e., bits [5:3]), and the inverse of `a` in the lower half.

      ![image-20210202214058562](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210202214058562.png)

    + 题目分析：实际上也是看图写代码，该题目主要想让我们知道，逻辑运算和按位运算的区别，如果是进行一位的运算时，则用逻辑运算和按位运算是一样的，当进行多位的运算时，则有所不同，逻辑运算最后的结果只有0或1两种结果，而按位运算则保留原来的位数

    + 解答：

      ``` verilog
      /*我的解答*/
      module top_module( 
          input [2:0] a,
          input [2:0] b,
          output [2:0] out_or_bitwise,
          output out_or_logical,
          output [5:0] out_not
      );
          
          assign out_or_bitwise = a | b;
          assign out_or_logical = a || b;
          assign out_not        = {~b,~a};
      
      endmodule
      
      /*官网解答*/
      module top_module(
      	input [2:0] a, 
      	input [2:0] b, 
      	output [2:0] out_or_bitwise,
      	output out_or_logical,
      	output [5:0] out_not
      );
      	
      	assign out_or_bitwise = a | b;
      	assign out_or_logical = a || b;
      
      	assign out_not[2:0] = ~a;	// Part-select on left side is o.
      	assign out_not[5:3] = ~b;	//Assigning to [5:3] does not conflict with [2:0]
      	
      endmodule
      
      ```

+ Four-input gates

    + 题目描述： Build a combinational circuit with four inputs, `in[3:0]`.

      There are 3 outputs:

      - out_and: output of a 4-input AND gate.
      - out_or: output of a 4-input OR gate.
      - out_xor: output of a 4-input XOR gate.

    + 题目分析：构建三个逻辑门，只不过输入是以矢量的形式，可以将矢量中的每一位提出来进行运算，也可以采用简略的写法如下代码所示。

    + 解答：

      ``` verilog
      /*我的解答*/
      
      module top_module( 
          input [3:0] in,
          output out_and,
          output out_or,
          output out_xor
      );
          
          assign out_and = in[0] & in[1] & in[2] & in[3]; 
          assign out_or  = in[0] | in[1] | in[2] | in[3];
          assign out_xor = in[0] ^ in[1] ^ in[2] ^ in[3];
      
          /*
          	或者
          assign out_and = &in;
          assign out_or  = |in;
          assign out_xor = ^in;
          */
          
      endmodule
      
      /*官网解答*/
      	无
      ```

+ Vector concatenation operator

    + 题目描述： Given several input vectors, concatenate them together then split them up into several output vectors. There are six 5-bit input vectors: a, b, c, d, e, and f, for a total of 30 bits of input. There are four 8-bit output vectors: w, x, y, and z, for 32 bits of output. The output should be a concatenation of the input vectors followed by two `1` bits:

      ![image-20210208221904653](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210208221904653.png)

    + 题目分析：该题主要让我们熟悉位拼接符的用法，有6个5位输入的矢量，根据图示将它们赋值给4个8位输出的矢量，例如w是由a和b的高3位组成，x是由b的低2位，c和d的一高位组成

    + 解答：

      ``` verilog
      /*我的解答*/
      module top_module (
          input [4:0] a, b, c, d, e, f,
          output [7:0] w, x, y, z );
      
          // assign { ... } = { ... };
          assign w[7:0] = {a,b[4:2]};
          assign x[7:0] = {b[1:0],c,d[4:4]};
          assign y[7:0] = {d[3:0],e[4:1]};
          assign z[7:0] = {e[0:0],f,{2{1'b1}}}; 
      endmodule
      
      //其中z用位拼接符的时候需要注意，如果写成assign z[7:0] = {e[0:0],f,2{1'b1}}是错误的，出错的原因出在2{1'b1},当需要多次使用{}时，需要将其包裹起来即{2{1'b1}},下面有一节“more replication”还可以看到。
      
      /*官网解答*/
      	无
      
      ```

+ Vector reversal 1

    + 题目描述：Given an 8-bit input vector [7:0], reverse its bit ordering. 

    + 题目分析：题目意思即将输入信号的从低位到高位赋值给输出信号，这题位数不多可以直接利用{}进行拼接，但当位数过多时，采用generate语句进行生成，官网的解答给了出来

    + 解答：

      ``` verilog
      /*我的解答*/
      module top_module( 
          input [7:0] in,
          output [7:0] out
      );
          assign out = {in[0],in[1],in[2],in[3],in[4],in[5],in[6],in[7]};
          
      endmodule
      
      
      
      /*官网解答*/
      module top_module (
      	input [7:0] in,
      	output [7:0] out
      );
      	
      	assign {out[0],out[1],out[2],out[3],out[4],out[5],out[6],out[7]} = in;
      	
      	/*
      	// I know you're dying to know how to use a loop to do this:
      
      	// Create a combinational always block. This creates combinational logic that computes the same result
      	// as sequential code. for-loops describe circuit *behaviour*, not *structure*, so they can only be used 
      	// inside procedural blocks (e.g., always block).
      	// The circuit created (wires and gates) does NOT do any iteration: It only produces the same result
      	// AS IF the iteration occurred. In reality, a logic synthesizer will do the iteration at compile time to
      	// figure out what circuit to produce. (In contrast, a Verilog simulator will execute the loop sequentially
      	// during simulation.)
      	always @(*) begin	
      		for (int i=0; i<8; i++)	// int is a SystemVerilog type. Use integer for pure Verilog.
      			out[i] = in[8-i-1];
      	end
      
      
      	// It is also possible to do this with a generate-for loop. Generate loops look like procedural for loops,
      	// but are quite different in concept, and not easy to understand. Generate loops are used to make instantiations
      	// of "things" (Unlike procedural loops, it doesn't describe actions). These "things" are assign statements,
      	// module instantiations, net/variable declarations, and procedural blocks (things you can create when NOT inside 
      	// a procedure). Generate loops (and genvars) are evaluated entirely at compile time. You can think of generate
      	// blocks as a form of preprocessing to generate more code, which is then run though the logic synthesizer.
      	// In the example below, the generate-for loop first creates 8 assign statements at compile time, which is then
      	// synthesized.
      	// Note that because of its intended usage (generating code at compile time), there are some restrictions
      	// on how you use them. Examples: 1. Quartus requires a generate-for loop to have a named begin-end block
      	// attached (in this example, named "my_block_name"). 2. Inside the loop body, genvars are read only.
      	generate
      		genvar i;
      		for (i=0; i<8; i = i+1) begin: my_block_name
      			assign out[i] = in[8-i-1];
      		end
      	endgenerate
      	*/
      	
      endmodule
      
      需要注意的是
      1.assign out[7:0] = in[0:7]; 这种声明在verilog中是不允许的
      ```

+ Replication operator

    + 题目描述：Build a circuit that sign-extends an 8-bit number to 32 bits. This requires a concatenation of 24 copies of the sign bit (i.e., replicate bit[7] 24 times) followed by the 8-bit number itself.

    + 题目分析：

    + 解答： 题目的意思是将输入信号的高位赋值24次，并和原来的信号进行拼接

      ``` verilog
      /*我的解答*/
      module top_module (
          input [7:0] in,
          output [31:0] out );//
      
          // assign out = { replicate-sign-bit , the-input };
          assign out = {{24{in[7]}},in};
      
      endmodule
      
      /*官网解答*/
      module top_module (
      	input [7:0] in,
      	output [31:0] out
      );
      
      	// Concatenate two things together:
      	// 1: {in[7]} repeated 24 times (24 bits)
      	// 2: in[7:0] (8 bits)
      	assign out = { {24{in[7]}}, in };
      	
      endmodule
      
      ```

+ More replication

    + 题目描述： Given five 1-bit signals (a, b, c, d, and e), compute all 25 pairwise one-bit comparisons in the 25-bit output vector. The output should be 1 if the two bits being compared are equal. 

      ![image-20210208223421389](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210208223421389.png)

    + 题目分析：熟悉复制的操作，将所给的5位信号进行如图所示的组合并将它们进行同或操作

    + 解答：
    
      ``` verilog
      /*我的解答*/
      module top_module (
          input a, b, c, d, e,
          output [24:0] out );//
      
          // The output is XNOR of two vectors created by 
          // concatenating and replicating the five inputs.
          // assign out = ~{ ... } ^ { ... };
          assign out = ~{{5{a}},{5{b}},{5{c}},{5{d}},{5{e}}} ^ {5{a,b,c,d,e}};
      endmodule
      
      /*官网解答*/
      module top_module (
      	input a, b, c, d, e,
      	output [24:0] out
      );
      
      	wire [24:0] top, bottom;
      	assign top    = { {5{a}}, {5{b}}, {5{c}}, {5{d}}, {5{e}} };
      	assign bottom = {5{a,b,c,d,e}};
      	assign out = ~top ^ bottom;	// Bitwise XNOR
      
      	// This could be done on one line:
      	// assign out = ~{ {5{a}}, {5{b}}, {5{c}}, {5{d}}, {5{e}} } ^ {5{a,b,c,d,e}};
      	
      endmodule
      ```


​    

​    

### 总结 

  1. **如何声明一个vectors**
    
    ```verilog
    type [upper:lower] vectors_name
    type 通常是wire或reg型，如果要声明端口则type也可以表示为input或output
     /*example*/
    wire [7:0] w;         // 8-bit wire
    reg  [4:1] x;         // 4-bit reg
    output reg [0:0] y;   // 1-bit reg that is also an output port (this is still a vector)
    input wire [3:-2] z;  // 6-bit wire input (negative ranges are allowed)
    output [3:0] a;       // 4-bit output wire. Type is 'wire' unless specified otherwise.
    wire [0:7] b;         // 8-bit wire where b[0] is the most-significant bit.
    ```

  2. **关于隐式的网络以及如何避免**
    
    ```verilog
    隐性的网络通常是我们难以检测的bug，
    net类型信号可以通过assign语句或将未声明的内容附加到模块端口来隐式创建，
    隐性的网络总是一位的wire 如果我们想要用vectors则会出现错误 用下列例子说明
        
    wire [2:0] a, c;   // Two vectors
    assign a = 3'b101;  // a = 101
    assign b = a;       // b =   1  implicitly-created wire
    assign c = b;       // c = 001  <-- bug
    my_module i1 (d,e); // d and e are implicitly one-bit wide if not declared.
                        // This could be a bug if the port was intended to be a vector.
    
    通常我们通过添加 `default_nettype none,来让这类错误变得可视
    ```

3. **矢量进行部分选择的操作**

   ```verilog
   类似于编程中“切片”的操作
   input [15:0] in;
   output [23:0] out;
   assign {out[7:0], out[15:8]} = in;         // Swap two bytes. Right side and left side are both 16-bit vectors.
   assign out[15:0] = {in[7:0], in[15:8]};    // This is the same thing.
   assign out = {in[7:0], in[15:8]};       // This is different. The 16-bit vector on the right is extended to
                                           // match the 24-bit vector on the left, so out[23:16] are zero.
                                           // In the first two examples, out[23:16] are not assigned.
   ```

4. **位拼接符{}的使用**

   ```verilog
   {3'b111, 3'b000} => 6'b111000
   {1'b1, 1'b0, 3'b101} => 5'b10101
   {4'ha, 4'd10} => 8'b10101010     // 4'ha and 4'd10 are both 4'b1010 in binary
   
   The replication operator allows repeating a vector and concatenating them together:
   可以进行复制的操作
   {num{vector}}
   num：必须是一个常量，并且两边的{}是不可以缺少的
   
   Examples:
   
   {5{1'b1}}           // 5'b11111 (or 5'd31 or 5'h1f)
   {2{a,b,c}}          // The same as {a,b,c,a,b,c}
   {3'd5, {2{3'd6}}}   // 9'b101_110_110. It's a concatenation of 101 with
                       // the second vector, which is two copies of 3'b110.
   ```

   