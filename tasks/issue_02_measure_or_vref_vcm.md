# Issue 02: Check AD9269 Rails, Reference, and Common Mode

## Goal

确认 AD9269 的 AVDD、DRVDD、VREF、VCM、SENSE 和 RBIAS 是否正常，避免把全局供电/参考异常误判为 B 通道局部损坏。

## Measurements

| Item | Pin | Expected Range | Record |
|---|---:|---|---|
| AVDD | 49/50/53/54/59/60/63/64 | 1.7-1.9 V | 1.817 V |
| DRVDD | 10/19/28/37 | 1.7-3.6 V | 1.815 V |
| VREF | 55 | about 0.99 V in internal 1 V mode | 1.01 V |
| SENSE | 56 | stable configuration level | 1.818 V |
| VCM | 57 | about 0.9 V | 0.908 V |
| RBIAS | 58 | about 10 kOhm to GND when powered off | 5.04 kOhm; `NEEDS-VERIFICATION` |

Detailed first-round procedure: `../docs/measurement_plan_round_01.md`, Steps 2-4.

## Interpretation

- VREF, VCM, AVDD, and DRVDD are close to expected values, so AD9269 global supply/reference is not the leading fault candidate at this stage.
- RBIAS is lower than the planned about 10 kOhm expectation. It must be rechecked before final ADC replacement decisions, but it does not by itself explain the B-channel-only Pin61/62 rail-out differential fault.

## Completion Criteria

- 所有测点都有数值、单位和上电/断电状态。
- 明确是否支持“故障局限于 B 通道输入链路”的判断。

## Safety

RBIAS 阻值只在断电后测量；通电时不要使用电阻档。
