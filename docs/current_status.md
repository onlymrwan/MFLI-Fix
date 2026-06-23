# Current Status Snapshot

Last updated: 2026-06-23

## Current Focus

The fault has been traced past the AD9269 B-channel input and into the upstream differential driver chain. With `R61_candidate` and `R62_candidate` isolated, the AD9269 side is 0 V / 0 V and the instrument no longer reports OVI. The abnormal frontend/clamp-side voltages remain and have been traced through:

```text
B8A / AD8512 -> ADA4932_1 -> relay/resistor network -> ADA4932_2 -> AD9269 H/L clamp network
```

The current leading suspect is now localized to:

```text
ADA4932_1 Pin3 input/feedback local network
or ADA4932_1 internal damage
```

## Key Evidence

- `ADA4932_2` directly drives the AD9269 H/L clamp side and produces `Pin10/Pin11 = +4 V / -3 V`.
- `ADA4932_1` is upstream of `ADA4932_2` and produces `Pin10/Pin11 = -3.41 V / +4.23 V`.
- `ADA4932_1 -> ADA4932_2` interstage paths are conductive and carry about 3 mA to 3.7 mA.
- B8A / AD8512 upstream of `ADA4932_1` is balanced at about 1.26 V on both outputs and local inputs.
- B8A Pin1 path to `ADA4932_1 Pin2` shows no strong drop: `1.264 V -> 1.265 V -> 1.33 V`.
- B8A Pin7 path to `ADA4932_1 Pin3` shows a strong drop: `1.2575 V -> 1.215 V -> -0.45 V`.
- The Pin3-side 1.457 kOhm section implies about 1.14 mA, far above ADA4932-1 normal input bias-current scale.
- Corrected feedback context: both ADA4932 stages have 1 kOhm feedback resistors.

## Primary Files

- Full status: `docs/repair_status.md`
- Active task: `tasks/issue_05_trace_frontend_clamp_side.md`
- Repair decision gate: `tasks/issue_04_repair_or_replace_decision.md`
- Latest B8A-to-ADA4932_1 path data: `measurements/2026-06-23_issue_05_b8a_to_ada4932_1_path_voltages.csv`
- B8A / AD8512 voltage data: `measurements/2026-06-22_issue_05_b8a_ad8512_upstream_voltages.csv`
- ADA4932 cascade topology: `measurements/2026-06-22_issue_05_ada4932_cascade_topology.csv`
- ADA4932_1-to-ADA4932_2 interstage data: `measurements/2026-06-22_issue_05_ada4932_interstage_path_voltages.csv`

## Next Step

Keep `R61_candidate` and `R62_candidate` isolated. Do not reconnect the AD9269 input path yet.

Recommended next checks:

1. Powered off, compare `ADA4932_1 Pin3` and `Pin2` to GND, positive supply, negative supply, VOCM, Pin1/Pin4 feedback nodes, and Pin10/Pin11 outputs.
2. Powered off, compare Pin3-side capacitors, clamps, relay contacts, and the 1.457 kOhm series path against the Pin2 side.
3. Short powered check only if safe: remeasure `ADA4932_1 Pin1/2/3/4/9/10/11/12` and the B8A Pin1/Pin7 path nodes in the same instrument state.
4. If isolation is needed, lift only the small series resistor between B8A Pin7 and `ADA4932_1 Pin3`, after documenting both pads. Do not desolder IC packages as the first split.

## Safety Boundary

- Do not apply external input signals.
- Do not use resistance, continuity, or diode mode while powered.
- Do not short AD9269 input nodes, clamp nodes, ADA4932 outputs, or summing nodes.
- Do not reconnect R61/R62 until the frontend abnormal source is resolved.
- Do not replace AD9269, ADA4932_2, or B8A based on the current evidence.
