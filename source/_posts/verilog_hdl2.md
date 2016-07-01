title: Verilog小叙(二)
date: 2016-03-08 22:23:35
toc: true
tags: 
- Verilog HDL
- 仿真
categories: Verilog 
---

好的，接着上篇文章[Verilog小叙(一)](/2016/03/06/verilog_hdl/)

# 常量 #

Verilog HDL中有三种常量：
整型、实型、字符串型。
下划线符号“_”可以随意用在整数或实数中，它们就数量本身没有意义。它们能用来提高易读性；唯一的限制是下划线符号不能用作为首字符。
下面主要介绍整型和字符串型。
## 整型 ##

整型数可以按如下两种方式书写：
1. 简单的十进制数格式
2. 基数格式

<!--more-->

## 简单的十进制格式 ##
这种形式的整数定义为带有一个可选的“+”（一元）或“－”（一元）操作符的数字序列。
下面是这种简易十进制形式整数的例子。
32 十进制数32
－15 十进制数－15

## 基数表示法 ##
这种形式的整数格式为：
[size ] 'base value
size 定义以位计的常量的位长；
base 为o或O（表示八进制），b或B（表示二进制），d或D（表示十进制），h 或H （表示十六进制）之一；
value 是基于base 的值的数字序列。值x和z以及十六进制中的a 到f 不区分大小写。
下面是一些具体实例：
```
5 'o37 //5位八进制数（二进制11111）
4 'd2 //4位十进制数（二进制0011）
4 'b1x_01 //4位二进制数
7 'hx //7位x(扩展的x), 即xxxxxxx
4 'hz //4位z(扩展的z) , 即zzzz
4 'd-4 //非法：数值不能为负
3' b 001 //非法：和基数b之间不允许出现空格
(2+3)'b10 //非法：位长不能够为表达式
```
注意，x（或z）在十六进制值中代表4位x（或z），在八进制中代表3位x（或z ），在二进制中代表1位x（或z）。
基数格式计数形式的数通常为无符号数。这种形式的整型数的长度定义是可选的。如果没有定义一个整数型的长度，数的长度为相应值中定义的位数。下面是两个例子：
'o 7219 3位八进制数
'h AF8 4位十六进制数

如果定义的长度比为常量指定的长度长，通常在左边填0补位。但是如果数最左边一位为x 或z，就相应地用x 或z 在左边补位。例如：
`10'b10`左边添0占位,0000000010
`10'bx0x1`左边添x占位, xxxxxxx0x1
如果长度定义得更小，那么最左边的位相应地被截断。例如：
`3 ' b1001_0011`与`3'b011`相等
`5'H0FFF`与`5'H1F`相等

## 字符串型 ##

字符串是双引号内的字符序列。字符串不能分成多行书写。
例如：
`"INTERNAL ERROR"`
`" REACHED－>HERE "`
用8位ASCII值表示的字符可看作是无符号整数。因此字符串是8位ASCII 值的序列。为存储字符串`“INTERNAL ERROR”`，变量需要8x14位。

1. `reg [1:8*14] Message;`

2. `Message = "INTERNAL ERROR"`


# 数据类型 #

Verilog HDL 主要包括两种数据类型
1. 线网类型(net type) 

2. 寄存器类型（reg type）

## 线网类型 ##

![](http://7xowaa.com1.z0.glb.clouddn.com/verilog_hdl.png)

1. wire 和 tri 定义

**线网类型主要有wire 和tri两种**。线网类型用于对结构化器件之间的物理连线的建模.如器件的管脚，内部器件如与门的输出等。以上面的加法器为例，输入信号A，B是由外部器件所驱动，异或门X1的输出S1是与异或门X2输入脚相连的物理连接线，它由异或门X1所驱动。简单地讲，S1由X1驱动。
由于线网类型代表的是物理连接线，因此它不存贮逻辑值。必须由器件所驱动。通常由assign进行赋值。如 assign A = B ^ C；
当一个wire 类型的信号没有被驱动时，缺省值为Z（高阻）。
信号没有定义数据类型时，缺省为 wire 类型。
如上面一位全加器的端口信号 A，B，SUM等，没有定义类型，故缺省为wire 线网类型。
**两者区别**
tri主要用于定义三态的线网。

## 寄存器类型 ##

1. 定义
reg 是最常用的寄存器类型，寄存器类型通常用于对存储单元的描述，如D型触发器、ROM等。存储器类型的信号当在某种触发机制下分配了一个值，在分配下一个值之时保留原值。
reg 类型定义语法如下：
```
reg [msb: lsb] reg1, reg2, . . . reg N;
msb 和lsb 定义了范围，并且均为常数值表达式。范围定义是可选的；如果没有定义范围，缺省值为1 位寄存器。例如：
reg [3:0] Sat; // Sat 为4 位寄存器。
reg Cnt; //1 位寄存器。
reg [1:32] Kisp, Pisp, Lisp ;
//寄存器类型的值可取负数，但若该变量用于表达式的运算中，则按无符号类型处理，如：
reg A ；
.....
A = -1；
....
则A的二进制为1111，在运算中，A总按 无符号数15来看待。
```

2. 寄存器类型的存储单元建模举例
用寄存器类型来构建两位的D触发器如下：
```
reg [1：0] dout ；
.....
always@(posedge clk)
dout <= din;
....
用寄存器数组类型来建立存储器的模型，如对2个8位的RAM建模如下：
reg [7：0] mem[0：1] ；
对存储单元的赋值必须一个个第赋值，如上2个8位的RAM的赋值必须用两条赋值语句：
.....
mem[0] = ’ h 55；
mem[1] = ’ haa；
....
```
3. 书写规范建议
对数组类型，请按降序方式，如[7：0] ；
**Tips:**
reg的类型不一定是寄存器，只有带有时钟的always块才能是寄存器，不带时钟的always块是组合逻辑。

## 运算符和表达式 ##

Verilog HDL中的操作符可以分为下述类型：

1. 算术操作符

2. 关系操作符

3. 相等操作符

4. 逻辑操作符

5. 按位操作符

6. 归约操作符

7. 移位操作符

8. 条件操作符

9. 连接和复制操作符

下表显示了所有操作符的优先级和名称。操作符从最高优先级（顶行）到最低优先级（底行）排列。同一行中的操作符优先级相同。

![](http://7xowaa.com1.z0.glb.clouddn.com/veriloghdl2.png)

## 算术运算符 ##

在常用的算术运算符主要是 ：

- 加法（二元运算符）：“+”；

- 减法 （二元运算符）：“-”；

- 乘法（二元运算符）：“*”；

在算术运算符的使用中，注意如下两个问题：

1. 算术操作结果的位数长度

算术表达式结果的长度由最长的操作数决定。在赋值语句下，算术操作结果的长度由操作符左端目标长度决定。考虑如下实例：

```
reg [3:0] arc, bar, crt;
reg [5:0] frx;
. . .
arc = bar + crt;
frx = bar + crt;
```
第一个加的结果长度由bar，crt 和 arc 长度决定，长度为4位。
第二个加法操作的长度同样由frx 的长度决定（frx 、bat 和crt 中的最长长度），长度为6位。
在第一个赋值中，加法操作的溢出部分被丢弃；而在第二个赋值中，任何溢出的位存储在结
果位frx [ 4 ]中。
在较大的表达式中，中间结果的长度如何确定？在Verilog HDL 中定义了如下规则：表达式中的所有中间结果应取最大操作数的长度（赋值时，此规则也包括左端目标）。考虑另一个实例：
wire [4:1] box, drt;
wire [5:1] cfg;
wire [6:1] peg;
wire [8:1] adt;
. . .
assign adt = (box + cfg) + (drt + peg) ;
表达式右端的操作数最长为6 ，但是将左端包含在内时，最大长度为8 。所以所有的加操作使用8 位进行。例如：box 和cfg 相加的结果长度为8 位。
2. 有符号数和无符号数在设计中，请先按无符号数进行。
## 关系运算符 ##

关系运算符有：
- ?>（大于）

- ?<（小于）

- ?>=（不小于）

- ?<=（不大于）

- = = （逻辑相等）

- = （逻辑不等）

关系操作符的结果为真（1 ）或假（0 ）。如果操作数中有一位为X 或Z ，那么结果为X 。
例：
23 > 45
结果为假（0 ），而：
52 < 8'hxFF
结果为x 。
如果操作数长度不同，长度较短的操作数在最重要的位方向（左方）添0 补齐。例如：
'b1000 > = 'b01110
等价于：
'b01000 > = 'b01110
结果为假（0 ）。
在逻辑相等与不等的比较中，只要一个操作数含有x 或z，比较结果为未知 （x），如：
假定：
Data = 'b11x0;
Addr = 'b11x0;
那么：
Data = = Addr 比较结果不定，也就是说值为x 。

## 逻辑运算符 ##

逻辑运算符有：
&& (逻辑与)
|| (逻辑或)
！(逻辑非)
用法为：（表达式1） 逻辑运算符 （表达式2） ....
这些运算符在逻辑值0（假） 或1（真） 上操作。逻辑运算的结果为0 或1 。例如, 假定：
crd = 'b0; //0 为假
dgs = 'b1; //1 为真
那么：
crd && dgs 结果为0 (假)
crd || dgs 结果为1 (真)
！dgs 结果为0 (假)

**按位逻辑运算符**

按位运算符有：
?~（一元非）：（相当于非门运算）
?&（二元与）：（相当于与门运算）
?|（二元或）： （相当于或门运算）
?^（二元异或）：（相当于异或门运算）
?~ ^, ^ ~（二元异或非即同或）：（相当于同或门运算）
这些操作符在输入操作数的对应位上按位操作，并产生向量结果。
例如，假定,
A = 'b0110;
B = 'b0100;
那么：
A | B 结果为0110
A & B 结果为0100
如果操作数长度不相等, 长度较小的操作数在最左侧添0补位。例如,
'b0110 ^ 'b10000
与如下式的操作相同：
'b00110 ^ 'b10000
结果为'b10110。

## 条件运算符 ##

条件操作符根据条件表达式的值选择表达式，形式如下：
cond_expr ? expr1 : expr2
如果cond_expr 为真(即值为1 )，选择expr1 ；如果cond_expr 为假(值为0 )，选择expr2 。如果cond_expr 为x 或z ，结果将是按以下逻辑expr1 和expr2 按位操作的值： 0 与0 得0 ，1 与1 得1 ，其余情况为x 。
如下所示:
wire [2:0] student = marks > 18 ? grade_a : grade_c;
计算表达式marks > 18; 如果真,grade_a 赋值为student；如果marks < =18, grade_c 赋值为student 。


## 连接运算符 ##

连接操作是将小表达式合并形成大表达式的操作。形式如下：
{expr1, expr2, . . .，exprN}
实例如下所示：
wire [7:0] Dbus;
assign Dbus [7:4] = {Dbus [0], Dbus [1], Dbus[2], Dbus[ 3 ]};
//以反转的顺序将低端4 位赋给高端4 位。
assign Dbus = {Dbus [3:0], Dbus [7:4]};
//高4 位与低4 位交换。
由于非定长常数的长度未知, 不允许连接非定长常数。例如,下列式子非法：
{Dbus,5} //不允许连接操作非定长常数。

## 条件语句 ##

if 语句的语法如下：
```
if(condition_1)
procedural_statement_1
{else if(condition_2)
procedural_statement_2}
{else
procedural_statement_3}
```

如果对condition_1 求值的结果为个**非1**，那么procedural_statement_1 被执，如果condition_1 的值为0 、x 或z ，那么procedural_statement_1 不执行。如果存在一个else 分支，那么这个分支被执行。

```C
if (sum < 60) begin
grade = c;//串行语句
total_c = total _c + 1;
end
else if(sum < 75) begin
grade = b;
total_b = total_b + 1;
end
else begin
grade = a;
total_a = total_a + 1;
end
```

若为if - if 语句，请使用块语句 begin --- end ：

```
if(clk == 1) begin
if (reset== 1)
q = 0;
else
q = d;
end
```

对if语句，除非在时序逻辑中，if 语句需要有else语句。若没有缺省语句，设计将产生一个锁存器，锁存器在asic设计中有诸多的弊端。
如下一例：
if （t == 1）
q = d；
没有else 语句，当t为1（真）时，d 被赋值给q，当t为0（假）时，因为没有else 语句，电路保持 q 以前的值，这就形成一个锁存器。

**case语句**

case 语句是一个多路条件分支形式，其语法如下：
```
case(case_expr)
case_item_expr{ ,case_item_expr} :procedural_statement
. . .
. . .
[default:procedural_statement]
endcase
```
case 语句首先对条件表达式case_expr 求值，然后依次对各分支项求值并进行比较，第一个与条件表达式值相匹配的分支中的语句被执行。可以在1 个分支中定义多个分支项；这些值不需要互斥。缺省分支覆盖所有没有被分支表达式覆盖的其他分支。

```C
case (HEX)
4'b0001 : led = 7'b1111001; // 1
4'b0010 : led = 7'b0100100; // 2
4'b0011 : led = 7'b0110000; // 3
4'b0100 : led = 7'b0011001; // 4
4'b0101 : led = 7'b0010010; // 5
4'b0110 : led = 7'b0000010; // 6
4'b0111 : led = 7'b1111000; // 7
4'b1000 : led = 7'b0000000; // 8
4'b1001 : led = 7'b0010000; // 9
4'b1010 : led = 7'b0001000; // a
4'b1011 : led = 7'b0000011; // b
4'b1100 : led = 7'b1000110; // c
4'b1101 : led = 7'b0100001; // d
4'b1110 : led = 7'b0000110; // e
4'b1111 : led = 7'b0001110; // f
default : led = 7'b1000000; // 0
endcase
```

case 的缺省项必须写，防止产生锁存器。

只有组合逻辑才可能产生锁存器，时序逻辑不会产生锁存器。

# 结构建模 #

## 模块定义结构 ##

我们已经了解到，一个设计实际上是由一个个module 组成的。一个模块module 的结构如下：
module module_name (
port_list
) ;
Declarations_and_Statements
endmodule
在结构建模中，描述语句主要是实例化语句，包括对Verilog HDL 内置门如与门（and）异或门（xor）等的例化，如全加器的xor 门的调用；及对其他器件的调用，这里的器件包括FPGA厂家提供的一些宏单元以及设计者已经有的设计。
在实际应用中，实例化语句主用指后者，对内置门建议不采纳，而用数据流或行为级方式对基本门电路的描述。
端口队列port_list 列出了该模块通过哪些端口与外部模块通信，在Verilog 2001语法中还包括输入输出、wire/reg类型、位宽定义。

## 模块端口 ##

模块的端口可以是输入端口、输出端口或双向端口。缺省的端口类型为线网类型（即wire 类型）。输入端口默认为wire类型，不需要定义，输出或双向端口能够声明为wire/reg 型，使用reg必须显式声明，使用wire也强烈建议显式声明。
Verilog 2001语法中的输入输出包括端口名、输入输出、wire/reg类型、位宽定义，极大的减少了端口声明占用的代码行数。当前已经非常普及。
例子：

```C
module LED (
//input
input sys_clk , //system clock;
input sys_rst_n , //system reset, low is active;
//output
output reg [7:0] LED );
```

该例子包括输入、输出、姓名名称、位宽、输出的信号类型。

## 实例化语句 ##

一个模块能够在另外一个模块中被引用，这样就建立了描述的层次。模块实例化语句形式如下：

`module_name instance_name(port_associations);`

信号端口可以通过位置或名称关联；

但是关联方式不能够混合使用。端口关联形式如下：

- PortName //通过位置。

- .PortName (port_expr) //通过名称。

建议使用名称进行关联。

```C
// instantance RR_CORE
RR_CORE U_RR_CORE (
.rr_en ( rr_en ),
.port_vld ( key ),
.last_sel_port ( last_sel_port ),
.port_win ( port_win )
);
```

port_expr 可以是以下的任何类型：

1. 标识符（reg 或net ）如 .C（T3），T3为wire型标识符。

2. 位选择 ，如 .C（D[0]），C端口接到D信号的第0bit 位。

3. 部分选择 ，如 .Bus （Din[5：4]）。

4. 上述类型的合并，如 .Addr（{ A1，A2[1：0]}。

5. 表达式（只适用于输入端口），如 .A （wire Zire = 0 ）。

建议：
在例化的端口映射中请采用名字关联，这样，当被调用的模块管脚改变时不易出错。

## 悬空端口的处理 ##

在我们的实例化中，可能有些管脚没用到，可在映射中采用空白处理，如：

```C
DFF U_DFF_0 (
.q(qs),
.qbar ( ), //管脚悬空
.data (d ) ,
.preset ( ), // 该管脚悬空
.clock (ck)
); //名称对应方式。
```

对输入管脚悬空的，则该管脚输入为高阻 Z，输出管脚被悬空的，该输出管脚废弃不用。
建议：
大规模电路设计的时候，悬空的端口最好使用XX_nc（wire类型）进行显式声明，提高检视代码的效率。

## 不同端口长度的处理 ##

当端口和局部端口表达式的长度不同时，端口通过无符号数的右对齐或截断方式进行匹配。

```C
module Child (Pba, Ppy) ;
input [5:0] Pba;
output [2:0] Ppy;
. . .
endmodule
module Top;
wire [1:2] Bdl;
wire [2:6] Mpr;
Child C1 (Bdl, Mpr) ;
endmodule
```

在对Child 模块的实例中，Bdl[2]连接到Pba[ 0 ]，Bdl[1] 连接到Pba[ 1 ]，余下的输入端口Pba[5]、Pba[4]、Pba[2]和Pba[3]悬空，因此为高阻态z 。与之相似，Mpr[6]连接到Ppy[0]，Mpr[5]连接到Ppy[1]，Mpr[4] 连接到Ppy[2]。

![](http://7xowaa.com1.z0.glb.clouddn.com/port_cond.png)


## 结构化建模具体实例 ##

对一个数字系统的设计，我们采用的是自顶向下的设计方式。可把系统划分成几个功能模块，每个功能模块再划分成下一层的子模块。每个模块的设计对应一个module，一个module 设计成一个verilog HDL 程序文件。因此，对一个系统的顶层模块，我们采用结构化的设计，即顶层模块分别调用了各个功能模块。下面以一个实例（一个频率计数器系统）说明如何用HDL进行系统设计。
在该系统中，我们划分成如下三个部分：2输入与门模块，LED显示模块，4位计数器模块。系统的层次描述如下：

![](http://7xowaa.com1.z0.glb.clouddn.com/top_down.png)

顶层模块CNT_BCD，文件名CNT_BCD.v，该模块调用了低层模块 AND2、CNT_4b和HEX2LED 。
顶层模块CNT_BCD对应的设计文件 CNT_BCD.v 内容为：
```C
module CNT_BCD (
// ------------ Port declarations --------- //
input CLK,
input GATE,
input RESET,
output wire [3:0] BCD_A,
output wire [3:0] BCD_B,
output wire [3:0] BCD_C,
output wire [3:0] BCD_D
) ;
// ----------- Signal declarations -------- //
wire NET104;
wire NET124;
wire NET132;
wire NET80;
// -------- Component instantiations -------//
CNT_4b U0(
.CLK(CLK),
.ENABLE(GATE),
.FULL(NET80),
.Q(BCD_A),
.RESET(RESET)
);
AND2 U6(
.A0(NET104),
.A1(NET124),
.Y(NET132)
);
endmodule
```

**Tips:**

这里的AND2是为了举例说明，在实际设计中，对门级不要重新设计成一个模块，同时对涉及保留字的（不管大小写）相类似的标识符最好不用。


