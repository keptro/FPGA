# FPGA学习： Verilog刷题记录（6）

### 刷题网站 ： HDLBits

 ### 第3章 ： Circuits

  #### 第1大节 ：Combinational Logic

#####  第1小节 : Basic Gates


 + **Conditional ternary operator**

   + 题目描述：Implement the following circuit:

     ![Exams m2014q4h.png](https://hdlbits.01xz.net/mw/images/d/d7/Exams_m2014q4h.png)

   + 题目分析：直接利用assign语句，将线连接起来

   + 解答：

   ```verilog
   /*我的解答*/
   module top_module (
       input in,
       output out);
   
       assign out = in;
       
   endmodule
   
   /*官网解答*/
   无
   ```


+ **GND**

  + 题目描述：Implement the following circuit:

    ![Exams m2014q4i.png](https://hdlbits.01xz.net/mw/images/5/54/Exams_m2014q4i.png)

  + 题目分析：将输出与地相连

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
    	output	out
    );
        
        assign out = 0;
        
    endmodule
    /*官网解答*/
    无
    ```

    

+ **NOR**

  + 题目描述：Implement the following circuit:

    ![Exams m2014q4e.png](https://hdlbits.01xz.net/mw/images/e/e9/Exams_m2014q4e.png)

  + 题目分析：实现一个或非门，直接用位运算符就可以了

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input in1,
        input in2,
        output out);
    
        assign out = ~( in1 | in2 );
        
    endmodule
    
    /*官网解答*/
    无
    ```

    

+ **Another gate **

  + 题目描述：Implement the following circuit:

    ![Exams m2014q4f.png](https://hdlbits.01xz.net/mw/images/b/b6/Exams_m2014q4f.png)

  + 题目分析：根据图直接用位运算符进行赋值即可

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input in1,
        input in2,
        output out);
    
        assign out = (in1) & (~in2);
        
    endmodule
    /*官网解答*/
    无
    ```

    

+ **Two gates**

  + 题目描述：Implement the following circuit:

    ![Exams m2014q4g.png](https://hdlbits.01xz.net/mw/images/e/e6/Exams_m2014q4g.png)

  + 题目分析：根据图直接用位运算符进行赋值即可

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input in1,
        input in2,
        input in3,
        output out);
    
        wire   nxor_out;
        assign nxor_out = ~( in1 ^ in2 ) ;
        assign out      = nxor_out ^ in3 ; 
        
    endmodule
    /*官网解答*/
    无
    ```

    

+ **More logic gates**

  + 题目描述：Ok, let's try building several logic gates at the same time. Build a combinational circuit with two inputs, `a` and `b`.

    There are 7 outputs, each with a logic gate driving it:

    - out_and: a and b
    - out_or: a or b
    - out_xor: a xor b
    - out_nand: a nand b
    - out_nor: a nor b
    - out_xnor: a xnor b
    - out_anotb: a and-not b

  + 题目分析：用位运算符进行赋值即可

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input a, b,
        output out_and,
        output out_or,
        output out_xor,
        output out_nand,
        output out_nor,
        output out_xnor,
        output out_anotb
    );
    
        assign out_and   =  a & b;
        assign out_or    =  a | b;
        assign out_xor   =  a ^ b;
        assign out_nand  =  ~ out_and;
        assign out_nor   =  ~ out_or ;
        assign out_xnor  =  ~ out_xor;
        assign out_anotb =  a & (~b) ; 
         
    endmodule
    /*官网解答*/
    无
    ```

    

+ **7420 chip**

  + 题目描述：Create a module with the same functionality as the 7420 chip. It has 8 inputs and 2 outputs.

    ![7420.png](https://hdlbits.01xz.net/mw/images/4/48/7420.png)

  + 题目分析：根据图示，进行相应的连接即可

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module ( 
        input p1a, p1b, p1c, p1d,
        output p1y,
        input p2a, p2b, p2c, p2d,
        output p2y );
    
        assign p1y = ~ ( p1a & p1b & p1c & p1d );
        assign p2y = ~ ( p2a & p2b & p2c & p2d );
        
    endmodule
    
    /*官网解答*/
    无
    ```

    

+ **Truth tables**

  + 题目描述：Create a combinational circuit that implements the above truth table.

    ![Truthtable1.png](https://hdlbits.01xz.net/mw/images/f/f6/Truthtable1.png)

    | Row    | *Inputs* |      |      | ouputs |
    | :----- | :------- | :--- | :--- | :----- |
    | number | x3       | x2   | x1   | f      |
    | 0      | 0        | 0    | 0    | 0      |
    | 1      | 0        | 0    | 1    | 0      |
    | 2      | 0        | 1    | 0    | 1      |
    | 3      | 0        | 1    | 1    | 1      |
    | 4      | 1        | 0    | 0    | 0      |
    | 5      | 1        | 0    | 1    | 1      |
    | 6      | 1        | 1    | 0    | 0      |
    | 7      | 1        | 1    | 1    | 1      |

  + 题目分析：根据真值表进行逻辑表达式的书写，可以化简后再赋值，也可以选择直接赋值

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input x3,
        input x2,
        input x1,  // three inputs
        output f   // one output
    );
    
        assign f = (x1 & x3) | (x2 & (~x3));
        
    endmodule
    
    /*官网解答*/
    module top_module (
    	input x3,
    	input x2,
    	input x1,
    	output f
    );
    	// This truth table has four minterms. 
    	assign f = ( ~x3 & x2 & ~x1 ) | 
    				( ~x3 & x2 & x1 ) |
    				( x3 & ~x2 & x1 ) |
    				( x3 & x2 & x1 ) ;
    				
    	// It can be simplified, by boolean algebra or Karnaugh maps.
    	// assign f = (~x3 & x2) | (x3 & x1);
    	
    	// You may then notice that this is actually a 2-to-1 mux, selected by x3:
    	// assign f = x3 ? x1 : x2;
    	
    endmodule
    
    ```

+ **Two-bit equality**

  + 题目描述：Create a circuit that has two 2-bit inputs `A[1:0]` and `B[1:0]`, and produces an output `z`. The value of `z` should be 1 if `A = B`, otherwise `z` should be 0.

  + 题目分析：看题目似乎是一个选择，这里用一个三目运算符对z进行赋值

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module ( input [1:0] A, input [1:0] B, output z ); 
        
        assign z = (A[1:0] == B[1:0] ) ? 1 : 0 ;
    
    endmodule
    
    //有点多此一举了，==本身就是用来判断的，成立就返回1，不成立就返回0，符合题目要求
    
    /*官网解答*/
    module top_module(
    	input [1:0] A,
    	input [1:0] B,
    	output z);
    
    	assign z = (A[1:0]==B[1:0]);	// Comparisons produce a 1 or 0 result.
    	
    	// Another option is to use a 16-entry truth table ( {A,B} is 4 bits, with 16 combinations ).
    	// There are 4 rows with a 1 result.  0000, 0101, 1010, and 1111.
    
    endmodule
    
    ```

    

+ **Simple circuit A**

  + 题目描述：Module A is supposed to implement the function `z = (x^y) & x`. Implement this module.

  + 题目分析：逻辑表达式已经给你，直接赋值即可 

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (input x, input y, output z);
    
        assign z = (x^y) & x; 
            
    endmodule
    
    /*官网解答*/
    无
    ```

    

+ **Simple circuit B**

  + 题目描述：Circuit B can be described by the following simulation waveform

    ![Mt2015 q4b.png](https://hdlbits.01xz.net/mw/thumb.php?f=Mt2015_q4b.png&width=800)

  + 题目分析：根据波形图，进行赋值，可以看出有两种情况z的值为1，第一种是x=y=0，第二种是x=y=1时，就是一个同或的关系

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module ( input x, input y, output z );
    
        assign z = (x & y) | ((~x) & (~y)); 
      `
        //assign z = ~(x ^ y);
        
    endmodule
    
    
    /*官网解答*/
    module top_module(
    	input x,
    	input y,
    	output z);
    
    	// The simulation waveforms gives you a truth table:
    	// y x   z
    	// 0 0   1
    	// 0 1   0
    	// 1 0   0
    	// 1 1   1   
    	// Two minterms: 
    	// assign z = (~x & ~y) | (x & y);
    
    	// Or: Notice this is an XNOR.
    	assign z = ~(x^y);
    
    endmodule
    ```

    

+ **Combine Circuits A and B**

  + 题目描述：Implement this circuit.

    ![](D:\fpga\srceenshoot\HDLBits\Basic Gates\Mt2015_q4.png)

  + 题目分析：实例化前面两个题目中的两个模块A，B，并将它们按图示连接到一起

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (input x, input y, output z);
    
        wire  zIA1,zIB1,zIA2,zIB2;
        wire  xor_z1,xor_z2;
        A_module IA1_inst(
            .x	(x)		,
            .y	(y)		,
            .z	(zIA1)
        );
        A_module IA2_inst(
            .x	(x)		,
            .y	(y)		,
            .z	(zIA2)
        );
        B_module IB1_inst(
            .x	(x)		,
            .y	(y)		,
            .z	(zIB1)
        );
        B_module IB2_inst(
            .x	(x)		,
            .y	(y)		,
            .z	(zIB2)
        );
        
       assign xor_z1 = zIA1 | zIA2 ;
       assign xor_z2 = zIB1 & zIB2 ;
       assign z		 = xor_z1 ^ xor_z2;
           
    endmodule
    
    //A模块
    module A_module (input x, input y, output z);
    
        assign z = (x^y) & x; 
            
    endmodule
    //B模块
    module B_module ( input x, input y, output z );
    
        assign z = (x & y) | ((~x) & (~y)); 
     
    endmodule
    
    /*官网解答*/
    module top_module(
    	input x,
    	input y,
    	output z);
    
    	wire o1, o2, o3, o4;
    	
    	A ia1 (x, y, o1);
    	B ib1 (x, y, o2);
    	A ia2 (x, y, o3);
    	B ib2 (x, y, o4);
    	
    	assign z = (o1 | o2) ^ (o3 & o4);
    
    	// Or you could simplify the circuit including the sub-modules:
    	// assign z = x|~y;
    	
    endmodule
    
    module A (
    	input x,
    	input y,
    	output z);
    
    	assign z = (x^y) & x;
    	
    endmodule
    
    module B (
    	input x,
    	input y,
    	output z);
    
    	assign z = ~(x^y);
    
    endmodule
    ```

    

+ **Ring or vibrate**

  + 题目描述：Suppose you are designing a circuit to control a cellphone's ringer and vibration motor. Whenever the phone needs to ring from an incoming call (`input **ring**`), your circuit must either turn on the ringer (`output **ringer** = 1`) or the motor (`output **motor** = 1`), but not both. If the phone is in vibrate mode (`input **vibrate_mode** = 1`), turn on the motor. Otherwise, turn on the ringer.

    Try to use only `assign` statements, to see whether you can translate a problem description into a collection of logic gates.

    ![Ringer.png](D:\fpga\srceenshoot\HDLBits\Basic Gates\Ringer.png)

    

  + 题目分析：实际上就是一个逻辑抽象的问题，通过描述可以知道振动模式开启时motor=1，其他情况motor=0，而电话响的时候要么ringer=1要么motor=1，且不能同时为1所以可以知道motor与ring和vibrate_mode时与的关系，可以到下面一张真值表，填写完motor后可以填补ringer，完成后即可用assing进行赋值。

    | ring | vibrate_mode | ringer | motor |
    | ---- | ------------ | ------ | ----- |
    | 0    | 0            | 0      | 0     |
    | 0    | 1            | 0      | 0     |
    | 1    | 0            | 1      | 0     |
    | 1    | 1            | 0      | 1     |

    

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input ring,
        input vibrate_mode,
        output ringer,       // Make sound
        output motor         // Vibrate
    );
    
        assign ringer = ring & (~vibrate_mode);
        assign motor  = ring &  vibrate_mode;
        
    endmodule
    
    /*官网解答*/
    module top_module(
    	input ring, 
    	input vibrate_mode,
    	output ringer,
    	output motor
    );
    	
    	// When should ringer be on? When (phone is ringing) and (phone is not in vibrate mode)
    	assign ringer = ring & ~vibrate_mode;
    	
    	// When should motor be on? When (phone is ringing) and (phone is in vibrate mode)
    	assign motor = ring & vibrate_mode;
    	
    endmodule
    
    ```

    

+ **Thermostat**

  + 题目描述：A heating/cooling thermostat controls both a heater (during winter) and an air conditioner (during summer). Implement a circuit that will turn on and off the heater, air conditioning, and blower fan as appropriate.

    The thermostat can be in one of two modes: heating (`mode = 1`) and cooling (`mode = 0`). In heating mode, turn the heater on when it is too cold (`too_cold = 1`) but do not use the air conditioner. In cooling mode, turn the air conditioner on when it is too hot (`too_hot = 1`), but do not turn on the heater. When the heater or air conditioner are on, also turn on the fan to circulate the air. In addition, the user can also request the fan to turn on (`fan_on = 1`), even if the heater and air conditioner are off.

    Try to use only `assign` statements, to see whether you can translate a problem description into a collection of logic gates.

  + 题目分析：同样分析完题目后，可以得到下面的真值表

    + 加热模式下(mode = 1) 只要too_cold = 1就让heater = 1,aircon = 0
    + 制冷模式下(mode = 0) 只要too_hot  =  1就让heater = 0,aircon = 1  
    + 当heater = 1 或者 aircon = 1时，需要打开风扇，且只要fan_on =1就打开风扇换言之就是三者只要有一个是1就打开风扇

    | too_cold | too_hot | mode | fan_on | heater | aircon | fan  |
    | -------- | ------- | ---- | ------ | ------ | ------ | ---- |
    | 0        | 0       | 0    | 0      | 0      | 0      | 0    |
    | 0        | 0       | 0    | 1      | 0      | 0      | 1    |
    | 0        | 0       | 1    | 0      | 0      | 0      | 0    |
    | 0        | 0       | 1    | 1      | 0      | 0      | 1    |
    | 0        | 1       | 0    | 0      | 0      | 1      | 1    |
    | 0        | 1       | 0    | 1      | 0      | 1      | 1    |
    | 0        | 1       | 1    | 0      | 0      | 0      | 0    |
    | 1        | 0       | 0    | 0      | 0      | 0      | 0    |
    | 1        | 0       | 0    | 1      | 0      | 0      | 1    |
    | 1        | 0       | 1    | 0      | 1      | 0      | 1    |
    | 1        | 0       | 1    | 1      | 1      | 0      | 1    |
    | 1        | 1       | 0    | 0      | 0      | 1      | 1    |
    | 1        | 1       | 0    | 1      | 0      | 1      | 1    |
    | 1        | 1       | 1    | 0      | 1      | 0      | 0    |
    | 1        | 1       | 1    | 1      | 1      | 0      | 1    |

    

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input too_cold,
        input too_hot,
        input mode,
        input fan_on,
        output heater,
        output aircon,
        output fan	
    );
        
        assign heater = too_cold & mode ;
        assign aircon = too_hot  & (~mode);
        assign fan    = fan_on | heater | aircon;
        
    endmodule
    /*官网解答*/
    module top_module(
    	input too_cold, 
    	input too_hot,
    	input mode,
    	input fan_on,
    	output heater,
    	output aircon,
    	output fan
    );
    	// Reminder: The order in which you write assign statements doesn't matter. 
    	// assign statements describe circuits, so you get the same circuit in the end
    	// regardless of which portion you describe first.
    
    	// Fan should be on when either heater or aircon is on, and also when requested to do so (fan_on = 1).
    	assign fan = heater | aircon | fan_on;
    	
    	// Heater is on when it's too cold and mode is "heating".
    	assign heater = (mode & too_cold);
    	
    	// Aircon is on when it's too hot and mode is not "heating".
    	assign aircon = (~mode & too_hot);
    	
    	// * Unlike real thermostats, there is no "off" mode here.
    	
    endmodule
    ```

    

+ **3-bit population count**

  + 题目描述：A "population count" circuit counts the number of '1's in an input vector. Build a population count circuit for a 3-bit input vector.

  + 题目分析：设计可以计数输入中1的个数的电路，比如 in = 011 则输出为 2 => 01

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input [2:0] in,
        output [1:0] out );
        
        always @(*) begin	
    		out = 0;
            for (int i=0;i<3;i++)
    			out = out + in[i];
    	end
    
    endmodule
    
    /*官网解答*/
    module top_module (
    	input [2:0] in,
    	output [1:0] out
    );
    
    	// This is a function of 3 inputs. One method is to use a 8-entry truth table:
    	// in[2:0] out[1:0]
    	// 000      00
    	// 001      01
    	// 010      01
    	// 011      10
    	// 100      01
    	// 101      10
    	// 110      10
    	// 111      11
    	assign out[0] = (~in[2] & ~in[1] & in[0]) | (~in[2] & in[1] & ~in[0]) | (in[2] & ~in[1] & ~in[0]) | (in[2] & in[1] & in[0]);
    	assign out[1] = (in[1] & in[0]) | (in[2] & in[0]) | (in[2] & in[1]);
    	
    	// Using the addition operator works too:
    	// assign out = in[0]+in[1]+in[2];
    	
    	// Yet another method uses behavioural code inside a procedure (combinational always block)
    	// to directly implement the truth table:
    	/*
    	always @(*) begin
    		case (in)
    			3'd0: out = 2'd0;
    			3'd1: out = 2'd1;
    			3'd2: out = 2'd1;
    			3'd3: out = 2'd2;
    			3'd4: out = 2'd1;
    			3'd5: out = 2'd2;
    			3'd6: out = 2'd2;
    			3'd7: out = 2'd3;
    		endcase
    	end
    	*/
    	
    endmodule
    ```

    

+ **Gates and vectors**

  + 题目描述：You are given a four-bit input vector in[3:0]. We want to know some relationships 		  between each bit and its neighbour:
    - **out_both**: Each bit of this output vector should indicate whether *both* the corresponding input bit and its neighbour to the **left** (higher index) are '1'. For example, `out_both[2]` should indicate if `in[2]` and `in[3]` are both 1. Since `in[3]` has no neighbour to the left, the answer is obvious so we don't need to know `out_both[3]`.
    - **out_any**: Each bit of this output vector should indicate whether *any* of the corresponding input bit and its neighbour to the **right** are '1'. For example, `out_any[2]` should indicate if either `in[2]` or `in[1]` are 1. Since `in[0]` has no neighbour to the right, the answer is obvious so we don't need to know `out_any[0]`.
    - **out_different**: Each bit of this output vector should indicate whether the corresponding input bit is different from its neighbour to the **left**. For example, `out_different[2]` should indicate if `in[2]` is different from `in[3]`. For this part, treat the vector as wrapping around, so `in[3]`'s neighbour to the left is `in[0]`.
  + 题目分析：根据题目意思
    + 若给定out_both的索引比如为2，我们需要将in中对应的第二位和它左边（从高到低排序）进行与操作看是否为1，共有3种情况 ,out_both[2],out_both[1],out_both[0]
    +  若给定out_any的索引比如为2，我们需要将in中对应的第二位和它右边（从高到低排序）进行或操作看是否为1，共有3种情况,out_any[3],out_any[2],out_any[1]
    + 若给定out_different的索引比如为2，我们需要将in中对应的第二位和它左边（从高到低排序）进行异或 操作看是否为1，共有4种情况,out_different[3],out_different2],out_different[1],out_different[0]

  

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module( 
        input [3:0] in,
        output [2:0] out_both,
        output [3:1] out_any,
        output [3:0] out_different );
    
        always@(*) begin
            for (int i=3;i>0;--i)
                out_both[i-1] = in[i] & in[i-1];
        end
        
        always@(*) begin
            for (int i=0;i<3;++i)
                out_any[i+1] = in[i] | in[i+1];
        end
        
         always@(*) begin
            for (int i=3;i>0;--i)
                out_different[i-1] = in[i] ^ in[i-1];
             out_different[3] = in[3] ^ in[0];
        end
        
    endmodule
    
    /*官网解答*/
    module top_module (
    	input [3:0] in,
    	output [2:0] out_both,
    	output [3:1] out_any,
    	output [3:0] out_different
    );
    
    	// Use bitwise operators and part-select to do the entire calculation in one line of code
    	// in[3:1] is this vector:   					 in[3]  in[2]  in[1]
    	// in[2:0] is this vector:   					 in[2]  in[1]  in[0]
    	// Bitwise-OR produces a 3 bit vector.			   |      |      |
    	// Assign this 3-bit result to out_any[3:1]:	o_a[3] o_a[2] o_a[1]
    
    	// Thus, each output bit is the OR of the input bit and its neighbour to the right:
    	// e.g., out_any[1] = in[1] | in[0];	
    	// Notice how this works even for long vectors.
    	assign out_any = in[3:1] | in[2:0];
    
    	assign out_both = in[2:0] & in[3:1];
    	
    	// XOR 'in' with a vector that is 'in' rotated to the right by 1 position: {in[0], in[3:1]}
    	// The rotation is accomplished by using part selects[] and the concatenation operator{}.
    	assign out_different = in ^ {in[0], in[3:1]};
        
        //这一块为了满足题目要求，将in的高位也就是in[3]和in[0]用位拼接符单独提出后进行异或，其余
        //的位不变
  	
    endmodule
    ```
    
    

+ **Even longer vectors**

  + 题目描述：You are given a 100-bit input vector in[99:0]. We want to know some relationships between each bit and its neighbour:
  
    - **out_both**: Each bit of this output vector should indicate whether *both* the corresponding input bit and its neighbour to the **left** are '1'. For example, `out_both[98]` should indicate if `in[98]` and `in[99]` are both 1. Since `in[99]` has no neighbour to the left, the answer is obvious so we don't need to know `out_both[99]`.
    - **out_any**: Each bit of this output vector should indicate whether *any* of the corresponding input bit and its neighbour to the **right** are '1'. For example, `out_any[2]` should indicate if either `in[2]` or `in[1]` are 1. Since `in[0]` has no neighbour to the right, the answer is obvious so we don't need to know `out_any[0]`.
    - **out_different**: Each bit of this output vector should indicate whether the corresponding input bit is different from its neighbour to the **left**. For example, `out_different[98]` should indicate if `in[98]` is different from `in[99]`. For this part, treat the vector as wrapping around, so `in[99]`'s neighbour to the left is `in[0]`.
  
  + 题目分析：解题思路和上一题的一样，用位选择和部分选择的方式进行赋值
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module( 
        input [99:0] in,
        output [98:0] out_both,
        output [99:1] out_any,
        output [99:0] out_different );
        
    	assign out_both = in & in[99:1];
    	assign out_any = in[99:1] | in ;
    	assign out_different = in ^ {in[0], in[99:1]};
       
    endmodule
    /*官网解答*/
    module top_module (
    	input [99:0] in,
    	output [98:0] out_both,
    	output [99:1] out_any,
    	output [99:0] out_different
    );
    
    	// See gatesv for explanations.
    	assign out_both = in & in[99:1];
    	assign out_any = in[99:1] | in ;
    	assign out_different = in ^ {in[0], in[99:1]};
    	
    endmodule
    ```
  
    



### 总结

+ 从真值表合成电路

```verilog
1.对于N个输入的布尔函数，有2^N个可能的输入组合。真值表的每一行列出一个输入组合，因此总是有2^N行。output列显示每个输入值的输出。

2.创建实现真值表函数的电路的一种简单方法是以乘积和的形式表示该函数，乘积和意味着在真值表的每一行使用一个N输入与门（以检测输入何时与每一行匹配），然后用一个或门进行加和，只需要关注输出为1的行。
```





   