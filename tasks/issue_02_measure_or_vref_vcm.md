# Issue 02: Check AD9269 Rails, Reference, and Common Mode

## Goal

确认 AD9269 的 AVDD、DRVDD、VREF、VCM、SENSE 和 RBIAS 是否正常，避免把全局供电/参考异常误判为 B 通道局部损坏。

## Measurements

| Item | Pin | Expected Range | Record |
|---|---:|---|---|
| AVDD | 49/50/53/54/59/60/63/64 | 1.7-1.9 V | TODO |
| DRVDD | 10/19/28/37 | 1.7-3.6 V | TODO |
| VREF | 55 | about 0.99 V in internal 1 V mode | TODO |
| SENSE | 56 | stable configuration level | TODO |
| VCM | 57 | about 0.9 V | TODO |
| RBIAS | 58 | about 10 kOhm to GND when powered off | TODO |

Detailed first-round procedure: `../docs/measurement_plan_round_01.md`, Steps 2-4.

## Completion Criteria

- 所有测点都有数值、单位和上电/断电状态。
- 明确是否支持“故障局限于 B 通道输入链路”的判断。

## Safety

RBIAS 阻值只在断电后测量；通电时不要使用电阻档。
