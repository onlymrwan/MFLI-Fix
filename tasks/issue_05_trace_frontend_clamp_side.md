# Issue 05: Trace Frontend and Clamp Side After R61/R62 Isolation

## Goal

在 `R61_candidate` / `R62_candidate` 保持隔离的状态下，沿二极管/钳位/前级侧的异常电压节点继续追踪，找出 2.54 V 和 -0.72 V 的来源。

## Current Evidence

Issue03 隔离后结果记录在：

```text
../measurements/2026-06-22_issue_03_post_isolation_results.csv
```

Key observations:

- `R61_candidate` and `R62_candidate` are confirmed disconnected from the AD9269 side.
- AD9269-side nodes after isolation: 0 V and 0 V.
- Diode/clamp/frontend-side nodes after isolation: 2.54 V and -0.72 V.
- Instrument OVI after isolation: not overrange.
- ORB/ORA static outputs are near low level; unit reporting should be verified because prior records used volts and the latest user report used millivolts.

Additional clamp-node measurements after disconnecting the reported 0 ohm resistor are recorded in:

```text
../measurements/2026-06-22_issue_05_clamp_node_voltages.csv
```

User-reported clamp-device three-pin voltages:

- High-side abnormal branch: 2.54 V, 0 V, 1.82 V.
- Low-side abnormal branch: -0.72 V, 0 V, 1.82 V.

The two clamp signal nodes were traced through about 50 ohm resistors to ADA4932-1:

```text
../measurements/2026-06-22_issue_05_ada4932_node_voltages.csv
```

User-reported ADA4932-1 measurements:

- Pin10 +OUT = +4 V.
- Pin11 -OUT = -3 V.
- Pin2 +IN = 0.647 V.
- Pin3 -IN = 0.259 V.
- Pins5-8 +VS = +5.3 V.
- Pins13-16 -VS = -5.0 V.
- Pin1 -FB = -3 V.
- Pin4 +FB = +4 V.
- Pin9 VOCM = 0.908 V.
- Pin12 PD = 5.398 V.
- Both feedback resistor paths = 1 kOhm. Prior 2.217 kOhm note is superseded by the user's corrected measurement.

Next ADA4932-1 measurements are planned in:

```text
../measurements/2026-06-22_issue_05_ada4932_next_measurement_plan.csv
```

Same-model ADA4932-1 comparison measurements are recorded in:

```text
../measurements/2026-06-22_issue_05_same_model_ada4932_reference.csv
```

Cascade topology notes are recorded in:

```text
../measurements/2026-06-22_issue_05_ada4932_cascade_topology.csv
```

B8A / AD8512 upstream measurements are recorded in:

```text
../measurements/2026-06-22_issue_05_b8a_ad8512_upstream_voltages.csv
```

B8A to `ADA4932_1` input-path voltage-gradient measurements are recorded in:

```text
../measurements/2026-06-23_issue_05_b8a_to_ada4932_1_path_voltages.csv
```

User-reported same-model comparison chip measurements:

- Pin1 -FB = +4.22 V.
- Pin2 +IN = +1.31 V.
- Pin3 -IN = -0.45 V.
- Pin4 +FB = -3.41 V.
- Pins5-8 +VS = +5.4 V.
- Pin9 VOCM = 0 V.
- Pin10 +OUT = -3.41 V.
- Pin11 -OUT = +4.22 V.
- Pin12 PD = +5.398 V.
- Pins13-16 -VS = -4.6 V.
- Feedback paths Pin4-Pin11 and Pin1-Pin10 = 1 kOhm. Prior 640 ohm note is superseded by the user's corrected measurement.

Updated user trace:

- The previously measured comparison chip is `ADA4932_1`.
- The currently suspicious downstream driver is `ADA4932_2`.
- `ADA4932_1` is upstream of `ADA4932_2`.
- The two stages are connected through resistors and relays.
- `ADA4932_2` outputs connect toward the AD9269 H/L clamp diode area marked by the red arrows in `images/raw/拆解图片/AD-DA/AD前级.jpg`.
- Relay contacts between `ADA4932_1` and `ADA4932_2` are confirmed normally conductive in the current traced path.
- Interstage path voltages are recorded in `../measurements/2026-06-22_issue_05_ada4932_interstage_path_voltages.csv`.
- `ADA4932_1` was rechecked and remained unchanged.
- The upstream 8-pin ADI chip marked `B8A` is identified from `docs/datasheets/B8A-ADI-AD8512.pdf` as AD8512, not the `H2P` / ADA4896-2 device.
- B8A / AD8512 pin voltages are: Pin1 OUT A = 1.26 V, Pin2 -IN A = 1.26 V, Pin3 +IN A = 1.26 V, Pin4 V- = -5.53 V, Pin5 +IN B = 1.26 V, Pin6 -IN B = 1.26 V, Pin7 OUT B = 1.26 V, Pin8 V+ = 5.40 V.
- B8A Pin1 path: 1.264 V -> 50 ohm -> 1.265 V -> 1.470 kohm -> 1.33 V -> `ADA4932_1` Pin2.
- B8A Pin7 path: 1.2575 V -> 50 ohm -> 1.215 V -> 1.457 kohm -> -0.45 V -> `ADA4932_1` Pin3.

## Interpretation

- The prior abnormal 2.54 V / -0.72 V pair remains on the frontend/clamp side after the ADC input path is isolated.
- The AD9269 side no longer recreates the prior rail-out abnormal voltage in the isolated state.
- The current primary suspect is therefore the frontend / clamp / switching network upstream of `R61_candidate` and `R62_candidate`.
- This does not prove AD9269 is fully healthy; it means AD9269 replacement is not the next immediate action.
- The clamp voltages are consistent with clamp conduction, not with a clamp that has no effect: 2.54 V is approximately 1.82 V + one diode drop, and -0.72 V is approximately 0 V - one diode drop.
- The clamp devices are therefore likely being driven by an upstream source with enough current or low enough impedance to hold the signal nodes at the clamp limits. The next task is to find that upstream source.
- The upstream source has now been traced to ADA4932-1 outputs through about 50 ohm resistors.
- The clamp node voltages are consistent with ADA4932-1 outputs driving into the clamp network: +4 V through about 50 ohm is clamped to about 2.54 V, and -3 V through about 50 ohm is clamped to about -0.72 V.
- ADA4932-1 supplies are present, so the next question is whether the driver is saturated because of input/feedback/VOCM/PD conditions or because the device output stage is damaged.
- Pin9 VOCM = 0.908 V is consistent with the AD9269 common-mode domain, so the +4 V / -3 V output pair is not explained by an obviously wrong output common-mode command.
- Pin12 PD = 5.398 V is consistent with enabled state for +VS around 5.3 V. The ADA4932-1 datasheet lists enabled as PD >= +VS - 2.2 V and powered down as PD <= +VS - 2.8 V.
- Both feedback resistor paths are corrected to 1 kOhm, matching the upstream stage feedback values and arguing against a simple single-side open feedback resistor.
- Pin1 -FB = -3 V and Pin4 +FB = +4 V are still rail-side abnormal feedback-node voltages. With VOCM, PD, supplies, and gross feedback continuity now less suspicious, the next split is between an input/feedback network fault that is forcing the loop and damage inside the ADA4932-1.
- `ADA4932_1` is not a clean known-good reference for `ADA4932_2`; it is now understood as an upstream stage in the same signal chain.
- Both stages have rail-side outputs with opposite polarity. This makes single-chip replacement premature: `ADA4932_2` may be saturated because `ADA4932_1` or the relay/resistor interstage path is forcing its input/feedback network.
- The problem is now a cascaded-stage isolation problem: determine whether the abnormal condition originates upstream of `ADA4932_2`, in the relay/resistor interstage network, or inside/local to `ADA4932_2`.
- The interstage voltage drops show approximately 3 mA to 3.7 mA current magnitude in both paths. This is active low-impedance drive from `ADA4932_1` into `ADA4932_2`, not a floating or weakly coupled node.
- Because the relay path is confirmed conductive, the immediate priority shifts upstream: determine why `ADA4932_1` is rail-side, or whether this relay path should be selected in the current input/range state.
- B8A / AD8512 does not appear rail-side at DC: both outputs and local inputs are about 1.26 V. Therefore the first confirmed rail-side active stage remains `ADA4932_1`, unless an intermediate relay/resistor/feedback path between B8A and `ADA4932_1` is forcing it.
- The B8A Pin1 to `ADA4932_1` Pin2 path has near-zero current magnitude and no strong forcing behavior.
- The B8A Pin7 to `ADA4932_1` Pin3 path has about 0.85 mA across the 50 ohm section and about 1.14 mA across the 1.457 kohm section. This is far above normal ADA4932-1 input bias-current scale and indicates that the Pin3 side is being pulled low by `ADA4932_1` local input/feedback network or internal damage.
- The fault focus now moves from B8A or relay selection to the `ADA4932_1` Pin3 half: Pin3 local network, feedback around Pin1/Pin10/Pin4/Pin11, nearby capacitors/clamps, or the `ADA4932_1` die.

## Keep State

- Keep `R61_candidate` and `R62_candidate` isolated for this issue.
- Do not reconnect the two 1 ohm candidates before the frontend-side source is traced.
- Do not inject voltage or signal into AD9269 Pin61/Pin62 or the lifted ADC-side pads.

## Powered-Off Checks

Perform only while the instrument is powered off:

1. Mark the signal pin of each clamp device: the pin that measured 2.54 V or -0.72 V.
2. Trace each clamp signal pin away from the clamp device to the next series part, via, relay contact, analog switch, or active-device output.
3. Measure each clamp signal pin to GND, 1.82 V rail, VCM, and nearby clamp rails for low resistance or diode-like paths.
4. Compare the high-side abnormal branch and low-side abnormal branch for symmetry: same source type, same series parts, and same upstream component family.
5. Compare with the corresponding A-channel frontend network if a visually symmetric node exists.
6. Record all measurements in a new CSV before changing or removing any part.

## Short Powered Checks

Only after powered-off checks show no direct short that would make power-on unsafe:

1. Keep Signal Input +V/-V with no external signal connected.
2. Keep `R61_candidate` / `R62_candidate` isolated.
3. Briefly power on and re-measure the two clamp signal pins: 2.54 V branch and -0.72 V branch.
4. Probe one upstream node at a time, starting from the closest series element or via connected to the clamp signal pin and moving away from AD9269.
5. Record LabOne/front-panel OVI, ORB, and ORA at the same time.
6. Power off immediately after the minimum measurements are recorded.

Recommended tracing order:

1. Keep `R61_candidate` and `R62_candidate` isolated from AD9269.
2. Confirm whether the conductive relay path should be selected in the current input/range state. Record LabOne range, input mode, coupling, and impedance settings with the measurement.
3. Keep B8A in place; it is not the current first replacement candidate.
4. Powered off, compare `ADA4932_1` Pin2 path and Pin3 path to GND, +5.4 V, -4.6 V/-5 V, VOCM, Pin1/Pin4 feedback nodes, and Pin10/Pin11 outputs.
5. Powered off, compare nearby capacitors, clamps, and relay contacts attached to `ADA4932_1` Pin3 against the Pin2 side.
6. Short powered check, if safe: repeat `ADA4932_1` Pin1/2/3/4/9/10/11/12 while also recording the B8A Pin1/Pin7 path nodes. Confirm Pin3 remains the only strongly pulled input side.
7. If an accessible series resistor exists between B8A Pin7 and `ADA4932_1` Pin3, consider lifting only that small resistor after documenting both pads. This is the cleanest low-risk split between external Pin3 loading and `ADA4932_1` internal/local failure.
8. If Pin3 normalizes after lifting the input resistor, the fault is upstream/external to `ADA4932_1` Pin3. If the `ADA4932_1` Pin3-side pad or outputs remain abnormal, `ADA4932_1` local network or die damage becomes the leading suspect.
9. Do not reconnect R61/R62 to AD9269 until the ADA4932_2 output abnormality is resolved.

## Decision Table

| Observation | Interpretation |
|---|---|
| 2.54 V / -0.72 V appears at an upstream active-device output | That active device or its feedback/control network becomes the next suspect |
| 2.54 V / -0.72 V appears only after a clamp/diode network | Clamp or rail-leakage path becomes the next suspect |
| The two nodes are tied through a relay or analog switch path | Switching/control state or damaged switch contact must be checked |
| A-channel symmetric nodes are normal while B side remains abnormal | Fault remains localized to B-side frontend path |
| Frontend-side nodes normalize after isolating a clamp/relay branch | That branch becomes the likely fault source |
| ADA4932-1 Pin10/Pin11 remain at +4 V / -3 V with supplies, VOCM, PD, and feedback network normal | ADA4932-1 output-stage damage becomes a leading suspect |
| ADA4932-1 Pin10/Pin11 abnormal because VOCM, PD, or feedback/input nodes are abnormal | Repair the control/feedback/input condition before replacing ADA4932-1 |
| VOCM and PD are normal, feedback resistors are symmetric, but Pin1/Pin4/Pin10/Pin11 remain rail-side | Next check input/feedback network loading and compare a symmetric driver; if normal, ADA4932-1 damage becomes high probability |
| Same-model comparison chip has different VOCM and feedback values | Treat it as a different configuration until physical circuit role is confirmed |
| ADA4932_1 rail-side outputs are connected through the relay/resistor path into ADA4932_2 | ADA4932_2 may be responding to upstream drive; trace ADA4932_1 and relay selection before replacing ADA4932_2 |
| ADA4932_2 remains rail-side after its interstage input path is isolated | ADA4932_2 local failure or local feedback/input loading becomes the leading suspect |
| Relay contact is closed when the selected mode should make it open | Relay control, relay contact damage, or mode-selection logic becomes a suspect |
| Relay path is conductive and both interstage paths carry about 3 mA to 3.7 mA | Treat ADA4932_1 as the first active rail-side driver; trace ADA4932_1 upstream before replacing ADA4932_2 |
| B8A outputs and inputs are all about 1.26 V while ADA4932_1 remains rail-side | B8A is not the current first suspect; check B8A-to-ADA4932_1 network or ADA4932_1 local fault |
| B8A Pin7 path drops from 1.215 V to ADA4932_1 Pin3 = -0.45 V through about 1.457 kohm | ADA4932_1 Pin3 half is strongly pulling current; inspect Pin3 local network or ADA4932_1 itself |
| B8A Pin1 path reaches ADA4932_1 Pin2 without significant drop | Pin2 half is comparatively normal and should be used as the local symmetry reference |

## Safety

- Do not use resistance, continuity, or diode mode while powered.
- Do not short the frontend-side abnormal nodes to GND, 1.82 V, VCM, or AD9269 pins.
- Do not reconnect R61/R62 or replace AD9269 before the frontend-side source is traced.
- Keep powered measurements short.
