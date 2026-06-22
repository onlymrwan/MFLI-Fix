# Issue 03: Isolate B Channel Series Input Resistors

## Goal

通过隔离 AD9269 Pin61/Pin62 前的两颗串联输入电阻，区分异常来自前级驱动还是 ADC B 通道输入级。

## Current Evidence Before Isolation

First-round measurements in `../measurements/2026-06-21_round_01_measurement_plan.csv` support moving this issue to main-dialog decision:

- VREF = 1.01 V, VCM = 0.908 V, AVDD = 1.817 V, DRVDD = 1.815 V.
- A/B clamp diode comparisons are close in both forward and reverse directions.
- No obvious B-channel-only external clamp short was observed.
- RBIAS = 5.04 kOhm is lower than the planned about 10 kOhm expectation and remains `NEEDS-VERIFICATION`.
- ORB/ORA static voltages are both near 0 V while instrument OVI is still recorded; do not claim ORB high-level confirmation of OVI.

## Preconditions

- 已拍照标注两颗串联输入电阻的位置。
- 已确认不是 61/62 之间的差分电阻，也不是差分电容。
- 已完成 ORA/ORB 和 VREF/VCM/AVDD/DRVDD 基础检查。

## Entry Checklist

Before lifting any resistor, confirm all items below:

- The two series resistors for Pin61 and Pin62 are marked in a photo.
- The marked parts are not the differential resistor between Pin61/Pin62 and not the differential capacitor.
- Each resistor has a clearly identified ADC side and frontend side.
- The instrument is powered off before soldering or resistance/diode measurements.
- No external signal is connected to Signal Input +V/-V.
- ESD precautions are in place.
- After isolation, power-on measurements are short and limited to frontend side, ADC side, Pin61/Pin62, LabOne OVI, ORB, and ORA.
- No external voltage is injected into AD9269 input nodes during this issue.

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
| 前级侧和 ADC 侧都恢复正常，但 ORB/OVI 仍在 | 需要补查 OR 输出解释、时钟、数字判定或软件检测路径，不能只按输入直流电压下结论 |

## Safety

不要在隔离后注入大信号；不要直接短接 ADC 输入到电源轨或地。
