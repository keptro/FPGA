# FPGA学习： Verilog刷题记录（10）

### 刷题网站 ： HDLBits

 ### 第三章 ： Circuits

  #### 第二节 ：Sequential Logic

##### 第一节：Latches and Flip-Flops

+ **D flip-flop**
  + 题目描述：Create a single D flip-flop.
  
    ![Dff.png](https://hdlbits.01xz.net/mw/images/6/6c/Dff.png)
  
  + 题目分析：创建一个D触发器，根据数电的知识知道，输出q=d(这里是clk的上升沿时刻)，用时序逻辑进行一个编写，这里需要注意的是如果使用时序逻辑进行编写的话，需要用非阻塞赋值的方式
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,    // Clocks are used in sequential circuits
        input d,
        output reg q );//
    
        // Use a clocked always block
        //   copy d to q at every positive edge of clk
        //   Clocked always blocks should use non-blocking assignments
        always@(posedge clk) begin
           		q <= d;
        end
    
    endmodule
    /*官网解答*/
    module top_module(
    	input clk,
    	input d,
    	output reg q);
    	
    	// Use non-blocking assignment for edge-triggered always blocks
    	always @(posedge clk)
    		q <= d;
    
    	// Undefined simulation behaviour can occur if there is more than one edge-triggered
    	// always block and blocking assignment is used. Which always block is simulated first?
    	
    endmodule
    ```
  
+ **D flip-flops**
  + 题目描述：Create 8 D flip-flops. All DFFs should be triggered by the positive edge of `clk`.
  
  + 题目分析：创建一个8位的D触发器，和上题类似，就是位宽不一样而已，clk还是上升沿触发
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input [7:0] d,
        output [7:0] q
    );
        
        always@(posedge clk) begin
           	  q <= d; 
        end
    
    endmodule
    
    /*官网解答*/
    module top_module(
    	input clk,
    	input [7:0] d,
    	output reg [7:0] q);
    	
    	// Because q is a vector, this creates multiple DFFs.
    	always @(posedge clk)
    		q <= d;
    	
    endmodule
    
    ```
  
+ **DFF with reset**
  + 题目描述：Create 8 D flip-flops with active high synchronous reset. All DFFs should be triggered by the positive edge of `clk`.
  
  + 题目分析：创建一个高电平有效复位的**同步**D触发器，也就是reset高电平时复位，低电平时赋值d
  
  + 解答: 
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,            // Synchronous reset
        input [7:0] d,
        output [7:0] q
    );
        
        always@(posedge clk) begin
            if (reset == 1'b0)
                q <= d;
            else 
                q <= 8'd0;
        end
    
    endmodule
    
    /*官网解答*/
    无
    ```
  
    
  
+ **DFF with reset value**
  
  + 题目描述：Create 8 D flip-flops with active high synchronous reset. The flip-flops must be reset to 0x34 rather than zero. All DFFs should be triggered by the **negative** edge of `clk`.
  
  + 题目分析：要求是创建一个高电平有效复位的**同步**时D触发器，其中复位时赋值为0x34，并且所有的D触发器都应该是clk的下降沿触发
  
  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,
        input [7:0] d,
        output [7:0] q
    );
        always@(negedge clk) begin
            if (reset == 1'b1)
                q <= 8'h34;
            else
                q <= d;
        end
    
    endmodule
    
    /*官网解答*/
    无
    ```
  
    
  
  
  
+ **DFF with asynchronous reset**
  
  + 题目描述：Create 8 D flip-flops with active high asynchronous reset. All DFFs should be triggered by the positive edge of `clk`.
  
  + 题目分析：要求创建一个高电平有效复位的**异步**D触发器，编写时与同步时序逻辑不一样的地方就在于敏感列表的编写不同，如果是异步的话，敏感列表中需要加上areset，官网的解答给出的解释
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input areset,   // active high asynchronous reset
        input [7:0] d,
        output [7:0] q
    );
        always@(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                q <= 8'd0;
            else 
                q <= d;
        end
    
    endmodule
    /*官网解答*/
    module top_module(
    	input clk,
    	input [7:0] d,
    	input areset,
    	output reg [7:0] q);
    	
    	// The only difference in code compared to synchronous reset is in the sensitivity list.
    	always @(posedge clk, posedge areset)
    		if (areset)
    			q <= 0;
    		else
    			q <= d;
    
    
    	// In Verilog, the sensitivity list looks strange. The FF's reset is sensitive to the
    	// *level* of areset, so why does using "posedge areset" work?
    	// To see why it works, consider the truth table for all events that change the input 
    	// signals, assuming clk and areset do not switch at precisely the same time:
    	
    	//  clk		areset		output
    	//   x		 0->1		q <= 0; (because areset = 1)
    	//   x		 1->0		no change (always block not triggered)
    	//  0->1	   0		q <= d; (not resetting)
    	//  0->1	   1		q <= 0; (still resetting, q was 0 before too)
    	//  1->0	   x		no change (always block not triggered)
    	
    endmodule 
    ```
  
+ **DFF with byte enable**
  + 题目描述：Create 16 D flip-flops. It's sometimes useful to only modify parts of a group of flip-flops. The byte-enable inputs control whether each byte of the 16 registers should be written to on that cycle. `byteena[1]` controls the upper byte `d[15:8]`, while `byteena[0]` controls the lower byte `d[7:0]`.
  
    `resetn` is a synchronous, active-low reset.
  
    All DFFs should be triggered by the positive edge of `clk`.
  
  + 题目分析：创建一个16位的**同步**D触发器，其中题目还引入一个字节控制的信号byteena，其中byteena的第一位控制d的高8位（byteena[1] = 1时有效），byteena的第二位控制d的低八位(byteena[0]=1时有效)，如果byteena的两位同时为1则输出d，同时为0则原样输出。（这里要注意，不同于数组，verilog中索引是从**1**开始的）
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input resetn,
        input [1:0] byteena,
        input [15:0] d,
        output [15:0] q
    );
        
        always@(posedge clk) begin
            if (resetn == 1'b0)
                q <= 16'd0;
            else begin
                case (byteena)
                    2'b10   : q <= {d[15:8],q[7:0]};
                    2'b01   : q <= {q[15:8],d[7:0]};
                    2'b11   : q <= d;
                    default : q <= q;
                endcase
            end
        end
    endmodule
    /*官网解答*/
    无
    ```
  
+ **D Latch**
  + 题目描述：Implement the following circuit:
  
    ![Exams m2014q4a.png](https://hdlbits.01xz.net/mw/images/0/03/Exams_m2014q4a.png)
  
  + 题目分析：latch由电平触发，非同步控制。在使能信号有效时latch相当于通路，在使能信号无效时latch保持输出状态。DFF由时钟沿触发，同步控制
  
    官网给出的提示
  
    - Latches are level-sensitive (not edge-sensitive) circuits, so in an always block, they use level-sensitive sensitivity lists.
    - However, they are still sequential elements, so should use non-blocking assignments.
    - A D-latch acts like a wire (or non-inverting buffer) when enabled, and preserves the current value when disabled.
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input d, 
        input ena,
        output q);
    
        always@(posedge d or posedge ena) begin
            if (ena == 1'b1)
                q <= d;
            else 
                q <= q;
        end
        
    endmodule
    /*官网解答*/
    ```
  
+ **DFF**
  + 题目描述：Implement the following circuit:
  
  ![](D:\Linux-windows\HDLBits\sequential logic\Exams_m2014q4b.png)
  
  + 题目分析：建立一个异步复位的D触发器
  
+ 解答：
  
  ```verilog
  /*我的解答*/
  module top_module (
      input clk,
      input d, 
      input ar,   // asynchronous reset
      output q);
  
      always @(posedge clk or posedge ar)begin
          if (ar == 1'b1)
              q <= 1'b0;
          else
              q <= d;
      end
      
  endmodule
  /*官网解答*/
  无
  ```
  
  
  
+ **DFF**
  + 题目描述：Implement the following circuit:
  
  ![](D:\Linux-windows\HDLBits\sequential logic\Exams_m2014q4c.png)
  
  + 题目分析：建立一个同步复位的D触发器
  + 解答：
  
  ```verilog
  /*我的解答*/
  module top_module (
      input clk,
      input d, 
      input r,   // synchronous reset
      output q);
  
      always @(posedge clk ) begin
          if (r == 1'b1)
              q <= 1'b0;
          else
              q <= d;
      end
      
  endmodule
  /*官网解答*/
  无
  ```
  
  
  
+ **DFF+gate**
  + 题目描述：Implement the following circuit:
  
  ![](D:\Linux-windows\HDLBits\sequential logic\Exams_m2014q4d.png)
  
  + 题目分析：和门电路的结合，可以先将门电路的输出表示出来，在和D触发器连接即可
  + 解答：
  
  ```verilog
  /*我的解答*/
  module top_module (
      input clk,
      input in, 
      output out);
      
      wire xor_q;
      assign xor_q = in ^ out;
      always @(posedge clk) begin
         out <= xor_q; 
      end
  
  endmodule
  /*官网解答*/
  无
  ```
  
  
  
+ **Mux and DFF**
  + 题目描述：Consider the sequential circuit below
  
    Assume that you want to implement hierarchical Verilog code for this circuit, using three instantiations of a submodule that has a flip-flop and multiplexer in it. Write a Verilog module (containing one flip-flop and multiplexer) named `top_module` for this submodule.
  
  ![](D:\Linux-windows\HDLBits\sequential logic\Mt2015_muxdff.png)
  
  + 题目分析：这里只要求我们写出这个框图里选择器和D触发器的两个功能结合的模块即可
  + 解答：
  
  ```verilog
/*我的解答*/
  module top_module (
  	input clk,
  	input L,
  	input r_in,
  	input q_in,
  	output reg Q);
      
      wire	mux_out;    // 定义连接线
      assign mux_out = (L == 1'b0) ? q_in : r_in;  //根据L做选择
      
      always @(posedge clk) begin
         Q<= mux_out; 
      end
  
  endmodule
  /*官网解答*/
  无
  ```
  
  
  
+ **Mux and DFF**
  + 题目描述：Consider the *n*-bit shift register circuit shown below:
  
    Write a Verilog module named top_module for one stage of this circuit, including both the flip-flop and multiplexers.
  
    ![Exams 2014q4.png](https://hdlbits.01xz.net/mw/thumb.php?f=Exams_2014q4.png&width=900)
  
  + 题目分析：题目要求编写一个模块，里面包括一个D触发器，两个选择器，连线如图所示，模块命名为top_module
  
  + 解答：
  
    ```verilog
    //我的解答
    module top_module (
        input clk,
        input w, R, E, L,
        output Q
    );
        wire	mux_1;		//第一个选择器的输出
        wire	mux_2;		//第二个选择器的输出
        wire	q	 ;		//将D触发器的输出引出 方便接反馈线
        
        //D触发器
        always@(posedge clk) begin
           q <= mux_2; 
        end
    	
        //第一个选择器
        assign  mux_1 = E ? w : q;
        //第二个选择器
        assign	mux_2 = L ? R : mux_1;
        //对输出赋值
        assign  Q = q;
        
        
    endmodule
    //官网解答
    ```
  
+ **DFFs and gates**
  + 题目描述：Given the finite state machine circuit as shown, assume that the D flip-flops are initially reset to zero before the machine begins.
  
    Build this circuit.
  
    ![](D:\Linux-windows\HDLBits\sequential logic\Ece241_2014_q4.png)
  
  + 题目分析：要我们根据图创建这个电路，这里通过加入一个D触发器模块，然后在顶层模块里例化
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input x,
        output z
    ); 
        /*第一部分的连接*/
        wire	d0		;
        wire	d0in	;
        assign d0in = d0 ^ x;
        dff_module dff_module_inst0(
            .clk	(clk)	,
            .d		(d0in)	,
            .q		(d0)	,
            .qn		( )		
        );
        /*第二部分的连接*/
        wire	d1n		;
        wire	d1		;
        wire	d1in	;
        assign	d1in = d1n & x; 
        dff_module dff_module_inst1(
            .clk 	(clk)	,
            .d		(d1in)	,
            .q		(d1)	,
            .qn		(d1n)
        );
        /*第三部分的连接*/
        wire	d2n		;
        wire	d2		;
        wire	d2in	;
        assign	d2in = d2n | x; 
        dff_module dff_module_inst2(
            .clk 	(clk)	,
            .d		(d2in)	,
            .q		(d2)	,
            .qn		(d2n)
        );
        
        /*最后的输出结果*/
        assign z = ~(d0 | d1 | d2);
    
    endmodule
    
    /*D触发器模块*/
    module dff_module(
    	input	wire	clk	,
        input	wire	d	,
        output	reg		q	,
        output	wire	qn	
    );
        always@(posedge clk) begin
           	q <= d; 
        end
        assign qn = ~q;
        
    endmodule
    /*官网解答*/
    无
    ```
  
+ **Create circuit from truth table**
  + 题目描述：A JK flip-flop has the below truth table. Implement a JK flip-flop with only a D-type flip-flop and gates. Note: Qold is the output of the D flip-flop before the positive clock edge.
  
    | J    | K    | Q     |
    | :--- | :--- | :---- |
    | 0    | 0    | Qold  |
    | 0    | 1    | 0     |
    | 1    | 0    | 1     |
    | 1    | 1    | ~Qold |
  
  + 题目分析：这里就是编写一个JK触发器，由真值表可以看出，四种情况都是不一样的，所以我的思路定义一个**sel**信号用来选择，而**sel**的组成是**J和K**一个作为高位，一个作为低位组成的，最后用case语句就能把所有情况列举出来了
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input j,
        input k,
        output Q); 
        
        wire	[1:0]	sel	;            //选择信号
        assign sel = {j,k};              //J做高位，K做低位
        always@(posedge clk) begin
            case (sel)                   //用case语句做选择     
                2'b00	:	Q <=  Q;
                2'b11	:   Q <= ~Q;
                default	:   Q <= sel[1];
            endcase
        end
    
    endmodule
    /*官网解答*/
    无
    ```
  
+ **Detect an edge**
  + 题目描述：For each bit in an 8-bit vector, detect when the input signal changes from 0 in one clock cycle to 1 the next (similar to positive edge detection). The output bit should be set the cycle after a 0 to 1 transition occurs.
  
    Here are some examples. For clarity, in[1] and pedge[1] are shown separately.
  
    ![wavedrom](D:\Linux-windows\HDLBits\latches and flip-flops\wavedrom.png)
  
  + 题目分析：这里是想要获取输入信号的上升沿，采用的方式是对输入信号进行打一拍的操作，然后将输入信号与打拍后的信号进行与非就抓取到了输入信号的上升沿，如下图所示
  
    ![image-20210503210741126](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210503210741126.png)
  
    打拍后的信号存放到in_reg中如图所示，然后进行与非的操作，“与非”是“有0出1”
  
    当检测到输入信号有上升沿时即对应输入信号的状态为“1”
  
    而对于打拍的输入信号此时对应的状态是“0”
  
    所以它俩一与非就表示了输入信号有上升沿
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input [7:0] in,
        output [7:0] pedge
    );
        reg	 [7:0] in_reg;
        always @(posedge clk )begin
            in_reg <= in;
            pedge <= in & (~in_reg);
        end
    
    endmodule
    /*官网解答*/
    module top_module(
    	input clk,
    	input [7:0] in,
    	output reg [7:0] pedge);
    	
    	reg [7:0] d_last;	
    			
    	always @(posedge clk) begin
    		d_last <= in;			// Remember the state of the previous cycle
    		pedge <= in & ~d_last;	// A positive edge occurred if input was 0 and is now 1.
    	end
    	
    endmodule
    ```
  
    
  
+ **Detect both edges**
  + 题目描述：For each bit in an 8-bit vector, detect when the input signal changes from one clock cycle to the next (detect any edge). The output bit should be set the cycle after a 0 to 1 transition occurs.
  
    Here are some examples. For clarity, in[1] and anyedge[1] are shown separately
  
    ![image-20210430172421063](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210430172421063.png)
  
  + 题目分析：这里是想要获取输入信号的双升沿，采用的方式是对输入信号进行打一拍的操作，然后将输入信号与打拍后的信号进行异或就抓取到了输入信号的双升沿，如下图所示
  
    ![image-20210503203736734](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210503203736734.png)
  
    异或是二者不同就为1，基于这个特点，对输入信号进行打拍后，对于打拍的信号来说它是输入信号的前一个状态，所以只要当输入信号出现沿的变化，和打拍的信号的状态肯定是不同的，对它们进行异或就表示每当有一个沿出现输出就置1，也就采集到了双边沿
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input [7:0] in,
        output [7:0] anyedge
    );
        reg [7:0] in_reg;
    
        always @(posedge clk) begin
           in_reg <= in;
           anyedge <= in ^ in_reg;
        end
        
    endmodule
    /*官网解答*/
    无
    ```
  
    
  
+ **Edge capture register**
  + 题目描述：For each bit in a 32-bit vector, capture when the input signal changes from 1 in one clock cycle to 0 the next. "Capture" means that the output will remain 1 until the register is reset (synchronous reset).
  
    Each output bit behaves like a SR flip-flop: The output bit should be set (to 1) the cycle after a 1 to 0 transition occurs. The output bit should be reset (to 0) at the positive clock edge when reset is high. If both of the above events occur at the same time, reset has precedence. In the last 4 cycles of the example waveform below, the 'reset' event occurs one cycle earlier than the 'set' event, so there is no conflict here.
  
    In the example waveform below, reset, in[1] and out[1] are shown again separately for clarity
  
    ![image-20210430172730590](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210430172730590.png)
  
  + 题目分析：这题难度升级了，大概就是希望我们采集到信号的输入信号的下降沿，并用这个下降沿做判断，构建一个SR触发器，我觉得这里主要有两点需要注意，第一个就是下降沿应该怎么获取，第二个就是如何用下降沿做判断，因为这里给出来的是32位，verilog里没有直接对多位宽的进行检测的语句，所以想到的是对下降沿信号的每一位做判断，这里就需要用到for来对信号的每一位进行遍历从而达到利用下降沿做判断的效果。如何获取信号的下降沿呢？如下图所示
  
    ![image-20210503210157226](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210503210157226.png)
  
    其实做法和采集输入信号上升沿的操作非常的类似，先要对输入信号进行打拍，接着也是进行与非的操作，与采集上升沿不同的是，采集下降沿时需要将**非号**加到输入信号上，其实就是与非左右操作数进行一下交换，这样就可以采集到下降沿。
  
    接着将采集到的下降沿，存到一个临时变量里，然后用for遍历每一位，出现1表示输入信号出现下降沿，此时将数据输出。
  
  + 解答：
  
  ```verilog
  /*我的解答*/
  module top_module (
      input clk,
      input reset,
      input [31:0] in,
      output [31:0] out
  );
      /*获取信号的下降沿*/
      reg [31:0] in_reg;             
      always @(posedge clk) begin
          in_reg <= in;
      end
      
      /*将结果寄存到临时的寄存器中*/
      reg		[31:0] 	temp_reg;
  	assign temp_reg = ~in&in_reg;
      
      //对每一位进行一个判断，这里需要用到for循环
      always @(posedge clk) begin
          if(reset == 1'b1) 
              out <= 0;
          else begin
              for(int i=0;i<32;i=i+1) begin
                  if(temp_reg[i]==1'b1)   //有哪位出现了下降沿，哪位数据发生改变
                      out[i] <= 1'b1;
                  else                   //没出现下降沿的保持不变
                      out[i] <= out[i];
              end
          end
      end
  endmodule
  /*官网解答*/
  无
  ```
  
  
  
+ **Dual-edge triggered flip-flop**
  + 题目描述：You're familiar with flip-flops that are triggered on the positive edge of the clock, or negative edge of the clock. A dual-edge triggered flip-flop is triggered on both edges of the clock. However, FPGAs don't have dual-edge triggered flip-flops, and `always @(posedge clk or negedge clk)` is not accepted as a legal sensitivity list.
  
    Build a circuit that functionally behaves like a dual-edge triggered flip-flop:
  
    ![](D:\Linux-windows\HDLBits\sequential logic\wavedrom.png)
  
  + 题目分析：题目要求构建一个在功能上表现为双边缘触发触发器的电路，这里在没有看到答案前我也没有弄清楚怎么写的，题目表示说verilog没有这样`always @(posedge clk or negedge clk)`书写的敏感列表，所以这种方式是不可取的，但是呢没说不可以分开进行操作，即分别写`always@(posedge clk)`和`always@(negedge clk)`将其产生的结果存起来，然后在利用组合逻辑进行最后输出的赋值即可。官网的答案给出来解答和解释，讲的很清楚了。
  
  + 解答：
  
  ```verilog
  /*我的解答*/
  module top_module (
      input clk,
      input d,
      output q
  );
      reg q_pedge; 
      reg q_nedge;
      
      always@(posedge clk)begin
          q_pedge <= d ^ q_nedge;
      end
      always@(negedge clk)begin
          q_nedge <= d ^ q_pedge;
      end
      
      assign q = q_pedge ^ q_nedge;  
  endmodule
  
  /*官网解答*/
  module top_module(
  	input clk,
  	input d,
  	output q);
  	
  	reg p, n;
  	
  	// A positive-edge triggered flip-flop
      always @(posedge clk)
          p <= d ^ n;
          
      // A negative-edge triggered flip-flop
      always @(negedge clk)
          n <= d ^ p;
      
      // Why does this work? 
      // After posedge clk, p changes to d^n. Thus q = (p^n) = (d^n^n) = d.
      // After negedge clk, n changes to p^n. Thus q = (p^n) = (p^p^n) = d.
      // At each (positive or negative) clock edge, p and n FFs alternately
      // load a value that will cancel out the other and cause the new value of d to remain.
      assign q = p ^ n;
      
      
  	// Can't synthesize this.
  	/*always @(posedge clk, negedge clk) begin
  		q <= d;
  	end*/ 
      
  endmodule
  
  ```
  
  

### 总结 

```verilog
1.如何获取信号的沿
首先对想要采集沿的信号(假设是in)进行打一拍的操作存放到临时寄存器中，假设是in_reg 则：
  获取上升沿：in &~ in_reg
  获取下降沿：in_reg &~ in
  获取双边沿：in ^ in_reg
2.同步复位与异步复位
  同步复位：指复位信号在时钟信号触发时，复位才有效
  异步复位：指复位信号不管时钟信号是否有触发，只有复位有效，输出立刻变为0
  写法：
 	 /*同步复位*/
	always @(posedge clk) begin
   	 	if (rst == 1'b? )
       	...	
	end
    /*异步复位*/    
    always @(posedge clk or negedge rst_n) begin
        if (rst_n == 1'b0)
                ...
    end
3.构建一个在功能上表现为双边缘触发触发器的电路
    // After posedge clk, p changes to d^n. Thus q = (p^n) = (d^n^n) = d.
    // After negedge clk, n changes to p^n. Thus q = (p^n) = (p^p^n) = d.
    // At each (positive or negative) clock edge, p and n FFs alternately
    // load a value that will cancel out the other and cause the new value of d to 
```



   