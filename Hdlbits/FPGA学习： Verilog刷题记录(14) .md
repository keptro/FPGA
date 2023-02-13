# FPGA学习： Verilog刷题记录（14）

### 刷题网站 ： HDLBits

 ### 第三章 ： Circuits

  #### 第二节 ：Sequential Logic

##### 第一节：Finite State Machines（1）

+ **Simple FSM 1(asynchronous reset)**

  + 题目描述：This is a Moore state machine with two states, one input, and one output. Implement this state machine. Notice that the reset state is B.

    This exercise is the same as [fsm1s](https://hdlbits.01xz.net/wiki/fsm1s), but using asynchronous reset.

    ![image-20210504154746388](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210504154746388.png)

  + 题目分析：根据状态图可以直接书写代码，这里编写的是三段式状态机

  + 解答：

    ```C
    /*我的解答*/
    module top_module(
        input clk,
        input areset,    // Asynchronous reset to state B
        input in,
        output out);//  
    
    	parameter A = 1'b0,
        		  B = 1'b1;
        
        reg c_state;
        reg n_state;
        
        //第一段 描述状态的转移 异步时序逻辑
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                c_state <= B;
            else
                c_state <= n_state;
        end
                
        //第二段 描述状态转移的条件 组合逻辑
        always @(*) begin
            case(c_state)
                A : begin
                    if (in == 1'b1)
                        n_state = A;
                    else
                        n_state = B;
                end
                B : begin
                    if (in == 1'b1)
                        n_state = B;
                    else
                        n_state = A;
                end
                default: n_state = B;
            endcase
        end
        //第三段 描述状态的输出
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                out <= 1'b1;
            else begin
                case (n_state)
                    A : out <= 1'b0;
                    B : out <= 1'b1;
                endcase
            end
        end
    
    endmodule
    /*官网解答*/
    module top_module (
    	input clk,
    	input in,
    	input areset,
    	output out
    );
    
    	// Give state names and assignments. I'm lazy, so I like to use decimal numbers.
    	// It doesn't really matter what assignment is used, as long as they're unique.
    	parameter A=0, B=1;
    	reg state;		// Ensure state and next are big enough to hold the state encoding.
    	reg next;
        
        
        // A finite state machine is usually coded in three parts:
        //   State transition logic
        //   State flip-flops
        //   Output logic
        // It is sometimes possible to combine one or more of these blobs of code
        // together, but be careful: Some blobs are combinational circuits, while some
        // are clocked (DFFs).
        
        
        // Combinational always block for state transition logic. Given the current state and inputs,
        // what should be next state be?
        // Combinational always block: Use blocking assignments.
        always@(*) begin
    		case (state)
    			A: next = in ? A : B;
    			B: next = in ? B : A;
    		endcase
        end
        
        
        
        // Edge-triggered always block (DFFs) for state flip-flops. Asynchronous reset.
        always @(posedge clk, posedge areset) begin
    		if (areset) state <= B;		// Reset to state B
            else state <= next;			// Otherwise, cause the state to transition
    	end
    		
    		
    		
    	// Combinational output logic. In this problem, an assign statement is the simplest.
    	// In more complex circuits, a combinational always block may be more suitable.
    	assign out = (state==B);
    
    	
    endmodule
    ```

    

+ **Simple FSM 1(synchronous reset)**

  + 题目描述：This is a Moore state machine with two states, one input, and one output. Implement this state machine. Notice that the reset state is B.

    This exercise is the same as [fsm1](https://hdlbits.01xz.net/wiki/fsm1), but using synchronous reset.

    ![image-20210505094358342](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505094358342.png)

  + 题目分析：根据状态图可以直接书写代码，这里编写的是三段式状态机

  + 解答：

    ```C
    /*我的解答*/
    // Note the Verilog-1995 module declaration syntax here:
    module top_module(clk, reset, in, out);
        input clk;
        input reset;    // Synchronous reset to state B
        input in;
        output out;//  
        reg out;
    
    	parameter A = 1'b0,
        		  B = 1'b1;
        
        reg c_state;
        reg n_state;
        
        //第一段 描述状态的转移 同步时序逻辑
        always @(posedge clk) begin
            if (reset == 1'b1)
                c_state <= B;
            else
                c_state <= n_state;
        end
                
        //第二段 描述状态转移的条件 组合逻辑
        always @(*) begin
            case(c_state)
                A : begin
                    if (in == 1'b1)
                        n_state = A;
                    else
                        n_state = B;
                end
                B : begin
                    if (in == 1'b1)
                        n_state = B;
                    else
                        n_state = A;
                end
                default: n_state = B;
            endcase
        end
        //第三段 描述状态的输出
        always @(posedge clk) begin
            if (reset == 1'b1)
                out <= 1'b1;
            else begin
                case (n_state)
                    A : out <= 1'b0;
                    B : out <= 1'b1;
                endcase
            end
        end
    
    
    endmodule
    ```

    

+ **Simple FSM 2(asynchronous reset)**

  + 题目描述：This is a Moore state machine with two states, two inputs, and one output. Implement this state machine.

    This exercise is the same as [fsm2s](https://hdlbits.01xz.net/wiki/fsm2s), but using asynchronous reset.

    ![image-20210505094835897](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505094835897.png)

  + 题目分析：根据状态图可以直接书写代码，这里编写的是三段式状态机。其中这里将`j`和`k`信号合并在一起，方便编写。

  + 解答：

    ```C
    /*我的解答*/
    module top_module(
        input clk,
        input areset,    // Asynchronous reset to OFF
        input j,
        input k,
        output out); //  
    
    	parameter OFF = 1'b0,
        		  ON  = 1'b1;
        
        reg c_state;
        reg n_state;
        wire [1:0] sel;
    
        assign sel = {j,k};
        
        //first stage
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                c_state <= OFF;
            else
                c_state <= n_state;
        end
        
        //second stage
        always @(*) begin
            case (c_state) 
                OFF: begin
                    case (sel)
                        2'b00 : n_state = OFF;
                        2'b01 : n_state = OFF;
                        2'b10 : n_state = ON ;
                        2'b11 : n_state = ON ;
                      default : n_state = OFF;
                    endcase
                end
                ON : begin
                    case (sel)
                        2'b00 : n_state = ON  ;
                        2'b01 : n_state = OFF ;
                        2'b10 : n_state = ON  ;
                        2'b11 : n_state = OFF ;
                      default : n_state = ON  ;
                    endcase
                end
                default: n_state = OFF;
            endcase
        end
                        
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                out <= 1'b0;
            else begin
                case(n_state)
                    OFF : out <= 1'b0;
                    ON  : out <= 1'b1;
                endcase
            end
        end                                 
    
    endmodule
    /*官网解答*/
    module top_module (
    	input clk,
    	input j,
    	input k,
    	input areset,
    	output out
    );
    	parameter A=0, B=1;
    	reg state;
    	reg next;
        
        always_comb begin
    		case (state)
    			A: next = j ? B : A;
    			B: next = k ? A : B;
    		endcase
        end
        
        always @(posedge clk, posedge areset) begin
    		if (areset) state <= A;
            else state <= next;
    	end
    		
    	assign out = (state==B);
    
    	
    endmodule
    ```

    

+ **Simple FSM 2(synchronous reset)**

  + 题目描述：This is a Moore state machine with two states, two inputs, and one output. Implement this state machine.

    This exercise is the same as [fsm2](https://hdlbits.01xz.net/wiki/fsm2), but using synchronous reset.

    ![image-20210505101436456](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505101436456.png)

  + 题目分析：根据状态图可以直接书写代码，这里编写的是三段式状态机。同上一题改用同步复位

  + 解答：

    ```C
    /*我的解答*/
    module top_module(
        input clk,
        input reset,    // Synchronous reset to OFF
        input j,
        input k,
        output out); //  
    
    	parameter OFF = 1'b0,
        		  ON  = 1'b1;
        
        reg c_state;
        reg n_state;
        wire [1:0] sel;
    
        assign sel = {j,k};
        
        //first stage
        always @(posedge clk) begin
            if (reset == 1'b1)
                c_state <= OFF;
            else
                c_state <= n_state;
        end
        
        //second stage
        always @(*) begin
            case (c_state) 
    			OFF : n_state = j ? ON  : OFF;
                ON  : n_state = k ? OFF : ON ;
            endcase
        end
                        
        always @(posedge clk) begin
            if (reset == 1'b1)
                out <= 1'b0;
            else begin
                case(n_state)
                    OFF : out <= 1'b0;
                    ON  : out <= 1'b1;
                endcase
            end
        end                                 
    endmodule
    ```

    

+ **Simple state transitions 3**

  + 题目描述：The following is the state transition table for a Moore state machine with one input, one output, and four states. Use the following state encoding: A=2'b00, B=2'b01, C=2'b10, D=2'b11.

    **Implement only the state transition logic and output logic** (the combinational logic portion) for this state machine. Given the current state (`state`), compute the `next_state` and output (`out`) based on the state transition table.

    ![image-20210505102716990](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505102716990.png)

  + 题目分析：可以由状态表直接读出各个状态转移的条件，以及它的输出。给出的是Moore型的状态机，表明输出仅仅与状态有关，与输入无关。这里我们只需要写出状态转移的逻辑以及输出的逻辑即可

  + 解答：

    ```C
    /*我的解答*/
    module top_module(
        input in,
        input [1:0] state,
        output [1:0] next_state,
        output out); //
    
        parameter A = 2'b00,
                  B = 2'b01,
                  C = 2'b10,
                  D = 2'b11;
        
        always @(*) begin
            case(state) 
                A : next_state = in ? B : A;
                B : next_state = in ? B : C;
                C : next_state = in ? D : A;
                D : next_state = in ? B : C;
            endcase
        end
        
        always @(*) begin
            case (state)
                D: out = 1'b1;
             default: out = 1'b0;
            endcase
        end
    
    endmodule
    /*官网解答*/
    ```

    

+ **Simple one-hot state transitions 3**

  + 题目描述：

    The following is the state transition table for a Moore state machine with one input, one output, and four states. Use the following one-hot state encoding: A=4'b0001, B=4'b0010, C=4'b0100, D=4'b1000.

    **Derive state transition and output logic equations by inspection** assuming a one-hot encoding. Implement only the state transition logic and output logic (the combinational logic portion) for this state machine. (The testbench will test with non-one hot inputs to make sure you're not trying to do something more complicated).

    ![image-20210505105250472](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505105250472.png)

  + 题目分析：同上题

  + 解答：

    ```C
    /*我的解答*/
    module top_module(
        input in,
        input [3:0] state,
        output [3:0] next_state,
        output out); //
    
        parameter A=0, B=1, C=2, D=3;
    
        // State transition logic: Derive an equation for each state flip-flop.
        assign next_state[A] = state[0] & (~in) | state[2] & (~in);
        assign next_state[B] = state[0] & in | state[1] & in | state[3] & in;
        assign next_state[C] = state[1] & (~in) | state[3] &(~in);
        assign next_state[D] = state[2] & in;
    
        // Output logic: 
        assign out = state[3];
    
    endmodule
    /*官网解答*/
    无，其中给了Derive state transition and output logic equations by inspection的说明
    What does "derive equations by inspection" mean?
    One-hot state machine encoding guarantees that exactly one state bit is 1. This means that it is possible to determine whether the state machine is in a particular state by examining only one state bit, not all state bits. This leads to simple logic equations for the state transitions by examining the incoming edges for each state in the state transition diagram.
    
    For example, in the above state machine, how can the state machine can reach state A? It must use one of the two incoming edges: "Currently in state A and in=0" or "Currently in state C and in = 0". Due to the one-hot encoding, the logic equation to test for "currently in state A" is simply the state bit for state A. This leads to the final logic equation for the next state of state bit A: next_state[0] = state[0]&(~in) | state[2]&(~in). The one-hot encoding guarantees that at most one clause (product term) will be "active" at a time, so the clauses can just be ORed together.
    
    When an exercise asks for state transition equations "by inspection", use this particular method. The judge will test with non-one-hot inputs to ensure your logic equations follow this method, rather that doing something else (such as resetting the FSM) for illegal (non-one-hot) combinations of the state bits.
    
    Although knowing this algorithm isn't necessary for RTL-level design (the logic synthesizer handles this), it is illustrative of why one-hot FSMs often have simpler logic (at the expense of more state bit storage), and this topic frequently shows up on exams in digital logic courses.
    ```

    

+ **Simple FSM 3(asynchronous reset)**

  + 题目描述：See also: [State transition logic for this FSM](https://hdlbits.01xz.net/wiki/Fsm3comb)

    The following is the state transition table for a Moore state machine with one input, one output, and four states. Implement this state machine. Include an asynchronous reset that resets the FSM to state A.

    ![image-20210505122257072](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505122257072.png)

  + 题目分析：可以由状态表直接读出各个状态转移的条件，以及它的输出，这里编写的是三段式状态机

  + 解答：

    ```C
    /*我的解答*/
    module top_module(
        input clk,
        input in,
        input areset,
        output out); //
    
        parameter A = 2'b00,
                  B = 2'b01,
                  C = 2'b10,
                  D = 2'b11;
        reg [1:0] c_state;
        reg [1:0] n_state;
        
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                c_state <= A;
            else
                c_state <= n_state;
        end
        
        always @(*) begin
            case(c_state) 
                A : n_state = in ? B : A;
                B : n_state = in ? B : C;
                C : n_state = in ? D : A;
                D : n_state = in ? B : C;
            endcase
        end
        
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                out <= 1'b0;
            else begin
                case(n_state)
    				D : out <= 1'b1;
                 default:out <= 1'b0;
                endcase
            end
        end  
    
    endmodule
    /*官网解答*/
    module top_module (
    	input clk,
    	input in,
    	input areset,
    	output out
    );
    
    	// Give state names and assignments. I'm lazy, so I like to use decimal numbers.
    	// It doesn't really matter what assignment is used, as long as they're unique.
    	parameter A=0, B=1, C=2, D=3;
    	reg [1:0] state;		// Make sure state and next are big enough to hold the state encodings.
    	reg [1:0] next;
        
    
    
    
        // Combinational always block for state transition logic. Given the current state and inputs,
        // what should be next state be?
        // Combinational always block: Use blocking assignments.    
        always@(*) begin
    		case (state)
    			A: next = in ? B : A;
    			B: next = in ? B : C;
    			C: next = in ? D : A;
    			D: next = in ? B : C;
    		endcase
        end
    
    
    
        // Edge-triggered always block (DFFs) for state flip-flops. Asynchronous reset.
        always @(posedge clk, posedge areset) begin
    		if (areset) state <= A;
            else state <= next;
    	end
    		
    
    		
    	// Combinational output logic. In this problem, an assign statement is the simplest.		
    	assign out = (state==D);
    	
    endmodule
    ```

    

+ **Simple FSM 3(synchronous reset)**

  + 题目描述：See also: [State transition logic for this FSM](https://hdlbits.01xz.net/wiki/Fsm3comb)

    The following is the state transition table for a Moore state machine with one input, one output, and four states. Implement this state machine. Include a synchronous reset that resets the FSM to state A. (This is the same problem as [Fsm3](https://hdlbits.01xz.net/wiki/Fsm3) but with a synchronous reset.)

    ![image-20210505122649756](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505122649756.png)

  + 题目分析：同上题的解法，只需将异步复位修改成同步复位即可。

  + 解答：

    ```C
    /*我的解答*/
    module top_module(
        input clk,
        input in,
        input reset,
        output out); //
    
        parameter A = 2'b00,
                  B = 2'b01,
                  C = 2'b10,
                  D = 2'b11;
        reg [1:0] c_state;
        reg [1:0] n_state;
        
        always @(posedge clk) begin
            if (reset == 1'b1)
                c_state <= A;
            else
                c_state <= n_state;
        end
        
        always @(*) begin
            case(c_state) 
                A : n_state = in ? B : A;
                B : n_state = in ? B : C;
                C : n_state = in ? D : A;
                D : n_state = in ? B : C;
            endcase
        end
        
        always @(posedge clk ) begin
            if (reset == 1'b1)
                out <= 1'b0;
            else begin
                case(n_state)
    				D : out <= 1'b1;
                 default:out <= 1'b0;
                endcase
            end
        end  
    
    endmodule
    /*官网解答*/
    无
    ```

    

+ **Design a Moore FSM**

  + 题目描述：

    ![image-20210505122858827](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210505122858827.png)

    Also include an active-high synchronous reset that resets the state machine to a state equivalent to if the water level had been low for a long time (no sensors asserted, and all four outputs asserted).

  + 题目分析：由题意可以得到下列信息，除dfr赋值的条件不太一样外，fr1，fr2，fr3的赋值仅与状态有关

    ​					官网的解答，划分了6个状态将dfr融入到了状态机内，十分不错的解法。
  
    ![image-20210726124715720](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210726124715720.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,
        input [3:1] s,
        output reg fr3,
        output reg fr2,
        output reg fr1,
        output reg dfr
    ); 
    
        reg [1:0] c_state ;
        reg [1:0] p_state ;
    
        parameter A = 2'b00, //Above S3
                  B = 2'b01, //Between S3 and S2
                  C = 2'b10, //Between S2 and S1
                  D = 2'b11; //Below S1
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                c_state <= D; 
            else
                c_state <= p_state;
        end
    
        always @(*) begin
            case(s)
                3'b000  : p_state = D;
                3'b001  : p_state = C;
                3'b011  : p_state = B; 
                3'b111  : p_state = A;
                default : p_state = D;
            endcase
        end
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                dfr <= 1'b1;
            else begin
                if(c_state > p_state)
                    dfr <= 1'b0;
                else if (c_state < p_state)
                    dfr <= 1'b1;
                else
                    dfr <= dfr;
            end
        end
    
        always @(posedge clk) begin
            if(reset == 1'b1) begin
                fr1 <= 1'b1;
                fr2 <= 1'b1;
                fr3 <= 1'b1;
            end
            else begin
                case(p_state)
                    A : begin
                        fr1 <= 1'b0;
                        fr2 <= 1'b0;
                        fr3 <= 1'b0;
                    end
                    B : begin
                        fr1 <= 1'b1;
                        fr2 <= 1'b0;
                        fr3 <= 1'b0;                
                    end
                    C :  begin
                        fr1 <= 1'b1;
                        fr2 <= 1'b1;
                        fr3 <= 1'b0;
                    end
                    D :  begin
                        fr1 <= 1'b1;
                        fr2 <= 1'b1;
                        fr3 <= 1'b1;
                    end
                    default : ;
                endcase
            end
        end
    
    endmodule
    /*官网解答*/
    module top_module (
    	input clk,
    	input reset,
    	input [3:1] s,
    	output reg fr3,
    	output reg fr2,
    	output reg fr1,
    	output reg dfr
    );
    
    
    	// Give state names and assignments. I'm lazy, so I like to use decimal numbers.
    	// It doesn't really matter what assignment is used, as long as they're unique.
    	// We have 6 states here.
    	parameter A2=0, B1=1, B2=2, C1=3, C2=4, D1=5;
    	reg [2:0] state, next;		// Make sure these are big enough to hold the state encodings.
    	
    
    
        // Edge-triggered always block (DFFs) for state flip-flops. Synchronous reset.	
    	always @(posedge clk) begin
    		if (reset) state <= A2;
    		else state <= next;
    	end
    
    
    
        // Combinational always block for state transition logic. Given the current state and inputs,
        // what should be next state be?
        // Combinational always block: Use blocking assignments.    
    	always@(*) begin
    		case (state)
    			A2: next = s[1] ? B1 : A2;
    			B1: next = s[2] ? C1 : (s[1] ? B1 : A2);
    			B2: next = s[2] ? C1 : (s[1] ? B2 : A2);
    			C1: next = s[3] ? D1 : (s[2] ? C1 : B2);
    			C2: next = s[3] ? D1 : (s[2] ? C2 : B2);
    			D1: next = s[3] ? D1 : C2;
    			default: next = 'x;
    		endcase
    	end
    	
    	
    	
    	// Combinational output logic. In this problem, a procedural block (combinational always block) 
    	// is more convenient. Be careful not to create a latch.
    	always@(*) begin
    		case (state)
    			A2: {fr3, fr2, fr1, dfr} = 4'b1111;
    			B1: {fr3, fr2, fr1, dfr} = 4'b0110;
    			B2: {fr3, fr2, fr1, dfr} = 4'b0111;
    			C1: {fr3, fr2, fr1, dfr} = 4'b0010;
    			C2: {fr3, fr2, fr1, dfr} = 4'b0011;
    			D1: {fr3, fr2, fr1, dfr} = 4'b0000;
    			default: {fr3, fr2, fr1, dfr} = 'x;
    		endcase
    	end
    	
    endmodule
    
    ```
  
    



   