# 面向信息物理系统的传感器攻击检测与辨识：一种数据驱动方法

![论文抬头：标题与作者](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/header.png)

- 作者：Kaiyu Wang, Dan Ye
- 关键词：Attack detection and identification; Data-driven; Zonotopes; Cyber-physical systems; Sensor attacks
- DOI / 论文链接：https://doi.org/10.1016/j.amc.2026.129954

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

这篇文章讨论的是**信息物理系统中的稀疏传感器攻击检测与定位**。与大量依赖已知系统矩阵 \(A,B,C\) 的安全监测方法不同，作者要解决的是更难的场景：系统动力学未知、攻击位置未知、攻击策略未知，但输入输出数据和噪声有界集合是可获得的。

作者的基本判断是：即便系统模型未知，只要能从历史数据中构造一个足够紧的**预测测量 zonotope**，就仍然可以用“真实测量是否落在该 zonotope 内”来做攻击检测；进一步地，如果把残差投影到各个传感器方向，还可以定位哪一维传感器最可能被攻击。

论文的系统模型与攻击模型写为

$$
x_{k+1}=Ax_k+Bu_k+w_k,\qquad
y_k=Cx_k+v_k,
$$

$$
x_{k+1}=Ax_k+Bu_k+w_k,\qquad
y_k^\alpha=Cx_k+E_L^\top \alpha_k+v_k,
$$

对应的长度为 \(f\) 的输出轨迹满足

$$
y_{k,f}^\alpha
=
O_f x_k + M_f^y u_{k,f}+M_f^\alpha \alpha_{k,f}+H_f w_{k,f}+v_{k,f}.
$$

其中 \(E_L^\top \alpha_k\) 表示攻击注入在被选中的传感器通道上。

### 1.2 方法框架与核心思路

全文的方法可以分成三步。第一步，证明在观测窗口足够长时，正常测量轨迹可以被一个 zonotope 过近似包住；第二步，仅用收集到的输入/输出数据和噪声集合，构造数据驱动的预测测量集 \(Z_{y_k}\)；第三步，用残差 \(d_k=y_k-\eta_{y_k}\) 相对生成矩阵的投影系数来完成攻击检测，并进一步逐传感器判断攻击位置。

它的亮点不在于某个单一公式，而在于把“数据驱动子空间方法”和“zonotope 闭包运算”结合起来：子空间方法解决未知模型问题，zonotope 结构解决未知但有界噪声问题。

### 1.3 主要创新点

**创新点**可以概括为三条。

- 在未知系统矩阵下，给出了基于 zonotope 的数据驱动攻击检测与辨识流程，不要求噪声满足高斯等统计假设。
- 推导了检测可行性的时间窗与信息量边界，明确说明历史数据需要达到什么规模才能可靠检测。
- 设计了列截断算法以降低生成矩阵维数，把攻击定位的计算量从组合爆炸压缩到按传感器维数线性增长。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

这篇文章的技术主线是：先用理论结果证明“预测测量集确实可以由 zonotope 表示”，再给出完全数据驱动的构造式，接着用一个非常直接的投影阈值完成攻击检测，最后再把同一套投影思想细化到单个传感器方向上完成定位。就逻辑结构来说，**Theorem 3** 负责“集合怎么来”，**Theorem 4** 负责“攻击怎么判”，**Algorithm 2** 负责“步骤怎么落地”，**Theorem 5** 负责“攻击位置怎么找”。

### 2.2 关键技术块解析

![theorem_3：数据驱动预测测量 zonotope 的构造](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/theorem_3.png)

**Theorem 3** 是全文最核心的建模结果。作者在满足前序秩条件与信息窗长度条件后，证明预测测量集可以写成

$$
Z_{y_k}=\langle \eta_{y_k},\Theta_{y_k}\rangle,
$$

其中中心和生成矩阵由收集到的轨迹数据直接构造：

$$
\eta_{y_k}
=
\Lambda (X_{1,T}-\mathbb{M}_{C_w})\Omega_0^\dagger
\begin{bmatrix}
\eta_{k-1}^\top & \eta_u^\top
\end{bmatrix}^\top,
$$

$$
\Theta_{y_k}
=
\left[
\Lambda (X_{1,T}-\mathbb{M}_{C_w})\Omega_0^\dagger
\begin{bmatrix}
\Theta_{k-1} & \Theta_u
\end{bmatrix},
\Lambda \Theta_w,\Theta_v
\right].
$$

这一步的意义非常大，因为它说明即使 \(A,B,C\) 完全未知，也可以利用输入状态轨迹、噪声矩阵 zonotope 约束以及子空间逆映射关系，直接得到当前时刻的输出可达集合。换句话说，论文把“模型驱动预测”改造成了“数据闭包预测”。

![theorem_4：基于投影系数的攻击检测准则](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/theorem_4.png)

有了预测测量集以后，**Theorem 4** 给出很干净的检测指标。令

$$
d_k=y_k-\eta_{y_k},
$$

若系统无攻击，则 \(d_k\) 应能由生成矩阵 \(\Theta_{y_k}\) 的列线性组合表示，因此满足

$$
\left\|\Theta_{y_k}^\top d_k\right\|_\infty \le 1.
$$

于是检测判据就变成

$$
\left\|\Theta_{y_k}^\top d_k\right\|_\infty > 1.
$$

这个判据的优点是实现成本低，而且和 zonotope 表达完全一致。它不是做概率意义上的异常评分，而是在做“当前测量是否已经逃出正常可达集”的集合判别。

![algorithm_2：数据驱动攻击检测与辨识总流程](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/algorithm_2.png)

**Algorithm 2** 把整篇文章的实现流程串了起来：先构造过去/未来数据矩阵，再做核表示分解，接着计算噪声矩阵约束集合 \(\mathbb{M}_{C_w}\)、预测 zonotope \(Z_{y_k}\)、残差 \(d_k\)，然后用列截断算法压缩生成矩阵，最后执行攻击检测与位置辨识。这里列截断非常关键，因为随着时间推进，\(\Theta_{y_k}\) 维数会不断增大；如果不截断，理论虽然成立，但在线开销会迅速上升。

![theorem_5：受攻击传感器位置的辨识条件](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/theorem_5.png)

在检测出攻击之后，**Theorem 5** 把判别从整体残差细化到单传感器方向。对第 \(i\) 个传感器，作者构造

$$
d_k^i=E_iE_i^\top d_k,\qquad
\varpi_{k,i}=(\Theta_y^\rho)^\top d_k^i,
$$

并定义辨识系数

$$
J_i=(\Theta_y^\rho)^\top E_iE_i^\top \Theta_y^\rho \,\varpi_{k,i}.
$$

若

$$
\|J_i\|_\infty>1,
$$

则可判定第 \(i\) 个传感器处于被攻击状态。这里的关键思想是：如果某一维残差只是在正常 zonotope 允许范围内波动，那么它在对应投影方向上不会越界；一旦该方向的投影响应持续越界，就说明攻击注入已经集中出现在该传感器通道。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文使用 IEEE 6-bus 电力系统进行验证，系统包含 3 台发电机和 6 个母线，传感器直接测量 3 台发电机频率。作者设置 \(Z_{x_0}=\langle \eta_{x_0},0.1E\rangle\)、\(Z_u=\langle \eta_u,\Theta_u\rangle\)、\(Z_w=\langle 0,\Theta_w\rangle\)、\(Z_v=\langle 0,\Theta_v\rangle\)，并选择过去/未来窗口参数 \(p=f=6,T=54\)。仿真不仅考察常规稀疏攻击，还考察了更高噪声与未知攻击传感器数量的复杂情形。

### 3.2 主要结果与对比说明

![figure_3：稀疏传感器攻击检测结果](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/figure_3.png)

**Fig. 3** 对应 Case 1 的检测结果：攻击分别在 \(k\in[200,500]\) 和 \(k\in[700,1000]\) 两个时间段注入。文章明确说明，攻击出现后，真实测量迅速逃离预测 zonotope，违反量 \(\|\Theta_{y_k}^\top d_k\|_\infty\) 同步越界，因此检测曲线可以准确标出两个攻击时段。这表明基于集合外逸的判据在有界噪声下确实能做到低误报。

![figure_4：稀疏传感器攻击位置辨识结果](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/figure_4.png)

**Fig. 4** 进一步展示了定位能力。第一次攻击只作用于 \(L=\{1\}\)，第二次攻击作用于 \(L=\{2,3\}\)。图中的对应辨识系数在攻击期间越过阈值，而未受攻击通道保持在阈值以下，说明 **Theorem 5** 的逐通道投影判据可以正确恢复攻击位置，而不是只给出“系统被攻击了”的粗判断。

![figure_7：辨识计算量对比](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@9a57fde/2026/sensor-attack-detection-and-identification-for-cybe-2026-applied-mathematics/images/figure_7.png)

**Fig. 7** 给出了计算复杂度上的对比。论文指出，当攻击传感器数量未知时，本文方法每个时刻只需要做 \(n_y\) 次计算，而对比方法需要枚举 \(\sum_{p=1}^{n_y} C_{n_y}^p\) 个候选集合。这个结果说明列截断与逐通道判别并不是附属优化，而是决定该方法能否扩展到多传感器系统的关键工程设计。

## 4. 后续研究方向
1. 初级想法 标题：基于自适应阈值的预测测量集在线收缩
   *核心想法：* 现有判据使用固定阈值 \(\|\Theta_{y_k}^\top d_k\|_\infty>1\)。可以进一步利用历史残差统计量或滑动窗口几何裕度，对阈值与列截断参数做在线调节，以降低不同运行工况下的保守性。
   数学推导难度：中等，主要涉及收缩策略稳定性与误报漏报界的重新估计。
2. 高级想法 标题：分布式传感网络中的局部 zonotope 协同辨识
   *核心想法：* 把当前集中式检测框架扩展到多节点分布式结构，让每个局部节点维护自身预测测量集，再通过一致性或交集传播完成跨区域攻击辨识，从而适配大规模电网和网络化工业系统。
   数学推导难度：较高，需要同时处理局部信息耦合、通信延迟和分布式集合融合误差。
3. 指导想法 标题：融合结构先验与数据闭包的安全监测统一理论
   *核心想法：* 本文完全摆脱模型矩阵是其优势，但未来可以进一步把稀疏性、图结构、物理守恒约束等先验以受约束 zonotope 或结构正则项的形式引入，构建兼具可解释性、数据驱动性和安全保证的统一攻击监测理论。
   数学推导难度：高，需要把子空间识别、集合传播、结构约束和可检测性分析整合到同一框架中。

## 5. 总结与评价

这篇论文的价值在于，它没有停留在“数据驱动也能做检测”这一层，而是进一步给出了可解释的 zonotope 闭包表达、明确的信息窗长度要求、低复杂度的在线检测准则以及逐通道定位策略。换言之，它把一个原本偏经验性的安全检测问题，做成了一个集合几何驱动的可证明流程。

从不足看，文章仍然建立在线性系统、有界噪声与足够信息激励的前提之上；当噪声集合严重放大时，Theorem 6 也说明存在不可检测的小攻击区间。但这恰恰说明它的理论边界是清楚的。对于想把数据驱动安全监测和 zonotope 方法结合起来的人，这篇文章是很好的参考起点。
