# uCOS-III 移植
## 环境
* 硬件 
   * 野火STM32F429开发板
   * 串口转USB模块
   * J-Link烧写模块
* 软件
   * Keil uVersion v5
   * XCOM串口调试工具 v2.0
## 为什么要移植到 STM32F429 ?
* 拥有相应的汇编器,C语言编译器与连接器
* 支持中断处理以及硬件定时器,可以产生硬件中断
* 拥有足够的内存 (512k Flash,256KB RAM) 支持uCOS-III运行
## 移植过程中需要修改的文件
* os_cup_a.asm
* os_cpu_c.c
* os_cpu.h
## 修改过程
1. 建立工程
<br>在现有的STM32keil工程上进行修改
<br>在原有工程上加入uCOS-III内核文件
修改后路径目录如下:
<br>![image.png](https://i.loli.net/2019/11/09/W1835L9BNuaZS26.png)
2. 修改目标
<br>在STM32中断向量表中,可以看到如下中断
   ![image.png](https://i.loli.net/2019/11/09/fKDqezALHnRBNlZ.png)
   需要将其连接到uCOS的中断上
   先注释掉在stm32f4xx_it.c中的以下函数声明
   ```c
   void PendSV_Handler(void){}
   void SysTick_Handler(void){}
   ```
   以免重复定义
3. 修改os_cpu_a.asm
<br>修改
   ```
   EXPORT  OS_CPU_PendSVHandler
   ```
   为
   ```
   EXPORT  PendSVHandler
   ```
   并将下面的定义
   ```
   OS_CPU_PendSVHandler
   ```
   修改为
   ```
   PendSVHandler
   ```
4. 修改os_cpu_c.c
<br>修改
   ```c
   void  OS_CPU_SysTickHandler (void)
   ```
   为
   ```c
   void  SysTickHandler (void)
   ```
   以上,修改完毕
## 测试
   1.编写串口初始化函数,用于调试:
   ```c
   void uart_init(u32 bound){
	   GPIO_InitTypeDef GPIO_InitStructure;
	   USART_InitTypeDef USART_InitStructure;
	   NVIC_InitTypeDef NVIC_InitStructure;
	
	   RCC_AHB1PeriphClockCmd(RCC_AHB1Periph_GPIOA,ENABLE);
	   RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1,ENABLE);
 
	   GPIO_PinAFConfig(GPIOA,GPIO_PinSource9,GPIO_AF_USART1);
	   GPIO_PinAFConfig(GPIOA,GPIO_PinSource10,GPIO_AF_USART1); 
	
	   GPIO_InitStructure.GPIO_Pin = GPIO_Pin_9 | GPIO_Pin_10; 
	   GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF;
	   GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	   GPIO_InitStructure.GPIO_OType = GPIO_OType_PP;
	   GPIO_InitStructure.GPIO_PuPd = GPIO_PuPd_UP;
	   GPIO_Init(GPIOA,&GPIO_InitStructure);

	   USART_InitStructure.USART_BaudRate = bound;
	   USART_InitStructure.USART_WordLength = USART_WordLength_8b;
	   USART_InitStructure.USART_StopBits = USART_StopBits_1;
	   USART_InitStructure.USART_Parity = USART_Parity_No;
	   USART_InitStructure.USART_HardwareFlowControl = USART_HardwareFlowControl_None;
	   USART_InitStructure.USART_Mode = USART_Mode_Rx | USART_Mode_Tx;
	   USART_Init(USART1, &USART_InitStructure);
	
	   USART_Cmd(USART1, ENABLE);
	
	   USART_ClearFlag(USART1, USART_FLAG_TC);
	
      #if EN_USART1_RX	
	   USART_ITConfig(USART1, USART_IT_RXNE, ENABLE);
	   NVIC_InitStructure.NVIC_IRQChannel = USART1_IRQn;
	   NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority=3;
	   NVIC_InitStructure.NVIC_IRQChannelSubPriority =3;	
	   NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
	   NVIC_Init(&NVIC_InitStructure);
      #endif
   }
   ```
   之后,编写main函数进行测试
   ```c
   #include "sys.h"
   #include "delay.h"
   #include "usart.h"   
   #include "includes.h"
   #include "os_app_hooks.h"
   
   #define START_TASK_PRIO		3	
   #define START_STK_SIZE 		512
   OS_TCB StartTaskTCB;
	
   CPU_STK START_TASK_STK[START_STK_SIZE];

   void start_task(void *p_arg);

   #define FLOAT_TASK_PRIO		6

   #define FLOAT_STK_SIZE		128

   OS_TCB	FloatTaskTCB;

   __align(8) CPU_STK	FLOAT_TASK_STK[FLOAT_STK_SIZE];

   void float_task(void *p_arg);

   int main(void)
   {
	   OS_ERR err;
	   CPU_SR_ALLOC();
	
	   delay_init(168);  
	   NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
	   uart_init(115200);          
	
	   OSInit(&err);		//Init UCOSIII
	   OS_CRITICAL_ENTER();//     
	      
	   OSTaskCreate((OS_TCB 	* )&StartTaskTCB,		
				 (CPU_CHAR	* )"start task", 		
                 (OS_TASK_PTR )start_task, 			   
                 (void		* )0,					         
                 (OS_PRIO	  )START_TASK_PRIO,        
                 (CPU_STK   * )&START_TASK_STK[0],	      
                 (CPU_STK_SIZE)START_STK_SIZE/10,	        
                 (CPU_STK_SIZE)START_STK_SIZE,		    
                 (OS_MSG_QTY  )0,				     
                 (OS_TICK	  )0,				
                 (void   	* )0,					
                 (OS_OPT      )OS_OPT_TASK_STK_CHK|OS_OPT_TASK_STK_CLR,    
                 (OS_ERR 	* )&err);		
	   OS_CRITICAL_EXIT();	
	   OSStart(&err); 
	   while(1);
   }
     
   void start_task(void *p_arg)
   {
	   OS_ERR err;
	   CPU_SR_ALLOC();
	   p_arg = p_arg;

	   CPU_Init();
   #if OS_CFG_STAT_TASK_EN > 0u
      OSStatTaskCPUUsageInit(&err);  	             
   #endif
	
   #ifdef CPU_CFG_INT_DIS_MEAS_EN		
       CPU_IntDisMeasMaxCurReset();	
   #endif

   #if	OS_CFG_SCHED_ROUND_ROBIN_EN  
	   OSSchedRoundRobinCfg(DEF_ENABLED,1,&err);  
   #endif		
	
	   OS_CRITICAL_ENTER();        
	   OSTaskCreate((OS_TCB 	* )&FloatTaskTCB,		
				      (CPU_CHAR	* )"float test task", 		
                  (OS_TASK_PTR )float_task, 			
                  (void		* )0,					
                  (OS_PRIO	  )FLOAT_TASK_PRIO,     	
                  (CPU_STK   * )&FLOAT_TASK_STK[0],	
                  (CPU_STK_SIZE)FLOAT_STK_SIZE/10,	
                  (CPU_STK_SIZE)FLOAT_STK_SIZE,		
                  (OS_MSG_QTY  )0,					
                  (OS_TICK	  )0,					
                  (void   	* )0,				
                  (OS_OPT      )OS_OPT_TASK_STK_CHK|        OS_OPT_TASK_STK_CLR, 
                  (OS_ERR 	* )&err);				 
	   OS_TaskSuspend((OS_TCB*)&StartTaskTCB,&err);				 
	   OS_CRITICAL_EXIT();	
   }

   void float_task(void *p_arg)
   {
	   CPU_SR_ALLOC();
	   static float float_num=0.01;
	   while(1)
	   {
		   float_num+=0.01f;
		   OS_CRITICAL_ENTER();	
		   printf("float_num   : %.4f\r\n",float_num);
		   OS_CRITICAL_EXIT();		
		   delay_ms(500);			
	   }
   }
```
   测试结果:
   ![image.png](https://i.loli.net/2019/11/09/q9pJ5aRozyIvXl2.png)
   可见,测试成功 !



