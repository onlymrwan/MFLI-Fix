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

## Current Gate After Issue03

Issue03 post-isolation test on 2026-06-22 currently supports frontend-side tracing before any AD9269 replacement:

- AD9269-side nodes no longer show the prior 2.54 V / -0.72 V abnormal pair after R61/R62 isolation.
- Instrument OVI is no longer reported in the isolated state.
- The abnormal pair remains on the diode/clamp/frontend side.
- Therefore AD9269 replacement is not the next immediate action; complete `issue_05_trace_frontend_clamp_side.md` first.

## Current Gate After Issue05 ADA4932-1 Measurements

Issue05 measurements on 2026-06-22 further localize the frontend-side abnormality to the ADA4932-1 driver stage or its immediate input/feedback network:

- ADA4932-1 Pin10/Pin11 remain at +4 V / -3 V and drive the clamp network through about 50 ohm resistors.
- Pin9 VOCM = 0.908 V, consistent with the ADC common-mode domain.
- Pin12 PD = 5.398 V, consistent with enabled state for +VS around 5.3 V.
- Feedback resistor values were corrected later to 1 kOhm for all four ADA4932 feedback resistors, so a simple single-side open feedback resistor is not currently supported.
- The next repair decision should not be AD9269 replacement. It should be whether the ADA4932-1 external input/feedback network is forcing saturation, or whether ADA4932-1 itself is damaged.

Same-model ADA4932-1 comparison measurement:

- Another ADA4932-1 has Pin9 VOCM = 0 V and outputs Pin10/Pin11 = -3.41 V / +4.22 V; its feedback paths were later corrected to 1 kOhm.
- The comparison chip has now been identified as `ADA4932_1`, an upstream stage of the suspicious downstream `ADA4932_2`.
- The two stages are connected through resistors and relays; visual reference is `images/raw/拆解图片/AD-DA/AD前级.jpg`.
- Before replacing either ADA4932 package, isolate the interstage path non-destructively: map relay contacts, measure both sides of each interstage resistor, and check whether the upstream rail-side output is actually driving the downstream stage.
- If `ADA4932_2` remains at +4 V / -3 V after the interstage path is proven open or safely isolated, `ADA4932_2` replacement becomes much better supported.
- If `ADA4932_2` normalizes when the upstream path is open, trace `ADA4932_1`, relay control, or relay contact damage first.

Updated after interstage voltage-gradient measurement:

- The relay path between `ADA4932_1` and `ADA4932_2` is reported normally conductive.
- `ADA4932_1 Pin10/Pin11` drive `ADA4932_2 Pin3/Pin2` through `100 ohm -> closed relay -> 100 ohm -> 817 ohm` paths.
- Both paths show about 3 mA to 3.7 mA estimated current magnitude, so `ADA4932_2` is being actively driven by the upstream stage rather than seeing a weak or floating node.
- The corrected feedback resistor value is 1 kOhm for all four feedback resistors across the two ADA4932 stages.
- Current repair gate: do not replace `ADA4932_2` first. Trace `ADA4932_1` upstream and confirm whether `ADA4932_1` is itself being forced, mis-selected by relay state, or locally damaged.

Updated after B8A / AD8512 upstream measurement:

- The upstream 8-pin ADI chip marked `B8A` is identified as AD8512 from `docs/datasheets/B8A-ADI-AD8512.pdf`.
- B8A / AD8512 measured Pin1/2/3/5/6/7 are all about 1.26 V, with supplies Pin4 = -5.53 V and Pin8 = +5.40 V.
- This does not support B8A as the current first rail-side fault source.
- Current repair gate: do not replace B8A first. Measure the B8A-to-`ADA4932_1` resistor/relay/feedback network and then decide whether the fault is an intermediate path fault or `ADA4932_1` local failure.

Updated after B8A-to-ADA4932_1 path voltage-gradient measurement:

- B8A Pin1 path to `ADA4932_1 Pin2` is near balanced: 1.264 V -> 1.265 V -> 1.33 V.
- B8A Pin7 path to `ADA4932_1 Pin3` is strongly asymmetric: 1.2575 V -> 1.215 V -> -0.45 V.
- The Pin3-side 1.457 kohm section implies about 1.14 mA current magnitude, far above normal ADA4932-1 input bias-current scale.
- Current repair gate: focus on `ADA4932_1 Pin3` side. Check external Pin3 loading/feedback/clamp/relay parts first; if isolating the Pin3 series resistor does not normalize the node or outputs, `ADA4932_1` local or die damage becomes the leading replacement candidate.

## Notes

AD9269 是 64-lead LFCSP 且有裸露焊盘，手工更换风险高。若决定更换，应先准备热风/预热台、助焊剂、显微检查条件，并记录拆焊前照片。
