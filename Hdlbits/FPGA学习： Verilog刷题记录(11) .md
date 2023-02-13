

### 刷题网站 ： HDLBits

 ### 第三章 ： Circuits

  #### 第二节 ：Sequential Logic

##### 第一节：Counters

+ **Four-bit binary counter**

  + 题目描述：Build a 4-bit binary counter that counts from 0 through 15, inclusive, with a period of 16. The reset input is synchronous, and should reset the counter to 0.

    ![img](https://img-blog.csdnimg.cn/20210719195857235.png#pic_center)

  + 题目分析：4位二进制数的计数器，弄清楚加一的条件，归0的条件即可

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,      // Synchronous active-high reset
        output [3:0] q);
        
        //同步复位
        always @(posedge clk) begin
            if (reset == 1'b1)    // 复位有效，输出归0             
                q <= 4'd0;
            else if (q == 4'd15)  // 计数到最大值，输出归0
                q <= 4'd0;
            else                  // 其他情况，计数
                q <= q + 1'b1;    
        end
    
    endmodule
    /*官网解答*/
    module top_module(
    	input clk,
    	input reset,
    	output reg [3:0] q);
    	
    	always @(posedge clk)
    		if (reset)
    			q <= 0;
    		else
    			q <= q+1;	
    // Because q is 4 bits, it rolls over from 15 -> 0.
    // If you want a counter that counts a range different from 0 to (2^n)-1, 
    // then you need to add another rule to reset q to 0 when roll-over should occur.
    	
    endmodule
    ```

    

+ **Decade counter**

  + 题目描述：Build a decade counter that counts from 0 through 9, inclusive, with a period of 10. The reset input is synchronous, and should reset the counter to 0.

    ![img](https://img-blog.csdnimg.cn/20210719195944789.png#pic_center)

  + 题目分析：写一个同步复位计数到9的计数器，同样的弄清楚加一的条件，归0的条件即可

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,        // Synchronous active-high reset
        output [3:0] q);
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                q <= 4'd0;
            else if (q == 9)
                q <= 4'd0;
            else
                q <= q + 1'b1;
        end
        
    endmodule
    /*官网解答*/
    module top_module(
    	input clk,
    	input reset,
    	output reg [3:0] q);
    	
    	always @(posedge clk)
    		if (reset || q == 9)	// Count to 10 requires rolling over 9->0 instead of the more natural 15->0
    			q <= 0;
    		else
    			q <= q+1;
    	
    endmodule
    ```

    

+ **Decade counter again**

  + 题目描述：Make a decade counter that counts 1 through 10, inclusive. The reset input is synchronous, and should reset the counter to 1

    ![img](https://img-blog.csdnimg.cn/20210719200006596.png#pic_center)

  + 题目分析：写一个1-10的同步复位计数器同样是弄清楚加一的条件，归0的条件即可，只不过这里不是归0而是归1

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,
        output [3:0] q);
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                q <= 4'd1;
            else if (q == 4'd10)
                q <= 4'd1;
            else
                q <= q + 1'b1;
        end
        
    endmodule
    /*官网解答*/
    无
    ```

    

+ **Slow decade counter**

  + 题目描述：Build a decade counter that counts from 0 through 9, inclusive, with a period of 10. The reset input is synchronous, and should reset the counter to 0. We want to be able to pause the counter rather than always incrementing every clock cycle, so the `slowena` input indicates when the counter should increment.

    ![img](https://img-blog.csdnimg.cn/20210719200140310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTkzMTAwOQ==,size_16,color_FFFFFF,t_70#pic_center)

    + 题目分析：这里要设计一个同步复位并且受slowena控制的10进制计数器，说白了就是控制加一的条件，可以这么理解在``slowena = 1``的这个大条件下我们正常计数即可，就是计数到最大值归0，其他时刻保持不变。

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input slowena,
        input reset,
        output [3:0] q);
        
        //同步复位
        always @(posedge clk) begin
            if (reset == 1'b1)
                q <= 4'd0;
            else if(slowena == 1'b1) begin
                if (q == 4'd9)
                    q <= 4'd0;
                else
                    q <= q + 1'b1;
            end
            else
                q <= q;      
        end
    
        /*错误做法*/
        always @(posedge clk) begin
            if (reset == 1'b1)
                q <= 4'd0;
            else if (q == 4'd9)
                q <= 4'd0;
            else if (slowena == 1'b1) 
                q <= q + 1'b1;
            else
                q <= q;
        //这种写法为什么不行呢？分析一下
        //这种做法错误的原因就在于，忽略了一种情况
        //当计数到最大值9时，如果此时slowena = 0
        //根据题目要求，最终的输出应该保持9不变
        //而按这种写法就会导致说，在slowena = 0并且计数到最大值时，输出为0
                
    endmodule
    /*官网解答*/
    无
    ```

+ **Counter 1-12**

  + 题目描述：

    Design a 1-12 counter with the following inputs and outputs:

    - **Reset** Synchronous active-high reset that forces the counter to 1
    - **Enable** Set high for the counter to run
    - **Clk** Positive edge-triggered clock input
    - **Q[3:0]** The output of the counter
    - **c_enable, c_load, c_d[3:0]** Control signals going to the provided 4-bit counter, so correct operation can be verified.

    You have the following components available:

    - the 4-bit binary counter (**count4**) below, which has Enable and synchronous parallel-load inputs (load has higher priority than enable). The **count4** module is provided to you. Instantiate it in your circuit.
    - logic gates

    ```verilog
    module count4(
    	input clk,
    	input enable,
    	input load,
    	input [3:0] d,
    	output reg [3:0] Q
    );
    ```

    The **c_enable**, **c_load**, and **c_d** outputs are the signals that go to the internal counter's **enable**, **load**, and **d** inputs, respectively. Their purpose is to allow these signals to be checked for correctness.

  + 题目分析：该题是希望我们例化题目所给的模块设计一个模12的计数器，并且满足“同步复位将计数器初值置为1”，“enable”高电平有效时启动定时器，“c_enable,c_load”分别是控制模块内部的信号

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,
        input enable,
        output [3:0] Q,
        output c_enable,
        output c_load,
        output [3:0] c_d
    ); //
    
        count4 the_counter (clk, c_enable, c_load, c_d, Q );
        
        assign c_enable = enable;  //直接将输入的使能信号给到c_enable即可
        
        //组合逻辑，同步复位
        always @(*) begin
            if(reset == 1'b1) begin
                c_load = 1'b1;    //这里的初值题目似乎没有明确说，提交后根据时序图进行修改  
                c_d    = 3'd1;
            end
            else begin
                if( Q==4'd12 && enable == 1'b1) begin //计数到12并且enable为高时，回到初值
                    c_load = 1'b1;
                    c_d    = 3'd1;
                end
                else begin
                    c_load = 1'b0;
                    c_d    = 3'd0;
                end
            end
        end
    
    endmodule
    /*官网解答*/
    无
    ```

    

+ **Counter 1000**

  + 题目描述：

    From a 1000 Hz clock, derive a 1 Hz signal, called **OneHertz**, that could be used to drive an Enable signal for a set of hour/minute/second counters to create a digital wall clock. Since we want the clock to count once per second, the **OneHertz** signal must be asserted for exactly one cycle each second. Build the frequency divider using modulo-10 (BCD) counters and as few other gates as possible. Also output the enable signals from each of the BCD counters you use (c_enable[0] for the fastest counter, c_enable[2] for the slowest).

    The following BCD counter is provided for you. **Enable** must be high for the counter to run. **Reset** is synchronous and set high to force the counter to zero. All counters in your circuit must directly use the same 1000 Hz signal.

    ```verilog
    module bcdcount (
    	input clk,
    	input reset,
    	input enable,
    	output reg [3:0] Q
    );
    ```

  + 题目分析：题目希望我们用1000hz的时钟来输出一个1hz的信号（即1000的分频器），同时将输出使能信号也进行输出，这实际上还是一个计数的问题。题目中给了我们一个模10的bcd计数器，(即计数从0到9计数)我们可以通过例化这个模块来实现输出1hz信号，具体思路就是将1000按位进行分解，由于是从000开始，所以只需要计数到999时，输出就能达成目标。所以可以将1000分解成**个位、十位、百位、千位**，然后每个位按一定条件进行0-9的循环计数，下面用时序图来描述下整个大概的流程

    ![image-20210719184001107](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210719184001107.png)

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module (
        input clk,
        input reset,
        output OneHertz,
        output [2:0] c_enable
    ); 
    
        wire [3:0] Q0,Q1,Q2; //定义个位，十位，百位上的数
        bcdcount counter0 (clk, reset, c_enable[0],Q0); //个位
        bcdcount counter1 (clk, reset, c_enable[1],Q1); //十位
        bcdcount counter2 (clk, reset, c_enable[2],Q2); //百位
       
        //使用组合逻辑
        always @(*) begin
            if(reset == 1'b1)
                c_enable <= 3'b001;       //先让个位上的数进行0-9的计数
            else begin
                if(Q0 == 4'd9) begin      //当个位上的数计数到9时
                   c_enable <= 3'b011;    //让十位上也开始计数
                    if(Q1 == 4'd9) begin  //当十位上的数也计数到9时
                       c_enable <= 3'b111; //让百位上也开始计数
                    end
                end
                else
                    c_enable <= 3'b001;
            end
        end
    	
        always @(*) begin
            if(reset == 1'b1)
                OneHertz = 1'b0;
            else if(Q0 == 4'd9 && Q1 == 4'd9 && Q2 == 4'd9) //满足三个位上都为9时，直接输出
                OneHertz = 1'b1;
            else
                OneHertz = 1'b0;
        end
    endmodule
    /*官网解答*/
    无
    ```

+ **4-digit decimal counter**

  + 题目描述：Build a 4-digit BCD (binary-coded decimal) counter. Each decimal digit is encoded using 4 bits: q[3:0] is the ones digit, q[7:4] is the tens digit, etc. For digits [3:1], also output an enable signal indicating when each of the upper three digits should be incremented.

    You may want to instantiate or modify some one-digit [decade counters](https://hdlbits.01xz.net/wiki/Count10).

  + 题目分析：题目要求设计一个4位的BCD码计数器，每一位上用4位数表示。思路是把数分解成**个位、十位、百位、千位**，然后满足某个条件时就让某一位进行显示输出。于是乎我们可以例化前面一道题目**Slow decade counter**的模块，因为它就可以实现按条件输出数字，所以我们只需要控制条件即可，这里将模块中所给的3位ena使能信号拆成3个，用来确认哪一位何时进1，大致流程如下时序图所示，根据时序图可以比较清楚的看出赋值的条件了，最后将输出用位拼接符连接即可

    ![image-20210719193249202](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210719193249202.png)

  + 解答：

    ```verilog 
    /*我的解答*/
    module top_module (
        input clk,
        input reset,   // Synchronous active-high reset
        output [3:1] ena,
        output [15:0] q);
        
        wire  [3:0] ge,shi,bai,qian; //定义4位的个、十、百、千
        
        //因为个位一直有，所以单独赋值
        always@(posedge clk) begin
            if (reset == 1'b1)
                ge <= 4'd0;
            else if(ge == 4'd9)
                ge <= 4'd0;
            else
                ge <= ge + 1'b1; 
        end
        
        assign q = {qian,bai,shi,ge};  //使用组合逻辑，立即输出
        //assign ena={ena[3],ena[2],ena[1]}; 可要可不要，因为后面已经对输出的每一位赋值了
        assign ena[1] = (ge == 4'd9) ? 1 : 0; 
        assign ena[2] = (shi == 4'd9 && ge == 4'd9) ? 1:0;
        assign ena[3] = (bai == 4'd9 && shi == 4'd9 && ge == 4'd9) ? 1 : 0;
        
        //例化模块
        counter10bcd counter10bcd_inst2(clk,ena[1],reset,shi);
        counter10bcd counter10bcd_inst3(clk,ena[2],reset,bai);
        counter10bcd counter10bcd_inst4(clk,ena[3],reset,qian);
            
    endmodule
    
    module counter10bcd (
        input clk,
        input ena,
        input reset,
        output [3:0] q);
        
        //同步复位
        always @(posedge clk) begin
            if (reset == 1'b1)
                q <= 4'd0;
            else if(ena == 1'b1) begin
                if (q == 4'd9)
                    q <= 4'd0;
                else
                    q <= q + 1'b1;
            end
            else
                q <= q;      
        end
    endmodule
    /*官网解答*/
    无
    ```

    

+ **12-hour clock**

  + 题目描述：Create a set of counters suitable for use as a 12-hour clock (with am/pm indicator). Your counters are clocked by a fast-running `clk`, with a pulse on `ena` whenever your clock should increment (i.e., once per second).

    `reset` resets the clock to 12:00 AM. `pm` is 0 for AM and 1 for PM. `hh`, `mm`, and `ss` are two **BCD** (Binary-Coded Decimal) digits each for hours (01-12), minutes (00-59), and seconds (00-59). Reset has higher priority than enable, and can occur even when not enabled.

    The following timing diagram shows the rollover behaviour from `11:59:59 AM` to `12:00:00 PM` and the synchronous reset and enable behaviour.

    ![image-20210719152532188](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210719152532188.png)

  + 题目分析：要我们做一个时钟，其中hh，mm，ss均用两位bcd码来表示，根据前面做题的思路，可以将需要显示的数字都分解成个位与十位分别显示。其中个人觉得对于hh的赋值是比较绕的，对于ss，mm基本同前面的题目类似。除此之外我们还需要对pm进行赋值，为0表示上午，为1表示下午。其中还需要注意这里的初值是设置在12:00:00,但复位为初值变为01:00:00，具体代码如下

  + 解答：

    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input reset,
        input ena,
        output reg pm,
        output [7:0] hh,
        output [7:0] mm,
        output [7:0] ss); 
    
        reg [3:0] ss_ge,ss_shi;        //秒钟上的个位与十位
        reg [3:0] mm_ge,mm_shi;        //分钟上的个位与十位
        reg [3:0] hh_ge,hh_shi;        //小时上的个位与十位
    
     /*===========================================================
         秒钟上 个位与十位的编写
         个位：在使能有效的情况下，进行判断如果计数到9，回零，
              否则自加1，其余情况保持原态
         十位：在使能有效并且个位为9的情况下，进行判断如果计数到9，回零，
              否则自加1，其余情况保持原态
     ============================================================*/   
        always @(posedge clk) begin
            if (reset == 1'b1)
                ss_ge <= 4'd0;
            else if(ena == 1'b1) begin
                if (ss_ge == 4'd9)
                    ss_ge <= 4'd0;
                else
                    ss_ge <= ss_ge + 1'b1;
            end
            else
                ss_ge <= ss_ge;      
        end
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                ss_shi <= 4'd0;
            else if(ena == 1'b1 && ss_ge == 4'd9) begin
                if(ss_shi == 4'd5)
                    ss_shi <= 4'd0;
                else
                    ss_shi <= ss_shi + 1'b1;
            end
            else
                ss_shi <= ss_shi;
        end
     /*=====================================================
      	分钟上 个位与十位的编写
      	个位：在秒为59时，并且使能有效情况下，才进行判断
      	十位：在秒为59时，分钟的个位为9，并且使能有效情况下，才进行判断
     ======================================================*/  
        always @(posedge clk) begin
            if (reset == 1'b1)
                mm_ge <= 4'd0;
            else if(ena == 1'b1 && ss_shi == 4'd5 && ss_ge == 4'd9) begin
                if (mm_ge == 4'd9)
                    mm_ge <= 4'd0;
                else
                    mm_ge <= mm_ge + 1'b1;
            end
            else
                mm_ge <= mm_ge;      
        end
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                mm_shi <= 4'd0;
            else if(ena == 1'b1 && mm_ge == 4'd9 && ss_shi == 4'd5 && ss_ge == 4'd9 ) begin
                if(mm_shi == 4'd5)
                    mm_shi <= 4'd0;
                else
                    mm_shi <= mm_shi + 1'b1;
            end
            else
                mm_shi <= mm_shi;
        end
     /*=========================================
         小时上 个位与十位的编写
         个位：在秒与分均为59，并使能有效的情况下，进行判断
       		  如果小时显示12，回归初态，初值为1
       		  如果小时个位计数到9，回归初态0，否则，自加1
       		  其他情况，保持当前状态
         十位：在秒与分均为59，并使能有效的情况下，进行判断
         	  如果小时显示12，回归初态，初值为0
         	  如果个位上的数计数到9，自加1
         	  其他情况，保持当前状态
     ==========================================*/     
        always @(posedge clk) begin
            if (reset == 1'b1)
                hh_ge <= 4'd2;
            else if(ena == 1'b1 && mm_shi == 4'd5 && mm_ge == 4'd9 && ss_shi == 4'd5 && ss_ge == 4'd9) begin
                if (hh_shi == 4'd1 && hh_ge == 4'd2)
                    hh_ge <= 4'd1;
                else if (hh_ge == 4'd9)
                    hh_ge <= 4'd0;
                else
                    hh_ge <= hh_ge + 1'b1;
            end
            else
                hh_ge <= hh_ge;      
        end
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                hh_shi <= 4'd1;
            else if(ena == 1'b1 && mm_shi == 4'd5 && mm_ge == 4'd9 && ss_shi == 4'd5 && ss_ge == 4'd9) begin
                if(hh_shi == 4'd1 && hh_ge == 4'd2)
                    hh_shi <= 4'd0;
                else if(hh_ge == 4'd9)
                    hh_shi <= hh_shi + 1'b1;
            end
            else 
                hh_shi <= hh_shi;
        end
     /*=========================================
         判别上午与下午标志位的编写
         当使能有效，并且显示11:59:59时，翻转pm的状态
     ==========================================*/      
        always @(posedge clk) begin
            if(reset == 1'b1)
                pm <= 1'b0;
            else if(ena == 1'b1 && hh_shi == 4'd1 &&hh_ge == 4'd1 && mm_shi == 4'd5 && mm_ge == 4'd9 && ss_shi == 4'd5 && ss_ge == 4'd9)
                pm <= ~pm;
            else 
                pm <= pm;
        end
     /*=========================================
        使用位拼接将秒，分钟，小时表示出来
     ==========================================*/       
        assign ss = {ss_shi,ss_ge};   
        assign mm = {mm_shi,mm_ge};
        assign hh = {hh_shi,hh_ge};
        
    endmodule
    
    /*官网解答*/
    无
    ```

    

### 总结 

```verilog
计数器的编写，其实就相当于在写循环。可以借助时序图进行理解
我们最需要关注的是
1.计数器的初态是什么
2.计数器的计数条件是什么
3.计数器回归初态的条件是什么
```



   