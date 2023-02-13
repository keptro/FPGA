# FPGA学习： Verilog刷题记录（14-2）

### 刷题网站 ： HDLBits

 ### 第三章 ： Circuits

  #### 第二节 ：Sequential Logic

##### 第一节：Finite State Machines（3）

+ **PS/2 packet parser**
  
  + 题目描述：
  
    The PS/2 mouse protocol sends messages that are three bytes long. However, within a continuous byte stream, it's not obvious where messages start and end. The only indication is that the first byte of each three byte message always has `bit[3]=1` (but bit[3] of the other two bytes may be 1 or 0 depending on data).
  
    We want a finite state machine that will search for message boundaries when given an input byte stream. The algorithm we'll use is to discard bytes until we see one with `bit[3]=1`. We then assume that this is byte 1 of a message, and signal the receipt of a message once all 3 bytes have been received (`done`).
  
    The FSM should signal `done` in the cycle immediately after the third byte of each message was successfully received.
  
    
  
    ### Some timing diagrams to explain the desired behaviour
  
    ![](C:\Users\lzck\Desktop\综测\1.png)
  
  + 题目分析：我们接收到3个字长的数据后就给出**done**信号，官网也给出了状态转移图直接根据转移图进行书写即可，比较简单
  
    ![](C:\Users\lzck\Desktop\综测\12.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input [7:0] in,
        input reset,    // Synchronous reset
        output done
    ); 
    
        parameter BYTE1 = 4'b0001 ,
                  BYTE2 = 4'b0010 ,
                  BYTE3 = 4'b0100 ,
                  DONE  = 4'b1000 ;
    
        reg  [3:0]  n_state ;
        reg  [3:0]  c_state ;
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                c_state <= BYTE1;
            else
                c_state <= n_state;
        end 
    
        always @(*) begin
            if(reset == 1'b1)
                n_state = BYTE1;
            else begin
                case(c_state)
                    BYTE1 : begin
                        if(in[3] == 1'b1)
                            n_state = BYTE2;
                        else
                            n_state = BYTE1;
                    end
                    BYTE2 : begin
                        n_state = BYTE3;
                    end
                    BYTE3 : begin
                        n_state = DONE;
                    end
                    DONE  : begin
                        if(in[3] == 1'b1)
                            n_state = BYTE2;
                        else   
                            n_state = BYTE1;
                    end
                    default : n_state = BYTE1;
                endcase
            end
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                done <= 1'b0;
            else if (n_state == DONE)
                done <= 1'b1;
            else
                done <= 1'b0;
        end
    
    endmodule
    /*官网解答*/
    无
    ```
  
    
  
+ **PS/2 packet parser and datapath**
  
  + 题目描述：See also: [PS/2 packet parser](https://hdlbits.01xz.net/wiki/Fsm_ps2).
  
    Now that you have a state machine that will identify three-byte messages in a PS/2 byte stream, add a datapath that will also output the 24-bit (3 byte) message whenever a packet is received (`out_bytes[23:16]` is the first byte, `out_bytes[15:8]` is the second byte, etc.).
  
    `out_bytes` needs to be valid whenever the `done` signal is asserted. You may output anything at other times (i.e., don't-care).
  
    For example:
  
    ![image-20210817150603883](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210817150603883.png)
  
  + 题目分析：在上一题的基础上增加输出正确数据的功能,每当到对应状态而时候就就将对应的数据给到**out_byte**即可，但是在我绘制其时序图时，发现题目中给的例子，**in[3]**是不是画错了？？？，于是我将通过的代码，写到了工程里编写了**testbeach**，进行查看，波形如下，**Testbeach**代码我也给了出来，
  
    如果是因为**Testbeach**编写有错误请告知下，谢谢哈哈。
  
    ![](C:\Users\lzck\Desktop\综测\13.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input clk,
        input [7:0] in,
        input reset,    // Synchronous reset
        output [23:0] out_bytes,
        output done
    ); 
    
        parameter BYTE1 = 4'b0001 ,
                  BYTE2 = 4'b0010 ,
                  BYTE3 = 4'b0100 ,
                  DONE  = 4'b1000 ;
    
        reg  [3:0]  n_state ;
        reg  [3:0]  c_state ;
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                c_state <= BYTE1;
            else
                c_state <= n_state;
        end 
    
        always @(*) begin
            if(reset == 1'b1)
                n_state = BYTE1;
            else begin
                case(c_state)
                    BYTE1 : begin
                        if(in[3] == 1'b1)
                            n_state = BYTE2;
                        else
                            n_state = BYTE1;
                    end
                    BYTE2 : begin
                        n_state = BYTE3;
                    end
                    BYTE3 : begin
                            n_state = DONE;
                    end
                    DONE  : begin
                        if(in[3] == 1'b1)
                            n_state = BYTE2;
                        else   
                            n_state = BYTE1;
                    end
                    default : n_state = BYTE1;
                endcase
            end
        end
    
        always @(posedge clk) begin
            if(reset == 1'b1)
                out_bytes <= 24'd0;
            else begin
                case (c_state)
                    BYTE1 : out_bytes[23:16] <= in ;
                    BYTE2 : out_bytes[15:8]  <= in ;
                    BYTE3 : out_bytes[7:0]   <= in ;
                    DONE  : out_bytes[23:16] <= in ;
                endcase
            end        
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                done <= 1'b0;
            else if (n_state == DONE)
                done <= 1'b1;
            else
                done <= 1'b0;
        end
    
    endmodule
    
    /*Testbeach*/
  `timescale 1ns/1ps
    module tb_top_module();
    
    reg             clk             ;
    reg             reset           ;
    reg     [7:0]   in              ;
    
    wire    [23:0]  out_bytes       ;
    wire            done            ;
    
    initial begin
        clk = 1'b0;
        reset = 1'b1;
        in  <= 8'h00;
        #90
        reset = 1'b0;
        in <= 8'h08;
        #20
        in <= 8'h01;
        #20
        in <= 8'h02;
        #20
        in <= 8'h38;
        #20
        in <= 8'hff;
        #20
        in <= 8'hfe;
        #20
        in <= 8'h08;
        #20
        in <= 8'h03;
        #20
        in <= 8'h04;
        #100
        $stop;
    end
    
    always #10 clk = ~clk;
    
    top_module top_module_inst(
        .clk           (clk),
        .in            (in),
        .reset         (reset),    
        .out_bytes     (out_bytes),
        .done          (done)
    ); 
    
    endmodule
    /*官网解答*/
    ```
    
    
  
+ **Serial receiver**
  
  + 题目描述：In many (older) serial communications protocols, each data byte is sent along with a start bit and a stop bit, to help the receiver delimit bytes from the stream of bits. One common scheme is to use one start bit (0), 8 data bits, and 1 stop bit (1). The line is also at logic 1 when nothing is being transmitted (idle).
  
    Design a finite state machine that will identify when bytes have been correctly received when given a stream of bits. It needs to identify the start bit, wait for all 8 data bits, then verify that the stop bit was correct. If the stop bit does not appear when expected, the FSM must wait until it finds a stop bit before attempting to receive the next byte.
  
    ### ![](C:\Users\lzck\Desktop\综测\3.png)
  
  + 题目分析：
  
    ​			这道题是想要让我们描述如图所示的串行通信协议，给定一个比特流，我们所描述的状态机需要正确的识别何时正确的接收字节，其中需要识别包括 一位起始位，8位有效数据，一位停止位 共10位，当正确检验后，**done**拉高一个时钟周期。如果接收最后一个停止位时失败则需要等待 **in**拉高后在重新识别，并且不接收先前的数据。
  
    ​			对于这道题，我设计了4个状态，一个空闲状态 **IDLE**，一个接收10位数据的状态 **DATA**，一个停止接收的状态 **STOP**和一个传输出错后的等待状态 **WAIT**，状态图如下
  
    ![](C:\Users\lzck\Desktop\综测\7.png)
  
    ​			这里定义了一个计数器 **count**,其作用是用来标记接收数据的个数，因为我这里是将起始位，数据位，停止位一起算上去了，所以是计数到 **9**时进行一个判断。比较难的其实就是**DATA**跳转的条件，一开始我并没有 **WAIT**状态，而是直接利用**count=9**这个条件进行跳转，显然这样做是错误的，情况没有考虑到位。
  
    ​             根据状态图就比较好书写代码了，这里在给出状态机描述的时序图
  
    ![](C:\Users\lzck\Desktop\综测\8.png)
  
  + 解答：
  
    ``` verilog 
    /*我的解答*/
    module top_module(
        input  wire      clk      ,
        input  wire      in       ,
        input  wire      reset    ,    // Synchronous reset
        output reg       done
    ); 
    
        parameter   IDLE  = 4'b0001  ,       //空闲
                    DATA  = 4'b0010  ,       //接收数据
                    STOP  = 4'b0100  ,       //停止接收
                    WAIT  = 4'b1000  ;       //等待
    
        reg     [3:0]       n_state  ;       //次态
        reg     [3:0]       c_state  ;       //现态
        reg     [3:0]       count    ;       
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                c_state <= IDLE;
            else
                c_state <= n_state;
        end
    
        always @(*) begin
            n_state = 'bx;
            case (c_state)
                IDLE : begin
                    if (in == 1'b0)
                        n_state = DATA;
                    else
                        n_state = IDLE;
                end
                DATA : begin
                    if (count == 4'd9 && in == 1'b1)
                        n_state = STOP;
                    else if (count == 4'd9 && in == 1'b0)
                        n_state = WAIT;
                    else
                        n_state = DATA;
                end
                STOP : begin
                    if (in == 1'b0)
                        n_state = DATA;
                    else
                        n_state = IDLE;
                end
                WAIT : begin
                    if (in == 1'b1)
                        n_state = IDLE;
                    else 
                        n_state = WAIT;
                end
                default : n_state = IDLE;
            endcase
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                count <= 4'd0;
            else if (n_state == DATA)
                count <= count + 1'b1;
            else if (count == 4'd9)
                count <= 4'd0;
            else
                count <= count;
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                done <= 1'b0;
            else if (n_state == STOP)
                done <= 1'b1;
            else
                done <= 1'b0;
        end
    
    endmodule
    
    /*官网解答*/
    ```
    
    
  
+ **Serial receiver and datapath**
  
  + 题目描述：
  
    See also: [Serial receiver](https://hdlbits.01xz.net/wiki/Fsm_serial)
  
    Now that you have a finite state machine that can identify when bytes are correctly received in a serial bitstream, add a datapath that will output the correctly-received data byte. `out_byte` needs to be valid when `done` is `1`, and is don't-care otherwise.
  
    Note that the serial protocol sends the *least* significant bit first.
  
    ![image-20210829153439133](C:\Users\lzck\AppData\Roaming\Typora\typora-user-images\image-20210829153439133.png)
  
  + 题目分析：
  
    ​		这道题目是上道题的延伸，需要我们将正确接收的字节输出。状态转移图同上题一致没有变化，关键是如何将串行的数据并行输出，官网给出提示是采用**移位**的方式，如何移位的？怎么移？
  
    ​		首先我们需要知道，传数据时先传的高位还是先传的低位，根据题目所给的时序图，可以看出传送数据时先传的低位，后传的高位，所以我们可以采用**右移**的方式，八位数据，右移八次，即可达到串转并的效果具体看下图
  
    ​	![](C:\Users\lzck\Desktop\综测\9.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input  wire      clk      ,
        input  wire      in       ,
        input  wire      reset    ,    // Synchronous reset
        output reg [7:0] out_byte ,
        output reg       done
    ); 
    
        parameter   IDLE  = 4'b0001  ,       //空闲
                    DATA  = 4'b0010  ,       //接收数据
                    STOP  = 4'b0100  ,       //停止接收
                    WAIT  = 4'b1000  ;       //等待
    
        reg     [3:0]       n_state  ;       //次态
        reg     [3:0]       c_state  ;       //现态
        reg     [3:0]       count    ;
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                c_state <= IDLE;
            else
                c_state <= n_state;
        end
    
        always @(*) begin
            n_state = 'bx;
            case (c_state)
                IDLE : begin
                    if (in == 1'b0)
                        n_state = DATA;
                    else
                        n_state = IDLE;
                end
                DATA : begin
                    if (count == 4'd9 && in == 1'b1)
                        n_state = STOP;
                    else if (count == 4'd9 && in == 1'b0)
                        n_state = WAIT;
                    else
                        n_state = DATA;
                end
                STOP : begin
                    if (in == 1'b0)
                        n_state = DATA;
                    else
                        n_state = IDLE;
                end
                WAIT : begin
                    if (in == 1'b1)
                        n_state = IDLE;
                    else 
                        n_state = WAIT;
                end
                default : n_state = IDLE;
            endcase
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                count <= 4'd0;
            else if (n_state == DATA)
                count <= count + 1'b1;
            else if (count == 4'd9)
                count <= 4'd0;
            else
                count <= count;
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                done <= 1'b0;
            else if (n_state == STOP)
                done <= 1'b1;
            else
                done <= 1'b0;
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                out_byte <= 8'd0;
            else if (n_state == DATA)
                out_byte <= {in,out_byte[7:1]};
            else
                out_byte <= out_byte;
        end
    
    endmodule
    /*官网解答*/
    ```
  
+ **Serial receiver with parity checking**
  
  + 题目描述：See also: [Serial receiver and datapath](https://hdlbits.01xz.net/wiki/fsm_serialdata)
  
    We want to add parity checking to the serial receiver. Parity checking adds one extra bit after each data byte. We will use odd parity, where the number of `1`s in the 9 bits received must be odd. For example, `101001011` satisfies odd parity (there are 5 `1`s), but `001001011` does not.
  
    Change your FSM and datapath to perform odd parity checking. Assert the `done` signal only if a byte is correctly received *and* its parity check passes. Like the [serial receiver FSM](https://hdlbits.01xz.net/wiki/fsm_serial), this FSM needs to identify the start bit, wait for all 9 (data and parity) bits, then verify that the stop bit was correct. If the stop bit does not appear when expected, the FSM must wait until it finds a stop bit before attempting to receive the next byte.
  
    You are provided with the following module that can be used to calculate the parity of the input stream (It's a TFF with reset). The intended use is that it should be given the input bit stream, and reset at appropriate times so it counts the number of `1` bits in each byte.
  
    ```
    module parity (
        input clk,
        input reset,
        input in,
        output reg odd);
    
        always @(posedge clk)
            if (reset) odd <= 0;
            else if (in) odd <= ~odd;
    
    endmodule
    ```
  
    Note that the serial protocol sends the least significant bit first, and the parity bit after the 8 data bits.
  
    ![](C:\Users\lzck\Desktop\综测\4-1.png)
  
  + 题目分析：
  
    ​		**Serial receiver**系列的最后一题，需要多一个校验位，并且只有当是奇校验和正确接收到数据后才拉高**done**与输出数据**out_byte**,题目中也给了一个判断传入数据的**1**的个数是否为奇数的模块。
  
    ​         这道题做了很久，提交了很多次，每次都不成功....弄了很久原先是状态转移的条件没有考虑清楚，后面是输出的条件没有控制好，不过还是不对，检查到最后原来还需要控制好模块的复位..感觉挺坑的
  
    ​         尽管如此，还是通过这道题积累了一些经验，本题因为多了一个校验位，所以状态转移的条件有所改变，如下图所示
  
    ![](C:\Users\lzck\Desktop\综测\10.png)
  
    ​		我是这么考虑的，这里无非是多了一个**odd**判断，所以把**odd**与**in**一起考虑**(这里指接收完11位数据后)**，一共4种情况，如图中所示，只要**in**为0，无论奇校验成不成功都没有用，因为没有接收到正确的停止位，所以直接进入**WAIT**等待状态。如果**in**为1了，表示说正确接收到停止位，这个时候再来判断**odd**，如果**odd**为1，说明满足所有的条件，可以进行输出。如果**odd**为0，表明奇校验没有通过，应当舍去，重新接收，所以回到**IDLE**状态即可。
  
    ​        除此之外需要注意，输出数据时需要多一个**count < 9 ** 这个条件，因为有三位不是数据位，如果直接当**n_state = DATA**的话，会把校验位也算进去，相当于多移了一次位，这样的数据是不符合要求的。
  
    ​        题目中给的模块，我们还需要控制其复位的状态，就是什么时候应当复位。我的考虑是只要**次态**不是**DATA**状态表示没有接收数据，这个时候就一直复位，并且要加顶层模块的**reset**这个条件，两者有一个成立就让**parity**复位
  
    ​        这里附上我绘制的时序图
  
    ![](C:\Users\lzck\Desktop\综测\11.png)
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input  wire      clk      ,
        input  wire      in       ,
        input  wire      reset    ,    // Synchronous reset
        output reg [7:0] out_byte ,
        output reg       done
    ); 
    
        parameter   IDLE  = 4'b0001  ,       //空闲
                    DATA  = 4'b0010  ,       //接收数据
                    STOP  = 4'b0100  ,       //停止接收
                    WAIT  = 4'b1000  ;       //等待
    
        reg     [3:0]       n_state  ;
        reg     [3:0]       c_state  ;
        reg     [3:0]       count    ;
        wire                odd      ;
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                c_state <= IDLE;
            else
                c_state <= n_state;
        end
    
        always @(*) begin
            n_state = 'bx;
            case (c_state)
                IDLE : begin
                    if (in == 1'b0)
                        n_state = DATA;
                    else
                        n_state = IDLE;
                end
                DATA : begin
                    if(count == 4'd10 && in == 1'b1) begin
                        if(odd == 1'b1)
                            n_state = STOP;
                        else
                            n_state = IDLE;
                    end
                    else if(count == 4'd10 && in == 1'b0)
                        n_state = WAIT;
                    else
                        n_state = DATA;
                end
                STOP : begin
                    if (in == 1'b0)
                        n_state = DATA;
                    else
                        n_state = IDLE;
                end
                WAIT : begin
                    if (in == 1'b1)
                        n_state = IDLE;
                    else 
                        n_state = WAIT;
                end
                default : n_state = IDLE;
            endcase
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                count <= 4'd0;
            else if (n_state == DATA)
                count <= count + 1'b1;
            else if (count == 4'd10)
                count <= 4'd0;
            else
                count <= count;
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                done <= 1'b0;
            else if (n_state == STOP)
                done <= 1'b1;
            else
                done <= 1'b0;
        end
    
        always @(posedge clk) begin
            if (reset == 1'b1)
                out_byte <= 8'd0;
            else if (n_state == DATA && count < 4'd9)
                out_byte <= {in,out_byte[7:1]};
            else
                out_byte <= out_byte;
        end
    
        parity parity_inst(clk,(n_state != DATA || reset == 1'b1),in,odd);
        
    endmodule
    /*官网解答*/
    ```
  ```
    
    
  ```
  
+ **Sequence recognition**
  
  + 题目描述：[Synchronous HDLC framing](https://en.wikipedia.org/wiki/High-Level_Data_Link_Control#Synchronous_framing) involves decoding a continuous bit stream of data to look for bit patterns that indicate the beginning and end of frames (packets). Seeing exactly 6 consecutive 1s (i.e., `01111110`) is a "flag" that indicate frame boundaries. To avoid the data stream from accidentally containing "flags", the sender inserts a zero after every 5 consecutive 1s which the receiver must detect and discard. We also need to signal an error if there are 7 or more consecutive 1s.
  
    Create a finite state machine to recognize these three sequences:
  
    - `0111110`: Signal a bit needs to be discarded (`disc`).
    - `01111110`: Flag the beginning/end of a frame (`flag`).
    - `01111111...`: Error (7 or more 1s) (`err`).
  
    When the FSM is reset, it should be in a state that behaves as though the previous input were 0.
  
    Here are some example sequences that illustrate the desired operation.
  
    ![](C:\Users\lzck\Desktop\综测\5.png)
  
  + 题目分析：官网已经给出了这道题的状态转移图
  
    ![](C:\Users\lzck\Desktop\综测\6.png)
  
    可以看出这里需要我们描述一个Moore型的状态机，其输出是与输入无关的，只与当前状态有关，所以直接描述即可
  
  + 解答：
  
    ```verilog
    /*我的解答*/
    module top_module(
        input    wire   clk         ,
        input    wire   reset       ,    // Synchronous reset
        input    wire   in          ,
        output   reg    disc        ,
        output   reg    flag        ,
        output   reg    err
    );
    
        localparam   NONE    =   4'b0001,
                     ONE     =   4'b0010,
                     TWO     =   4'b0011,
                     THREE   =   4'b0100,
                     FOUR    =   4'b0101,
                     FIVE    =   4'b0110,
                     SIX     =   4'b0111,
                     ERROR   =   4'b1000,
                     DISCARD =   4'b1001,
                     FLAG    =   4'b1010;
        
        reg     [3:0]   n_state ;
        reg     [3:0]   c_state ;
        
        always @(posedge clk) begin
            if (reset == 1'b1)
                c_state <= NONE;
            else
                c_state <= n_state;
        end
        
        always @(*) begin
            n_state = 'bx; 
            case(c_state) 
                NONE    :    n_state = in ? ONE   : NONE    ;
                ONE     :    n_state = in ? TWO   : NONE    ;
                TWO     :    n_state = in ? THREE : NONE    ;   
                THREE   :    n_state = in ? FOUR  : NONE    ;
                FOUR    :    n_state = in ? FIVE  : NONE    ;
                FIVE    :    n_state = in ? SIX   : DISCARD ;
                SIX     :    n_state = in ? ERROR : FLAG    ;
                ERROR   :    n_state = in ? ERROR : NONE    ;
                DISCARD :    n_state = in ? ONE   : NONE    ;
                FLAG    :    n_state = in ? ONE   : NONE    ;
                default :    n_state = NONE;
            endcase
        end
        
        always @(posedge clk) begin
            if (reset == 1'b1)
                disc <= 1'b0;
            else if (n_state == DISCARD)  
                disc <= 1'b1;
            else
                disc <= 1'b0;
        end
        
        always @(posedge clk) begin
            if (reset == 1'b1)
                flag <= 1'b0;
            else if(n_state == FLAG)
                flag <= 1'b1;
            else
                flag <= 1'b0;
        end
        
        always @(posedge clk) begin
            if (reset == 1'b1)
                err <= 1'b0;
            else if (n_state == ERROR)
                err <= 1'b1;
            else
                err <= 1'b0; 
        end
        
        endmodule
    /*官网解答*/
    无
    ```
  
    
  
    