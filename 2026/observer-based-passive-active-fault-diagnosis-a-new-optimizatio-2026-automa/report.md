# 基于观测器的被动/主动故障诊断：一种新的优化视角

![论文抬头：标题与作者](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/observer-based-passive-active-fault-diagnosis-a-new-optimizatio-2026-automa/images/header.png)

- 作者：Yuxin Sun, Feng Xu
- 关键词：Fault diagnosis; Set-valued observers; Observer gain optimization; State set separation tendency
- DOI / 论文链接：https://doi.org/10.1016/j.automatica.2026.112835

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

这篇论文处理的是**带乘性执行器故障的离散线性时不变系统**故障诊断问题，而且同时覆盖被动故障诊断（PFD）与主动故障诊断（AFD）。作者认为，现有基于集合值观测器的方法虽然鲁棒，但观测器增益、输入激励和诊断灵敏度之间往往互相牵制，尤其在 AFD 中，经常出现“为了设计输入先固定增益，而固定后的增益又会反过来抵消输入激励效果”的矛盾。

论文的突破口不是继续在输出估计集合上直接做指标，而是把诊断判据改写成**状态估计集合之间是否分离**的问题。这样一来，观测器增益优化就从“推动输出离开错误模式”变成“推动两个状态集合更快分开”，几何含义更清楚，也更适合做每一步的在线优化。

论文研究的系统与集合值观测器主线可写为

$$
x_{k+1}=Ax_k+B G_k^i u_k+E\omega_k,\qquad
y_k=Cx_k+F\eta_k,
$$

$$
\hat X_{k+1}^i=(A-L_k^i C)\hat X_k^i \oplus B G^i u_k \oplus E W \oplus L_k^i Y_k,
\qquad
\hat Y_{k+1}^i=C\hat X_{k+1}^i \oplus F V,
$$

其中 \(Y_k=y_k\oplus(-FV)\)，\(G^i\) 表示第 \(i\) 个执行器故障模式，\(W,V\) 分别是过程扰动与测量噪声的有界集合。

### 1.2 方法框架与核心思路

作者先定义与当前测量 \(y_k\) 一致的状态集合 \(X_{y_k}\)，再证明传统判据 \(y_{k+1}\in \hat Y_{k+1}^i\) 等价于某两个状态集合是否相交。随后构造两个同模式状态估计集合：一个保留待优化增益，另一个利用最精确前向估计作为基准，再通过“分离倾向”这个新指标度量二者的几何远离程度。最后把每一时刻的观测器增益设计成一个最大化分离倾向的优化问题。

这条路线的关键好处是：观测器增益的设计不再依赖当前输入 \(u_k\) 的具体取值，因此在 AFD 中可以先定增益、后定输入，避免了旧方案中的相互掣肘。

### 1.3 主要创新点

**创新点**主要有三层。

- 把经典基于输出集合的一致性判据，重写为基于状态估计集合分离的判据，给出新的诊断解释框架。
- 给出了输出一致状态集 \(X_{y_k}\) 的显式受约束 zonotope 表达，使状态集合层面的分析可以直接落地。
- 提出“分离倾向”这一几何指标，并据此构造适用于 PFD 和 AFD 的统一观测器增益优化方法，而且该优化与当前输入解耦。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

论文第二部分的逻辑非常紧：先由 **Proposition 4.1** 把测量约束对应到状态集合，再由 **Proposition 4.2** 把传统故障诊断条件改写成集合相交性测试；接着 **Theorem 4.1** 说明基准状态集合应如何选取，最后 **Theorem 4.2** 把“如何让两个集合尽量分开”写成可求解优化问题。也就是说，这篇文章不是单独给出一个新优化模型，而是先完成**诊断判据重解释**，再完成**几何指标定义**，最后才落到**增益设计**。

### 2.2 关键技术块解析

![proposition_4_1：输出一致状态集的显式表示](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/observer-based-passive-active-fault-diagnosis-a-new-optimizatio-2026-automa/images/proposition_4_1.png)

**Proposition 4.1** 给出了输出一致状态集的显式表达，这是全文从输出视角切换到状态视角的基础。它先定义

$$
X_{y_k}=\{x\in \mathbb{R}^{n_x}: Cx\in Y_k\},
$$

再把它写成

$$
X_{y_k}
=
\left\langle
C^\dagger(y_k-F\eta_c),\,
-C^\dagger F H_\eta,\,
(I_{n_y}-CC^\dagger)F H_\eta,\,
(I_{n_y}-CC^\dagger)(y_k-F\eta_c)
\right\rangle_{CZ}
\oplus
(I_{n_x}-C^\dagger C)\mathbb{R}^{n_x}.
$$

这个结果的重要性在于，它明确告诉我们：测量一致性对应的不是一个抽象可行域，而是“受约束 zonotope + 零空间子空间”的组合结构。后面所有几何分离分析，都建立在这个结构之上。

作者进一步由 **Proposition 4.2** 证明，传统判据

$$
y_{k+1}\in \hat Y_{k+1}^i
$$

等价于

$$
0\in X_{y_{k+1}}\oplus(-\hat X_{k+1}^i),
\qquad\text{也等价于}\qquad
X_{y_{k+1}}\cap \hat X_{k+1}^i\neq \varnothing.
$$

这一步把“输出能否被某模式观测器解释”变成了“该模式状态集合是否还与测量一致状态集合相交”。因此，后续只要想办法让错误模式下的集合更快分离，诊断速度就会提高。

![theorem_4_1：最精确基准状态集的选择](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/observer-based-passive-active-fault-diagnosis-a-new-optimizatio-2026-automa/images/theorem_4_1.png)

**Theorem 4.1** 解决的是“拿谁作为比较基准”这个问题。作者证明：当已知 \(X_{y_k}\) 时，若令基准观测器增益取零，则

$$
AX_{y_k}\oplus BG^i u_k \oplus EW
\subseteq
(A-L_k^i C)X_{y_k}\oplus BG^i u_k \oplus EW \oplus L_k^iY_k,\quad \forall L_k^i\neq 0.
$$

这说明 \(L_k^i=0\) 给出的前向传播集合最“紧”，因此最适合作为第二个参考状态集。它直接支撑后文的双集合构造：一个集合用于保持可调增益，另一个集合用于提供最精确的比较标尺。没有这一步，后面的分离优化就缺少几何参照。

![theorem_4_2：分离倾向与观测器增益优化](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/observer-based-passive-active-fault-diagnosis-a-new-optimizatio-2026-automa/images/theorem_4_2.png)

在选定基准集合后，论文把两类集合记为 \(\bar X_{k+1}^{i,1}\) 与 \(\bar X_{k+1}^{i,2}\)，并用受约束 zonotope 的缩放交界来定义“分离倾向” \(\hat\delta_{k+1}^i\)。其设计目标可概括为

$$
\hat\delta_{k+1}^{i,*}=\max_{L_k^i}\hat\delta_{k+1}^i.
$$

几何含义是：把两个集合分别绕中心缩放，直到刚好在边界接触；所需缩放系数越大，说明原始集合越分离。由于文章还考虑了 \(X_{y_k}\) 中由 \((I-C^\dagger C)\mathbb{R}^{n_x}\) 带来的无界方向，所以 **Theorem 4.2** 先把集合投影到相应正交补空间，再在投影空间中求分离倾向。这一步把“状态视角的诊断灵敏度”真正转成了可求解的优化问题，是全文最核心的数学连接点。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

作者使用一个两输入、四状态、两输出的离散系统进行 MATLAB 仿真，考虑健康模式 \(G_0=I_2\) 与两个单执行器故障模式 \(G_1,G_2\)。噪声与扰动均由 zonotope 有界，单次设置下重复 100 次随机测试，并设置最大诊断时域 \(k_{\max}=21\)。因此，仿真考察的不是单次幸运命中，而是给定输入或输入约束下的**诊断成功率与诊断步数**。

### 3.2 主要结果与对比说明

![figure_2：第一类执行器故障的 PFD 成功率](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/observer-based-passive-active-fault-diagnosis-a-new-optimizatio-2026-automa/images/figure_2.png)

**Fig. 2** 展示第一类执行器故障在不同常值输入设置下的 PFD 成功率分布。它说明本文方法不是只对个别输入有效，而是可以把输入空间中“更有利于诊断”的区域显式暴露出来。也就是说，状态集分离视角不只是改善单次判定，它还能提供输入选择的方向感。

![figure_4：较宽输入约束下的第一故障模式 AFD 结果](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/observer-based-passive-active-fault-diagnosis-a-new-optimizatio-2026-automa/images/figure_4.png)

**Fig. 4** 给出第一故障模式在 \(U=\langle 0,5I_2\rangle_Z\) 下的 AFD 结果。论文正文明确指出，在 \(k=2\) 时，真实输出只落在故障模式 \(1\) 的输出估计集合中，而不属于健康模式和另一故障模式的集合，因此可以在第 2 步完成诊断。这说明经优化后的观测器增益确实加速了错误模式排除。

![figure_6：缩小输入约束后的第一故障模式 AFD 结果](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/observer-based-passive-active-fault-diagnosis-a-new-optimizatio-2026-automa/images/figure_6.png)

**Fig. 6** 进一步把输入约束缩小到 \(\tilde U=\langle 0,1.5I_2\rangle_Z\)。这时诊断仍然成功，但完成时间从 \(k=2\) 推迟到 \(k=3\)。这个结果很关键，因为它揭示了本文方法的真实收益边界：增益优化确实能增强集合分离，但输入激励幅值仍然直接影响分离速度。换句话说，论文并没有宣称“增益可以替代输入”，而是证明“在输入受限时，合适的增益能尽量保住诊断能力”。

## 4. 后续研究方向
1. 初级想法 标题：考虑时变噪声界的自适应分离诊断
   *核心想法：* 文章当前默认扰动与测量噪声集合边界固定，可以进一步把 \(W,V\) 做成在线收缩或膨胀的自适应集合，从而研究分离倾向在保守性变化下的鲁棒稳定性与误诊率变化。
   数学推导难度：中等，需要把原有固定集合递推改写为时变集合递推，并重新证明分离指标的可比性。
2. 高级想法 标题：多故障耦合下的状态集分离优化
   *核心想法：* 当前示例主要针对单执行器故障。可以把多个对角元同时失效的复合故障写入 \(G^i\) 的组合模式族，研究多模式爆炸下如何压缩候选集合，并构造兼顾可分性与计算复杂度的分层增益优化。
   数学推导难度：较高，需要处理复合模式下的集合交并关系、模式数增长以及优化问题的可解性。
3. 指导想法 标题：状态集分离与输入激励协同设计的统一诊断框架
   *核心想法：* 本文的核心优点是把增益设计从输入中解耦，但更长远的方向是把“先增益、后输入”进一步推广成双层协同框架：上层保证状态集可分性，下层在能耗、执行器约束和任务目标下分配激励资源，从而形成适合复杂工业系统的统一主动诊断理论。
   数学推导难度：高，需要联合优化、可行域投影与递推可诊断性分析，适合作为系统性研究计划。

## 5. 总结与评价

这篇论文最有价值的地方，不只是提出了一个新的观测器增益优化问题，而是把集合值观测器故障诊断的解释重心从输出集合切换到了状态集合。这个视角转换让“为什么某个增益更利于诊断”获得了明确的几何解释，也使 PFD 与 AFD 能在同一套结构下统一讨论。

从方法成熟度看，文章的理论链条是完整的：显式状态集表达、诊断判据等价、最精确基准集选择、分离倾向优化、仿真验证，前后衔接较顺。局限也很清楚：结果依赖线性模型、集合有界与模式保持前提，而且在线优化在高维多模式情形下的复杂度仍然可能上升。即便如此，这篇工作仍然给出了一个很值得继续扩展的 zonotope/受约束 zonotope 诊断设计方向。
