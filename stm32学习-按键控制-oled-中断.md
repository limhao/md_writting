---
title: stm32学习 按键控制 oled 中断 
---

今天 研究的东西有点多 

心态有点疲倦

看来三块时间还是得分割一下

2023/9/22

p8-p12

## 按键控制

主要是按键的消抖以及模块化编程的思想

key.h 文件

```c++
#ifndef __KEY_H
#define __KEY_H

void Key_Init(void);
uint8_t Key_GetNum(void);

#endif
```

key.c 文件

```c++
#include "stm32f10x.h"                  // Device header
#include "Delay.h"

void Key_Init(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
	
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_1 | GPIO_Pin_11;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOB, &GPIO_InitStructure);
}

uint8_t Key_GetNum(void)
{
	uint8_t KeyNum = 0;
	if (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_1) == 0)
	{
		Delay_ms(20);
		while (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_1) == 0);
		Delay_ms(20);
		KeyNum = 1;
	}
	if (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_11) == 0)
	{
		Delay_ms(20);
		while (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_11) == 0);
		Delay_ms(20);
		KeyNum = 2;
	}
	
	return KeyNum;
}

```

## 逻辑电路

<img src="https://pic.imgdb.cn/item/650da654c458853aef792681.jpg" style="zoom: 25%;" />

## 中断控制

EXTI外部中断

中断：在主程序运行过程中，出现了特定的中断触发条件（中断源），使得CPU暂停当前正在运行的程序，转而去处理中断程序，处理完成后又返回原来被暂停的位置继续运行

![](https://pic.imgdb.cn/item/650da409c458853aef7734e1.jpg)

针对于不同的GPIO 使用的pin不能相同 否者会打架

中断的代码逻辑

1. RCC启动时钟
2. 设置端口为输入模式
3. 配置AFIO
4. 配置EXTI 选择触发方式
5. 配置NVIC
   1. 配置文件在 misc.h 中 被称为杂项文件
   2. nvic_priorConfig
   3. nvic_init

其中的代码

```c++
void CountSensor_Init(void)
{
    // 貌似NVIC一直是常启动 然后导致exti也不需要RCC启动
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_AFIO, ENABLE);
	
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_14;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOB, &GPIO_InitStructure);
	// 这行 是配置AFIO 的 主要是选择线 和 引脚
	GPIO_EXTILineConfig(GPIO_PortSourceGPIOB, GPIO_PinSource14);
	// 初始化结构体
    // 加定义
    // 初始化
	EXTI_InitTypeDef EXTI_InitStructure;
	EXTI_InitStructure.EXTI_Line = EXTI_Line14;
	EXTI_InitStructure.EXTI_LineCmd = ENABLE;
	EXTI_InitStructure.EXTI_Mode = EXTI_Mode_Interrupt;
	EXTI_InitStructure.EXTI_Trigger = EXTI_Trigger_Falling;
	EXTI_Init(&EXTI_InitStructure);
	// 一样一样
    // 在配置nvic中断之前 先指定下分组
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
	//跟上面差不多
    // 因为使用到14这个pin
    // 所以channel这里要用EXTI15_10_IRQn
	NVIC_InitTypeDef NVIC_InitStructure;
	NVIC_InitStructure.NVIC_IRQChannel = EXTI15_10_IRQn;
	NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
	NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 1;
	NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
	NVIC_Init(&NVIC_InitStructure);
}
```

