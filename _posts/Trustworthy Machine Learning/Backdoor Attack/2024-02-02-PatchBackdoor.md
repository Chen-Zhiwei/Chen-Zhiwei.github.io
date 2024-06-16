---
title: "PatchBackdoor: Backdoor Attack against Deep Neural Networks without Model Modification"
description: Tsinghua 2023 WWW
date: 2024-2-3
# last_modified_at: 2024-2-3
categories: [Trustworthy Machine Learning, Backdoor Attack]
tags: [Backdoor Attack, Representation, WWW]

pin: false
math: false
mermaid: false

render_with_liquid: false
image: 
  path: /assets/posts/PatchBackdoor/compare.png
  alt: The difference between existing attacks and PatchBackdoor.
---

## **不用修改模型的后门攻击**
开源代码： https://github.com/XaiverYuan/PatchBackdoor
思路很清晰，优化目标为一个固定位置的Patch，这个Patch会导致Target的分类结果

![workflow_github](https://github.com/XaiverYuan/PatchBackdoor/raw/main/markdown/train.PNG)
<center>Workflow in Github</center>

上图为开源社区中展示的Workflow，很好的解释了其优化Patch的思路，类似于对抗样本优化Variable。

下图为WWW论文中展示的Workflow。

![workflow_paper](/assets/posts/PatchBackdoor/workflow.png){: w="900"}
<center>Workflow in WWW</center>

### 论文中的Real-world Condition c是什么？

主要是为了实现Physical-world Feasibility，使这个Patch能在物理世界对victim模型实现攻击
> Our key idea is to model the digital-physical gap with a differentiable transformation, and optimizing the backdoor patch using this transformation

![physical](/assets/posts/PatchBackdoor/physical.png)

数字世界到物理世界要经过shape和color的transform校准。
校准通过建立digital到physical的颜色和shape实现，用一个校准板和arUco这个透视工具。实现物理到数字的颜色以及形状的“映射”。

Real-world Condition c在实现上，其实就是在物理世界放一块校准板，这个校准板的位置未来会放上优化好的Patch。由校准板就可以确定其在数字世界Patch的触发器位置。
当优化完成后，又通过校准板的映射关系，可以打印出一个Color转换好的物理的Patch，这个Patch放在原位置上，就可以实现攻击了。

#### Experiment
实验在多个模型下，都表现出了可观(90%)的攻击成功率。对比方法为5%和10%的BadNet。
还在物理世界做了实验，但实验数据使自己手机的，攻击成功率为100%，干净样本准确度为90.73%。

**个人观点：** 
- 完全不能够修改模型，提出了物理世界到数字世界的转换方法，都大大提高了该方法在现实世界中的实用性。
- 实用性还有几个方面可以改进：
    1. 实验假设掌握了完整的数据集
    2. 实验假设掌握了模型的参数，从而可以获得梯度信息
    3. 没有说明干净模型的训练是否采用了数据增强，从直觉上，数据增强是很影响此方法的