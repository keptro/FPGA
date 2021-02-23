# FPGA学习： Verilog刷题记录（1）

### 刷题网站 ： HDLBits

### 第一章 Getting Started

+ **Getting Started**

  + 题目描述： Build a circuit with no inputs and one output. That output should always drive 1 (or logic high). 

  + 题目分析：让输出为逻辑1

  + 解答：

    ``` verilog
    /*我的解答*/
    module top_module( output one );
        
        assign one = 1'b1;
        
    endmodule
    
    
    /*官网解答*/
    module top_module( output one );
    	
    	assign one = 1'b1;
    	
    endmodule
    
    ```

+ **Output zero**

  + 题目描述：Build a circuit with no inputs and one output that outputs a constant `0`
  + 题目分析：让输出为常数0
  + 解答：

  ``` verilog
  /*我的解答*/
  module top_module(
      output zero
  );
      assign zero = 1'b0;
      
  endmodule
  
  /*官网解答*/
  module top_module ( output zero );
  	
  	assign zero = 1'b0;
  	
  endmodule
  
  ```

  ### 第二章 ： Verilog Language

  #### 第一节 ：Basics

+ **Simple wire**

  + 题目描述：Create a module with one input and one output that behaves like a wire
  + 题目分析：将输入赋值给输出即可
  + 解答：

  ```verilog
  /*我的解答*/
  module top_module( input in, output out );
  
      assign out = in;
      
  endmodule
  /*官网解答*/
  module top_module( input in, output out );
  	
  	assign out = in;
  	// Note that wires are directional, so "assign in = out" is not equivalent.
  	
  endmodule
  ```

+ **Four wire**

  + 题目描述：Create a module with 3 inputs and 4 outputs that behaves like wires that makes these connections:

    ```verilog
    a -> w
    b -> x
    b -> y
    c -> z
    ```

    ![Four wire](/home/troke/Github/FPGA/HdlBits/HDLBits-screenshots/Wire4.png)

    

  + 题目分析：利用assign将对应的输入与输出连接即可

  + 解答：

  ```verilog
  /*我的解答*/
  module top_module( 
      input a,b,c,
      output w,x,y,z );
  
     assign w = a;
     assign z = c;
     assign x = b;
     assign y = b;
      
  endmodule
  
  /*官网解答*/
  module top_module (
  	input a,
  	input b,
  	input c,
  	output w,
  	output x,
  	output y,
  	output z  );
  	
  	assign w = a;
  	assign x = b;
  	assign y = b;
  	assign z = c;
  
  	// If we're certain about the width of each signal, using 
  	// the concatenation operator is equivalent and shorter:
      // assign {w,x,y,z} = {a,b,b,c};  采用拼接符连接
  	
  endmodule
  
  ```

  + 波形结果

  

+ **inverter**

  + 题目描述：Create a module that implements a NOT gate.

  ![image-20210201121411943](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210201121411943.png)

  + 题目分析：直接用逻辑符创建一个非门即可
  + 解答：

  ```verilog
  /*我的解答*/
  module top_module( input in, output out );
  
      assign out = !in;
      
  endmodule
  
  /*官网解答*/
  module top_module(
  	input in,
  	output out
  );
  	
  	assign out = ~in;
  	
  endmodule
  ```

+ **And gate**

  + 题目描述：Create a module that implements an AND gate.

  ![image-20210201122050204](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210201122050204.png)

  + 题目分析： 用逻辑符构建一个或门

  + 解答：

  ```verilog
  /*提示：Verilog has separate bitwise-AND (&) and logical-AND (&&) operators, like C.   
         Since we're working with a one-bit here, it doesn't matter which we choose.
         即这里只是关于一位的的操作，用位操作或逻辑操作都可以
  */
  
  /*我的解答*/
  module top_module( 
      input a, 
      input b, 
      output out );
      
      assign out = a&&b;
    //assign out = a&b;
  
  endmodule
  /*官网解答:无*/
  ```

  

+ **NOR gate**

  + 题目描述：Create a module that implements a NOR gate. A NOR gate is an OR gate with its output inverted

  ![image-20210201122606680](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210201122606680.png)

  + 题目分析：直接用逻辑操作符构建一个与非门
  + 解答：

  ```verilog
  /*我的解答*/
  module top_module( 
      input a, 
      input b, 
      output out );
      
      assign out = !(a||b);
  
  endmodule
  ```

  

+ **XNOR gate**

  + 题目描述：Create a module that implements an XNOR gate

  ![image-20210201122841446](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210201122841446.png)

  + 题目分析：直接用逻辑操作符创建一个同或门
  + 解答：

  ```verilog
  /*我的解答*/
  module top_module( 
      input a, 
      input b, 
      output out );
      
      assign out = (a && b) || ((!a) && (!b));
  
  endmodule
  ```

  

+ **Declaring wires**

  + 题目描述：Implement the following circuit. Create two intermediate wires (named anything you want) to connect the AND and OR gates together. Note that the wire that feeds the NOT gate is really wire `out`, so you do not necessarily need to declare a third wire here

  ![image-20210201123333742](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210201123333742.png)

  + 题目分析：练习如何用wire创建数据，根据图示逻辑图表达输入和输出，可以看出需要我们用wire创建三个变量
  + 解答：

  ```verilog
  /*我的解答*/
  `default_nettype none
  module top_module(
      input a,
      input b,
      input c,
      input d,
      output out,
      output out_n   ); 
      
      wire and_1 , and_2 , or_1 ;
      assign and_1 = a && b;
      assign and_2 = c && d;
      assign or_1  = and_1 || and_2;
      assign out   = or_1;
      assign out_n = !or_1;
      
  endmodule
  
  /*官网解答*/
  module top_module (
  	input a,
  	input b,
  	input c,
  	input d,
  	output out,
  	output out_n );
  	
  	wire w1, w2;		// Declare two wires (named w1 and w2)
  	assign w1 = a&b;	// First AND gate
  	assign w2 = c&d;	// Second AND gate
  	assign out = w1|w2;	// OR gate: Feeds both 'out' and the NOT gate
  
  	assign out_n = ~out;	// NOT gate
  	
  endmodule
  
  
  ```

  

+ **7458 chip**

  + 题目描述：

  ![image-20210201124001283](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210201124001283.png)

  + 题目分析：与上题类似，根据图示用verilog表述出输入和输出,可以看出我们需要4个wire型变量
  + 解答：

  ```verilog
  /*我的解答*/
  module top_module ( 
      input p1a, p1b, p1c, p1d, p1e, p1f,
      output p1y,
      input p2a, p2b, p2c, p2d,
      output p2y );
  
      wire and_1 , and_2 , and_3 , and_4;
      assign and_1 = p1a && p1b && p1c;
      assign and_2 = p1d && p1e && p1f;
      assign and_3 = p2a && p2b;
      assign and_4 = p2c && p2d;
      assign p1y = and_1 || and_2;
      assign p2y = and_3 || and_4;
  
  endmodule
  ```

  
