---
title: GPIO工程
---

## 说在开头

今天是2023/9/21

学了点灯 点流水灯 点蜂鸣器

![](https://pic.imgdb.cn/item/650c4c0ec458853aefe2de60.jpg)

## 工程创建

- start

startup/arm

ST/STM32F10x

CM3/CoreSupport

- library

STM32_Driver/inc

STM32_Driver/src

- user

Project/template

## 低电平还是有较强的驱动能力的

高电平弱驱动

低电平强驱动

## GPIO操作

1. RCC开启GPIO时钟
2. GPIO_init 初始化GPIO
   1. mode
   2. pin
   3. speed
3. 输入输出函数控制GPIO口
   1. 8个输入输出

## 输入输出模式

https://zhuanlan.zhihu.com/p/361640739

输入模式

-输入浮空（GPIO_Mode_IN_FLOATING）

-输入上拉(GPIO_Mode_IPU)

-输入下拉(GPIO_Mode_IPD)

-模拟输入(GPIO_Mode_AIN)

输出模式

-开漏输出(GPIO_Mode_Out_OD)

-开漏复用功能(GPIO_Mode_AF_OD)

-推挽式输出(GPIO_Mode_Out_PP)

-推挽式复用功能(GPIO_Mode_AF_PP)

## 点灯

代码逻辑是这样

```c++
#include "stm32f10x.h"                  // Device header
#include "Delay.h"

int main(void)
{
    // RCC初始化 GPIOA时钟
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	// 结构体定义 GPIO
	GPIO_InitTypeDef GPIO_InitStructure;
    // 这里使用的是推挽模式
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    // 这里需要输入GPIO结构体地址 &取地址符
	GPIO_Init(GPIOA, &GPIO_InitStructure);
	
	while (1)
	{
        // 这里reset 是端口置0
		GPIO_ResetBits(GPIOA, GPIO_Pin_0);
		Delay_ms(500);
        // 置1
		GPIO_SetBits(GPIOA, GPIO_Pin_0);
		Delay_ms(500);
		
		GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_RESET);
		Delay_ms(500);
		GPIO_WriteBit(GPIOA, GPIO_Pin_0, Bit_SET);
		Delay_ms(500);
		
	}
}
```

## 点流水灯

```c++
#include "stm32f10x.h"                  // Device header
#include "Delay.h"

int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);
	// 定义
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_All;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOA, &GPIO_InitStructure);
	
	while (1)
	{
        // 写的功能是写1
        // ~ 取反
		GPIO_Write(GPIOA, ~0x0001);	//0000 0000 0000 0001
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0002);	//0000 0000 0000 0010
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0004);	//0000 0000 0000 0100
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0008);	//0000 0000 0000 1000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0010);	//0000 0000 0001 0000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0020);	//0000 0000 0010 0000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0040);	//0000 0000 0100 0000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0080);	//0000 0000 1000 0000
		Delay_ms(100);
	}
}
```

## 蜂鸣器

```c++
#include "stm32f10x.h"                  // Device header
#include "Delay.h"

int main(void)
{
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);
	
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_12;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOB, &GPIO_InitStructure);
	
	while (1)
	{
        // 较为简单自己看看就能懂
		GPIO_ResetBits(GPIOB, GPIO_Pin_12);
		Delay_ms(100);
		GPIO_SetBits(GPIOB, GPIO_Pin_12);
		Delay_ms(100);
		GPIO_ResetBits(GPIOB, GPIO_Pin_12);
		Delay_ms(100);
		GPIO_SetBits(GPIOB, GPIO_Pin_12);
		Delay_ms(700);
	}
}

```

