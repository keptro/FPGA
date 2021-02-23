# FPGA学习： Verilog刷题记录（7）

### 刷题网站 ： HDLBits

 ### 第3章 ： Circuits

  #### 第1大节 ：Combinational Logic

##### 第2小节 : Multiplexers



+ **2-to-1 multiplexer**

  + 题目描述：Create a one-bit wide, 2-to-1 multiplexer. When sel=0, choose a. When sel=1, choose b.

  + 题目分析：选择器的编写，这里因为变量比较少，选择三目运算符进行assign语句的赋值

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input a, b, sel,
        output out ); 
    
        assign out = sel ? b : a;
        
    endmodule
    /*官网解答*/
    module top_module (
    	input a,
    	input b,
    	input sel,
    	output out
    );
    
    	assign out = (sel & b) | (~sel & a);	// Mux expressed as AND and OR
    	
    	// Ternary operator is easier to read, especially if vectors are used:
    	// assign out = sel ? b : a;
    	
    endmodule
    
    ```

    

+ **2-to-1 bus multiplexer**

  + 题目描述：Create a 100-bit wide, 2-to-1 multiplexer. When sel=0, choose a. When sel=1, choose b.

  + 题目分析：和上一题类似，因为位宽一样所以直接用assign+三目运算符赋值

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input [99:0] a, b,
        input sel,
        output [99:0] out );
    
        assign out = sel ? b : a;
        
    endmodule
    
    /*官网解答*/
    module top_module (
    	input [99:0] a,
    	input [99:0] b,
    	input sel,
    	output [99:0] out
    );
    
    	assign out = sel ? b : a;
    	
    	// The following doesn't work. Why?
    	// assign out = (sel & b) | (~sel & a);
    	
    endmodule
    ```

    

+ **9-to-1 multiplexer**

  + 题目描述：Create a 16-bit wide, 9-to-1 multiplexer. sel=0 chooses a, sel=1 chooses b, etc. For the unused cases (sel=9 to 15), set all output bits to '1'.

  + 题目分析：创建一个16位宽，9选1的选择器，0~8分别输出a,b,c,d,e,f,g,h,i其余情况均让输出的所有位上赋值为1,这里采用case语句综合电路

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input [15:0] a, b, c, d, e, f, g, h, i,
        input [3:0] sel,
        output [15:0] out );
        
        always@(*) begin
            case (sel)
              4'd0  :  out = a;
              4'd1  :  out = b;
              4'd2  :  out = c;
              4'd3  :  out = d;
              4'd4  :  out = e;
              4'd5  :  out = f;
              4'd6  :  out = g;
              4'd7  :  out = h;
              4'd8  :  out = i;
              4'd9  :  out = 16'hff_ff;
              4'd10 :  out = 16'hff_ff;
              4'd11 :  out = 16'hff_ff;
              4'd12 :  out = 16'hff_ff;
              4'd13 :  out = 16'hff_ff;
              4'd14 :  out = 16'hff_ff;
              4'd15 :  out = 16'hff_ff;
              4'd16 :  out = 16'hff_ff;
       		default :  out = 16'hff_ff;
            endcase
        end
    
    endmodule
    
    /*官网解答*/
    module top_module (
    	input [15:0] a,
    	input [15:0] b,
    	input [15:0] c,
    	input [15:0] d,
    	input [15:0] e,
    	input [15:0] f,
    	input [15:0] g,
    	input [15:0] h,
    	input [15:0] i,
    	input [3:0] sel,
    	output logic [15:0] out
    );
    
    	// Case statements can only be used inside procedural blocks (always block)
    	// This is a combinational circuit, so use a combinational always @(*) block.
    	always @(*) begin
    		out = '1;		// '1 is a special literal syntax for a number with all bits set to 1.
    						// '0, 'x, and 'z are also valid.
    						// I prefer to assign a default value to 'out' instead of using a
    						// default case.
    		case (sel)
    			4'h0: out = a;
    			4'h1: out = b;
    			4'h2: out = c;
    			4'h3: out = d;
    			4'h4: out = e;
    			4'h5: out = f;
    			4'h6: out = g;
    			4'h7: out = h;
    			4'h8: out = i;
    		endcase
    	end
    	
    endmodule
    
    ```

    

+ **256-to-1 multiplexer**

  + 题目描述：Create a 1-bit wide, 256-to-1 multiplexer. The 256 inputs are all packed into a single 256-bit input vector. sel=0 should select `in[0]`, sel=1 selects bits `in[1]`, sel=2 selects bits `in[2]`, etc.

  + 题目分析：

    + 有一个8位的选择信号sel，要求sel=1时输出in[1], sel=2时输出in[2],以此类推
    +  用case或if来书写，显然需要讨论的情况太多，过于冗杂
    + verilog提供一种更加方便的做法，利用矢量索引，因为在位宽确定的情况下矢量的索引是可变的，就是说，我们可以将sel直接放进in[]中，即 **in[sel]**，它实现的效果就是sel=1时输出in[1], sel=2时输出in[2],以此类推

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input [255:0] in,
        input [7:0] sel,
        output out );
    
        assign out = in [sel];
        
    endmodule
    /*官网解答*/
    module top_module (
    	input [255:0] in,
    	input [7:0] sel,
    	output  out
    );
    
    	// Select one bit from vector in[]. The bit being selected can be variable.
    	assign out = in[sel];
    	
    endmodule
    ```

    

+ **256-to-1 4-bit multiplexer**

  + 题目描述：Create a 4-bit wide, 256-to-1 multiplexer. The 256 4-bit inputs are all packed into a single 1024-bit input vector. sel=0 should select bits `in[3:0]`, sel=1 selects bits `in[7:4]`, sel=2 selects bits `in[11:8]`, etc.

  + 题目分析：这题我也不会，网上搜到的答案，提交后又看了官网给的答案，学习了，这道题就是上道题的升级版，因为位宽过大，用case显然不适合，官网给的提示也是说用case不合适，采用了矢量分割的方式来解决这道题目，因为矢量的索引是可以变化的，前提是合成器能够计算出所选位的宽度是恒定的。并且官网还提示说“An error saying "... is not a constant" means it couldn't prove that the select width is constant. In particular, in[ sel*4+3 : sel\*4 ] does not work.”这种写法是不正确的，应该避免

  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [1023:0] in,
        input [7:0] sel,
        output [3:0] out );
    
    	assign out = {in[sel*4+3], in[sel*4+2], in[sel*4+1], in[sel*4+0]};
        
    endmodule
    /*官网解答*/
    module top_module (
    	input [1023:0] in,
    	input [7:0] sel,
    	output [3:0] out
    );
    
    	// We can't part-select multiple bits without an error, but we can select one bit at a time,
    	// four times, then concatenate them together.
    	assign out = {in[sel*4+3], in[sel*4+2], in[sel*4+1], in[sel*4+0]};
    
    	// Alternatively, "indexed vector part select" works better, but has an unfamiliar syntax:
    	// assign out = in[sel*4 +: 4];		// Select starting at index "sel*4", then select a total width of 4 bits with increasing (+:) index number.
    	// assign out = in[sel*4+3 -: 4];	// Select starting at index "sel*4+3", then select a total width of 4 bits with decreasing (-:) index number.
    	// Note: The width (4 in this case) must be constant.
    
    endmodule
    ```

### 总结

+ **关于case**

  ```verilog
  1.case语句只能在过程块里使用
  2.当遇到多种情况需要赋值为1的情况时，可以先在书写case前将输出全部赋值为1，再根据需要将一些特殊情况利用case赋值为其他值，这种方式比将所有情况都列出的方式，或利用default更好。其中
      out = '1 表示说将out的所有位都赋值为1
      out = '0,out = 'x,out ='z 也都是合法的操作
  ```

  

+ **其他**

  ```verilog
  1.assign out = in[sel*4 +: 4]; 
  in[sel*4 +: 4]  
  "+:" 相当于"+=" 
  sel*4选定了起始，然后从起始开始每次加4，4个取为一组 
  in[sel*4+3 -: 4]; 也是一样的道理
  
  特别的 in[ sel*4+3 : sel*4 ]，起始和终点都随着sel变化，这是不允许的，verilog中只允许起始或终点发生变化，其他保持常量，这样做是为了保证宽度不发生改变
  ```

  ![image-20210223105148449](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210223105148449.png)