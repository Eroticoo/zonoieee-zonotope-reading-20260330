# 基于椭球分析的连续时间线性切换系统区间估计

![论文抬头：标题与作者](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/interval-estimation-for-continuous-time-linear-swit-2026-nonlinear-analysis/images/header.png)

- 作者（英文）：Fan Zhang; Zhenhua Wang; Nacim Meslem; Tarek Raïssi; Yi Shen
- 关键词：区间估计；连续时间线性切换系统；鲁棒观测器；椭球分析；平均驻留时间
- DOI / 论文链接：https://doi.org/10.1016/j.nahs.2026.101675

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

这篇论文研究的是**连续时间线性切换系统**在未知但有界扰动与测量噪声作用下的区间估计问题。作者关注的不是单纯构造一个点估计器，而是希望同时得到真实状态 $x(t)$ 的上下界，并且这个包络要尽量紧，不能因为切换或保守传播而迅速膨胀。

文中要解决的核心难点有三层。第一，切换系统在任意切换信号或平均驻留时间约束下都可能表现出显著不同的稳定性结构，传统单一 Lyapunov 设计往往偏保守。第二，现有 interval observer 通常依赖 cooperative / Metzler 型条件，适用范围受限。第三，直接对总误差做逐步集合传播容易把初值不确定性和持续外扰混在一起，导致区间逐步放大。

作者因此提出一个两步框架：先设计鲁棒观测器，压低点估计误差；再把总误差拆成“初值传播项”和“外扰驱动项”，其中前者用切换线性系统的状态转移矩阵精确传播，后者用椭球不变集与最小 box 包络转换成显式区间。这样做的直接目标，就是在不过分增加在线计算复杂度的前提下，把估计区间做得更紧。

### 1.2 方法框架与核心思路

论文考虑的连续时间切换系统为



$$
\begin{aligned}
\dot{x}(t) &= A_{\sigma(t)}x(t) + B_{\sigma(t)}u(t) + D_{\sigma(t)}w(t),\\
y(t) &= C_{\sigma(t)}x(t) + E_{\sigma(t)}v(t),
\end{aligned}
$$



其中 $x(t)$ 是状态，$u(t)$ 是已知输入，$y(t)$ 是输出，$w(t)$ 是未知但有界扰动，$v(t)$ 是测量噪声，$\sigma(t)\in\{1,\dots,m\}$ 是可在线测得的切换信号。初值与外生信号满足



$$
\underline{x}(0)\le x(0)\le \overline{x}(0),\qquad
-\mathbf{1}\le w(t)\le \mathbf{1},\qquad
-\mathbf{1}\le v(t)\le \mathbf{1}.
$$



对应的模态依赖观测器写成



$$
\dot{\hat{x}}(t)
= A_{\sigma(t)}\hat{x}(t) + B_{\sigma(t)}u(t)
+ L_{\sigma(t)}\bigl(y(t)-C_{\sigma(t)}\hat{x}(t)\bigr),
$$



并令估计误差为



$$
e(t)=x(t)-\hat{x}(t).
$$



于是误差系统变为



$$
\dot{e}(t)=\bigl(A_{\sigma(t)}-L_{\sigma(t)}C_{\sigma(t)}\bigr)e(t)
+ \bigl(\bar{D}_{\sigma(t)}-L_{\sigma(t)}\bar{E}_{\sigma(t)}\bigr)d(t),
$$



其中



$$
d(t)=\begin{bmatrix}w(t)\\ v(t)\end{bmatrix},\qquad
\bar{D}_{\sigma(t)}=\begin{bmatrix}D_{\sigma(t)} & 0\end{bmatrix},\qquad
\bar{E}_{\sigma(t)}=\begin{bmatrix}0 & E_{\sigma(t)}\end{bmatrix}.
$$



作者进一步把总误差分解为



$$
e(t)=e_0(t)+e_d(t),
$$



并得到两个子系统



$$
\dot{e}_0(t)=A_{L,\sigma(t)}e_0(t),\qquad
\dot{e}_d(t)=A_{L,\sigma(t)}e_d(t)+D_{L,\sigma(t)}d(t),
$$



其中 $A_{L,\sigma(t)}=A_{\sigma(t)}-L_{\sigma(t)}C_{\sigma(t)}$，$D_{L,\sigma(t)}=\bar{D}_{\sigma(t)}-L_{\sigma(t)}\bar{E}_{\sigma(t)}$。这一步是全文最关键的建模动作，因为后续所有“紧区间”都建立在这个误差分裂之上。

### 1.3 主要创新点

- 论文把**鲁棒观测器设计**与**椭球型区间估计**真正串成一条闭合链条，而不是先有点估计、后做经验型外包络。
- 论文把总误差拆成初值驱动项与扰动驱动项，避免初值不确定性在长期传播中持续污染外扰包络，这正是区间收紧的来源。
- 对任意切换与 ADT 切换两种情形，作者分别给出可求解的 LMI 条件；前者基于公共 Lyapunov 函数，后者转向多 Lyapunov 函数以降低保守性。
- 论文最终给出的不是抽象椭球，而是把椭球通过最小 box 显式转成状态上下界，因此结果可直接用于区间估计比较或下游鲁棒控制设计。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

这篇文章的技术主线可以概括为：

系统模型与观测器建模  
$\rightarrow$ 在任意切换下用 `Theorem 4.1` 设计鲁棒观测器增益  
$\rightarrow$ 用 `Theorem 4.2` 给外扰驱动误差 $e_d(t)$ 构造时变椭球包络  
$\rightarrow$ 把 $e_0(t)$ 通过状态转移矩阵显式传播，并在 `Theorem 4.3` 中与 $e_d(t)$ 做 Minkowski 合成  
$\rightarrow$ 在 ADT 情形下引入多 Lyapunov 函数，把相同两步结构扩展为 `Theorem 5.1` 与 `Theorem 5.2`  
$\rightarrow$ 用仿真说明区间更紧、适用范围更广。

其中最重要的思想，不是单独某条 LMI，而是“**观测器稳定化 + 误差分裂 + 椭球到区间的显式转换**”这个整体流程。前一部分保证误差不会失控，后一部分保证区间不会因为粗糙传播而过度保守。

对于 ADT 情形，作者使用的切换约束为



$$
N_\sigma(t_1,t_2)\le N_0+\frac{t_2-t_1}{\tau_a},
$$



这里 $N_\sigma(t_1,t_2)$ 是区间 $(t_1,t_2)$ 内的切换次数，$\tau_a$ 是平均驻留时间。这个条件的作用是把“允许切换，但不能太频繁”写成可分析的上界，从而给多 Lyapunov 函数的收敛分析提供基础。

### 2.2 关键技术块解析

![Theorem 4.1：任意切换下的鲁棒观测器设计条件](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/interval-estimation-for-continuous-time-linear-swit-2026-nonlinear-analysis/images/theorem_4_1.png)

`Theorem 4.1` 是全文第一条核心结论。它在任意切换情形下给出一组 LMI，只要存在 $P\succ 0$、$\mu>0$ 和 $W_i$ 使这些 LMI 可行，就可以恢复观测器增益



$$
L_i=P^{-1}W_i.
$$



更关键的是，这个定理同时给出了估计误差范数的衰减上界



$$
\|e(t)\|
\le
\sqrt{\rho^{-1}\left(e^\top(0)Pe(0)e^{-\alpha t}+\frac{\mu}{\alpha}\bigl(1-e^{-\alpha t}\bigr)\right)}.
$$



这里的含义很清楚：$e(0)$ 带来的影响按 $e^{-\alpha t}$ 衰减，而外扰带来的稳态影响由 $\mu/\alpha$ 控制。因此 `Theorem 4.1` 并不直接产生区间，但它提供了后续两步区间估计成立所需的稳定骨架与增益设计入口。

![Theorem 4.2：外扰驱动误差的椭球包络与显式上下界](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/interval-estimation-for-continuous-time-linear-swit-2026-nonlinear-analysis/images/theorem_4_2.png)

`Theorem 4.2` 处理的是分裂后的外扰驱动项 $e_d(t)$。这部分误差满足零初值、持续受扰，因此作者不再跟踪点轨迹，而是直接给出一个时变椭球



$$
e_d(t)\in \mathcal{E}\bigl(0,Q(t)\bigr),\qquad
Q(t)=\tilde{P}^{-1}\bigl(1-e^{-\alpha t}\bigr).
$$



进一步，利用对角元就可以写出对应的上下界



$$
\underline{e}_d(t)=
\begin{bmatrix}
-\sqrt{Q_{11}(t)}\\
\vdots\\
-\sqrt{Q_{n_xn_x}(t)}
\end{bmatrix},
\qquad
\overline{e}_d(t)=
\begin{bmatrix}
\sqrt{Q_{11}(t)}\\
\vdots\\
\sqrt{Q_{n_xn_x}(t)}
\end{bmatrix}.
$$



这一块的技术价值在于：作者没有直接传播 box，而是先用椭球刻画扰动项的最小紧包络，再把椭球转成区间。这样既保留了椭球在线性系统下易处理的优势，又能输出最终需要的区间形式。换句话说，`Theorem 4.2` 才是论文从“稳定性分析”走向“紧区间估计”的桥梁。

![Theorem 5.2：ADT 情形下的模式依赖椭球包络](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/interval-estimation-for-continuous-time-linear-swit-2026-nonlinear-analysis/images/theorem_5_2.png)

`Theorem 5.2` 把上一条思路推广到 ADT 情形。由于每个模态允许拥有不同的 Lyapunov 矩阵，外扰误差包络变成



$$
Q(t)=\tilde{P}_{\sigma(t_k)}^{-1}\bigl(1-e^{-\alpha(t-t_k)}\bigr),\qquad t\in[t_k,t_{k+1}),
$$



于是每个驻留区间内的椭球大小会随当前模态切换而改变。这个结论背后的真正意义，是作者不再强行用一个公共 Lyapunov 函数覆盖所有模态，而是允许不同模态拥有不同尺度的误差椭球，再通过 ADT 约束控制模式切换带来的增长。

因此，`Theorem 5.2` 不是简单重复 `Theorem 4.2`，而是说明在 ADT 条件下可以用更低保守性的模式依赖分析取代统一包络。文中后面的仿真也正是利用这一点，展示 ADT 方案相对于任意切换公共 Lyapunov 方案的区间收紧效果。

综合来看，这篇文章的关键技术链条很清晰：`Theorem 4.1` 负责把误差系统稳定住，`Theorem 4.2` 负责把外扰驱动误差变成时变椭球区间，`Theorem 5.2` 则把这种椭球区间推广到模式依赖的 ADT 场景。初值驱动项 $e_0(t)$ 则通过状态转移矩阵显式传播，并与 $e_d(t)$ 合成得到最终状态上下界。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文给出三类互补证据。第一类是二维三模态系统，在任意切换下与文献 [36] 的方法做区间比较。第二类仍使用同一系统，但把切换限制改成 ADT 条件，用来比较公共 Lyapunov 与多 Lyapunov 框架。第三类则换成五维系统，检验该方法是否还能处理高维状态估计。

在二维算例中，系统矩阵来自文献 [36]，输入在任意切换情形取常值 $u(t)=0.5$。作者先用 

$$
\alpha=0.5,\rho=19
$$

 求解观测器，再用 `Theorem 4.2` 计算 $e_d(t)$ 的最小椭球。ADT 算例中则采用



$$
u(t)=0.3\sin(t)-0.2\sin(3t),
$$



并设置 

$$
\alpha=0.5,\rho=330,\eta=2
$$

 设计观测器，再以 $\alpha=0.9$ 计算模式依赖椭球。五维算例主要用来说明本文方法不需要像对比方法那样满足更苛刻的 Metzler 型结构限制。

### 3.2 主要结果与对比说明

![Fig. 5：ADT 情形下误差椭球、其最小 box 与总误差外包络](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/interval-estimation-for-continuous-time-linear-swit-2026-nonlinear-analysis/images/figure_5.png)

`Fig. 5` 给出的不是普通时间序列，而是 ADT 情形下若干时刻的几何包络关系。图中同时画出了真实状态点、外扰驱动误差对应的椭球 $\mathcal{E}(\hat{x}(t),Q(t))$、其最小 box，以及总误差的更大外包络。它证明了一件很关键的事情：本文的两步分解并不是抽象推导，而是确实把“扰动项椭球包络”和“总误差区间包络”区分开了。随着时间演化，总误差 box 会逐渐向由外扰主导的稳态包络收缩，这恰好回应了作者想降低初值传播保守性的初衷。

![Fig. 3：任意切换下与现有方法的区间估计对比](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/interval-estimation-for-continuous-time-linear-swit-2026-nonlinear-analysis/images/figure_3.png)

`Fig. 3` 是任意切换情形下最直接的对比证据。黑线是真实状态，绿色虚线是文献 [34] 的包络，蓝色虚线是本文 Section 4 的区间结果。可以看到，两种方法都覆盖了真实状态，但本文给出的蓝色区间明显更紧，尤其在若干切换瞬间和稳态阶段都保留了更小的上下界间距。因此，这张图支持的不是“方法可用”这种弱结论，而是“在同样可覆盖真实状态的前提下，本文方法显著降低了保守性”。

![Fig. 8：五维系统上的区间估计结果](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@main/2026/interval-estimation-for-continuous-time-linear-swit-2026-nonlinear-analysis/images/figure_8.png)

`Fig. 8` 对应五维算例的五个状态分量区间估计。这里最值得注意的不是某一条曲线更紧，而是对比方法在该例上由于结构条件无法应用，而本文方法仍能正常给出五维状态的上下界包络。从图上看，所有真实轨迹都被包络覆盖，且各维区间宽度没有出现失控膨胀。这说明论文方法除了精度优势之外，还有一个更实用的优点：适用范围比依赖 cooperative / Metzler 结构的方法更广。

把这三张图合起来看，论文的证据链是完整的：`Fig. 5` 展示误差分裂与椭球-box 转换的几何含义，`Fig. 3` 展示在标准二维例子上的精度提升，`Fig. 8` 则展示高维情形下的可扩展性与适用性。

## 4. 后续研究方向
1. 初级想法  
   标题：面向研一 / 研二硕士的参数自动整定区间估计  
   *核心想法：面向研一 / 研二学生，在现有两步框架上把 $\alpha,\rho,\eta$ 的人工迭代搜索改成离线自动整定，并以区间宽度与 LMI 可行性作为联合指标。*  
   数学推导难度：中
2. 高级想法  
   标题：面向博士的切换不确定性与输入饱和联合区间估计  
   *核心想法：面向博士生，把切换信号不可完全测得、执行器饱和和未知输入同时并入误差系统，建立模式依赖 Lyapunov 函数与椭球包络的联合设计条件。*  
   数学推导难度：很高
3. 指导想法  
   标题：面向教授 / 导师的分层选题与论文推进路线  
   *核心想法：建议教授或导师把课题拆成“任意切换收紧分析、ADT 低保守性推广、网络化或攻击下扩展”三层，分别交给不同研究生推进，再以理论稿和应用稿分阶段投稿。*  
   数学推导难度：高

## 5. 总结与评价

这篇论文的核心价值，在于把连续时间切换系统的区间估计问题从“直接传播 box”转成了“鲁棒观测器稳定化 + 误差分裂 + 椭球包络 + 区间外包络”的结构化方案。这样做既保留了椭球方法在线性系统中的矩阵运算优势，又避免了初值与持续外扰混合传播带来的显著保守性。

从技术质量上看，文章最强的部分有两点。第一，它对任意切换与 ADT 两种情形都给出成体系的 LMI 条件，逻辑闭合。第二，它的仿真不仅展示了 tighter interval，也展示了在高维系统和更宽结构条件下的适用性。相应地，它的局限也很明确：当前结果仍是充分条件，保守性不可能完全消失；参数整定也主要依赖离线迭代搜索。但作为一篇围绕 interval estimation、switched systems 和 ellipsoidal analysis 展开的论文，这篇工作已经提供了一条相当清晰、且后续容易扩展的技术模板。
