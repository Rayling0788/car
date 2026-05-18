# PCB 开画速查表（开画前必读）

---

## 一、板子规格

| 项目 | 参数 |
|------|------|
| 尺寸 | 80mm × 60mm（根据底盘调整） |
| 层数 | 双层（顶层走线，底层全覆铜GND） |
| 安装孔 | 4 × Φ3.2mm，距板边 3mm 四角 |
| 板厚 | 1.6mm（默认） |
| 铜厚 | 1oz（默认） |

---

## 二、全部元件清单（原理图放元件用）

### 插座/接口类

| 位号 | 元件 | 规格 | 数量 |
|------|------|------|------|
| U1 | C8T6 Blue Pill 插座 | 2.54mm 单排母座 20P × 2条 | 1组 |
| U2 | MP1584① 主控路 | 2.54mm 单排母座 4P × 2条（两侧） | 1 |
| U3 | MP1584② 瞄准路 | 同上 | 1 |
| U4 | MP1584③ 电机路 | 同上 | 1 |
| U5 | TB6612 电机驱动 | 买到后量引脚，通常 8P × 2侧 | 1 |
| J1 | 电池输入 | KF301-2P 5.08mm | 1 |
| J2 | 左电机输出 | KF301-2P 5.08mm | 1 |
| J3 | 右电机输出 | KF301-2P 5.08mm | 1 |
| J4 | 舵机①（水平） | 2.54mm 排针 3P（GND/VCC/SIG） | 1 |
| J5 | 舵机②（俯仰） | 2.54mm 排针 3P（GND/VCC/SIG） | 1 |
| J6 | MaixCam 串口 | 2.54mm 排针 3P（GND/RX/TX）| 1 |
| J7 | 5路循迹模块 | 2.54mm 排针 7P（VCC/GND/OUT1~5） | 1 |
| J8 | OLED | 2.54mm 排针 4P（GND/VCC/SCL/SDA） | 1 |
| J9 | 激光模组 | 2.54mm 排针 3P（GND/VCC/SIG） | 1 |
| J10 | SWD 下载口 | 2.54mm 排针 4P（GND/SWCLK/SWDIO/3.3V） | 1 |

### 分立元件类

| 位号 | 元件 | 规格 | 数量 |
|------|------|------|------|
| SW1 | 总电源开关 | SS-12F44 立式拨动开关 3脚 | 1 |
| SW2 | 主控路开关 | 同上 | 1 |
| SW3 | 瞄准路开关 | 同上 | 1 |
| F1 | 自恢复保险丝 | PTC 2A 直插（脚距5mm） | 1 |
| D1 | 绿色LED | 3mm 直插 | 1 |
| D2 | 红色LED | 3mm 直插 | 1 |
| D3 | 红色LED | 3mm 直插 | 1 |
| R1 | 限流电阻 | 1kΩ 1/4W 直插 | 1 |
| R2 | 限流电阻 | 1kΩ 1/4W 直插 | 1 |
| R3 | 限流电阻 | 1kΩ 1/4W 直插 | 1 |
| R4 | 栅极电阻 | 1kΩ 1/4W 直插 | 1 |
| R5 | 下拉电阻 | 10kΩ 1/4W 直插 | 1 |
| C1 | 滤波电容 | 100μF/25V 电解 直插 Φ6.3 | 1 |
| C3~C7 | 去耦电容 | 0.1μF (104) 独石 直插 | 5 |

---

## 三、完整连线表（网表）

### 电源部分

```
J1(BAT+) → SW1(COM脚)
SW1(NO脚) → F1(IN)
F1(OUT) → VBAT节点（接三个MP1584输入）
F1(OUT) → R1 → D1(+) → D1(-) → GND   [绿LED总电源指示]
J1(BAT-) → GND

U2(IN+) ← VBAT
U2(OUT+) → SW2(COM脚)
SW2(NO脚) → VCC_MCU节点
VCC_MCU → R2 → D2(+) → D2(-) → GND  [红LED主控路指示]
VCC_MCU → U1(5V引脚)    C8T6供电
VCC_MCU → J7(VCC)       循迹供电
VCC_MCU → J8(VCC)       OLED供电
VCC_MCU → C3(+) → GND  去耦

U3(IN+) ← VBAT
U3(OUT+) → SW3(COM脚)
SW3(NO脚) → VCC_AIM节点
VCC_AIM → R3 → D3(+) → D3(-) → GND  [红LED瞄准路指示]
VCC_AIM → J4(VCC)       舵机①供电
VCC_AIM → J5(VCC)       舵机②供电

VCC_AIM → J9(VCC)       激光供电
VCC_AIM → C4(+) → GND  去耦

U4(IN+) ← VBAT
U4(OUT+) → VCC_MOT节点（无开关，直通）
VCC_MOT → U5(VM)        TB6612电机功率
VCC_MOT → U5(VCC)       TB6612逻辑
VCC_MOT → C5(+) → GND  去耦

所有 GND 全部汇到同一 GND 网络（底层覆铜）
```

### C8T6 → TB6612 电机驱动

```
U1(PA0)  → U5(PWMA)   左电机PWM（TIM2_CH1）
U1(PA1)  → U5(PWMB)   右电机PWM（TIM2_CH2）
U1(PA4)  → U5(AIN1)   左电机方向
U1(PA5)  → U5(AIN2)   左电机方向
U1(PB8)  → U5(BIN1)   右电机方向
U1(PB9)  → U5(BIN2)   右电机方向
U1(PB10) → U5(STBY)   电机使能（代码默认低电平禁用）
U5(AO1)  → J2(+)      左电机正极
U5(AO2)  → J2(-)      左电机负极
U5(BO1)  → J3(+)      右电机正极
U5(BO2)  → J3(-)      右电机负极
U5(GND)  → GND
```

### C8T6 → 舵机

```
U1(PA6) → J4(SIG)     舵机①信号（水平轴，TIM3_CH1）
U1(PA7) → J5(SIG)     舵机②信号（俯仰轴，TIM3_CH2）
J4: Pin1=GND / Pin2=VCC_AIM / Pin3=SIG
J5: Pin1=GND / Pin2=VCC_AIM / Pin3=SIG
```

### C8T6 → 激光

```
U1(PB0) → J9(SIG)     高电平=激光开
J9: Pin1=GND / Pin2=VCC_AIM / Pin3=SIG
```

### C8T6 ↔ MaixCam（UART）

```
U1(PA9/TX)  → J6(Pin2/RX)   C8T6发 → MaixCam收
U1(PA10/RX) → J6(Pin3/TX)   C8T6收 ← MaixCam发
J6: Pin1=GND / Pin2=RX / Pin3=TX（1×3P）
注意：丝印TX/RX站C8T6视角
```

### C8T6 → 5路循迹

```
U1(PB3)  → J7(OUT1)   最左（⚠️需在代码里释放JTAG）
U1(PB4)  → J7(OUT2)   左（⚠️同上）
U1(PB5)  → J7(OUT3)   中
U1(PB12) → J7(OUT4)   右
U1(PB13) → J7(OUT5)   最右
J7: Pin1=VCC / Pin2=GND / Pin3=OUT1 / Pin4=OUT2 / Pin5=OUT3 / Pin6=OUT4 / Pin7=OUT5
```

### C8T6 → OLED（I2C1）

```
U1(PB6) → J8(SCL)
U1(PB7) → J8(SDA)
J8: Pin1=GND / Pin2=VCC / Pin3=SCL / Pin4=SDA
```

### SWD 下载口

```
U1(PA13) → J10(SWDIO)
U1(PA14) → J10(SWCLK)
J10: Pin1=GND / Pin2=SWCLK / Pin3=SWDIO / Pin4=3.3V（可不接）
```

---

## 四、布局顺序（建议）

```
1. 先放 U1（C8T6插座）在板子中央偏右
2. 三个 MP1584（U2/U3/U4）放左上角，靠近电池输入J1
3. SW1/SW2/SW3 紧贴对应MP1584输出
4. TB6612（U5）放U1正下方
5. 电机端子J2/J3放板下边缘
6. 排针接口（J4~J10）沿板边缘排列，方便插拔
7. LED（D1/D2/D3）放在板边能看见的位置
8. SWD口J10放在板边角，方便插ST-Link
```

---

## 五、走线规则

| 类型 | 线宽 |
|------|------|
| 电池线 7.4V | ≥ 2mm |
| 5V 电源线 | ≥ 1.5mm |
| 信号线（GPIO/UART/I2C） | 0.25mm |
| PWM线（舵机/电机） | 0.3mm |
| GND | 底层全覆铜 |

---

## 六、开画前检查

- [ ] TB6612 模块到手，量好引脚间距和顺序
- [ ] Blue Pill 两排针间距确认（用卡尺量，通常15.24mm）
- [ ] MP1584 模块引脚位置确认（IN+/IN-/OUT+/OUT-）
- [ ] 所有接口丝印方向确认（GND永远Pin1）

---

## 七、画完后DRC必查项

- [ ] 无短路报错
- [ ] 电源线宽达标
- [ ] 底层GND覆铜无孤岛
- [ ] 4个安装孔存在且未被走线遮挡
- [ ] 所有焊盘有丝印标注

---

## 八、下单参数（嘉立创）

| 参数 | 选项 |
|------|------|
| 数量 | 5片 |
| 板厚 | 1.6mm |
| 颜色 | 绿色（最便宜） |
| 铜厚 | 1oz |
| 表面处理 | 有铅喷锡（便宜）或无铅（环保） |
| 预计费用 | 打样费约 0~30元，运费约10元 |
