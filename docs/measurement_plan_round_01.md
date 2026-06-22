# 第一轮安全测量计划：AD9269 OR/供电参考/钳位对比

本文档用于执行第一轮非拆焊测量。目标是在不进入隔离串联电阻实验前，先确认 OVI 来源、AD9269 全局供电/参考状态，以及 A/B 输入钳位网络是否存在明显不对称。

## 1. 测量目标

1. 确认 ORB/ORA 状态，判断 LabOne 或面板 OVI 是否主要由 B 通道触发。
2. 确认 AD9269 的 AVDD、DRVDD、VREF、VCM、SENSE 是否正常。
3. 断电测 RBIAS 到地阻值，确认偏置脚外围是否明显异常。
4. 断电二极管档比较 A/B 输入钳位网络，检查 B 通道是否存在短路或异常导通。

## 2. 总体安全规则

- 不向 Signal Input +V / -V 输入任何外部信号。
- 输入端保持无外部信号；如需要短接，只使用受控短接帽，不注入电压。
- 通电时只使用万用表 DC 电压档，不使用电阻档、蜂鸣档或二极管档。
- 上电测量只测必要点，测完立即断电。
- 已知 AD9269 Pin61/Pin62 出现过超过绝对最大范围的电压，测量时避免表笔滑动短接相邻引脚。
- 如果任一电源轨明显异常、元件发热、异味或冒烟，立即断电并停止本轮测量。

## 3. 推荐顺序

### Step 0：断电准备

状态：断电。

检查：

- 确认仪器未连接外部输入信号。
- 确认万用表表笔、接地夹和探针状态良好。
- 确认 AD9269 Pin22、Pin43、Pin55、Pin56、Pin57、供电脚和 RBIAS Pin58 的位置已经在照片或板上标注。
- 不进行任何拆焊。

记录字段：

- date
- operator
- instrument_state
- input_state
- note

### Step 1：上电测 ORB / ORA

状态：上电，短时间测量。

万用表档位：DC 电压档。

| Pin | Name | 目的 | 记录 |
|---:|---|---|---|
| 22 | ORB | B 通道 overrange 输出 | measured_value_v、logic_state、stable_or_toggling |
| 43 | ORA | A 通道 overrange 输出 | measured_value_v、logic_state、stable_or_toggling |

同时记录：

- 当前量程；
- 输入状态；
- LabOne 是否显示 OVI；
- 面板 OVI 指示灯状态；
- 测量时间是否短于计划时间。

判断：

- ORA 无效、ORB 有效：支持 OVI 来自 B 通道。
- ORA 有效、ORB 无效：需要重新核对通道映射。
- ORA/ORB 均有效：可能存在全局参考、供电或数字状态问题。
- ORA/ORB 均无效但 LabOne OVI：可能存在 FPGA/软件或其他检测路径。

### Step 2：上电测参考与共模

状态：上电，短时间测量。

万用表档位：DC 电压档。

| Pin | Name | 正常预期 | 记录 |
|---:|---|---|---|
| 55 | VREF | 内部 1 V 模式约 0.99 V | measured_value_v |
| 57 | VCM | 典型约 0.9 V | measured_value_v |
| 56 | SENSE | 稳定配置电平 | measured_value_v、logic_or_analog_note |

判断：

- VREF/VCM 正常：支持问题更局限于输入链路。
- VREF/VCM 异常：不要急于更换 ADC 或前级，需先查参考/电源/负载。

### Step 3：上电测 AVDD / DRVDD

状态：上电，短时间测量。

万用表档位：DC 电压档。

| Rail | Pins | 正常预期 | 记录 |
|---|---|---|---|
| AVDD | 49/50/53/54/59/60/63/64 | 1.7-1.9 V，典型 1.8 V | measured_value_v |
| DRVDD | 10/19/28/37 | 1.7-3.6 V | measured_value_v |

判断：

- 同一电源轨多个脚应接近一致。
- 任一电源轨明显偏低、偏高或波动，需要先停止后续上电测量。

### Step 4：断电测 RBIAS

状态：断电，确认电源电容已基本放电后测量。

万用表档位：电阻档。

| Pin | Name | 正常预期 | 记录 |
|---:|---|---|---|
| 58 | RBIAS 到 GND | 约 10 kOhm | measured_value_ohm |

注意：

- 只在断电后测。
- 如果读数不稳定，记录稳定后的范围，不要只写单一“正常”。

### Step 5：断电二极管档比较 A/B 输入钳位

状态：断电。

万用表档位：二极管档。

目标：比较 A 通道正常输入节点与 B 通道异常输入节点到 0 V 和 1.82 V 电源轨的正反向压降。

| Channel | Pins | Compare To | Direction | 记录 |
|---|---|---|---|---|
| A | 51 VIN+A, 52 VIN-A | 0 V | forward/reverse | diode_drop_v 或 OL |
| A | 51 VIN+A, 52 VIN-A | 1.82 V rail | forward/reverse | diode_drop_v 或 OL |
| B | 61 VIN-B, 62 VIN+B | 0 V | forward/reverse | diode_drop_v 或 OL |
| B | 61 VIN-B, 62 VIN+B | 1.82 V rail | forward/reverse | diode_drop_v 或 OL |

判断：

- A/B 压降基本一致：钳位网络未显示明显单侧短路，异常可能来自前级或 ADC 内部。
- B 通道某方向短路或明显低阻：B 通道钳位网络或 ADC 输入级损坏更可疑。
- B 通道对轨异常导通且与 Pin61/62 异常电压方向一致：需要在主对话中决定是否进入串联电阻隔离实验。

## 4. 记录文件

本轮计划行见：

```text
measurements/2026-06-21_round_01_measurement_plan.csv
```

建议记录字段：

```text
date,step,measurement_state,dmm_mode,target,component,pin_or_node,reference_node,expected,measured_value,unit,input_condition,instrument_range,ovi_status,observation,operator_note,source_note
```

实测后不要覆盖原计划含义。建议在同一 CSV 的 `measured_value`、`unit`、`observation`、`operator_note` 字段补充结果；若跨日期测量，则新建对应日期 CSV。

## 5. 不进入的操作

本轮不执行：

- 拆焊；
- 翘起 Pin61/Pin62 串联输入电阻；
- 给 ADC 输入节点注入外部电压；
- 更换 AD9269 或前级器件；
- 恢复/替换输入保护器件。
