# HDLbits刷题出bug总结

### 1.assign 语句是不能用非阻塞语句赋值的 ，只有reg才可以用非阻塞语句赋值，但是assign既可以给wire型变量赋值也可以给reg型变量赋值。

### 2.异或和同或的一个简单辨析方法，一般我们都是先学习异或，而且异或用的比较多，所以异或是^，同或是异或的非，所以同或是~ ^。

### 3.模块实例化的过程中，赋值的变量不能是一个值

add16 unit1(.a(a[15:0]),.b(b[15:0]),.sum(sum[15:0]),.cout(cin1),.cin(1’b0));//会报错

但是改成下面这种形式就正确啦

​    wire cin2;
​    assign cin2 = 1'b0;

```verilog
add16 unit1(.a(a[15:0]),.b(b[15:0]),.sum(sum[15:0]),.cout(cin1),.cin(cin2));    
```

或者改成下面这种形式也是正确的

add16 unit1(.a(a[15:0]),.b(b[15:0]),.sum(sum[15:0]),.cout(cin1),.cin(0));

因为. cin（）表示的是连接,表示连接到某个电平或者某条线，如果是1'b0则会默认它是一个值

### 4.关于verilog中出现latch的问题

csdn地址：[(9条消息) 数字电路中应避免产生不必要的锁存器 Latch_weixin_33858336的博客-CSDN博客](https://blog.csdn.net/weixin_33858336/article/details/94738787?utm_source=app&app_version=4.6.1)

[(9条消息) verilog代码中避免出现latch方法_Jimbo的博客-CSDN博客](https://blog.csdn.net/tianyake_1/article/details/79031582?utm_source=app&app_version=4.6.1)

可以通过赋初值的方法来避免latch

### 5.关于Verilog复制问题，如何复制一个变量或常量多次

csdn地址：https://blog.csdn.net/Reborn_Lee/article/details/82775152

### 6.case和endcase是配套的，不要忘记endcase

### 7.casez和casex的区别

csdn地址：https://blog.csdn.net/vivid117/article/details/102611051

### 8.关于奇偶校验的一些问题

一位操作符

csdn地址：http://blog.sina.com.cn/s/blog_7e9a22770102v6aw.html

即不用一位一位的检验，可以通过一个操作符对所有位进行操作

### 9.使用integer来进行一些变量的存储，比如用于计数的i

### 10.verilog中generate语句的用法

csdn地址：http://xilinx.eetrend.com/blog/2019/100045571.html

### 11.在写模块的时候，不要忘记；

  module mt2015_q4b(input x,input y,output z);   ------》to be attention
        if(x == y)
            z = 1;
        else
            z = 0;
    endmodule

### 12.集成综合不支持综合嵌套模块声明(Integrated Synthesis does not support synthesizing nested module declarations)

### 13.冒号“：”操作符：A[a : b]的含义理解为vectorA中选择位的范围，目前其中a和b只能是常数，而不能是变量。此处应该区分动态数组。

```verilog
Error (10734): Verilog HDL error at top_module.v(6): sel is not a constant File: /home/h/work/hdlbits.628232/top_module.v Line: 6
```

csdn地址：https://blog.csdn.net/weixin_34551573/article/details/112633632

代码改成如下就好啦

module top_module( 
    input [1023:0] in,
    input [7:0] sel,
    output [3:0] out );


    assign out  = {in[sel*4+3], in[sel*4+2], in[sel*4+1], in[sel*4+0]};

endmodule

### 14.有符号的加法器signed adder设计
    题目： 假设你有两个8位有符号的输入，即a [7：0]和b [7：0]。这些数字相加产生s [7：0]，还要计算是否发生溢出情况。

module top_module (
    input [7:0] a,
    input [7:0] b,
    output [7:0] s,
    output overflow
); 

endmodule
    

    一个有符号数的最高位代表正负，最高位是0代表正数，最高位是1代表负数。那什么时候会发生溢出（overflow）的情况？只有两个正数（负数）相加才会发生溢出，相减（即一正一负）是不会发生溢出的。
    
    两个正数相加（符号位都为0），如果结果为负数，则发生溢出；两个负数相加（符号位都为1），如果结果为正数，则发生溢出，其他情况都为没有发生溢出。因此我的答案如下

module top_module (
    input [7:0] a,
    input [7:0] b,
    output [7:0] s,
    output overflow
); //

```verilog
assign s = a+b;
assign overflow = (a[7]==b[7])?((a[7])?(!s[7]?1:0):(s[7]?1:0)):0;
```

endmodule

 

     在其他博客看到有更简单做法，原理也是一样的，a[7] & b[7] & ~s[7]是判断两个负数的情况，当s[7]为0，即相加结果为正数，发生溢出情况，overflow为1；~a[7] & ~b[7] & s[7]是判断两个正数的情况，当s[7]为1，即相加结果为负数，发生溢出情况，overflow为1。

module top_module (
    input [7:0] a,
    input [7:0] b,
    output [7:0] s,
    output overflow
); 
    assign s = a + b;
    assign overflow = ( a[7] & b[7] & ~s[7] ) | (~a[7] & ~b[7] & s[7]);

endmodule

### 15.学长在GitHub上的笔记

笔记地址：![img](file:///C:\Users\lenovo\AppData\Roaming\Tencent\QQTempSys\%W@GJ$ACOF(TYDYECOKVDYB.png)https://github.com/nen9mA0/myNote/blob/master/fpga/verilog.md

### 16.sop是最小项的意思，pos是最大项的意思

### 17.这个题有毛病。。。感觉测试文件有问题，有时间一定要反馈

地址：https://hdlbits.01xz.net/wiki/Exams/ece241_2013_q2

### 18.wire为无逻辑连线，wire本身不带逻辑性，所以输入什么就的输出什么。所以如果用always语句对wire变量赋值，对不起，编译器报错。

csdn地址：https://blog.csdn.net/u013025203/article/details/53410715

### 19.注意一位的加法里面：1+1=0

地址：https://hdlbits.01xz.net/wiki/Exams/ece241_2014_q3

module top_module (
    input c,
    input d,
    output [3:0] mux_in
); 
    

```verilog
reg[1:0] i;
reg[1:0] j;
always @(*)
begin
    for(i = 0 ;i<= 1;i++)
    begin
        for(j = 0 ;j<=1;j++)
        begin
            mux_in[{i[0],j[0]}] =(~i[0])&(~j[0])&d|(~i[0])&(~j[0])&c|i[0]&(~j[0])&(~d)|i[0]&j[0]&c&d; 
        end
    end
end
```
endmodule

### 20.创建具有高电平有效同步复位的8 D触发器。所有DFF应由clk的上升沿触发。

module top_module (
    input clk,
    input reset,            // Synchronous reset
    input [7:0] d,
    output [7:0] q
);

```verilog
always @(posedge clk)
begin
    if(reset)
    begin
        q <= 0;
    end
    else
    begin
        q <= d;
    end
end
```
endmodule

### 21.同步复位：

顾名思义，同步复位就是指复位信号只有在时钟上升沿到来时，才能有效。否则，无法完成对系统的复位工作。用Verilog描述如下：

always @ (posedge clk) begin
	if (!Rst_n)
		...
end
1
2
3
4

### 异步复位：

指无论时钟沿是否到来，只要复位信号有效，就对系统进行复位。用Verilog描述如下：

always @ (posedge clk or negedge Rst_n) begin
	if (!Rst_n)
		...
end
1
2
3
4

### 22.错误（10200）：key_led.v（13）上的Verilog HDL条件语句错误：无法将条件中的操作数与Always结构的封闭事件控件中的相应边相匹配

csdn地址：https://blog.csdn.net/sinat_37173952/article/details/103317149?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-8.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-8.control

### 23.一道很经典的题目，透过现象看到本质

题目地址：https://hdlbits.01xz.net/wiki/Exams/2014_q4a

#### 另外，wire类型数据也可以进行非阻塞赋值  即wire a <= 1'b0;

### 24.注意：HDLbits答案解析：https://blog.csdn.net/qq_42334072/article/details/113854656

### 25.关于使用寄存器来检测边沿触发

知乎网址：https://zhuanlan.zhihu.com/p/342769017

### 26.十二进制计数器的问题：题目本身就很迷，然后有点靠猜。。。

![img](file:///C:\Users\lenovo\Documents\Tencent Files\1827013497\Image\C2C\NN}U7B2%[54DH)1AWV[9AR8.png)

csdn地址[(9条消息) Verilog专题（十二）Counter Design_Andy_ICer的博客-CSDN博客](https://blog.csdn.net/Andy_ICer/article/details/105513496)

### 27.中途的一些小感想。。。

写到这里的时候我已经开始麻了，感觉真的不太容易的事情，我连题目都开始看不懂了呜呜呜，我老是感觉是因为自己前面的知识没有理解透彻，所以我打算把这一个小节的题目刷完，然后去用vivado去把综合出来的电路图仔仔细细看看，看来还是前面刷题的时候有点不认真，没有把一些问题彻底解决。不过讲真的，当系统提醒你已经做了75道题的时候还是很开心，题目有的时候也会有一些小Bug,但是基本上无伤大雅。----peace and love 

### 28.BCD计数器

csdn地址：[(9条消息) FPGA初学-BCD计数器_清华小辣鸡的博客-CSDN博客](https://blog.csdn.net/weixin_42101039/article/details/104124388?utm_source=app&app_version=4.6.1)

关于这个题目还有一点比较重要的是：不要忘了清0条件。

### 29.关于counters的最后一道题

csdn地址：[﻿HDLBits--(Verilog在线学习)--"105: Count Clock"（ 续） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/143243822)

### 30.这道题之前用寄存器把q[0]和q[99]的值提前存了一下再用就不对，但是直接赋值就可以，好神奇啊。

地址：https://hdlbits.01xz.net/wiki/Rotate100

**后续**：发现其实是自己对非阻塞赋值和阻塞赋值的理解不到位，可能有这个印象，但是没有在脑子中去实际模拟演练这个结果。如果用的非阻塞赋值的话(也就是我的那种出错的情况，两者同时赋值，肯定当时他的值为空呀),改成阻塞赋值就好了。

```markdown
阻塞赋值“=”与非阻塞赋值“<=”的本质区别在于：
非阻塞赋值语句右端表达式计算完后并不立即赋值给左端，而是同时启动下一条语句继续执行，可以将其理解为所有的右端表达式RHS1、RHS2等在进程开始时同时计算，计算完后 ，等进程结束时同时分别赋给左端变量LHS1、LHS2等；
而阻塞赋值语句在每个右端表达式计算完后立即赋给左端变量，即赋值语句LHS1=RHS1执行完后LHS1是立即更新的，同时只有LHS1=RHS1执行完后才可执行语句LHS1=RHS2，依次类推。前一条语句的执行结果直接影响到后面语句的执行结果。
```

csdn地址：[(10条消息) verilog中的阻塞赋值与非阻塞赋值详解_~动次~打次~动次~打次~-CSDN博客](https://blog.csdn.net/u010277881/article/details/38318255?utm_source=app&app_version=4.6.1)

[(10条消息) 阻塞与非阻塞赋值的区别_ffdia的博客-CSDN博客](https://blog.csdn.net/ffdia/article/details/103507233?utm_source=app&app_version=4.6.1

### 31.串联操作中不允许出现大小不确定的常量，哪怕它是0。

### 32.线性反馈移位寄存器（Linear feedback shift register，LFSR）

### 33.关于按键消抖

**定义**：通常的按键所用开关为[机械](https://baike.baidu.com/item/机械/68186)弹性开关，当机械触点断开、闭合时，由于机械触点的[弹性](https://baike.baidu.com/item/弹性/6371513)作用，一个按键开关在闭合时不会马上稳定地接通，在断开时也不会一下子断开。因而在闭合及断开的瞬间均伴随有一连串的[抖动](https://baike.baidu.com/item/抖动/5253230)，为了不产生这种现象而作的措施就是按键消抖。

### 34.Verilog中不能使用自增运算符，而且尽可能避免使用for循环，很浪费电路资源。

网址：https://www.cnblogs.com/xd-elegant/p/4520920.html

### 35.输出一般不指明类型的话，都是reg类型的。

### 36.FSM：

网址：https://verilogguide.readthedocs.io/en/latest/verilog/fsm.html

### 37.关于testbench文件的编写

知乎网址：https://zhuanlan.zhihu.com/p/31479079

地址；https://zhuanlan.zhihu.com/p/62758134