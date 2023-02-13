# FPGA学习： Verilog刷题记录（14-1）

### 刷题网站 ： HDLBits

 ### 第三章 ： Circuits

  #### 第二节 ：Sequential Logic

##### 第一节：Finite State Machines（2）

+ **Lemmings1**

  + 题目描述：The game [Lemmings](https://en.wikipedia.org/wiki/Lemmings_(video_game)) involves critters with fairly simple brains. So simple that we are going to model it using a finite state machine.

    In the Lemmings' 2D world, Lemmings can be in one of two states: walking left or walking right. It will switch directions if it hits an obstacle. In particular, if a Lemming is bumped on the left, it will walk right. If it's bumped on the right, it will walk left. If it's bumped on both sides at the same time, it will still switch directions.

    Implement a Moore state machine with two states, two inputs, and one output that models this behaviour.

  + 题目分析：其实官网也给出了提示如下图，这样一来状态图已经有啦，只需要按步骤编写即可

    ![Lemmings1.png](C:\Users\lzck\Desktop\Lemmings1.png)

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input areset,    // Freshly brainwashed Lemmings walk left.
        input bump_left,
        input bump_right,
        output walk_left,
        output walk_right); 
    
        reg     c_state     ;    //current state
        reg     n_state     ;    //next state
    
        parameter LEFT  = 1'b0  ,    //LEFT
                  RIGHT = 1'b1  ;    //RIGHT
    
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                c_state <= LEFT ;
            else
                c_state <= n_state;
        end 
    
        always @(*) begin
            case(c_state)
               LEFT : begin       //向左走
                    if(bump_left == 1'b1)
                        n_state = RIGHT;
                    else if(bump_left == 1'b0)
                        n_state = LEFT;
                    else
                        n_state = n_state;
                end
               RIGHT : begin     //向右走
                    if(bump_right == 1'b1)
                        n_state = LEFT;
                    else if (bump_right == 1'b0)
                        n_state = RIGHT;
                    else
                        n_state = n_state;
                end
            default: n_state = LEFT;
            endcase
        end
    
        always @(posedge clk or posedge areset) begin
            if(areset == 1'b1) begin
                walk_left <= 1'b1;
                walk_right <= 1'b0;
            end
            else begin
                case(n_state)
                   LEFT : begin
                        walk_left <= 1'b1;
                        walk_right <= 1'b0;
                    end
                   RIGHT : begin
                        walk_left <= 1'b0;
                        walk_right <= 1'b1; 
                    end
                default: begin
    					walk_left <= 1'b1;
                        walk_right <= 1'b0;                
                	end
                endcase
            end
    
        end
    
    endmodule
    /*官网解答*/
    module top_module (
    	input clk,
    	input areset,
    	input bump_left,
    	input bump_right,
    	output walk_left,
    	output walk_right
    );
    
    	// Give state names and assignments. I'm lazy, so I like to use decimal numbers.
    	// It doesn't really matter what assignment is used, as long as they're unique.
    	parameter WL=0, WR=1;
    	reg state;
    	reg next;
        
        
        // Combinational always block for state transition logic. Given the current state and inputs,
        // what should be next state be?
        // Combinational always block: Use blocking assignments.    
        always@(*) begin
    		case (state)
    			WL: next = bump_left  ? WR : WL;
    			WR: next = bump_right ? WL : WR;
    		endcase
        end
        
        
        // Combinational always block for state transition logic. Given the current state and inputs,
        // what should be next state be?
        // Combinational always block: Use blocking assignments.    
        always @(posedge clk, posedge areset) begin
    		if (areset) state <= WL;
            else state <= next;
    	end
    		
    		
    	// Combinational output logic. In this problem, an assign statement are the simplest.
    	// In more complex circuits, a combinational always block may be more suitable.		
    	assign walk_left = (state==WL);
    	assign walk_right = (state==WR);
    
    	
    endmodule
    ```

    

+ **Lemmings2**

  + 题目描述：In addition to walking left and right, Lemmings will fall (and presumably go "aaah!") if the ground disappears underneath them.

    In addition to walking left and right and changing direction when bumped, when `ground=0`, the Lemming will fall and say "aaah!". When the ground reappears (`ground=1`), the Lemming will resume walking in the same direction as before the fall. Being bumped while falling does not affect the walking direction, and being bumped in the same cycle as ground disappears (but not yet falling), or when the ground reappears while still falling, also does not affect the walking direction.

    Build a finite state machine that models this behaviour.

  + 题目分析：这题是在前一题基础上的扩展，添加了一个ground信号，当`ground=0`时表明地面塌陷，aaah此时置1，当`ground=1`时表明地面恢复原状，此时行进的方向应当是地面塌陷前的行进方向，即如果塌陷前是向左走的，那恢复后也应该是向左走。另外官网也给出了响应的提示，如下状态图

    ![Lemmings2.png](https://hdlbits.01xz.net/mw/images/2/27/Lemmings2.png)

    作者将其分成了4个状态分别叙述它所代表的情况，下图是我将其补全后的状态转移图

    ![image-20210726202806053](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210726202806053.png)

    **LEFT**与**RIGHT**状态和上一题类似，关键是**FALL_L**与**FALL_R**首先我们得清楚这两个状态所表示的情况，**FALL_L**表示说塌陷前是向左行进的，而**FALL_R**则表示说塌陷前是向右行进的，弄清楚这个后状态图就比较好画了，`ground=0`时**LEFT**与**RIGHT**分别跳转至相应状态即可。

    另外需要弄清楚的是，每个状态具体是做什么事情，比如说**FALL_L**当处在这个状态时除了`aaah=1`外，`walk_left`要将其置为0，我开始没有注意这一点，所以一直无法通过验证**FALL_R**也是类似。除此之外，在状态转移那一块，特别需要注意优先级的问题，ground的优先级是最高的，用`if-else`书写代码时尤其需要注意这点。

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input areset,    // Freshly brainwashed Lemmings walk left.
        input bump_left,
        input bump_right,
        input ground,
        output reg  walk_left,
        output reg  walk_right,
        output reg  aaah 
    ); 
    
        reg   [1:0]  c_state     ;
        reg   [1:0]  n_state     ;
    
        parameter LEFT   = 2'b00  ,    
                  RIGHT  = 2'b01  ,    
                  FALL_L = 2'b10  ,    
                  FALL_R = 2'b11  ;    
    
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                c_state <= LEFT ;
            else
                c_state <= n_state;
        end
     
        always @(*) begin
            case(c_state)
                LEFT : begin       //向左走
                    if(ground == 1'b0)     //ground的优先级最高
                        n_state = FALL_L;
                    else if(bump_left == 1'b1)
                        n_state = RIGHT;
                    else
                        n_state = LEFT;
                end
                RIGHT : begin     //向右走
                    if(ground == 1'b0)    //ground的优先级最高
                        n_state = FALL_R;
                    else if(bump_right == 1'b1)
                        n_state = LEFT;
                    else
                        n_state = RIGHT;
                end
                FALL_R : begin
                    if(ground == 1'b1)
                        n_state = RIGHT;
                    else
                        n_state = FALL_R;
                end
                FALL_L : begin
                    if(ground == 1'b1)
                        n_state = LEFT;
                    else
                        n_state = FALL_L;
                end
            default: n_state = LEFT;
            endcase
        end
    
        always @(posedge clk or posedge areset) begin
            if(areset == 1'b1) begin
                walk_left  <= 1'b1;
                walk_right <= 1'b0;
                aaah       <= 1'b0;
            end
            else begin
                case(n_state)
                    LEFT : begin
                        walk_left  <= 1'b1;
                        walk_right <= 1'b0;
                        aaah       <= 1'b0;
                    end
                    RIGHT : begin
                        walk_left  <= 1'b0;
                        walk_right <= 1'b1;
                        aaah       <= 1'b0; 
                    end
                    FALL_R : begin
                        aaah <= 1'b1;
                        walk_right <= 1'b0;
                    end
                    FALL_L : begin
                        aaah <= 1'b1;
                        walk_left <= 1'b0; 
                    end
                default: begin
    					walk_left  <= 1'b1;
                        walk_right <= 1'b0;  
                        aaah       <= 1'b0;              
                	end
                endcase
            end
        end
    
    endmodule
    /*官网解答*/
  无
    ```
    
    

+ **Lemmings3**

  + 题目描述：In addition to walking and falling, Lemmings can sometimes be told to do useful things, like dig (it starts digging when `dig=1`). A Lemming can dig if it is currently walking on ground (`ground=1` and not falling), and will continue digging until it reaches the other side (`ground=0`). At that point, since there is no ground, it will fall (aaah!), then continue walking in its original direction once it hits ground again. As with falling, being bumped while digging has no effect, and being told to dig when falling or when there is no ground is ignored.

    (In other words, a walking Lemming can fall, dig, or switch directions. If more than one of these conditions are satisfied, fall has higher precedence than dig, which has higher precedence than switching directions.)

    Extend your finite state machine to model this behaviour.

  + 题目分析：如果上一题弄清楚了，这一题也是依葫芦画瓢，仅是多了一个`dig`信号，状态转换图如下

    ![image-20210726224327283](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210726224327283.png)

    这里仍需要清楚，每个状态所代表的事件是什么，谁的优先级高

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input areset,    // Freshly brainwashed Lemmings walk left.
        input bump_left,
        input bump_right,
        input ground,
        input dig,
        output reg  walk_left,
        output reg  walk_right,
        output reg  aaah,
        output reg  digging 
    );
    
        reg   [5:0]  c_state     ;
        reg   [5:0]  n_state     ;
    
        parameter LEFT   = 6'b000001  ,    //LEFT
                  RIGHT  = 6'b000010  ,    //RIGHT
                  FALL_L = 6'b000100  ,    //FALL_L
                  FALL_R = 6'b001000  ,    //FALL_R
                  DIG_L  = 6'b010000  ,    //DIG_L
                  DIG_R  = 6'b100000  ;
    
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                c_state <= LEFT ;
            else
                c_state <= n_state;
        end
     
        always @(*) begin
            case(c_state)
                LEFT : begin       //向左走
                    if(ground == 1'b0)
                        n_state = FALL_L;
                    else if(dig == 1'b1)
                        n_state = DIG_L;
                    else if(bump_left == 1'b1)
                        n_state = RIGHT;
                    else
                        n_state = LEFT;
                end
                RIGHT : begin     //向右走
                    if(ground == 1'b0)
                        n_state = FALL_R;
                    else if(dig == 1'b1)
                        n_state = DIG_R;
                    else if(bump_right == 1'b1)
                        n_state = LEFT;
                    else
                        n_state = RIGHT;
                end
                FALL_R : begin
                    if(ground == 1'b1)
                        n_state = RIGHT;
                    else
                        n_state = FALL_R;
                end
                FALL_L : begin
                    if(ground == 1'b1)
                        n_state = LEFT;
                    else
                        n_state = FALL_L;
                end
                DIG_R : begin
                    if(ground == 1'b0)
                        n_state = FALL_R;
                    else
                        n_state = DIG_R;
                end
                DIG_L : begin
                    if(ground == 1'b0)
                        n_state = FALL_L;
                    else
                        n_state = DIG_L;
                end
            default: n_state = LEFT;
            endcase
        end
    
        always @(posedge clk or posedge areset) begin
            if(areset == 1'b1) begin
                walk_left  <= 1'b1;
                walk_right <= 1'b0;
                aaah       <= 1'b0;
                digging    <= 1'b0;
            end
            else begin
                case(n_state)
                    LEFT : begin
                        walk_left  <= 1'b1;
                        walk_right <= 1'b0;
                        aaah       <= 1'b0;
                        digging    <= 1'b0;
                    end
                    RIGHT : begin
                        walk_left  <= 1'b0;
                        walk_right <= 1'b1;
                        aaah       <= 1'b0; 
                        digging    <= 1'b0;
                    end
                    FALL_R : begin
                        aaah       <= 1'b1;
                        walk_right <= 1'b0;
                        digging    <= 1'b0;
                    end
                    FALL_L : begin
                        aaah      <= 1'b1;
                        walk_left <= 1'b0;
                        digging   <= 1'b0; 
                    end
                    DIG_R : begin
                        digging    <= 1'b1;
                        aaah       <= 1'b0;
                        walk_right <= 1'b0;
                    end
                    DIG_L : begin
                        digging    <= 1'b1;
                        aaah       <= 1'b0;
                        walk_left  <= 1'b0;                   
                    end
                default: begin
    					walk_left  <= 1'b1;
                        walk_right <= 1'b0;  
                        aaah       <= 1'b0;   
                        digging    <= 1'b0;           
                	end
                endcase
            end
        end
    
    endmodule
    /*官网解答*/
  无
    ```
    
    

+ **Lemmings4**

  + 题目描述：
  
    Although Lemmings can walk, fall, and dig, Lemmings aren't invulnerable. If a Lemming falls for too long then hits the ground, it can splatter. In particular, if a Lemming falls for more than 20 clock cycles then hits the ground, it will splatter and cease walking, falling, or digging (all 4 outputs become 0), forever (Or until the FSM gets reset). There is no upper limit on how far a Lemming can fall before hitting the ground. Lemmings only splatter when hitting the ground; they do not splatter in mid-air.
  
    Extend your finite state machine to model this behaviour.
  
    ![image-20210727143602419](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210727143602419.png)
  
  + 题目分析：Lemming系列的最后一题。当下落时间超过20个周期，并且撞击地面旅鼠死亡。状态转换图如下所示，此题的要点是计数器的位宽，因为题目也说可以无限下落，为了防止位宽过小，在下落的过程中计数器溢出，所以应该把计数器设的大一点，其他部分和前三题基本一致，所以只是在上面几题的基础上做出的修改
  
    ![image-20210727143213397](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210727143213397.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input areset,    // Freshly brainwashed Lemmings walk left.
        input bump_left,
        input bump_right,
        input ground,
        input dig,
        output reg  walk_left,
        output reg  walk_right,
        output reg  aaah,
        output reg  digging 
    );
    
        reg   [6:0]  c_state     ;
        reg   [6:0]  n_state     ;
        reg   [7:0]  cnt_clk     ;
    
        parameter LEFT   = 7'b0000001  ,    
                  RIGHT  = 7'b0000010  ,    
                  FALL_L = 7'b0000100  ,    
                  FALL_R = 7'b0001000  ,    
                  DIG_L  = 7'b0010000  ,    
                  DIG_R  = 7'b0100000  ,    
                  SPLAT  = 7'b1000000  ;
    
        always @(posedge clk or posedge areset) begin
            if (areset == 1'b1)
                c_state <= LEFT ;
            else
                c_state <= n_state;
        end
     
        always @(*) begin
            case(c_state)
                LEFT : begin       //向左走
                    if(ground == 1'b0)
                        n_state = FALL_L;
                    else if(dig == 1'b1)
                        n_state = DIG_L;
                    else if(bump_left == 1'b1)
                        n_state = RIGHT;
                    else
                        n_state = LEFT;
                end
                RIGHT : begin     //向右走
                    if(ground == 1'b0)
                        n_state = FALL_R;
                    else if(dig == 1'b1)
                        n_state = DIG_R;
                    else if(bump_right == 1'b1)
                        n_state = LEFT;
                    else
                        n_state = RIGHT;
                end
                FALL_R : begin
                    if(ground == 1'b1) begin
                        if(cnt_clk >= 8'd21)
                            n_state = SPLAT;
                        else
                            n_state = RIGHT;
                    end
                    else
                        n_state = FALL_R;
                end
                FALL_L : begin
                    if(ground == 1'b1) begin
                        if(cnt_clk >= 8'd21)
                            n_state = SPLAT;
                        else
                            n_state = LEFT;
                    end
                    else
                        n_state = FALL_L;
                end
                DIG_R : begin
                    if(ground == 1'b0)
                        n_state = FALL_R;
                    else
                        n_state = DIG_R;
                end
                DIG_L : begin
                    if(ground == 1'b0)
                        n_state = FALL_L;
                    else
                        n_state = DIG_L;
                end
                SPLAT : begin
                    n_state = SPLAT;
                end
            default: n_state = LEFT;
            endcase
        end
    
        always @(posedge clk or posedge areset) begin
            if(areset == 1'b1) begin
                walk_left  <= 1'b1;
                walk_right <= 1'b0;
                aaah       <= 1'b0;
                digging    <= 1'b0;
                cnt_clk    <= 8'd0;
            end
            else begin
                case(n_state)
                    LEFT : begin
                        walk_left  <= 1'b1;
                        walk_right <= 1'b0;
                        aaah       <= 1'b0;
                        digging    <= 1'b0;
                        cnt_clk    <= 8'd0;
                    end
                    RIGHT : begin
                        walk_left  <= 1'b0;
                        walk_right <= 1'b1;
                        aaah       <= 1'b0; 
                        digging    <= 1'b0;
                        cnt_clk    <= 8'd0;
                    end
                    FALL_R : begin
                        aaah       <= 1'b1;
                        walk_right <= 1'b0;
                        digging    <= 1'b0;
                        cnt_clk    <= cnt_clk + 1'b1;
                    end
                    FALL_L : begin
                        aaah      <= 1'b1;
                        walk_left <= 1'b0;
                        digging   <= 1'b0; 
                        cnt_clk   <= cnt_clk + 1'b1;
                    end
                    DIG_R : begin
                        digging    <= 1'b1;
                        aaah       <= 1'b0;
                        walk_right <= 1'b0;
                        cnt_clk    <= 8'd0;
                    end
                    DIG_L : begin
                        digging    <= 1'b1;
                        aaah       <= 1'b0;
                        walk_left  <= 1'b0;    
                        cnt_clk    <= 8'd0;               
                    end
                    SPLAT : begin
                        walk_left  <= 1'b0;
                        walk_right <= 1'b0;
                        aaah       <= 1'b0;
                        digging    <= 1'b0;
                    end
                default: begin
    					walk_left  <= 1'b1;
                        walk_right <= 1'b0;  
                        aaah       <= 1'b0;   
                        digging    <= 1'b0;    
                        cnt_clk    <= 8'd0;       
                	end
                endcase
            end
        end
    
    endmodule
    /*官网解答*/
    无
    ```
  
    