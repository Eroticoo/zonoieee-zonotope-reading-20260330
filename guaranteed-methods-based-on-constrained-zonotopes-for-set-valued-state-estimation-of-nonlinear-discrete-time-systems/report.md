# 基于受约束Zonotope的非线性离散时间系统集合值状态估计保证性方法

![论文抬头：标题与作者](__PUBLIC_IMAGE_PREFIX__/header.png)

- 关键词：受约束Zonotope；集合值状态估计；非线性离散时间系统；均值扩张；一阶Taylor扩张；可达集传播；广义交集
- DOI / 论文链接：https://doi.org/10.1016/j.automatica.2019.108614

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

本文面向**未知但有界不确定性**条件下的非线性离散时间系统状态估计问题。作者关心的不是单点最优估计，而是如何在每个时刻构造一个**保证包含真实状态**的集合估计器。对这类问题，递推估计天然分为两步：先把上一时刻的可行状态集通过非线性动力学传播出去，再把该预测集与当前测量一致性集合做交集。难点在于，这两步分别要求处理**非线性映射下的外包络**与**集合交集的精确表示**。

现有集合表示存在明显折衷：区间、椭球、平行多面体和普通 zonotope 计算便宜，但在非线性传播或测量更新时往往保守；一般凸多面体表达能力强，但顶点表示与半空间表示之间的转换成本很高。本文的切入点非常明确：在尽量保留 zonotope 低计算负担的同时，引入能够**精确表示交集**的受约束Zonotope，从而同时改善 prediction 和 update 两个环节的保守性。

### 1.2 方法框架与核心思路

论文考虑如下非线性离散时间系统：

$$
\begin{aligned}
x_k &= f(x_{k-1}, u_{k-1}, w_{k-1}), \\
y_k &= C x_k + D_u u_k + D_v v_k,
\end{aligned}
$$

其中，$x_k \in \mathbb{R}^n$ 为系统状态，$u_k$ 为已知输入，$w_k$ 为过程扰动，$y_k$ 为测量输出，$v_k$ 为测量不确定性。文中假设 $f$ 属于 $C^2$ 类，且 $w_k \in W_k$、$v_k \in V_k$ 都满足有界集合约束。

![图1：系统模型与问题定义](__PUBLIC_IMAGE_PREFIX__/system_model.png)

对应的集合值递推目标是构造预测集 $\bar{X}_k$ 与更新集 $\hat{X}_k$，满足：

$$
\bar{X}_k \supseteq \{ f(x, u_{k-1}, w) : x \in \hat{X}_{k-1},\; w \in W_{k-1} \},
$$

$$
\hat{X}_k \supseteq \{ x \in \bar{X}_k : Cx + D_u u_k + D_v v = y_k,\; v \in V_k \}.
$$

本文的整体思路可以概括为三层：

第一层，用**受约束Zonotope**代替普通 zonotope 作为集合描述对象，使线性映射、Minkowski 和以及广义交集都能在统一的 CG-rep（constrained generator representation）下处理。

第二层，在 prediction 步针对非线性映射分别构造两条保证型传播链：一条基于**均值定理**，一条基于**一阶 Taylor 展开与二阶余项界**。两条链分别形成 CZMV 和 CZFO 两种 constrained-zonotope 估计器。

第三层，在 update 步利用受约束Zonotope 对广义交集的闭包性，直接精确实现

$$
\hat{X}_k = \bar{X}_k \cap_C \bigl((y_k - D_u u_k) \oplus (-D_v V)\bigr),
$$

从而避免普通 zonotope 在测量更新中对交集做外近似带来的对称性误差和累积保守性。

### 1.3 主要创新点

**创新点 1：** 论文把针对普通 zonotope 的非线性传播思想，系统推广到**受约束Zonotope**框架，给出了均值扩张与一阶 Taylor 扩张两套新的保证型外包络方法。

**创新点 2：** 论文围绕 Zonotope 的核心瓶颈进行了针对性修补。普通 zonotope 在线性映射和 Minkowski 和上很高效，但对交集并不闭合；受约束Zonotope 通过在线性生成变量上加入等式约束，保留了生成元结构，又获得了更强的凸多面体表达能力。

**创新点 3：** 论文不仅给出主定理，还进一步给出**选点策略**与**复杂度分析**。特别是 Corollary 1 和后续 LP 选点规则，直接针对 Theorem 2 的保守性来源动手术，这使得方法不仅“可证”，而且“可调”。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

论文的技术主线可以压缩为如下链条：

1. 用受约束Zonotope表示当前状态集 $X$ 和扰动集 $W$。
2. 先建立 interval matrix 与 constrained zonotope 乘积的 **CZ-inclusion**。
3. 在此基础上构造 **Theorem 2** 的均值扩张，将非线性映射转化为“基点值 + 导数区间作用于偏移量”的受约束Zonotope 包络。
4. 再构造 **Theorem 3** 的一阶 Taylor 扩张，将 Hessian 余项严格包进一个新的受约束Zonotope 余项集合。
5. 在测量更新阶段，直接用受约束Zonotope 的广义交集公式完成精确更新。

其中，`Zonotope` 在本文中不是背景名词，而是贯穿整个方法设计的几何骨架。作者真正关心的是：如何在保持生成元表示高效性的同时，把普通 zonotope 对交集和复杂几何形状的表达弱点补上。受约束Zonotope 的 CG-rep 正是这个桥梁：

$$
Z = \{ c_z + G_z \omega : \|\omega\|_\infty \le 1,\; A_z \omega = b_z \}.
$$

这里，$G_z$ 的列向量给出生成元，$c_z$ 是中心，$A_z \omega = b_z$ 则把普通 zonotope 的“自由超立方体像”收紧成一个受线性等式约束的子集。这个结构带来两点对本文至关重要的收益：

- 它仍保留了 zonotope 的生成元线性代数结构，便于传播和降阶。
- 它能表达任意凸多面体，因此在 update 步对交集的表达明显强于普通 zonotope。

### 2.2 关键技术块解析

#### 技术块 A：Definition 1 给出受约束Zonotope 的基本表示

![图2：Definition 1，受约束Zonotope定义](__PUBLIC_IMAGE_PREFIX__/definition_1.png)

Definition 1 是全文最基础的几何对象定义。它说明受约束Zonotope 并非完全抛弃 zonotope，而是在 zonotope 的生成变量 $\omega$ 上加入等式约束。对本文而言，这一步的意义不只是“定义一个更复杂的集合”，而是把**普通 zonotope 的低成本生成元参数化**与**凸多面体的高表达能力**拼接到一起。

从 Zonotope 视角看，这一定义解决了两个关键问题。首先，普通 zonotope 天然关于中心对称，而真实测量更新后的可行集通常不保持这种对称性；加入 $A_z \omega = b_z$ 后，集合形状可以偏离对称结构。其次，更新步往往需要做交集，而交集正是普通 zonotope 的弱项。本文后续所有结果，包括精确广义交集、均值扩张和 Taylor 余项封装，都是建立在这一定义之上的。

#### 技术块 B：Theorem 1 构造 CZ-inclusion，是 Theorem 2 的直接支撑

![图3：Theorem 1，CZ-inclusion](__PUBLIC_IMAGE_PREFIX__/theorem_1.png)

Theorem 1 解决的问题是：当一个**区间矩阵** $J$ 作用在一个受约束Zonotope $X$ 上时，如何得到一个仍然是受约束Zonotope 的可靠外包络。它给出的结构是

$$
S \subseteq \mathcal{I}(J, X) \triangleq \mathrm{mid}(J) X \oplus P B_\infty^n.
$$

这里的技术本质是把不确定矩阵分成“中值项 + 半径项”。中值项仍作用在原集合上，半径项则被压缩进一个对角矩阵 $P$ 作用的 box 中。这个定理在证明层面提供的是一个**矩阵不确定性到几何包络的不等式桥梁**；在工程层面，它提供的是一个可以被后续非线性传播直接调用的工具。

它之所以重要，是因为 Theorem 2 在均值定理展开后，会得到一个形如 $\hat{J}(x-h)$ 的项。如果没有 Theorem 1，这个项仍然落在“区间矩阵乘集合”的难处理区域里。换言之，Theorem 1 不是附属结果，而是 Theorem 2 的技术骨架。

#### 技术块 C：Theorem 2 给出基于均值定理的受约束Zonotope 扩张

![图4：Theorem 2，均值扩张](__PUBLIC_IMAGE_PREFIX__/theorem_2.png)

Theorem 2 是本文的第一条主结果。它把非线性映射 $\mu(X, W)$ 的外包络写成

$$
\mu(X, W) \subseteq Z \oplus \mathcal{I}(J, X-h),
$$

其中，$Z$ 包含基点 $h$ 处随扰动 $W$ 变化的像集 $\mu(h, W)$，$J$ 则是状态导数 $\nabla_x \mu(X, W)$ 的区间包络。证明逻辑非常清楚：先对每个分量应用均值定理，再把导数不确定性塞进区间矩阵，最后调用 Theorem 1 的 CZ-inclusion 完成封装。

从 Zonotope 细节看，Theorem 2 的价值有三层：

第一，它把普通 zonotope 上已有的 mean value extension 一致地推广到了 constrained zonotope，说明 CG-rep 并没有破坏这一类传播思路。

第二，它把 prediction 步中最难的非线性传播问题，降解为“基点值集合 $Z$ + 导数区间乘偏移集”的组合问题，从而能持续留在受约束Zonotope 算法域内。

第三，它为后续 update 步留下了结构优势。因为 prediction 结果本身已经是 constrained zonotope，更新时可以直接走精确广义交集，而无需先退化为近似 zonotope 再做外近似交集。

#### 技术块 D：Corollary 1 通过选点 $h$ 直接压缩 Theorem 2 的保守性

![图5：Corollary 1，均值扩张的紧化选点规则](__PUBLIC_IMAGE_PREFIX__/corollary_1.png)

Corollary 1 讨论的是一个非常实际的问题：Theorem 2 中参考点 $h \in X$ 该怎么选。作者分析了 iterated constraint elimination 之后的中心项 $c^{(0)}$，并指出如果选择

$$
h = c_x + \sum_{\ell=1}^{n_c} \left(\bar{G}^{(\ell)} \omega_m^{(\ell)} + G''^{(\ell)} \tilde{b}^{(\ell)}\right),
$$

就可以令约束消除后所得 zonotope 的中心对齐到零，从而减小区间项 $m$，进一步减小 Theorem 1 中对角 box 项的半径。这一步直接瞄准了保守性来源，而不是简单地“换个中心再试试”。

对 Zonotope 方法而言，这个结论很关键。普通 zonotope 常常把“取中心”当成默认操作，但在 constrained zonotope 中，CG-rep 的中心未必属于集合本身，因此“中心点”不再是天然合理的线性化点。Corollary 1 说明：一旦进入受约束Zonotope 框架，线性化点选择本身就变成一个可优化的几何问题。

#### 技术块 E：Theorem 3 给出一阶 Taylor 扩张及二阶余项的严格受约束Zonotope 封装

![图6：Theorem 3，一阶Taylor扩张](__PUBLIC_IMAGE_PREFIX__/theorem_3.png)

Theorem 3 是第二条主结果。与 Theorem 2 不同，它不再只用一阶导数区间，而是从一阶 Taylor 展开出发，把二阶余项通过 Hessian 区间界和生成元二次型封装成一个新的 remainder set $R$，从而得到

$$
\varsigma(Z) \subseteq \varsigma(h) \oplus \nabla \varsigma(h) (Z-h) \oplus R.
$$

这条结果的贡献主要体现在两个方面。其一，它给出了一个**严格带余项的非线性传播定理**，因此相较简单线性化更有理论完整性。其二，它展示了受约束Zonotope 对二次项结构的适配能力：作者并没有把余项粗暴塞进一个大 box，而是利用生成元坐标、Hessian 区间、对角块和附加约束拼出一个更精细的 remainder representation。

从方法比较上看，Theorem 3 并不自动优于 Theorem 2。论文的仿真也表明，两者的优劣依赖于动力学形状、集合尺度和复杂度限制。对 Zonotope 研究者来说，这一点很重要：本文并未把 Taylor 扩张包装成绝对优势方法，而是把它放在与 mean value extension 并列的、可根据几何场景选择的第二条传播路线中。

#### 技术块 F：更新步的核心优势来自受约束Zonotope 对广义交集的精确表示

在 update 步，作者利用线性测量模型直接写出

$$
\hat{X}_k = \bar{X}_k \cap_C \bigl((y_k - D_u u_k) \oplus (-D_v V)\bigr).
$$

这一步是全文最能体现“为什么必须是 constrained zonotope”的地方。对于普通 zonotope，交集通常需要额外外近似，因此误差会在时间递推中累积；对受约束Zonotope，广义交集可以在 CG-rep 中通过线性代数公式直接处理。也就是说，本文真正压低保守性的根源并不只在于 prediction 传播更精细，更在于**prediction 与 update 两步终于落在了同一个闭合的集合计算体系里**。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文给出两组数值实验。第一组是二维非线性离散系统，重点检验 prediction 与 update 的几何保守性；第二组是 12 维四旋翼 UAV 离散模型，重点检验方法在高维非线性系统上的有效性。对比对象包括：

- **CZMV**：基于 Theorem 2 的 constrained-zonotope mean value estimator；
- **CZFO**：基于 Theorem 3 的 constrained-zonotope first-order estimator；
- **ZMV / ZFO**：对应的普通 zonotope 版本。

文中不仅比较可行集半径，还比较复杂度限制下的平均半径比（ARR）与每步计算时间，因此证据并非单一几何图，而是“集合形状 + 半径统计 + 计算代价”的组合。

### 3.2 主要结果与对比说明

#### 证据块 A：Fig. 2 直接展示 update 交集的几何优势

![图7：Fig. 2，初始更新步中的交集效果](__PUBLIC_IMAGE_PREFIX__/figure_2.png)

Fig. 2 展示了初始集合 $X_0$、不确定测量条带以及更新后的交集结果。作者比较了 Bravo 等人方法得到的 zonotope 交集外近似与式 (22) 生成的 constrained zonotope 交集。结论非常明确：由于广义交集后的集合不再保持中心对称，普通 zonotope 无法精确表示该几何对象，而 constrained zonotope 可以直接给出精确交集。这个图实际上是全文最强的 Zonotope 证据之一，因为它把“普通 zonotope 更新保守”这一点从代数层面落实到了几何层面。

#### 证据块 B：Fig. 3 显示前四步状态估计包络明显收紧

![图8：Fig. 3，前四个时刻的集合估计对比](__PUBLIC_IMAGE_PREFIX__/figure_3.png)

Fig. 3 比较了无过程扰动场景下 CZMV 与 ZMV 在前四个时刻的 update 后集合。黑点表示满足当前测量的一致样本轨迹。可以看到，CZMV 生成的集合更贴近样本轨迹外缘，而 ZMV 包络更松。这个结果说明 Theorem 2 带来的收益并不只体现在单步可达集传播上，而是能在递推估计中持续保留。对集合估计问题而言，这种“多步之后仍能维持紧包络”的能力比单步漂亮图更重要。

#### 证据块 C：Fig. 4 用 ARR 量化了 constrained zonotope 相对普通 zonotope 的保守性下降

![图9：Fig. 4，二维案例中的半径比较](__PUBLIC_IMAGE_PREFIX__/figure_4.png)

Fig. 4 给出二维系统在 100 个时刻上的 update 集半径比较。论文报告：

- CZMV 相对 ZMV 的平均半径比（ARR）约为 **51.4%**；
- CZFO 相对 ZFO 的 ARR 约为 **53.66%**；
- CZFO 与 CZMV 的 ARR 约为 **98.75%**。

这组数据说明两点。第一，换用 constrained zonotope 后，集合半径几乎缩小到普通 zonotope 的一半量级，收益非常显著。第二，在这个二维案例中，Theorem 2 与 Theorem 3 的效果非常接近，说明更新步的精确交集能力与 prediction 步的 tighter enclosure 共同决定了最终效果，而不是某一条传播公式单独支配全部结果。论文同时给出 Table 3 的时间对比，也表明 CZFO 尤其在约束数增加时更昂贵，因此精度收益与计算代价之间仍有明显折衷。

#### 证据块 D：Fig. 6 说明方法在 12 维四旋翼系统上仍保持优势

![图10：Fig. 6，四旋翼系统中的半径比较](__PUBLIC_IMAGE_PREFIX__/figure_6.png)

第二个例子把系统扩展到 12 维四旋翼 UAV。Fig. 6 比较 CZMV 与 ZMV 的 update 集半径。论文给出的结论是，尽管两者都能为高维非线性状态提供有界外包络，但 CZMV 的保守性仍明显更低，其相对 ZMV 的 ARR 约为 **74.41%**。这个数字没有二维算例中那样激进，但它在高维系统中更有说服力，因为高维情况下集合复杂度、非线性耦合和降阶误差都更难控制。

从 Zonotope 视角看，这里最有价值的信息是：受约束Zonotope 的优势并没有随着维度升高而完全被复杂度增长吞掉。它仍然能在受限生成元与受限约束数下维持较紧的 interval hull。

#### 证据块 E：Fig. 7 表明 CZFO 在高维场景下同样优于 ZFO

![图11：Fig. 7，四旋翼系统中 CZFO 与 ZFO 的比较](__PUBLIC_IMAGE_PREFIX__/figure_7.png)

Fig. 7 给出四旋翼场景下 CZFO 与 ZFO 的 update 集半径对比。作者报告 CZFO 相对 ZFO 的 ARR 约为 **74.45%**，而 CZMV 与 CZFO 的 ARR 约为 **99.93%**。这意味着在该案例中，两种 constrained-zonotope 方法的最终精度几乎重合；真正拉开差距的仍然是 constrained zonotope 与普通 zonotope 之间在集合表达能力上的差别。

论文同时指出，当前 MATLAB 实现下，高维案例的计算时间已经超过采样时间，这限制了其直接在线部署。但这并不削弱本文的核心结论：**从集合几何精度上看，受约束Zonotope 框架确实为非线性集合值估计提供了更强的表达与更新能力**。后续工程化工作主要应集中在结构化降阶、稀疏实现和实时求解加速上。

## 4. 后续研究方向
1. 标题：结合非线性不变量的受约束Zonotope更新机制  
   *核心想法：在本文精确广义交集的基础上，把非线性等式约束或不变量也编码进 CG-rep，使 measurement update 不再只依赖线性测量条带，而能显式融合系统内在守恒结构。*  
   数学推导难度：高  
2. 标题：面向高维系统的结构化 Hessian 余项压缩  
   *核心想法：针对 Theorem 3 中余项集合 $R$ 的生成元与约束快速膨胀问题，引入 Hessian 稀疏性、块对角结构或低秩近似，构造带误差证书的 remainder reduction。*  
   数学推导难度：很高  
3. 标题：分布式多传感器场景下的 constrained-zonotope 融合估计  
   *核心想法：把本文 prediction-update 框架推广到多传感器异步测量与分布式融合场景，研究局部受约束Zonotope 与全局一致集合之间的可证明融合规则。*  
   数学推导难度：高  

## 5. 总结与评价

这篇论文的核心贡献不在于单独提出某个估计器名字，而在于把**非线性传播、集合更新与 Zonotope 几何表示**统一到了一个更强的 constrained-zonotope 框架中。对普通 zonotope 而言，真正棘手的是交集与复杂非对称几何；本文通过 CG-rep 使这些问题能够在统一代数形式下处理，从而显著降低了集合值估计的保守性。

从理论链条看，Definition 1 给出几何基础，Theorem 1 提供区间矩阵乘集合的 CZ-inclusion，Theorem 2 与 Theorem 3 分别给出两条非线性传播路线，Corollary 1 则进一步把保守性压缩问题转化为可计算的选点问题。这条主线清晰而完整。

从数值结果看，二维案例里 CZMV / CZFO 相比 ZMV / ZFO 的平均半径大约降到一半；四旋翼高维案例中仍能保持约四分之一量级的改进。这些证据足以支持本文的主张：**在集合值状态估计中，受约束Zonotope 相比普通 zonotope 的关键优势，来自其对交集和复杂凸集合的高保真表达能力，而非单纯增加了若干约束变量。**
