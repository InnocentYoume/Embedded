# 嵌入式操作系统
## 嵌入式系统基础概念
* 嵌入式计算机是诞生于微处理器发展时代
* 以应用为中心的分类方法,对计算机的分类
    * 非嵌入式应用 - 通用计算机
    * 嵌入式应用 - 嵌入式计算机
* 定义 : 以应用为中心,以计算机技术为基础,软件硬件可裁剪适应应用系统对功能,可靠性,成本,功耗严格要求的专用计算机系统
* 嵌入式系统按表现形式及使用硬件种类分为:
    * 芯片级嵌入 - 系统中使用含程序或算法的处理器的嵌入式系统
    * 模块级嵌入 - 系统中使用某个模块核心的嵌入式系统
* 嵌入式系统按软件实时性需求分为:
    * 非实时系统
    * 软实时系统
    * 硬实时系统
* 嵌入式系统的**硬件架构**,是以嵌入式处理器为中心,配置存储器,I/O设备,通信模块以及电源等必要的辅助接口组成
* 嵌入式系统的**硬件核心**,是嵌入式微处理器,有时为了提高系统的信息处理能力,常外接DSP(Digital Signal Processing 数字型号处理)或DSP协处理器来完成高性能信号处理
* 使用操作系统的嵌入式系统,有如下结构:
    * 设备驱动层
    * 实时操作系统(RTOS)
    * 实际应用程序层
* 嵌入式系统的启动初始化程序 - **BootLoader**
* 嵌入式系统的发展趋势
    * 随着信息化与数字化的发展,嵌入式设备实现网络互联是未来发展的趋势
    * 优化嵌入式系统的硬件内核,提高系统运行速度,降低功耗与硬件成本
    * 指令级的并行计算技术将引入嵌入式微处理器
    * 嵌入式微处理器将会向多核技术发展
    * 嵌入式系统向智能化发展
    * 嵌入式技术将引领信息时代

---
## ARM技术与ARM体系结构
* 两种典型的计算机结构
    * 冯·诺依曼 : 将数据与指令都存储在存储器 , ARM 7 使用冯诺依曼结构
    * 哈佛结构 : 数据与指令有独立的存储器 , ARM 9 使用哈佛体系结构
* **ISA**(Instruction-Set Architecture)指令集体系结构 , 硬件工程师与软件工程师之间的桥梁
* 通用ISA模型
    * 复杂指令集计算机CISC : Intel x86
    * 精简指令集计算机RISC : ARM
* ARM主要架构
    * 加载/存储架构
        * 程序员需要在数据被处理之前显式的把数据从存储器**加载**到寄存器
        * 程序员必须在数据被处理之后显式的把输出数据**存储**到寄存器
    * 三地址架构 : 一个单指令可以涉及三个寄存器
* ARM版本
    * V1 : 26位寻址空间 , 基本的无乘法数据处理指令 , Load/Store , SWI软件中断
    * V2 : 添加乘法,协处理器操作,SWP/SWPB交换指令,快速中断模式
    * V3 : 寻址空间32位 , CPSR程序状态寄存器 , SPSR程序状态保存寄存器 , MRS/MSR指令访问CPSR和SPSR , 异常处理返回指令
    * V4 : Thumb指令集 , 处理器特权模式
    * V5 : 提升混合使用效率 , CLZ前导零计数指令 , BKPT软件断点指令 , 协处理器指令扩充
    * V6 : 强化了图形处理性能
    * V7 : 各项优化
* ARM版本命名规则  
ARM {x} {y} {z} {T} {D} {M} {I} {E} {J} {F} {-S}
    * x - 系列号 , 如ARM7的7
    * y - 内部存储管理/保护单元 , 如ARM72的2
    * z - 内含高速缓存Cache
    * T ~~humb~~ - 支持Thumb
    * D ~~ebug~~ - 支持JTAG片上调试
    * M ~~ultiply~~ - 支持长乘法
    * I ~~terrupt~~ - 支持嵌入式追踪宏单元ETM 来设置断点调试
    * E ~~nhanse~~ - 加强DSP指令(基于TDMI)
    * J ~~ava~~ - 支持Java
    * F ~~loat~~ - 向量浮点单元
    * S ~~ynthesize~~ - 可综合版本,提供源码
* ARM7内核结构 : 三级流水线
    * 取指 (Fetch) :将指令从存储器取出放入指令Cache中
    * 译码 (Decode) : 由译码逻辑单元完成 , 对指令Cache指令解释并告诉CPU
    * 执行 (Execute) : 执行代码
* PC指向**正被取指的指令**而不是正在执行的指令
* ARM处理器**工作模式**
    * 用户模式 : **非特权模式** , 大部分任务在此模式执行 , 除此模式外其余模式为**特权模式**
    * FIQ模式 : (Fast Interrupt Request) 快速中断模式 , 高优先级中断产生时进入 , 支持高速数据传输
    * IRQ模式 : (Interrupt Request) 普通中断模式 , 低优先级中断产生时进入 , 按处理中端的处理器分:
        * 非中断向量
        * 中断向量
    * SVC模式 : 管理模式 , 操作系统保护模式 , 复位或软中断指令执行时进入
    * 中止模式 : 存取异常时进入 ,实现保护
    * 未定义指令异常模式 : 执行未定义指令时进入 , 处理未定义指令陷阱
    * 系统模式 : 特权模式 , 运行特权级的操作系统任务 ,除此特权模式外其余特权模式都有自己的异常处理入口点 , 故称为**异常模式**
* ARM处理器寄存器  
共有37个寄存器 , 其中
    * 31个通用寄存器 : 包括PC等 , 32位
        * r13 : SP(Stake Point)栈指针
        * r14 : LR(Link Register)链接寄存器
        * r15 : PC(Program Counter)
    * 6个状态寄存器 : 32位
        * CPSR(Current Program Status Register)程序状态寄存器 , 特权模式存取
        * SPSR(Saved Program Status Register)程序状态保存寄存器
* **CPSR**  
![image.png](https://i.loli.net/2019/11/10/zGeQ5gKlJxW734V.png)
    * 条件位
        * N : 1则结果为负 , 0为正或0
        * Z : 1结果为0 , 0结果非0
        * C : 1进位 , 0借位
        * V : 1结果溢出 , 0未溢出
    * Q位
        * ARM5TE/J支持 , 表示增强型DSP是否溢出
    * J位
        * ARM5TE/J支持 , 为1时表示在Jazelle状态 (Java支持)
    * 中断禁止位
        * I : 为1时禁止IRQ
        * F : 为1时禁止FIQ
    * MODE处理器模式位
        * 10000 : User (用户模式)
        * 10001 : FIQ (快速中断模式)
        * 10010 : IRQ (普通中断模式)
        * 10011 : Supervisor (SVC管理模式)
        * 10111 : Abort (中止模式)
        * 11011 : Undefned (未定义指令异常模式)
        * 11111 : System (系统模式)
* 状态对比
    * ARM
        * 指令32bits宽
        * 按字(word)对齐
        * PC值由[31:2]决定 , [1:0]未定义
    * Thumb
        * 指令16bits宽
        * 按半字(halfword)对齐
        * PC由[31:1]决定 , bit[0]未定义
* ARM处理器的异常
    * 数据中止 : 访问无效存储器地址或无访问权限
    * 快速中断请求 : FIQ 优先级高于IRQ
    * 普通中断请求 : IRQ 
        * BL使用之前要改变为System模式防止LR被冲掉
        * 恢复IRQ模式
        * 在返回时禁止中断来防止SPSR被冲掉
    * 预取指中止 : 欲取指地址不存在或访问无效地址
        * 发生于 LR-4:
            * 有存储器管理的系统: 修正问题 , 返回并重新执行异常值令
            * 无存储器管理的系统: 报错退出
        * 发生于 LR-8:
            * 同上 , 但若使用了MMU , 数据异常地址在其Fault Address寄存器中
    * 软件中断 : SWI 最低优先级
        * SWI处理程序必须确定SWI调用在ARM还是Thumb状态
        * 可以检查SPSR的T位
        * 参数传递超过4个则压栈
    * 复位 : 优先级最高 , 程序启动时调用 , 初始化
    * 未定义指令 : 执行未定义指令 , 优先级最低  
    处理方法 :
        * 执行协处理器仿真
        * 禁止在非超级用户模式操作
        * 报错退出
* 异常出现时采取的响应
    1. 将下一条指令地址存入LR
        * 异常从ARM状态进入 , 则LR保存PC+4/PC+8(与异常类型有关) 
        * 异常从Thumb状态进入 , 则LR保存PC的偏移量)
    2. CPSR复制到SPSR
    3. 根据异常类型强制设置CPSR的MODE位 (如需要也要设置中断禁止位)
    4. 强制PC取异常向量地址取指 , 从而跳到异常处理程序
* 异常返回时采取的响应
    1. 从SPSR恢复CPSR   
    2. 从LR恢复PC       
* ARM存储方式
    * ARM地址空间 2^32 = 4G 字节
    * ARM态 PC + 4 , Thumb态 PC + 2
    * 小端(little-Endian) : 从右往左读
    * 大端(Big-Endian) : 从左往右读

---
## ARM指令集寻址方式
### ARM指令的编码格式
```armasm  
    <opcode>{<cond>}{S} , <Rd> , <Rn> , <operand2>
```
* 第二操作数 
    * 立即数   
    每个立即数由一个8位的常数进行32位循环移位 *偶数位* 得到 , 其中移位数由一个4位二进制的2倍表示 , 即:
        ```c
        immdeiate = immed_8进行32位循环移位(2 * rotate_4)
        ```
        * 判断一个立即数是否合法的办法 :        
            1. 数字写为2进制 , 看顶点两个1之间的最大间隔(循环和顺序) , 要是顺序和循环都间隔大于8 , 则非法 , 否则 : 
            2. 顺序看最大间隔 *等于8* , 看最高位1前或最低位1后是否有偶数个0,只要由则合法
            3. 循环看最大间隔 *小于等于8* , 则看两端间隔有一个是偶数则合法
        ```armasm
        SUB R0,R1,#4        ;R0<-R1-4
        ```
    * 寄存器 
        ```armasm   
        SUB R0,R1,R2        ;R0<-R1-R2
        ```
    * 寄存器移位 
        ```armasm
        <Rm>,<shift> #<shift_imm>
        SUB R0,R1,R2,LSR #2 ;R0<-R1+(R2/4)
        ADD R0,R1,LSR R3    ;R0<-R1+R3左移R3位
        ADD R0,R1,R2,RRX    ;R0<-R1+R2移位
        ```
        注意:立即数位数为 *5位*
        * 移位方式 :
            * LSL 逻辑左移(补0)
            * LSR 逻辑右移(补0)
            * ASR 算数右移(补首位即符号位)
            * ROR 循环右移(补移出位)
            * RRX 带进位右移(补进位C)
### Load/Store指令寻址  
1. 寄存器间接寻址
    ```armasm
    LDR R0,[R1]     ;R0<-[R1],即地址为R1
    ```
2. 基址加变址寻址
    * 字、无符号字节寻址
        ```armasm
        LDR {<cond>}{B}{T}<Rd> , <addressing_mode>
        ```
        addressing_mode
        * 前变址法 : 先变址再写入
            * 前变址不回写 :
                ``` 
                立即数 : [<Rn> , #+/- <immed_offset>]
                寄存器 : [<Rn> , +/-<Rm>]
                寄存器带移位 : [<Rn> , +/-<Rm> , <shift>#shift_amount ]
                ```
            * 前变址回写 : 
                ```
                立即数 : [<Rn> , #+/- <immed_offset>]!
                寄存器 : [<Rn> , +/-<Rm>]!
                寄存器带移位 : [<Rn> , +/-<Rm> , <shift>#shift_amount ]!
                ```
        * 后变址法 : 写入后变址
            * 后变址回写 :
                ``` 
                立即数 : [<Rn>] , #+/- <immed_offset>
                寄存器 : [<Rn>] , +/-<Rm>
                寄存器带移位 : [<Rn>] , +/-<Rm>, <shift>#shift_amount 
                ```
        ```armasm
        LDR R0 , [R1] , +R2 , LSL#2 ;R0<-[R1]+R2左移2位 
        ```
    * 半字、有符号字节寻址
        ```
        加载有符号字节到寄存器 : 
            LDR {<cond>}SB <Rd>,<addressing_mode>
        加载有符号半字到寄存器 : 
            LDR {<cond>}SH <Rd>,<addressing_mode>
        加载无符号半字到寄存器 : 
            LDR {<cond>}H  <Rd>,<addressing_mode>
        存储无符号半字到内存 : 	
            STR {<cond>}H  <Rd>,<addressing_mode>
        ```
        addressing_mode 同上
    * 批量寻址
        ```
        批量加载 : 
            LDM {<cond>}<addr_mode> <Rn> {!}, <register>{^}
        批量存储 : 
            STM {<cond>}<addr_mode> <Rn> {!}, <register>{^}
        ```
        ```armasm
        STMFD SP! , {R0}     ;把R0保存到堆栈（sp指向的地址）中
        ```
### 堆栈操作  
* 先进先出
* 堆栈位向下增长还是向上增长
### 协处理器指令寻址方式
```
<opcode>{<cond>}{L} <coproc> , <CRd> , <addressing_mode>
```
* opcode为指令操作码  
* coproc为协处理器名称  
* addressing_mode为指令寻址模式
    * 内存地址索引格式
        ```
        前变址不回写形式 : 	[<Rn> , #+/-<imm_offset8>*4 ]
        前变址回写形式 : 	[<Rn> , #+/-<imm_offset8>*4]！
        后变址回写形式 : 	[<Rn>] , #+/-<imm_offset8>*4
        ```
    * 内存地址非索引格式
        ```
        [<Rn>] , <user-define>
        ```

---
## ARM指令集系统
### 数据传送指令 
* MOV{cond}{S} Rd,operand2  
operand2 传递到 Rd, 包含S则影响CPSR标志位
* MVN{cond}{S} Rd,operand2  
operand2 按位取反后传递到 Rd, 包含S则影响CPSR标志位
### 算数运算
* ADD{cond}{S} Rd,Rn,operand2   Rn+operand2 -> Rd
* ADC   带进位C的ADD
* SUB{cond}{S} Rd,Rn,operand2   Rn-operand2 -> Rd
* SBC   带借位C的ADD
* RSB{cond}{S} Rd,Rn,operand2   operand2-Rn -> Rd   逆向减法
* RSC   带借位C的RSB
* 32位乘法
    * MUL{cond}{S}Rd,Rm,Rs      Rm*Rs -> Rd(32bits)
    * MLA{cond}{S}Rd,Rm,Rs,Rn   (Rm*Rs)+Rn -> Rd(32bits)
* 64位乘法
    * UMULL{cond}{S}RdLo,RdHi,Rm,Rs     无符号Rm*Rs -> {Rdlo|RdHi}
    * UMLAL{cond}{S}RdLo,RdHi,Rm,Rs     无符号Rm*Rs + {Rdlo|RdHi} -> {Rdlo|RdHi}
    * SMULL{cond}{S}RdLo,RdHi,Rm,Rs     有符号Rm*Rs -> {Rdlo|RdHi}
    * SMLAL{cond}{S}RdLo,RdHi,Rm,Rs     有符号Rm*Rs + {Rdlo|RdHi} -> {Rdlo|RdHi}
* CLZ\<cond>\<Rd>,\<Rm>  
计算32位寄存器 *高位* 中“0”的个数
### 逻辑运算
* AND{cond}{S} Rd,Rn,operand2   Rn 按位与 operand2 -> Rd
* ORR{cond}{S} Rd,Rn,operand2   Rn 按位或 operand2 -> Rd
* EOR{cond}{S} Rd,Rn,operand2   Rn 异或   operand2 -> Rd
* BIC{cond}{S} Rd,Rn,operand2   Rn 按位与 operand 的反码 -> Rd
### 比较指令
* CMP{cond}  Rn, operand2   Rn-operand2 并影响位C
* CMN{cond}  Rn, operand2   比较负数,影响C
* TST{cond}  Rn, operand2   Rn与operand2逻辑与,影响CPSR
* TEQ{cond}  Rn, operand2   相等测试,按位异或,影响CPSR
### 跳转指令
* B	    跳转指令
* BL	带返回的跳转指令,即PC值保存到LR
* BX	带状态切换(ARM&Thumb)的跳转指令
* BLX	带返回和状态切换的跳转指令
### 加载/存储指令
* LDR{cond}     Rd, \<addressing>
* STR{cond}     Rd, \<addressing>  
小端为低八位 , 大端为高八位 , 读或写32位
* LDR{cond}T    Rd, \<addressing>
* STR{cond}T    Rd, \<addressing>  
在用户模式下使用无效，在特权模式下只能使用前变址形式
* LDR{cond}B    Rd, \<addressing>
* STR{cond}B    Rd, \<addressing>  
取字节到目标寄存器低八位
*LDR{cond}BT    Rd，\<addressing>
*STR{cond}BT    Rd，\<addressing>  
当在特权极的处理器模式下使用本指令时，内存系统将该操作当作一般用户模式下的内存访问操作。这种指令在用户模式下使用无效，在特权模式下只能使用前变址形式。
* 批量加载：
LDM {\<cond>}\<addr_mode> \<Rn> {!},\<registers>
* 批量存储：
STM{\<cond>}\<addr_mode> \<Rn> {!}, \<registers>
    ```armasm
    MOV	    R1，#0x9000
    MOV	    R0，#0x11
    MOV	    R2，#0x22
    MOV 	R3，#0x33
    MOV 	R4，#0x44
    MOV 	R5，#0x55
    STMDB 	R1! ,{R0,R2-R5}
    ```
    ```armasm
    ;例题:
    ;批量字数据加载指令实现IRQ中断的返回?
    ;IRQ中断处理程序时
    SUB		R14,R14,#4
    STMFD  	R13!, {R0-R3, R12, LR} 
    ;从数据栈中恢复寄存器R0~R3及R12的值，将返回地址传送到PC中      
    LDMFD   R13!, {R0-R3, R12, PC}^
    ```
### 数据交换指令
* SWP{\<cond>} \<Rd>, \<Rm>, [\<Rn>]  
[Rn]->Rd,Rm->[Rn]
* SWP{\<cond>}{B}  \<Rd>, \<Rm>,[\<Rn>]  
[Rn]->Rd,Rm->[Rn] (只读取一个字节)
### 读写程序状态寄存器指令
* MRS {\<cond>} \<Rd>,  CPSR
* MRS {\<cond>} \<Rd>,  SPSR  
读CPSR或SPSR到Rd
* MRS {\<cond>} \<Rd>,  CPSR
* MRS {\<cond>} \<Rd>,  SPSR
写CPSR或SPSR到Rd
* MSR {\<cond>}	 CPSR_\<fields>,  \<operand2>   
* MSR {\<cond>}	 SPSR_\<fields>,  \<operand2>  
其中operand2有立即数或寄存器
    ```armasm
    ;例题:
    ;将ARM处理器切换到未定义模式，并关闭中断
    .equ 	Undef_Mode,	0x1B
    .equ	Mode_Mask	0x1F
    .equ 	NOINT,		0xC0
    MRS	    R0, CPSR			;读CPSR		
    BIC	    R0, R0, #Mode_Mask 
    ORR	    R1, R0, #Undef_Mode
    MSR	    CPSR_cxsf, R1
    MSR	    CPSR_cxsf, R1		    ;进入未定义模式
    ```
### 协处理器操作指令
* CDP{\<cond>} \<Cp_num>,\<opcode_1>,\<CRd>,\<CRn>,\<CRm>,{\<opcode_2>}  
ARM处理器通知ARM协处理器执行特定的操作，若协处理器不能成功完成特定的操作，则产生未定义指令异常
* 加载存储指令
    * LDC{\<cond>}{L} \<coproc>，\<CRd>，\<addressing_mode>
    * STC{\<cond>}{L} \<coproc>，\<CRd>，\<addressing_mode>
* ARM寄存器到协处理器寄存器的数据传送指令MCR
* 协处理器寄存器到ARM寄存器的数据传送指令MRC
### 异常产生指令
* 软中断指令 SWI{\<cond>} <immed_24>  
24位的立即数指定用户程序调用系统例程的类型
* BKPT (B ~~rea~~ KP ~~oin~~ T)指令产生软件断点中断，可用于程序的调试  
BKPT \<immed_16>  
immed_16为16位的立即数，此立即数被调试软件用来保存额外的断点信息
### 伪指令  
```armasm
.global _start		    @给_start外部链接属性
.section .text		    @指定当前段为代码段
.ascii .byte .short .long .word .quad .float .string    
                        @ 定义数据
.align 4			    @以16字节对齐
.balignl 16 0xabcdefgh 	@16字节对齐填充
.equ			        @类似于C中宏定义
.end			        @标识文件结束
.include			    @头文件包含
.arm / .code32	        @声明以下为arm指令
.thumb / .code16	    @声明以下为thubm指令
```
* LDR{cond} register , =expression  
LDR伪指令将一个32位的常数或者一个地址值读取到寄存器中
* ADRL{cond} register , = expression  
将基于PC相对偏移的地址值或基于寄存器相对偏移的地址值读取到寄存器中
    ```armasm
    ADRL R0,_start      ;即_start与PC的偏移量读到R0
    ```
* ADR{cond} register , = expression  
ADR为小范围地址读取伪指令，它将基于PC相对偏移的地址值读取到寄存器中
* 空操作伪指令NOP  
NOP是空操作伪指令，在汇编时将会被替代成ARM中的空操作 
### 符号定义伪操作
* 常量定义伪操作 .equ或.set
    ```armasm
    .equ 	symbol, expr
    .set 	symbol, expr
    ```
* 常量定义伪操作 .equiv
    ```armasm
    .equiv 	symbol, expr
    ```
* 声明全局常量伪操作 .global或.globl
    ```armasm
    .global symbol
    .globl 	symbol
    ```
* 声明外部常量伪操作 .extern
    ```armasm
    .extern symbol
    ```
### 数据定义伪操作
* 字节定义  .byte
* 半字定义  .hword 或 .short
* 字定义    .word或.int或.long
    ```armasm
    ;分配一段字节内存单元,并用25,0x11,031,‘x’,0x36进行初始化
    
    .byte 	25, 0x11, 031, ‘x’,0x36
    ```
* 字符串定义 .ascii 和 .asciz 或 .string  
    ```armasm
    .ascii	”ARM9TDMI”
    ```
* 双字定义  .quad
    ```armasm
    ;分配一段双字内存单元，并用0x1234567887654321，0xAAAABBBBCCCCDDDD进行初始化。
    .quad 	0x1234567887654321，0xAAAABBBBCCCCDDDD
    ```
* 四字定义  .octa
* 单精度浮点数定义 .float 或 .single
    ```armasm
    ;分配一段字内存单元，并用32位的 IEEE 单精度浮点数0f1.23,	0f568.23E7	,0f3.1E12进行初始化。
    .float 	0f1.23,	0f568.23E7	,0f3.1E12
    ;也可以用.single来实现
    .single 0f1.23,	0f568.23E7	,0f3.1E12
    ```
* 双精度浮点数定义 .double
    ```armasm
    ;分配一段字内存单元，并用64位的 IEEE 双精度浮点数0f1.23,	0f568.23E7	,0f3.1E12进行初始化。
    .double 	0f1.2387,	0f528.23E15	,0f3.1E9
    ```
* 重复内存单元定义 .fill
    ```armasm
    ;分配一段内存单元，并用长为8字节的数值0x55AA55AA55AA55AA填充100次。
    .fill		100，8，0x55AA55AA55AA55AA
    ```
* 零填充字节内存单元定义 .zero
    ```armasm
    ;分配一段长度为100个字节的内存单元，并用0进行初始化。
    .zero		100
    ```
* 固定填充字节内存单元定义.space或.skip
    ```armasm
    ;分配一段长度为100个字节的内存单元，并用0x55进行初始化。
    .space	    100, 	0x55
    ;也可以用.skip来实现：
    .skip	    100, 	0x55
    ```
