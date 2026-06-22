# Zurich MFLI Input Repair

本工程用于整理 Zurich Instruments MFLI 锁相放大器 Signal Input 电压输入端过压后的故障排查、维修证据、测量记录和后续任务。

## 当前判断

最新状态见 [docs/repair_status.md](docs/repair_status.md)。

截至当前记录，故障已从输入保护/前级泛化怀疑收敛到 AD9269BCPZ-65 双通道 ADC 的 B 通道输入链路：

- A 通道输入：Pin51 = 0.88 V，Pin52 = 0.88 V，静态正常。
- B 通道输入：Pin61 = -0.72 V，Pin62 = 2.54 V，差分约 3.26 V，超过 AD9269 输入绝对最大范围。
- 下一步决定性实验：隔离 Pin61/Pin62 前的两颗串联输入电阻，分别测前级侧与 ADC 侧电压，判断异常来自前级驱动还是 ADC B 通道输入级。

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

## 下一步

按 `tasks/` 中任务顺序推进，优先完成：

1. 测 ORA/ORB，确认 OVI 是否由 B 通道触发。
2. 测 AVDD/DRVDD/VREF/VCM/RBIAS，排除 ADC 全局供电与参考异常。
3. 比较 A/B 输入钳位器件。
4. 隔离 B 通道 Pin61/Pin62 串联输入电阻，定位故障源头。
