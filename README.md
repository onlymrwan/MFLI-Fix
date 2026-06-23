# Zurich MFLI Input Repair

本工程用于整理 Zurich Instruments MFLI 锁相放大器 Signal Input 电压输入端过压后的故障排查、维修证据、测量记录和后续任务。

## 当前判断

最新快速入口见 [docs/current_status.md](docs/current_status.md)，完整状态见 [docs/repair_status.md](docs/repair_status.md)。

截至当前记录，故障已从输入保护/前级泛化怀疑，经 AD9269 B 通道输入链路，进一步追踪到 AD9269 上游的 ADA4932-1 差分 ADC 驱动器输出侧：

- A 通道输入：Pin51 = 0.88 V，Pin52 = 0.88 V，静态正常。
- B 通道输入：Pin61 = -0.72 V，Pin62 = 2.54 V，差分约 3.26 V，超过 AD9269 输入绝对最大范围。
- 已隔离 Pin61/Pin62 前的两颗 1 Ω 级串联输入电阻：隔离后 AD9269 侧为 0 V / 0 V，仪器不再超限；二极管/钳位/前级侧仍为 2.54 V / -0.72 V。
- 两个钳位节点经约 50 Ω 电阻连接到 ADA4932-1 的 Pin10/Pin11，实测 ADA4932-1 输出为 +4 V / -3 V，供电为 +5.3 V / -5.0 V。
- ADA4932-1 的 Pin9 VOCM = 0.908 V，Pin12 PD = 5.398 V；反馈电阻经复测修正为两颗 ADA4932 的四个反馈电阻均为 1 kΩ，因此异常暂不能优先解释为 VOCM 明显错误、PD 掉电状态或单侧反馈电阻开路。
- 已确认另一颗同型号 ADA4932-1 是当前下级驱动器的上级，记为 `ADA4932_1`；当前直接驱动 AD9269 H/L 钳位二极管区域的下级记为 `ADA4932_2`。
- `ADA4932_1` 通过电阻和 Omron 继电器触点连接到 `ADA4932_2`，因此当前问题应按串级链路定位，而不是把 `ADA4932_1` 当作独立正常对照。
- 已确认级间继电器正常导通，`ADA4932_1` 到 `ADA4932_2` 两路通路均正常；两路级间电阻压降对应约 3 mA 到 3.7 mA 的电流量级。
- 用户修正反馈电阻信息：两颗 ADA4932 的四个反馈电阻均为 1 kΩ。
- `ADA4932_1` 的上游 8 引脚 `B8A` 已核对为 AD8512，复测 Pin1/2/3/5/6/7 均约 1.26 V，供电为 -5.53 V / +5.40 V；B8A 静态下不像 rail-side 异常源。
- B8A 到 `ADA4932_1` 的路径已进一步测量：Pin1 路到 `ADA4932_1 Pin2 = 1.33 V` 基本无明显压降；Pin7 路经 1.457 kΩ 到 `ADA4932_1 Pin3 = -0.45 V`，存在约 1 mA 量级电流。
- 当前优先级：保持 R61/R62 与 AD9269 隔离，围绕 `ADA4932_1 Pin3` 支路检查局部输入/反馈网络、周边电容/钳位/继电器触点，必要时只隔离 Pin3 侧小串联电阻；暂不直接更换 AD9269、ADA4932_2 或 B8A。

## 目录结构

```text
.
├── README.md
├── docs/
│   ├── repair_status.md
│   ├── manuals/
│   ├── datasheets/
│   ├── prior_analysis/
│   └── reports/
├── measurements/
│   ├── *.csv
│   └── spreadsheets/
├── images/
│   ├── raw/
│   └── diagrams/
├── tasks/
├── media/
│   └── videos/
└── archives/
    └── source_packages/
```

## Git 管理策略

- 推荐跟踪：Markdown、CSV、Excel/Word 测量记录、PDF 手册/数据手册、维修照片和示意图。
- 默认忽略：视频文件、原始压缩包、Office 临时锁文件、Python/LaTeX/系统生成缓存。
- 若未来要把视频或大批量原始照片推到远程仓库，建议先评估 Git LFS，不要直接把大二进制文件长期写入普通 Git 历史。

## 协作调度

为减少长对话压缩造成的信息丢失，本项目采用“主对话 + 少量长期子对话 + 文件长期记忆”的协作方式。规则见 [docs/project_coordination.md](docs/project_coordination.md)。

## Git 学习线

本工程同时作为 Git/GitHub 实践案例。初学者安全流程见 [docs/git_workflow.md](docs/git_workflow.md)。

## 安全边界

- 修复前不要向 Signal Input +V 输入任何外部信号。
- AD9269 Pin61/Pin62 已出现超过绝对最大范围的电压，后续上电测量应短时间、少测点、测完断电。
- 不要在通电状态下使用电阻档、蜂鸣档或二极管档。
- 已拆除或缺失的输入保护器件在最终恢复前需要补回，否则再次测试风险很高。
- 当前 R61/R62 已作为定位实验隔离，不要在前级异常源确认前回焊；不要向 AD9269 输入侧或 ADA4932-1 输出侧注入外部电压。

## 下一步

当前按 `tasks/issue_05_trace_frontend_clamp_side.md` 推进，优先完成 `ADA4932_1 Pin3` 支路局部排查：

1. 记录当前 LabOne / 仪器量程、输入模式、耦合和阻抗状态，确保后续测量状态一致。
2. 断电比较 `ADA4932_1 Pin3` 与 Pin2 到 GND、正负电源、VOCM、Pin1/Pin4、Pin10/Pin11 的阻值/二极管档差异。
3. 检查 Pin3 侧周边电容、钳位件、继电器触点和 1.457 kΩ 串联路径，和 Pin2 侧作对称比较。
4. 短时上电复测 `ADA4932_1 Pin1/2/3/4/9/10/11/12` 与 B8A Pin1/Pin7 路径节点，确认 Pin3 仍是唯一强拉低侧。
5. 若需要隔离，优先翘起 B8A Pin7 到 `ADA4932_1 Pin3` 的小串联电阻一端，而不是拆 IC。
6. 隔离后若 Pin3/输出恢复，查外部 Pin3 支路；若仍异常，再把 `ADA4932_1` 本体或局部反馈网络列为高概率故障。
