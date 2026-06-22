# Issue 03: Isolate B Channel Series Input Resistors

## Goal

通过隔离 AD9269 Pin61/Pin62 前的两颗串联输入电阻，区分异常来自前级驱动还是 ADC B 通道输入级。

## Preconditions

- 已拍照标注两颗串联输入电阻的位置。
- 已确认不是 61/62 之间的差分电阻，也不是差分电容。
- 已完成 ORA/ORB 和 VREF/VCM/AVDD/DRVDD 基础检查。

## Procedure

1. 断电。
2. 将 Pin61 对应串联电阻翘起一端。
3. 将 Pin62 对应串联电阻翘起一端。
4. 短时间上电，分别测前级侧、ADC 侧、ORB 状态。
5. 断电并记录照片、测点、结果。

## Decision Table

| Observation | Interpretation |
|---|---|
| 前级侧仍为 -0.72 / 2.54 V | 前级驱动器损坏或饱和 |
| 前级侧恢复正常，ADC 侧异常 | AD9269 B 通道输入级损坏更可疑 |
| ADC 侧自发出现 -0.72 / 2.54 V | AD9269 B 通道内部损坏高度可疑 |
| 隔离后 ORB/OVI 消失 | 前级是异常源 |
| 隔离后 ORB/OVI 仍在 | ADC B 通道或数字判定异常 |

## Safety

不要在隔离后注入大信号；不要直接短接 ADC 输入到电源轨或地。
