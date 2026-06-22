# Issue 01: Confirm AD9269 ORA/ORB Overrange Source

## Goal

确认 LabOne / 面板 OVI 是否由 AD9269 B 通道触发。

## Measurements

| Pin | Name | Record |
|---:|---|---|
| 22 | ORB | TODO |
| 43 | ORA | TODO |

Detailed first-round procedure: `../docs/measurement_plan_round_01.md`, Step 1.

## Completion Criteria

- 记录 ORA 和 ORB 的高/低/跳变状态。
- 同时记录仪器量程、输入端状态、是否仍出现 OVI。
- 将结果写入 `measurements/2026-06-21_or_rail_measurements.csv` 或新日期测量文件。

## Safety

不外接输入信号；只短时间上电测必要点。
