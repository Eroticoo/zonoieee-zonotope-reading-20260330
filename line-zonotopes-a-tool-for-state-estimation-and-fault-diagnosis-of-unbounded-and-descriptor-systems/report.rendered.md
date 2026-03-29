# 线Zonotope：无界与描述符系统状态估计及故障诊断工具

![论文抬头：标题与作者](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/header.png)

- 关键词：线Zonotope；受约束Zonotope；描述符系统；无界集合；状态估计；主动故障诊断；可达管
- DOI / 论文链接：https://doi.org/10.1016/j.automatica.2025.112380

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

本文针对线性描述符系统（LDS）的两个核心任务展开：**集合值状态估计**与**主动故障诊断（AFD）**。描述符系统的难点在于，系统既包含动态方程，也包含由奇异矩阵 $E$ 引入的静态代数约束；同时，在不稳定或部分不可观的情况下，系统状态集往往天然呈现无界性。已有的区间、椭球和普通 zonotope 方法在这种场景下要么保守，要么无法直接嵌入静态约束。

受约束Zonotope（CZ）已经能够处理线性等式约束，因此比普通 zonotope 更适合描述符系统。然而，作者指出，CZ 方法通常仍要求一个**先验有界的 admissible set** 在整个实验区间内包住所有状态轨迹。对不稳定系统而言，这个假设往往不现实。一旦真实轨迹离开先验有界域，CZ 方法就会失效或退化为空集。

本文的核心回应是引入**线Zonotope（line zonotope, LZ）**。它在受约束Zonotope 的基础上加入无界“线”方向，因而能同时表示 strips、hyperplanes 以及整个 $\mathbb{R}^n$。这一步并非只是几何扩展，而是直接解决了“描述符系统存在静态约束，同时状态包络可能无界”这一结构性矛盾。

### 1.2 方法框架与核心思路

论文考虑如下线性离散时间描述符系统族：



$$
\begin{aligned}
E^{[i]} x_k^{[i]} &= A^{[i]} x_{k-1}^{[i]} + B^{[i]} u_{k-1} + B_w^{[i]} w_{k-1}, \\
y_k^{[i]} &= C^{[i]} x_k^{[i]} + D^{[i]} u_k + D_v^{[i]} v_k,
\end{aligned}
$$



其中，$x_k$ 为状态，$u_k$ 为输入，$w_k$ 和 $v_k$ 分别为过程与测量不确定性，模型索引 $i \in \{1,\dots,n_m\}$ 对应不同故障模式。若 $E^{[i]}$ 奇异，则系统含有静态约束。

![图1：描述符系统模型与问题定义](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/system_model.png)

通过对 $E$ 做奇异值分解并进行坐标变换，作者把系统重写为动态部分与静态部分耦合的形式：



$$
\tilde z_k = \tilde A z_{k-1} + \tilde B u_{k-1} + \tilde B_w w_{k-1},
$$





$$
0 = \check A z_k + \check B u_k + \check B_w w_k,
$$





$$
y_k = C^T z_k + D u_k + D_v v_k.
$$



这里，$\tilde z_k$ 对应可递推的动态部分，$\check z_k$ 对应描述符系统的静态一致性约束。整篇论文的主线就是：在这个变换后的状态空间中，用线Zonotope 统一表示无界可行域、静态约束和输出可达管，再把这种表示同时用于状态估计和故障诊断。

### 1.3 主要创新点

**创新点 1：** 论文提出线Zonotope，作为受约束Zonotope 的无界扩展，使描述符系统中的 strips、hyperplanes 和整个状态空间都能被统一地写进 CLG-rep。

**创新点 2：** 论文给出 line elimination、constraint elimination 和 generator reduction 的配套复杂度控制机制，其中 line elimination 是**等价变换**而非保守外近似，这一点对递推估计非常关键。

**创新点 3：** 论文把 LZ 同时应用到两类任务：一是无 admissible set 条件下的 LDS 状态估计，二是基于**output reachable tubes** 的主动故障诊断，使诊断判据不再依赖最终时刻的可达输出集，而是使用整个输出序列。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

本文的技术路线可以归纳为三段。

第一段，构造新的集合表示。作者从 CZ 出发，引入“线”变量 $\delta$，把有界生成元和无界方向同时纳入统一表示。

第二段，构造针对 LDS 的状态估计器。通过变换后的状态空间，把动态递推和静态一致性都嵌入线Zonotope 约束，从而在无初始有界域的前提下仍可递推得到有效包络。

第三段，把故障诊断对象从最终输出可达集推广到**输出可达管（output reachable tubes, ORTs）**。借助线Zonotope 对无界集和线性约束的处理能力，作者把 AFD 的“分离所有可能输出管”转成一个 line-zonotope 非成员条件。

从 Zonotope 角度看，这篇论文的突破点很明确：CZ 已经解决了“静态等式约束无法进入普通 zonotope”的问题，而 LZ 进一步解决了“状态或输出集合本身可能无界，因而 CZ 仍不够用”的问题。

### 2.2 关键技术块解析

#### 技术块 A：Definition 2 给出线Zonotope 的 CLG-rep

![图2：Definition 2，线Zonotope定义](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/definition_2.png)

Definition 2 定义了线Zonotope：



$$
Z = \{ c_z + M_z \delta + G_z \xi : \delta \in \mathbb{R}^{n_\delta},\; \|\xi\|_\infty \le 1,\; S_z \delta + A_z \xi = b_z \}.
$$



其中，$G_z$ 的列仍是传统意义上的生成元，对应有界线段；$M_z$ 的列则是**无界直线方向**。这意味着 LZ 把 CZ 的“有界几何骨架”扩展成“有界生成元 + 无界方向 + 统一约束”的混合表示。

这一点对 Zonotope 研究尤其重要。普通 zonotope 只能表达中心对称有界集合；CZ 则通过等式约束提升到更一般的有界凸多面体；LZ 进一步把无界性纳入同一个生成元框架。也就是说，LZ 不再把“无界”当作算法外的特殊情况，而是把它变成集合表示本身的一等公民。

#### 技术块 B：Proposition 1 证明 LZ 可以表示 $\mathbb{R}^n$、strip 与 hyperplane

![图3：Proposition 1，LZ 的几何表达能力](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/proposition_1.png)

Proposition 1 给出的不是一个边角结论，而是 LZ 理论成立的最关键论据之一：$\mathbb{R}^n$、strip 和 hyperplane 都是 LZ。特别是 strip



$$
S = \{ x \in \mathbb{R}^n : |\rho_s^T x - d_s| \le \sigma_s \}
$$



能被写成 LZ，这使得输出一致性集合、约束平面以及部分不可观方向的无界包络都可以落在同一个框架内。对描述符系统与 AFD 而言，这一步非常关键，因为两者都离不开由线性关系定义的条带或超平面集合。

换言之，Proposition 1 证明的不只是“LZ 能表示更多集合”，而是它恰好能表示本文后续状态估计和故障诊断真正需要的那类无界集合。

#### 技术块 C：Algorithm 1 的 line elimination 是等价变换，不引入保守性

![图4：Algorithm 1，线消除过程](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/algorithm_1.png)

Algorithm 1 是本文最值得关注的 Zonotope 机制之一。作者通过求解约束 $S\delta + A\xi = b$ 中某个 line 变量 $\delta_j$，再把它回代到集合表达式中，从而一次消去一条 line。与 generator elimination 不同，line 变量本身是无界的，因此这种消除不会丢失有界信息，也不会引入额外外近似。

论文进一步指出，最多可以消去 `rank(S_z)` 条 line，并同步消去相同数量的约束。如果所有 line 都能被消掉，LZ 就退化回 CZ。这一点意义很大：它说明 LZ 并不是与 CZ 并列且割裂的新对象，而是一个可在适当条件下**连续退化回 CZ** 的更大类集合。

从复杂度角度，作者也明确给出：每次 line elimination 的复杂度是多项式级别，而且比 generator reduction 更便宜。这一点后面在实验中直接转化成 LZ 相比 CZ 的计算优势。

#### 技术块 D：Lemma 1 给出描述符系统预测步的 LZ 包络

![图5：Lemma 1，描述符系统预测包络](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/lemma_1.png)

Lemma 1 是状态估计部分的核心结果。它在变换后的状态空间中，把上一时刻的 LZ 估计 $\hat Z_{k-1}$ 与当前、下一时刻过程不确定性一起组合成新的预测集 $\bar Z_k$。其关键不在于公式块本身有多复杂，而在于：

1. 静态约束 $0 = \check A z_k + \check B u_k + \check B_w w_k$ 被直接嵌入 CLG-rep；
2. 预测集中的 lines、generators 和 constraints 数量增长规律被显式控制；
3. 该构造不依赖先验 bounded admissible set。

对 Zonotope 视角而言，这个引理说明：LZ 不只是“会表示无界集合”，而是确实能和描述符系统的递推结构对接，形成完整的 prediction-update 算法。

#### 技术块 E：Theorem 1 把 AFD 的可分离性转写为 line-zonotope 非成员判据

![图6：Theorem 1，AFD 中的分离输入判据](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/theorem_1.png)

Theorem 1 是故障诊断部分的核心。它给出一个等价判据：输入序列 $\vec u$ 是可行分离输入，当且仅当对所有模型对 $(i,j)$，某个关于 $\vec u$ 的线性映射结果不属于相应的 line zonotope $\vec Y^{(q)}$。这里的 $\vec Y^{(q)}$ 本质上编码了两条输出可达管之间的差集结构。

这条定理的价值在于，它把“输出可达管互不相交”的集合分离问题，转成了一个可以通过线性规划检验的 LZ 非成员问题。由于 ORTs 本身可能无界，CZ 在这里往往难以直接处理，而 LZ 的无界表示能力正好匹配这个任务。

需要特别指出的是，论文此处不仅把集合从 final reachable set 扩展到 reachable tube，也把诊断证据从“终点是否分离”扩展成“整个输出序列是否分离”。这在诊断保守性上是实质性改进。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文给出两组实验：

- **状态估计实验**：比较 Zonotope、CZ 和 LZ 对 LDS 状态包络的效果；
- **主动故障诊断实验**：比较 AFDLZ 与 AFDCZ 对不同故障模型输出可达管的分离能力。

其中，状态估计实验重点检验“不稳定系统 + 无先验有界域”这一场景下谁还能持续给出有效包络；AFD 实验则重点检验 reachability over tubes 是否比只看最终时刻集合更有效。

### 3.2 主要结果与对比说明

#### 证据块 A：Fig. 2 说明 LZ 在不稳定描述符系统上优于 CZ

![图7：Fig. 2，状态估计半径与投影比较](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/figure_2.png)

Fig. 2 比较了 Zonotope、CZ 和 LZ 在 $k \in [0, 100]$ 上的状态包络半径，以及对状态分量 $x_3$ 的投影。作者指出，CZ 与 LZ 都显式利用了描述符系统的静态约束，因此都显著优于普通 zonotope。然而，CZ 方法依赖 admissible set $X_A$，在不稳定系统中，当真实状态轨迹离开 $X_A$ 后就会在 $k \ge 25$ 产生空集；LZ 则不需要这样的先验有界集合，因此仍能继续给出有效包络。

文中还给出一组很有代表性的时间数据：在 $k \in [1,21]$ 上，Zonotope、CZ 和 LZ 的平均计算时间分别约为 **0.37 ms、2.43 ms 和 2.02 ms**。这说明 LZ 不仅解决了 CZ 的适用域限制，还因为 line elimination 比 generator reduction 更便宜，而在该例中比 CZ 更快。

#### 证据块 B：Table 1 显示 AFDLZ 能完全分离所有输出可达管

![图8：Table 1，输出可达管交集结果](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/table_1.png)

Table 1 汇总了不同输入序列下各模型对之间 ORTs 的交集情况。文中说明：

- 参考输入序列 $\tilde u$ 不能分离任何 ORT；
- AFDCZ 只能分离少数模型对；
- AFDLZ 设计出的输入 $\vec u_{\mathrm{AFDLZ}}$ 可以使所有模型对的 ORTs 全部互不相交。

这张表的重要性在于它直接验证了 Theorem 1 的设计目标。AFDCZ 的失败并不只是“输入没调好”，而是因为某些模型下状态集本身无界，违反了 CZ 方法所需的有界性假设。LZ 通过对无界 ORTs 的直接建模，才使得“可保证分离”成为可能。

#### 证据块 C：Fig. 3 直观展示了 AFDLZ 设计出的 ORTs 在整个时间域上分离

![图9：Fig. 3，不同模型下的输出可达管投影](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@f481fedf93d2e2510420e3ad3bb00c75869f3516/line-zonotopes-a-tool-for-state-estimation-and-fault-diagnosis-of-unbounded-and-descriptor-systems/images/figure_3.png)

Fig. 3 给出注入 $\vec u_{\mathrm{AFDLZ}}$ 后，各模型在 $k \in \{1,2,3\}$ 上的输出可达管投影。图中不同颜色的 ORTs 相互分离，说明诊断依据不是单一终点，而是整个输出时间序列。作者进一步指出，即便把 CZ 所需的有界域 $X_A$ 取得很大，也无法保证其覆盖所有故障模式下的轨迹，尤其在含不稳定模型时更是如此；因此 AFDCZ 在该问题上原则上不可靠，而 AFDLZ 可以提供保证性诊断。

## 4. 后续研究方向
1. 标题：面向非线性描述符系统的线Zonotope 扩张  
   *核心想法：把本文的 LZ 表示与非线性 Jacobian / Hessian 包络结合，构造适用于非线性描述符系统的无界集合预测-更新框架。*  
   数学推导难度：很高  
2. 标题：闭环主动故障诊断中的 tube 反馈设计  
   *核心想法：将当前开环输入分离设计扩展到闭环 tube-based AFD，使输入在线依赖当前 LZ 状态估计与历史输出，以缩短分离时域并降低输入能量。*  
   数学推导难度：高  
3. 标题：稀疏大规模网络描述符系统的结构化 line elimination  
   *核心想法：针对大规模稀疏系统中的块结构约束，设计保结构的 line elimination 与 generator reduction 规则，避免通用降阶带来的额外耦合膨胀。*  
   数学推导难度：高  

## 5. 总结与评价

这篇论文的根本贡献在于，它不再把“无界集合”视为 set-based 估计与诊断中的例外，而是构造出一个能够把无界性、静态约束和生成元结构统一起来的新集合类。线Zonotope 继承了受约束Zonotope 的约束表达能力，又通过 line 变量把 strips、hyperplanes 和整个空间纳入统一表示域。

从技术链条看，Definition 2 给出 LZ 表示，Proposition 1 建立其几何表达能力，Algorithm 1 提供无保守性的 line elimination，Lemma 1 将其接入描述符系统状态估计，Theorem 1 则将其接入基于 output reachable tubes 的 AFD。整条链路非常完整。

从 Zonotope 发展脉络看，本文的意义尤其明确：普通 zonotope 擅长有界对称集，CZ 擅长有界含约束集，而 LZ 则进一步覆盖**无界含约束集**。因此，这篇工作不只是为描述符系统加了一个特例工具，而是把 Zonotope 家族向更一般的集合计算场景推进了一步。
