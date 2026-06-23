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

## Visual Evidence Added on 2026-06-22

New user-provided evidence:

- User sketch: `../images/raw/拆解图片/电路绘图/AD9269通道B外围电路示意图.jpg`.
- Board photo: `../images/raw/拆解图片/AD-DA/主AD.jpg`.
- User orientation note: the upper-left corner of the AD9269 in the board photo is Pin64.
- Annotated candidate photo: `../images/raw/拆解图片/电路绘图/AD9269_Pin61_Pin62_candidates_annotated.png`.
- Annotated side-orientation photo: `../images/raw/拆解图片/电路绘图/AD9269_Pin61_Pin62_candidates_sides_annotated.png`.

AD9269 datasheet cross-check:

- Pin61 = VIN-B.
- Pin62 = VIN+B.
- Pin63/64 = AVDD.
- If the Pin64 orientation note is correct, Pin62 and Pin61 are the third and fourth pins along the same package edge from Pin64.

Current visual status:

- The photo shows small 0402 passives near the expected Pin61/Pin62 area, consistent with the user sketch containing two series resistors, a differential capacitor, and rail clamp paths.
- These parts are currently only **candidate** Pin61/Pin62 series input resistors.
- User annotation labels the upper boxed part as `R61_candidate` and the lower boxed part as `R62_candidate`.
- User annotation identifies the arrow side of both candidates as the AD9269 side; the opposite side is the diode/clamp-network side.
- Candidate locations and side orientation are now marked, but the parts have not yet been lifted or electrically isolated.
- Do not lift or remove any candidate part until the ADC side, frontend side, and part function have been confirmed.

## Powered-Off Resistance Evidence Added on 2026-06-22

Recorded in `../measurements/2026-06-22_issue_03_powered_off_continuity.csv`:

- The resistor/network drawn between Pin61 and Pin62 measured about 690 ohm in-circuit while powered off.
- The two paths from Pin61/Pin62 to the diode or clamp-network side both measured at about the 1 ohm level while powered off.

Interpretation:

- The about 690 ohm element is a cross-differential part and must not be used as the Pin61/Pin62 series isolation point.
- The two about 1 ohm paths further support the nearby two low-ohm parts as candidate series or jumper-like isolation parts for Pin61 and Pin62.
- Because these are in-circuit resistance measurements, treat the values as approximate until a part is lifted or a full net trace is confirmed.
- Before lifting either 1 ohm candidate, mark which pad is connected to the ADC pin and which pad continues toward the frontend/diode/clamp network.
- The side-orientation annotation marks the arrow side as the AD9269-side pad and the opposite side as the diode/clamp-network side for both candidates.

## Side Orientation and Isolation Decision Note

Based on the latest user annotation:

- `R61_candidate`: arrow-side pad is the AD9269 side; the opposite pad is the diode/clamp-network side.
- `R62_candidate`: arrow-side pad is the AD9269 side; the opposite pad is the diode/clamp-network side.
- The 690 ohm cross-differential network between Pin61 and Pin62 remains excluded from this isolation issue.

Main-dialog decision status:

- Candidate location and side orientation are now sufficient to enter the isolation decision.
- If isolation is approved, prefer lifting the AD9269-side pad of `R61_candidate` and `R62_candidate` so the frontend/diode/clamp network remains intact for measurement.
- Perform lifting only while powered off. After lifting, inspect for solder bridges or lifted pads before any powered measurement.
- This step is still a localization experiment; do not treat any result as an ADC replacement decision until post-isolation voltages and OVI/ORB/ORA status are recorded.

## Preconditions

- 已拍照标注两颗串联输入电阻的位置。
- 已确认不是 61/62 之间的差分电阻，也不是差分电容。
- 已完成 ORA/ORB 和 VREF/VCM/AVDD/DRVDD 基础检查。

## Entry Checklist

Before lifting any resistor, confirm all items below:

- The two series resistors for Pin61 and Pin62 are marked in a photo.
- The marked parts are not the differential resistor between Pin61/Pin62 and not the differential capacitor.
- Each resistor has a clearly identified ADC side and frontend side; user annotation marks the arrow side as AD9269 side and the opposite side as diode/clamp-network side.
- Powered-off continuity checks and resistance measurements support Pin61 and Pin62 connection to the two marked about 1 ohm candidate parts.
- Powered-off checks support that the 690 ohm cross-differential network is not one of the two isolation parts.
- A marked photo or sketch records the two confirmed parts, their ADC-side pads, and their frontend/diode/clamp-side pads.
- The instrument is powered off before soldering or resistance/diode measurements.
- No external signal is connected to Signal Input +V/-V.
- ESD precautions are in place.
- After isolation, power-on measurements are short and limited to frontend side, ADC side, Pin61/Pin62, LabOne OVI, ORB, and ORA.
- No external voltage is injected into AD9269 input nodes during this issue.

## Low-Risk Confirmation Before Soldering

Perform these checks only while the instrument is powered off:

1. Use continuity or low-ohm mode to map Pin62 to the nearest candidate part end.
2. Use continuity or low-ohm mode to map Pin61 to the nearest candidate part end.
3. Record which pad is the ADC side for each candidate part.
4. Check whether any candidate part is directly between Pin61 and Pin62; if yes, it is a differential part and must not be used as the isolation point.
5. Check the differential capacitor shown in the sketch and keep it separate from the two series resistor candidates.
6. Take or annotate one close-up photo showing Pin64 orientation, Pin62/Pin61 pins, the two candidate parts, ADC-side pads, and frontend-side pads.

## Procedure

1. 断电。
2. 将 `R61_candidate` 的 AD9269 侧焊盘（箭头侧）翘起一端。
3. 将 `R62_candidate` 的 AD9269 侧焊盘（箭头侧）翘起一端。
4. 短时间上电，分别测二极管/前级侧、ADC 侧、Pin61/Pin62、LabOne OVI、ORB/ORA 状态。
5. 断电并记录照片、测点、结果。


## Post-Isolation Test Plan

Measurement template: `../measurements/2026-06-22_issue_03_post_isolation_test_plan.csv`.

Assumption to verify first: both candidates were lifted on the AD9269 side, as planned. If a different side was lifted, record the actual lifted side before interpreting voltages.

Before any powered measurement:

1. Keep the instrument powered off and disconnected from external input.
2. Inspect `R61_candidate` and `R62_candidate` under magnification for solder bridges, cracked/lifted pads, or accidental contact with nearby parts.
3. Measure resistance between each lifted AD9269-side pad and its diode/clamp-side pad. Stop and do not power on if either path still reads about 1 ohm or otherwise appears not isolated.
4. Record a photo of the lifted parts if possible.

Short powered test, only after the powered-off checks pass:

1. Power on briefly with no external input connected.
2. Measure the AD9269 side of `R61_candidate` / Pin61 to GND.
3. Measure the AD9269 side of `R62_candidate` / Pin62 to GND.
4. Measure the diode/clamp/frontend side of `R61_candidate` to GND.
5. Measure the diode/clamp/frontend side of `R62_candidate` to GND.
6. Record LabOne/front-panel OVI status at the same time.
7. Measure ORB Pin22 and ORA Pin43 to GND if safe to probe.
8. Optionally recheck VCM and AVDD if any reading is unexpected.
9. Power off immediately after the minimum measurements are recorded.

Do not use resistance, continuity, or diode mode while powered. Do not inject any voltage or signal into the lifted AD9269 input nodes.

Post-isolation interpretation:

| Observation | Interpretation |
|---|---|
| Diode/clamp/frontend side remains near -0.72 V / 2.54 V, while AD9269 side returns near safe/common-mode range | Frontend or clamp network remains the likely source of the abnormal drive. |
| AD9269 side remains rail-out or recreates -0.72 V / 2.54 V after isolation | AD9269 B input structure is more suspicious. |
| Both sides return near safe/common-mode range and OVI disappears | Isolation removed the abnormal coupling; compare frontend path and ADC loading before deciding replacement. |
| Both sides return near safe/common-mode range but OVI remains | Recheck ORB/ORA interpretation, clock/digital path, or software/FPGA OVI path. |
| Powered-off isolation check fails | Do not power on; rework or inspect the lifted parts first. |

## Post-Isolation Results Added on 2026-06-22

Recorded in `../measurements/2026-06-22_issue_03_post_isolation_results.csv`.

User-reported results after lifting the two candidate resistors:

- Powered-off isolation: `R61_candidate` and `R62_candidate` are fully disconnected from the AD9269 side.
- Powered-on short test, in the planned measurement order:
  - AD9269 side, R61/Pin61 path: 0 V.
  - AD9269 side, R62/Pin62 path: 0 V.
  - Diode/clamp/frontend side, one path: 2.54 V.
  - Diode/clamp/frontend side, the other path: -0.72 V.
  - Instrument OVI: not overrange.
  - ORB Pin22: 0.0022 mV as reported by user.
  - ORA Pin43: 0.002 mV as reported by user.

Interpretation:

- The prior abnormal pair, about 2.54 V and -0.72 V, remains on the diode/clamp/frontend side after isolation.
- The AD9269 side no longer shows the prior rail-out abnormal voltage, and the instrument no longer reports OVI.
- This strongly shifts the current primary fault suspicion toward the frontend/diode/clamp network side rather than an AD9269 B input node that independently recreates the fault after isolation.
- This does not yet prove that the AD9269 is fully healthy; it means ADC replacement is not the next immediate action before tracing the frontend-side source.
- ORB/ORA are recorded near low/static level after isolation. Keep interpreting ORB/ORA together with LabOne/front-panel OVI rather than as standalone proof.

Next decision:

- Keep `R61_candidate` and `R62_candidate` isolated for now.
- Do not reconnect or inject any signal into the AD9269 input side.
- Move to frontend-side tracing from the two diode/clamp/frontend pads that remain at 2.54 V and -0.72 V.
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
