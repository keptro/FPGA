# FPGA学习： Verilog刷题记录（12）

### 刷题网站 ： HDLBits

 ### 第三章 ： Circuits

  #### 第二节 ：Sequential Logic

##### 第三小节：Shift Registers

+ **4-bit shift register**

  + 题目描述：

    Build a 4-bit shift register (right shift), with asynchronous reset, synchronous load, and enable.

    - `areset`: Resets shift register to zero.
    - `load`: Loads shift register with `data[3:0]` instead of shifting.
    - `ena`: Shift right (`q[3]` becomes zero, `q[0]` is shifted out and disappears).
    - `q`: The contents of the shift register.

    If both the `load` and `ena` inputs are asserted (1), the `load` input has higher priority.

  + 题目分析：构建一个移位寄存器，要求异步复位，同步加载，并且受使能信号的控制，同时加载信号的优先级比使能信号的优先级要高。

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input areset,  // async active-high reset to zero
        input load,
        input ena,
        input [3:0] data,
        output reg [3:0] q); 
        
        
        always @(posedge clk or posedge areset) begin
            if(areset == 1'b1)
                q <= 4'd0;			  //异步复位
            else if (load == 1'b1)    //加载
                q <= data;
            else if (ena == 1'b1)    //使能有效，进行移位
                q <= {1'b0,q[3:1]};  //移位主要通过位拼接的形式完成
            else
                q <= q;              //其余情况保持当前状态
        end
    
    endmodule
    /*官网解答*/
    module top_module(
    	input clk,
    	input areset,
    	input load,
    	input ena,
    	input [3:0] data,
    	output reg [3:0] q);
    	
    	// Asynchronous reset: Notice the sensitivity list.
    	// The shift register has four modes:
    	//   reset
    	//   load
    	//   enable shift
    	//   idle -- preserve q (i.e., DFFs)
    	always @(posedge clk, posedge areset) begin
    		if (areset)		// reset
    			q <= 0;
    		else if (load)	// load
    			q <= data;
    		else if (ena)	// shift is enabled
    			q <= q[3:1];	// Use vector part select to express a shift.
    	end
    	
    endmodule
    
    ```

    

+ **Left/right rotator**

  + 题目描述：

    Build a 100-bit left/right rotator, with synchronous load and left/right enable. A rotator shifts-in the shifted-out bit from the other end of the register, unlike a shifter that discards the shifted-out bit and shifts in a zero. If enabled, a rotator rotates the bits around and does not modify/discard them.

    - `load`: Loads shift register with `data[99:0]` instead of rotating.

    - ena[1:0]

      : Chooses whether and which direction to rotate.

      - `2'b01` rotates right by one bit
      - `2'b10` rotates left by one bit
      - `2'b00` and `2'b11` do not rotate.

    - `q`: The contents of the rotator.

  + 题目分析：与上一题类似，同步进行加载，有使能控制。不同的是现在是q内部进行一个移位，并且用ena控制移位的方向。

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input load,
        input [1:0] ena,
        input [99:0] data,
        output reg [99:0] q); 
        
        always @(posedge clk ) begin
            if(load == 1'b1)
                q <= data;   //加载数据
            else begin
                case (ena)
                    2'b01: q <= {q[0],q[99:1]};   //右移
                    2'b10: q <= {q[98:0],q[99]};  //左移
                    default: q <= q ;
                endcase
            end
        end
    
    endmodule
    
    /*官网解答*/
    module top_module(
    	input clk,
    	input load,
    	input [1:0] ena,
    	input [99:0] data,
    	output reg [99:0] q);
    	
    	// This rotator has 4 modes:
    	//   load
    	//   rotate left
    	//   rotate right
    	//   do nothing
    	// I used vector part-select and concatenation to express a rotation.
    	// Edge-sensitive always block: Use non-blocking assignments.
    	always @(posedge clk) begin
    		if (load)		// Load
    			q <= data;
    		else if (ena == 2'h1)	// Rotate right
    			q <= {q[0], q[99:1]};
    		else if (ena == 2'h2)	// Rotate left
    			q <= {q[98:0], q[99]};
    	end
    endmodule
    ```

    

+ **Left/right arithmetic shift by 1 or 8**

  + 题目描述：

    Build a 64-bit *arithmetic* shift register, with synchronous load. The shifter can shift both left and right, and by 1 or 8 bit positions, selected by `amount`.

    An *arithmetic* right shift shifts in the sign bit of the number in the shift register (`q[63]` in this case) instead of zero as done by a *logical* right shift. Another way of thinking about an arithmetic right shift is that it assumes the number being shifted is signed and preserves the sign, so that arithmetic right shift divides a signed number by a power of two.

    There is no difference between logical and arithmetic *left* shifts.

    - `load`: Loads shift register with `data[63:0]` instead of shifting.

    - `ena`: Chooses whether to shift.

    - amount

      : Chooses which direction and how much to shift.

      - `2'b00`: shift left by 1 bit.
      - `2'b01`: shift left by 8 bits.
      - `2'b10`: shift right by 1 bit.
      - `2'b11`: shift right by 8 bits.

    - `q`: The contents of the shifter.

  + 题目分析：建立一个算术移位寄存器，左移时没有特别需要注意的。关键是右移，如果考虑有符号数的移位，则有所不同。

    1. 如果符号位为1，则右移后是补1
    2. 如果符号位为0，则右移后是补0

    ![image-20210720130929568](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210720130929568.png)这幅图给出的是有符号数进行移位时的情况，移动3位。

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input load,
        input ena,
        input [1:0] amount,
        input [63:0] data,
        output reg [63:0] q); 
        
    
        always @(posedge clk) begin
            if(load == 1'b1) 
                q <= data;
            else if(ena == 1'b1) begin
                case (amount)
                    2'b00: q <= {q[62:0],1'b0};         //左移1位
                    2'b01: q <= {q[55:0],{8{1'b0}}};    //左移8位
                    2'b10: q <= {q[63],q[63:1]};        //右移1位
                    2'b11: q <= {{8{q[63]}},q[63:8]};   //右移8位
                    default: ;
                endcase
            end
            else
                q <= q;
        end
    
    endmodule
    /*官网解答*/
    无
    ```

    

+ **5-bit LFSR**

  + 题目描述：A [linear feedback shift register](https://en.wikipedia.org/wiki/Linear_feedback_shift_register) is a shift register usually with a few XOR gates to produce the next state of the shift register. A Galois LFSR is one particular arrangement where bit positions with a "tap" are XORed with the output bit to produce its next value, while bit positions without a tap shift. If the taps positions are carefully chosen, the LFSR can be made to be "maximum-length". A maximum-length LFSR of n bits cycles through 2n-1 states before repeating (the all-zero state is never reached).

    The following diagram shows a 5-bit maximal-length Galois LFSR with taps at bit positions 5 and 3. (Tap positions are usually numbered starting from 1). Note that I drew the XOR gate at position 5 for consistency, but one of the XOR gate inputs is 0.
    [![Lfsr5.png](https://hdlbits.01xz.net/mw/images/9/9a/Lfsr5.png)](https://hdlbits.01xz.net/wiki/File:Lfsr5.png)

    Build this LFSR. The `reset` should reset the LFSR to 1.

  + 题目分析：创建一个线性反馈移位寄存器，此题因为位数比较小，并且也给出了设计图，所以可以直接通过图描述出代码

  + 解答：

    ```verilog 
    /*我的解答*/
    module top_module(
      input clk,
        input reset,    // Active-high synchronous reset to 5'h1
        output [4:0] q
    ); 
        
        always @(posedge clk) begin
            if(reset == 1'b1)
                q <= 5'h1;
            else begin
                q <= {q[0],q[4],q[3]^q[0],q[2:1]};
            end
        end
    
    endmodule
    
    /*官网解答*/
    module top_module(
    	input clk,
    	input reset,
    	output reg [4:0] q);
    	
    	reg [4:0] q_next;		// q_next is not a register
    
    	// Convenience: Create a combinational block of logic that computes
    	// what the next value should be. For shorter code, I first shift
    	// all of the values and then override the two bit positions that have taps.
    	// A logic synthesizer creates a circuit that behaves as if the code were
    	// executed sequentially, so later assignments override earlier ones.
    	// Combinational always block: Use blocking assignments.
    	always @(*) begin
    		q_next = q[4:1];	// Shift all the bits. This is incorrect for q_next[4] and q_next[2]
    		q_next[4] = q[0];	// Give q_next[4] and q_next[2] their correct assignments
    		q_next[2] = q[3] ^ q[0];
    	end
    	
    	
    	// This is just a set of DFFs. I chose to compute the connections between the
    	// DFFs above in its own combinational always block, but you can combine them if you wish.
    	// You'll get the same circuit either way.
    	// Edge-triggered always block: Use non-blocking assignments.
    	always @(posedge clk) begin
    		if (reset)
    			q <= 5'h1;
    		else
    			q <= q_next;
    	end
    	
    endmodule
    ```
    
    

+ **3-bit LFSR**

  + 题目描述：Taken from 2015 midterm question 5. See also the first part of this question: 

    ![](C:\Users\lzck\Desktop\3.png)

    Write the Verilog code for this sequential circuit (Submodules are ok, but the top-level must be named `top_module`). Assume that you are going to implement the circuit on the DE1-SoC board. Connect the `R` inputs to the `SW` switches, connect Clock to `KEY[0]`, and `L` to `KEY[1]`. Connect the `Q` outputs to the red lights `LEDR`.

  + 题目分析：其实也是看图连接的题目，我这里选择设计一个子模块包括选择器，D触发器，接着按图所示进行调用即可

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
    	input [2:0] SW,      // R
    	input [1:0] KEY,     // L and clk
    	output [2:0] LEDR);  // Q
    
        
        submodule lfsr1(KEY[0],SW[0],LEDR[2],KEY[1],LEDR[0]);   //r0
        submodule lfsr2(KEY[0],SW[1],LEDR[0],KEY[1],LEDR[1]);   //r1
        submodule lfsr3(KEY[0],SW[2],LEDR[1]^LEDR[2],KEY[1],LEDR[2]);   //r2
       
    endmodule
    
    module submodule(
    	input		clk			,
        input		r_in		,
        input 		q_in		,
        input		L			,
        output		q
    );
        wire	mux_out ;
        assign  mux_out =  L ? r_in : q_in ;
        
        always @(posedge clk) begin
            q <= mux_out;
        end
        
    endmodule
    /*官网解答*/
    无
    ```

    

+ **32-bit LFSR**

  + 题目描述：

    See [Lfsr5](https://hdlbits.01xz.net/wiki/Lfsr5) for explanations.

    Build a 32-bit Galois LFSR with taps at bit positions 32, 22, 2, and 1.

  + 题目分析：同编写5位的线性反馈移位寄存器一样，这里只是位数变多了，题目已经将需要加异或门的位置告诉了我们，我们只需要注意所给位置即可。这里是模仿网站作者的写法，觉得比较不容易出错，用位拼接的方式容易把自己弄晕

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input reset,    // Active-high synchronous reset to 32'h1
        output [31:0] q
    ); 
        reg	[31:0]	q_next;
        
        always @(*) begin
            q_next = q[31:1];
            q_next[31] = q[0];
            q_next[21] = q[22]^q[0];
            q_next[1]  = q[2]^q[0];
            q_next[0]  = q[1]^q[0];
        end
        
        always @(posedge clk) begin
            if(reset == 1'b1)
                q <= 32'h1;
            else
                q<=q_next;
        end
    
    endmodule
    /*官网解答*/
    无
    ```

    

+ **Shift register**

  + 题目描述：

    Implement the following circuit:

    ![Exams m2014q4k.png](https://hdlbits.01xz.net/mw/images/1/15/Exams_m2014q4k.png)

  + 题目分析：电路图已经给我们，可以直接根据电路编写，out实际为in打了四拍的一个结果

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input resetn,   // synchronous reset
        input in,
        output out);
        
        reg in1,in2,in3;
        always @(posedge clk) begin
            if(resetn == 1'b0) begin
                in1 <= 1'b0;
                in2 <= 1'b0;
                in3 <= 1'b0;
                out <= 1'b0; 
            end
            else begin
                in1 <= in;
                in2 <= in1;
                in3 <= in2;
                out <= in3;
            end
        end
    
    endmodule
    /*官网解答*/
    module top_module (
    	input clk,
    	input resetn,
    	input in,
    	output out
    );
    
    	reg [3:0] sr;
    	
    	// Create a shift register named sr. It shifts in "in".
    	always @(posedge clk) begin
    		if (~resetn)		// Synchronous active-low reset
    			sr <= 0;
    		else 
    			sr <= {sr[2:0], in};
    	end
    	
    	assign out = sr[3];		// Output the final bit (sr[3])
    
    endmodule
    ```

    

+ **Shift register**

  + 题目描述：

    Consider the *n*-bit shift register circuit shown below:

    [![Exams 2014q4.png](https://hdlbits.01xz.net/mw/thumb.php?f=Exams_2014q4.png&width=900)](https://hdlbits.01xz.net/wiki/File:Exams_2014q4.png)

    Write a top-level Verilog module (named top_module) for the shift register, assuming that *n* = 4. Instantiate four copies of your MUXDFF subcircuit in your top-level module. Assume that you are going to implement the circuit on the DE2 board.

    - Connect the *R* inputs to the *SW* switches,
    - *clk* to *KEY[0]*,
    - *E* to *KEY[1]*,
    - *L* to *KEY[2]*, and
    - *w* to *KEY[3]*.
    - Connect the outputs to the red lights *LEDR[3:0]*.

  + 题目分析：题目要求其实不难，需要我们编写子模块电路，然后在顶层模块中进行例化即可，同前面的一道题类似，关键点就是要弄清楚各个模块间的连接关系

  + 解答：

    ```verilog
  /*我的解答*/
    module top_module (
      input [3:0] SW,
        input [3:0] KEY,
        output [3:0] LEDR
    ); //
    
        MUXDFF MUXDFF1(KEY[0],KEY[1],KEY[2],SW[0],LEDR[1],LEDR[0]);
        MUXDFF MUXDFF2(KEY[0],KEY[1],KEY[2],SW[1],LEDR[2],LEDR[1]);
        MUXDFF MUXDFF3(KEY[0],KEY[1],KEY[2],SW[2],LEDR[3],LEDR[2]);
        MUXDFF MUXDFF4(KEY[0],KEY[1],KEY[2],SW[3],KEY[3],LEDR[3]);
        
    endmodule
    
    module MUXDFF (
    	input	wire		clk			,
        input	wire		selE		,
        input	wire		selL		,
        input	wire		r_in		,
        input	wire		w			,
        output	reg			q	
    );
        wire	mux1_out ,mux2_out;
        assign mux1_out = selE ? w : q;
        assign mux2_out = selL ? r_in : mux1_out;
        
        always @(posedge clk) begin
        	q <= mux2_out;     
        end
       
    endmodule
    /*官网解答*/
    无
    ```
    
    

+ **3-input LUT**

  + 题目描述：

    In this question, you will design a circuit for an 8x1 memory, where writing to the memory is accomplished by shifting-in bits, and reading is "random access", as in a typical RAM. You will then use the circuit to realize a 3-input logic function.

    First, create an 8-bit shift register with 8 D-type flip-flops. Label the flip-flop outputs from Q[0]...Q[7]. The shift register input should be called **S**, which feeds the input of Q[0] (MSB is shifted in first). The **enable** input controls whether to shift. Then, extend the circuit to have 3 additional inputs **A**,**B**,**C** and an output **Z**. The circuit's behaviour should be as follows: when ABC is 000, Z=Q[0], when ABC is 001, Z=Q[1], and so on. Your circuit should contain ONLY the 8-bit shift register, and multiplexers. (Aside: this circuit is called a 3-input look-up-table (LUT)).

  + 题目分析：该题要我们设计一个8x1的内存，它的值是移位寄存器的输出。要求我们先创建一个8位的移位寄存器，根据ABC的取值，给出对应的输出，这里定义一个线网型变量sel(注意其位宽)，然后使用位拼接将ABC拼接赋值给sel，后用case进行选择。网站作者给出的答案更加简洁些

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input enable,
        input S,
    input A, B, C,
        output Z ); 
        
        wire[2:0] sel;    //3位的选择信号
        reg [7:0] sr;     //8个DFF
        
        
        assign sel = {A,B,C}; //sel由ABC组成，用位拼接可实现
        
        //用组合逻辑+时序逻辑实现移位的效果
        always @(posedge clk) begin
            if(enable == 1'b1)          //使能有效的情况下，进行移位
                sr <= {sr[6:0],S};      //相当于左移
            else
                sr <= sr;               
        end
        
        always @(*) begin
            case (sel)                //根据题目要求进行选择
               3'b000 : Z = sr[0];
               3'b001 : Z = sr[1];
               3'b010 : Z = sr[2];
               3'b011 : Z = sr[3];
               3'b100 : Z = sr[4];
               3'b101 : Z = sr[5];
               3'b110 : Z = sr[6];
               3'b111 : Z = sr[7];
               default : Z = Z;
           endcase
        end
    
    endmodule
    
    /*官网解答*/
    module top_module (
    	input clk,
    	input enable,
    	input S,
    	
    	input A, B, C,
    	output reg Z
    );
    
    	reg [7:0] q;
    	
    	// The final circuit is a shift register attached to a 8-to-1 mux.
    	
    
    
    	// Create a 8-to-1 mux that chooses one of the bits of q based on the three-bit number {A,B,C}:
    	// There are many other ways you could write a 8-to-1 mux
    	// (e.g., combinational always block -> case statement with 8 cases).
    	assign Z = q[ {A, B, C} ];
    
    
    	// Edge-triggered always block: This is a standard shift register (named q) with enable.
    	// When enabled, shift to the left by 1 (discarding q[7] and and shifting in S).
    	always @(posedge clk) begin
    		if (enable)
    			q <= {q[6:0], S};	
    	end
    	
    ```
    
    


### 总结 

```verilog
本次主要学习了如何构建一个移位寄存器，构建的几种方式
左移：
无需考虑是否为有符号还是无符号数，都是进行补零处理
同时左移可以看成是原数乘了2的幂次方，即X‘=X*(2^M)
这里 X指需要被移位的数，M只移位的大小
例如 7=0111，左移2位后变成 011100 = 28 = 7*2^2；
右移：
当移位的数是有符号数时需要注意
如果最高位是1，则移位后补1处理
如果最高位是0，则移位后补0处理
```



   