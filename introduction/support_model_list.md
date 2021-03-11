# 模型支持

Tengine 已完成对主流的计算机视觉模型的进行支持，包括分类、检测、识别、分割、关键点等功能。

| 类别 | 模型           | 支持平台                 |
| ---- | -------------- | ------------------------ |
| 分类 | MobileNet V1   | ARM、X86、MIPS、GPU、NPU |
| 检测 | MobileNet-SSD  | ARM、X86、MIPS、GPU、NPU |
| 识别 | MobileFaceNets | ARM、X86、GPU            |
| 分割 | YOLCAT         | ARM                      |

**提示**：
- 模型链接来自 Tengine Model ZOO，我们将持续更新;
- 支持平台列表中的 NPU 中，部分模型采用异构计算实现，即 CPU+NPU。
