# 简易自行瞄准装置（E题）

单片机：STM32F103C8T6（Blue Pill） | 供电：7.4V 2S 锂电池 | 视觉：MaixCAM Pro

## 目录

| 文件 | 说明 |
|------|------|
| `output/01_PCB设计指南.md` | PCB 封装、布局、走线规则、检核清单 |
| `output/02_PCB开画速查表.md` | 画板前速查（引脚汇总、避坑要点） |
| `output/03_PCB分模块设计文档.md` | 完整原理图连接参考（MOD-1~MOD-8） |

## 模块一览

| 模块 | 功能 | 关键器件 |
|------|------|----------|
| MOD-1 | 电源管理 | 7.4V → 3× MP1584 降压 → 三路独立 5V |
| MOD-2 | 主控 | STM32F103C8T6 Blue Pill |
| MOD-3 | 电机驱动 | TB6612FNG 双路直流减速电机 |
| MOD-4 | 云台舵机 | MG90S ×2（水平+俯仰） |
| MOD-5 | 激光模组 | 2N7000 NMOS 电子开关 |
| MOD-6 | 视觉通信 | MaixCAM Pro UART1（3线串口） |
| MOD-7 | 循迹传感器 | TCRT5000 5路数字循迹 |
| MOD-8 | OLED 显示 | SSD1306 0.96" I2C |

## 供电架构

```
7.4V 电池 → SW1(总开关) → F1(PTC 2A) → VBAT_FUSED
  ├── U2 MP1584 → VCC_MCU (主控路) → C8T6、循迹、OLED
  ├── U3 MP1584 → VCC_AIM (瞄准路) → 舵机×2、激光
  └── U4 MP1584 → VCC_MOT (电机路) → TB6612
```
