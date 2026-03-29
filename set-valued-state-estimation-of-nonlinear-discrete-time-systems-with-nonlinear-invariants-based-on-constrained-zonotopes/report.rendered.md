# 基于受约束Zonotope的含非线性不变量的非线性离散时间系统集合值状态估计

![论文抬头：标题与作者](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/header.png)

- 关键词：集合值状态估计；非线性不变量；受约束Zonotope；非线性测量更新；一致性步骤；Taylor扩张
- DOI / 论文链接：https://doi.org/10.1016/j.automatica.2021.109638

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

本文研究离散时间非线性系统的集合值状态估计。目标是在未知但有界的初始误差、过程扰动和测量噪声下，递推构造每个时刻真实状态集合 $X_k$ 的外包络。对这类问题，困难集中在三个环节。

第一，非线性动力学会使可达集传播迅速变保守。第二，非线性测量方程难以像线性情形那样被直接并入一个低复杂度更新公式。第三，很多实际系统还满足守恒律、单位范数约束、几何闭环约束这类**非线性等式不变量**，但已有方法往往没有把这些结构持续地嵌入每一步估计中。

作者针对的正是第三点带来的方法缺口。文中并不满足于在预测和更新之后得到一个“已经可用”的包络，而是进一步引入一个新的 **consistency step**，把不变量 $h(x_k)=0$ 直接转化为对当前集合的再次收缩。对 Zonotope 研究线索而言，这一改动很关键，因为它说明受约束Zonotope不仅能用于传播和测量融合，还能容纳来自系统结构本身的等式信息。

### 1.2 方法框架与核心思路

论文考虑如下非线性离散时间系统：



$$
\begin{aligned}
x_k &= f(x_{k-1}, u_{k-1}, w_{k-1}), \qquad k \ge 1, \\
y_k &= g(x_k, u_k, v_k), \qquad k \ge 0,
\end{aligned}
$$



其中 $x_k \in \mathbb{R}^n$ 为状态，$u_k$ 为已知输入，$w_k \in W$ 为过程不确定性，$v_k \in V$ 为测量不确定性，$y_k$ 为测量输出。初始条件满足 $x_0 \in \bar X_0$。作者进一步假设存在 $C^2$ 函数 $h:\mathbb{R}^n \to \mathbb{R}^{n_h}$，使得所有可行轨迹都满足



$$
h(x_k)=0, \qquad \forall k \ge 0.
$$



这类 $h$ 的分量被称为不变量，典型来源包括质量守恒、动量守恒、单位四元数约束等。

![图1：预测-更新-一致性递推](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/system_model.png)

围绕这一结构，论文将传统的 prediction-update 框架扩展为三步递推：



$$
\bar X_k \supseteq \{f(x_{k-1}, u_{k-1}, w_{k-1}) : x_{k-1}\in \tilde X_{k-1},\, w_{k-1}\in W\},
$$





$$
\hat X_k \supseteq \{x_k \in \bar X_k : g(x_k, u_k, v_k)=y_k,\, v_k\in V\},
$$





$$
\tilde X_k \supseteq \{x_k \in \hat X_k : h(x_k)=0\}.
$$



这里，$\bar X_k$ 是预测集合，$\hat X_k$ 是测量更新后的集合，$\tilde X_k$ 是再经过不变量一致性收缩后的最终估计。这样定义的意义在于：测量信息和不变量信息被分成两个逻辑上不同但都可保证的收缩步骤，后者专门处理“系统结构必须成立”的几何约束。

在系统模型层面，文中的关键假设是：每一步产生的集合都用受约束Zonotope表示，并通过均值定理版本与 Taylor 版本两条技术路线分别构造预测、更新和一致性公式。这样做保留了 Zonotope 在线性映射、Minkowski 和、广义交等运算上的高效性，也把非线性误差控制纳入统一的区间矩阵框架。

### 1.3 主要创新点

**创新点 1：** 在集合值状态估计中正式引入 consistency step，用不变量 $h(x_k)=0$ 对更新后的集合做第三次收缩，并给出均值定理版本与一阶 Taylor 版本的可计算公式。

**创新点 2：** 给出适用于**非线性测量方程**的受约束Zonotope更新算法。此前相关 constrained-zonotope 方法主要处理线性测量更新，本文把非线性输出映射也纳入同一框架。

**创新点 3：** 改进已有 prediction 方法中的近似点选择规则。文中允许近似点从区间包络 $\square X$ 或 $\square Z$ 中选取，而不必强制落在原集合本体中，从而简化实现并在部分情形下降低保守性。

**创新点 4：** 说明如果已知不变量可行域的一个先验外包络，则可先与该外包络求交，再执行 consistency step，进一步缩紧结果。这一工程处理在四元数示例中有直观表现。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

全文的方法核心，是把受约束Zonotope作为整个估计器的统一集合语言。其基本表示为



$$
Z = \{c_z + G_z \omega : \|\omega\|_\infty \le 1,\; A_z \omega = b_z\}.
$$



其中 $G_z$ 是生成元矩阵，$c_z$ 是中心，$A_z \omega=b_z$ 则把普通 zonotope 无法表达的线性耦合关系显式编码进去。这种表示的价值在于：线性映射、Minkowski 和、以及广义交都能在 CG-rep 下以封闭且低代价的方式实现。

![图2：Definition 1，受约束Zonotope定义](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/definition_1.png)

围绕这一表示，论文的整体算法链路可以概括为：

1. 用受约束Zonotope表达上一步状态集、扰动集和噪声集。
2. 在预测端使用均值定理或 Taylor 扩张，把非线性动力学映射包进新的受约束Zonotope。
3. 在更新端把非线性测量方程写成“受约束Zonotope与线性像集的广义交”问题。
4. 在一致性端把不变量 $h(x)=0$ 再写成一次类似的广义交问题。
5. 必要时执行复杂度约简，使生成元数和约束数保持在可控范围。

![图3：Algorithm 1，三步递推算法](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/algorithm_1.png)

Algorithm 1 的作用，不只是把三步写成流程图。它实际上给出了整篇论文的结构依赖关系：预测负责传播动态可行域，更新负责吸收测量数据，一致性步骤负责吸收模型冗余信息。三步都建立在同一种集合表示和同一类广义交运算上，因此整个估计器在数学上是闭合的。

### 2.2 关键技术块解析

#### 技术块 A：Assumption 1 明确不变量是可持续利用的结构信息

作者首先在 **Assumption 1** 中规定：存在函数 $h$，使得所有满足系统方程且初值可行的轨迹都始终满足 $h(x_k)=0$。这个假设不是装饰性的建模补充，而是整篇论文成立的前提，因为 consistency step 的正确性完全依赖于“真实状态永远留在不变量流形上”这一事实。

![图4：Assumption 1，不变量假设](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/assumption_1.png)

这一步建立了一个重要分工：测量方程 $g$ 用来表达“当前观测是否一致”，不变量 $h$ 用来表达“系统结构是否一致”。两者都以等式约束的形式出现，但语义不同，因此也被安排在递推链条中的不同步骤。这种分离让作者能够单独评估“不变量到底带来了多少额外收缩”。

#### 技术块 B：Lemma 1 与 Lemma 2 为三步递推提供共同的非线性线性化工具

文中后续命题都依赖两条基础引理。**Lemma 1** 基于均值定理，给出非线性函数在集合 $X$ 上的精确线性差值表达；**Lemma 2** 基于一阶 Taylor 展开与二阶余项界，给出带 Hessian 区间上界的等价表达。两条引理共同服务于预测、更新和一致性步骤，因此它们属于整篇论文的“公共代数底座”。

![图5：Lemma 1，均值定理型线性表示](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/lemma_1.png)

这里最值得注意的改动，是近似点 $\vartheta_x$ 或 $\vartheta_z$ 可以从区间包络 $\square X$、$\square Z$ 中选择，而不需要强制属于原受约束Zonotope本体。这个变化带来两层收益：

1. 近似点选择不再需要反复求解线性规划做成员性测试，实现上更直接。
2. 当 CG-rep 的中心不属于集合本体但属于区间包络时，新的选点规则仍然有效，因此可能得到更紧的外包络。

因此，这两条引理的角色，不是单纯提供误差界，而是为后续所有命题释放了一个更灵活的近似点选择自由度。

#### 技术块 C：Proposition 3 把非线性测量更新写成受约束Zonotope的广义交

更新步骤的核心结论是 **Proposition 3**。对 $C^1$ 测量方程 $g$，若已知区间 Jacobian $J \supseteq \nabla_x g(\square X, u, V)$，并用一个受约束Zonotope $Z_v$ 包住 $-g(\vartheta_x, u, V)$，则测量可行集



$$
\{x\in X : g(x,u,v)=y,\; v\in V\}
$$



可以被写成



$$
X \cap_C Y
$$



的形式，其中 $C=\tilde J$，而



$$
Y = (y+\tilde J\vartheta_x)\oplus Z_v \oplus \infty(\tilde J-J,\; X-\vartheta_x).
$$



![图6：Proposition 3，非线性测量的均值定理更新](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/proposition_3.png)

这个命题的重要性在于，它把“非线性测量等式”转化成了“线性映射落在某个受约束Zonotope中”的问题。对受约束Zonotope而言，这意味着更新步骤仍然能够用广义交来实现，而不必切换到完全不同的集合类型。换句话说，非线性并没有迫使算法放弃 CZ 的闭合代数结构。

从 proof flow 看，Proposition 3 直接调用 Lemma 1 所给出的精确线性差值表示。Lemma 1 负责把 $g(x,u,v)$ 拆成中心项、线性项与可包络余项，Proposition 3 则把这些项重新组织成一个广义交公式。这个依赖关系决定了 Lemma 1 不是边缘准备，而是更新公式真正可计算的钥匙。

#### 技术块 D：Proposition 4 用二阶信息构造更紧的一阶 Taylor 更新

对 $C^2$ 测量方程，作者进一步给出 **Proposition 4**。它使用 Hessian 区间矩阵 $Q[q]$ 及其由生成元诱导的二次型包络，把二阶余项装入一个新的集合 $R$ 中，并最终仍写成



$$
X \cap_C Y
$$



的广义交形式。

![图7：Proposition 4，一阶Taylor更新公式](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/proposition_4.png)

与 Proposition 3 相比，Proposition 4 的角色可以概括为“代价更高，但在某些结构上明显更紧”。原因在于：当测量函数的二阶结构比较规整时，二阶余项的区间包络可能远比均值定理版本更精确。论文后面的四元数姿态估计算例就属于这一类，测量映射本质上由二次多项式构成，因此 Taylor 版本会展现出显著优势。

这也解释了文中为何始终保留 CZMV-like 与 CZFO-like 两条方法线。作者并没有把二者做成简单的“高低配”关系，而是强调它们是面向不同非线性结构的两种外包络机制。

#### 技术块 E：Corollary 1 与 Corollary 2 把不变量直接转化为一致性收缩

整篇论文最具标识性的结果，是 **Corollary 1** 与 **Corollary 2**。它们将更新步骤中的思路直接迁移到不变量集合



$$
\{x\in X : h(x)=0\}.
$$



对 $C^1$ 情形，Corollary 1 给出



$$
\{x\in X : h(x)=0\} \subseteq X \cap_D H,
$$



其中



$$
H=(\tilde J\vartheta_x-h(\vartheta_x))\oplus \infty(\tilde J-J,\; X-\vartheta_x).
$$



![图8：Corollary 1，均值定理型一致性步骤](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/corollary_1.png)

对 $C^2$ 情形，Corollary 2 则把二阶余项写进集合 $R$，得到 Taylor 版本的一致性收缩公式。

![图9：Corollary 2，一阶Taylor型一致性步骤](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/corollary_2.png)

这两条推论在方法链条中的地位最高，因为它们回答了本文的核心问题：**如何把不变量真正变成递推算法中的一个计算步骤**。若没有这一步，不变量只能作为离线分析信息存在；有了这一步，不变量会在每个时刻主动削减保守性。

从 Zonotope 角度看，这里最值得强调的并不是“又得到了一条收缩公式”，而是收缩后的结果仍然保留在受约束Zonotope框架内。也就是说，不变量没有把估计器拉出 CZ 体系，反而进一步证明了 CZ 对等式结构的适配性。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文给出两个主要例子和一个一致性步骤的二维演示子例。

第一个例子是二维非线性系统，含非线性测量，但不使用不变量。这个例子主要验证新的**非线性测量更新公式**是否有效。对比方法包括传统 zonotope 方法 ZMV、ZFO，以及本文的 CZMV、CZFO。文中将生成元和约束数分别限制为 8 和 3。

第二个例子是飞行机器人姿态估计。状态使用四元数 $x_k\in\mathbb{R}^4$，满足单位范数不变量



$$
\|x_k\|_2^2 = 1,
\qquad
h(x_k)=\|x_k\|_2^2-1=0.
$$



这里比较的方法更加完整，包括 ZMV、ZMV+F、CZMV、CZMV+C、CZMV+F、CZMV+FC，以及相应的 ZFO-like、CZFO-like 版本。记号中，`C` 表示加入 consistency step，`F` 表示在一致性前先与不变量可行域的已知外包络求交。生成元和约束数上限分别取 12 和 5。

在进入完整姿态算例前，作者先用一个二维圆周约束子例说明“先与可行域外包络求交再做一致性收缩”的价值。

![图10：Figure 4，先验可行域对一致性步骤的帮助](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/figure_4.png)

### 3.2 主要结果与对比说明

#### 证据块 A：第一个例子验证了非线性测量更新本身的有效性

在第一个例子中，传统 zonotope 方法 ZMV 与 ZFO 的第一次更新几乎不起作用，后续半径会继续增大并最终失去可用性。相比之下，CZMV 与 CZFO 能够在第一次测量后就明显收缩集合，并保持有界。文中报告的平均每步执行时间分别为：

- ZMV：30.5 ms
- CZMV：65.3 ms
- ZFO：47.4 ms
- CZFO：72.2 ms

因此，这个例子的可靠结论是：仅仅把**更新步骤**改写成受约束Zonotope上的非线性广义交，就足以把原先会发散的估计过程变成可用的估计过程，计算代价处于可接受范围。

#### 证据块 B：Figure 4 说明先验可行域会显著增强 consistency step

Figure 4 展示了二维单位圆约束下的一个直观现象。若直接对初始集合 $X_0$ 施加 Corollary 1，一致性步骤确实会收缩集合，但结果仍然偏保守；若先把 $X_0$ 与已知可行域外包络求交，再施加 Corollary 1，收缩后的结果明显更紧。

这张图的意义在于，它把一个容易被忽略的工程细节说清楚了：consistency step 的效果不仅取决于不变量本身，还取决于进入这一步的集合是否已经被先验几何信息预收缩。对于四元数这类已知必须落在单位球面附近的问题，这一点尤其重要。

#### 证据块 C：Figure 5 表明在均值定理路线中，不变量一致性步骤是决定性因素

![图11：Figure 5，CZMV-like 方法的半径比较](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/figure_5.png)

Figure 5 给出姿态估计算例中 ZMV-like 与 CZMV-like 方法的半径变化。结论非常明确：

- ZMV 与 ZMV+F 的包络半径都会持续增大，无法给出有意义的状态估计。
- CZMV 与 CZMV+F 即使使用受约束Zonotope，也仍然不足以稳定压住误差增长。
- 真正稳定且紧致的结果来自 CZMV+C 与 CZMV+FC，也就是**把一致性步骤显式加入递推之后**。

文中用 ARR（average radius ratio）量化这一优势。以最强的均值定理对比为例，CZMV+C 相对 ZMV+F 的 ARR 只有 **6.7%**，意味着半径大约下降到后者的十五分之一量级；对应平均每步时间从 **0.4610 s** 上升到 **0.5635 s**，增幅仅 **22.2%**。这说明 consistency step 带来的收益远大于其额外成本。

#### 证据块 D：Figure 6 表明一阶 Taylor 路线在四元数例子中更强，而 consistency step 仍然继续收缩

![图12：Figure 6，CZFO-like 方法的半径比较](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/figure_6.png)

Figure 6 展示了 ZFO-like 与 CZFO-like 方法的表现。在这个例子里，CZFO 本身已经能给出相当紧的包络；作者解释其原因在于，测量方程由二次多项式构成，因此 Proposition 4 中的二阶矩阵上界在该问题上特别有利。尽管如此，加入 consistency step 后的 CZFO+C 与 CZFO+FC 仍然继续带来收缩。

具体来说，文中指出：

- CZFO+C 相对 ZFO+F 的 ARR 约为 **5.1%**；
- CZFO+C 的平均每步时间为 **1.1671 s**，相对 ZFO+F 的 **1.0563 s** 仅增加 **10.5%**；
- CZFO+C 相对 CZFO 的 ARR 为 **69.64%**，这说明即便在 Taylor 版本已经很紧的情况下，不变量一致性步骤仍能继续削减约三成半的半径。

#### 证据块 E：Table 3 汇总了各方法之间的整体半径关系

![图13：Table 3，平均半径比 ARR 汇总](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/set-valued-state-estimation-of-nonlinear-discrete-time-systems-with-nonlinear-invariants-based-on-constrained-zonotopes/images/table_3.png)

Table 3 的价值在于，它把图形比较变成了系统性矩阵比较。表中既能看到“传统 zonotope 对比 constrained zonotope”的整体差距，也能看到“是否加入 consistency step”的额外贡献。读这张表时，有两个稳定结论：

1. 在姿态估计算例中，加入不变量一致性步骤后的方法始终位于更优一侧。
2. CZFO-like 方法整体优于 CZMV-like 方法，但并未消除 consistency step 的作用，二者是可叠加的。

因此，实验部分最终支持的判断是：本文的主要改进不是单个局部命题的微调，而是**把非线性测量更新与不变量一致性收缩同时装入同一个受约束Zonotope递推框架**。只有这两部分同时存在时，方法优势才会完整显现。

## 4. 后续研究方向
1. 标题：含松弛误差的不精确不变量一致性估计  
   *核心想法：将 $h(x_k)=0$ 扩展为 $h(x_k,d_k)=0$ 并显式引入额外有界变量，研究不精确守恒律下 consistency step 的稳定性与保守性传播。*  
   数学推导难度：高  
2. 标题：均值定理与Taylor路线的自适应切换机制  
   *核心想法：依据局部 Hessian 尺度、集合直径和实时算力预算，在 CZMV-like 与 CZFO-like 步骤间动态切换，形成在线精度-效率调度器。*  
   数学推导难度：很高  
3. 标题：面向高维几何系统的稀疏受约束Zonotope约简  
   *核心想法：利用四元数、刚体系统和守恒网络中的稀疏结构，设计保结构的生成元与约束削减规则，降低 consistency step 后的复杂度膨胀。*  
   数学推导难度：高  

## 5. 总结与评价

这篇论文的核心贡献，可以概括为：在受约束Zonotope状态估计框架中，引入了一个真正可计算的不变量一致性步骤，并同时补齐了非线性测量更新这一关键模块。这样一来，预测、更新和结构一致性三种信息源都被统一到同一种集合语言中。

从技术细节看，Lemma 1 与 Lemma 2 提供共同的非线性代数工具，Proposition 3 与 Proposition 4 负责非线性测量更新，Corollary 1 与 Corollary 2 则把不变量转化为一致性收缩。这个证明链条层次清楚，且每一步都保持在受约束Zonotope可处理的运算闭包内。

从 Zonotope 专题角度看，本文最重要的意义在于：它证明了受约束Zonotope并不是一个只能做“传播和包络”的被动容器，而是一个能够同时吸收测量等式、结构等式和复杂度约简需求的主动几何接口。对于所有带守恒律、单位范数约束、闭环几何约束的非线性系统，这篇论文都提供了一个很有代表性的技术模板。
