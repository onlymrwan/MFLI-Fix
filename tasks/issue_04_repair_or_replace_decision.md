# Issue 04: Repair or Replace Decision

## Goal

在完成隔离实验后，决定优先更换前级/钳位器件，还是更换 AD9269BCPZ-65。

## Replace AD9269 Only If

- B 通道隔离后 ADC 侧仍异常。
- VREF、VCM、AVDD、DRVDD、CLK 基本正常。
- ORB 仍异常。
- 前级不再提供异常电压或已被排除。

## Prefer Frontend Repair If

- 隔离后前级侧仍维持异常电压。
- ADC 侧恢复到合理共模附近。
- ORB/OVI 随隔离或前级断开而消失。

## Notes

AD9269 是 64-lead LFCSP 且有裸露焊盘，手工更换风险高。若决定更换，应先准备热风/预热台、助焊剂、显微检查条件，并记录拆焊前照片。
