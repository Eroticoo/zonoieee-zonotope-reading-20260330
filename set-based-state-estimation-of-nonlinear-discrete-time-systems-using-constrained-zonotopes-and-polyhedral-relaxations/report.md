# 基于受约束Zonotope与多面体松弛的非线性离散时间系统集合值状态估计

![论文抬头：标题与作者](__PUBLIC_IMAGE_PREFIX__/header.png)

- 关键词：受约束Zonotope；多面体松弛；factorable representation；非线性测量；集合值状态估计；可达性分析
- DOI / 论文链接：https://arxiv.org/abs/2504.00130

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

本文关注非线性离散时间系统的集合值状态估计问题。在这类问题中，难点并不只在于把上一时刻状态集合通过非线性动力学传播出去，更在于如何把**当前非线性测量方程**也纳入同一个保证型递推里，同时还要控制集合复杂度。

已有基于 zonotope 或 constrained zonotope 的方法大多依赖线性化，再用均值定理、Taylor 定理或 DC 分解去界定线性化误差。作者指出，这类策略对小集合往往尚可，但一旦状态不确定域变大，整块区域上统一的线性误差界会迅速变得保守。本文因此选择换一个思路：不再先线性化整体非线性映射，而是把非线性函数分解成 factorable representation，再在 lifted space 中逐步构造 polyhedral relaxation，最后借助 constrained zonotope 的运算性质投回状态空间。

### 1.2 方法框架与核心思路

论文考虑如下非线性离散时间系统：

$$
\begin{aligned}
x_k &= f(x_{k-1}, w_{k-1}, u_{k-1}), \qquad k \ge 1, \\
y_k &= g(x_k, v_k), \qquad k \ge 0,
\end{aligned}
$$

其中，$x_k$ 为系统状态，$u_k$ 为已知输入，$y_k$ 为测量输出，$w_k$ 与 $v_k$ 为有界不确定性。作者假定 $f$ 与 $g$ 都可写成 factorable functions。

![图1：系统模型与问题定义](__PUBLIC_IMAGE_PREFIX__/system_model.png)

对应的递推目标是：

$$
\hat X_0 \supseteq \{ x_0 \in X_0 : g(x_0, v_0) = y_0,\; v_0 \in V \},
$$

$$
\hat X_k \supseteq \{ x_k = f(x_{k-1}, w_{k-1}, u_{k-1}) :
y_k = g(x_k, v_k),\;
(x_{k-1}, w_{k-1}, v_k) \in \hat X_{k-1} \times W \times V \}.
$$

这篇论文的关键操作是定义复合函数

$$
\ell(x_{k-1}, w_{k-1}, v_k, u_{k-1})
\triangleq
g\bigl(f(x_{k-1}, w_{k-1}, u_{k-1}), v_k\bigr),
$$

然后不直接在原状态空间里界定 $f$ 和 $g$ 的整体误差，而是在它们的 factorable representation 所引入的 lifted variables 上构造半空间包络。这样做的收益有两点：

1. 各个基本非线性运算（包括文中首次加入的三角函数松弛）都能分别处理；
2. 测量等式 $y_k = \ell(\cdot)$ 可以直接作为 lifted polyhedron 的等式约束并入，而不是在投影后再做二次修补。

### 1.3 主要创新点

**创新点 1：** 提出 CZPR（constrained zonotopes with polyhedral relaxations）递推，把 constrained zonotope 的几何运算能力与 lifted polyhedral relaxation 的局部非线性包络能力结合起来。

**创新点 2：** 首次给出三角函数的 polyhedral relaxations，从而把更广泛的非线性系统，尤其机器人和过程系统中的 trig dynamics，纳入该框架。

**创新点 3：** 证明某些 lifted equality constraints 可以在不引入保守性的前提下消去，得到与原始 enclosure 等价但生成元和约束更少的 CZ 表示。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

本文的方法可以概括为以下链条：

1. 对系统动力学 $f$ 与测量函数 $g$ 构造 factorable representation；
2. 将动力学与测量合并为复合函数 $\ell$；
3. 在 lifted variable 空间上，对各个基本运算构造 halfspace enclosures，得到 polyhedral relaxation；
4. 将测量值 $y_k$ 作为等式约束并入 lifted polyhedron；
5. 再把原始的 constrained zonotope 输入集合与 lifted polyhedron 交集，最终通过线性映射投影回状态空间；
6. 对所得 CZ 做等价降复杂度处理，形成递推可用的 CZPR 算法。

从 Zonotope 视角看，这篇论文并不是放弃 zonotope，而是把 constrained zonotope 放在“前端和后端”：前端负责表示原始状态、不确定集及其耦合关系，后端负责把 lifted polyhedron 交回去并投影成新的状态包络。polyhedral relaxation 只在中间层处理非线性，从而避免整个方法退化成纯多面体计算。

### 2.2 关键技术块解析

#### 技术块 A：Proposition 2 构造 lifted halfspace enclosure

![图2：Proposition 2，lifted polyhedral relaxation](__PUBLIC_IMAGE_PREFIX__/proposition_2.png)

Proposition 2 是整篇论文的第一块核心砖。它说明：给定 factorable function 的中间因子及其区间域，可以在 lifted space 中为这些因子构造一个半空间包络 $P_\ell$。这个结果的重要性不在于“又得到了一个多面体”，而在于它让非线性函数的像集计算从“整体线性化”变成了“逐基本运算松弛并组合”。

对 Zonotope 方法而言，这一步的价值非常大。传统 CZMV / CZDC 方法仍需要对整个非线性映射给一个全局近似；Proposition 2 则把困难拆散到了 factorable graph 的各个局部节点上，因此在大不确定域下通常更紧。

#### 技术块 B：Proposition 3 给出 CZPR 的主估计公式

![图3：Proposition 3，主状态估计递推](__PUBLIC_IMAGE_PREFIX__/proposition_3.png)

Proposition 3 给出了本文最核心的估计公式：

$$
\hat X_k \triangleq
E_f\Bigl(
(\hat X_{k-1} \times W \times V \times \tilde Z)
\cap
(P_\ell \cap P_y)
\Bigr),
$$

其中 $P_y$ 由测量等式 $E_\ell z = y_k$ 定义，$P_\ell$ 是 Proposition 2 给出的 lifted polyhedral enclosure，$E_f$ 则把 lifted variable 投影回状态空间。

这条公式体现了本文最重要的结构思想：

- 先用 polyhedral relaxation 捕捉非线性关系；
- 再把 measurement equality 直接并入 lifted polyhedron；
- 最后利用 constrained zonotope 与 polyhedron 的交集和线性映射，把结果重新装回 CZ。

与常规“先 prediction、再用 measurement correction”相比，本文实际上在 lifted space 中把 dynamics 与 measurements 编织到了同一个可行域里，因此能显著减少保守性。

#### 技术块 C：Lemma 1 与 Lemma 2 说明等式因子可被无保守性消元

![图4：Lemma 1，部分消去等式约束后的等价多面体](__PUBLIC_IMAGE_PREFIX__/lemma_1.png)

在 Proposition 3 得到的 lifted polyhedron 中，部分因子对应的运算实际上只是加法、减法和常数缩放。这些因子带来的 equality constraints 不是“必要复杂度”，而是 factorization 过程中附带产生的冗余结构。Lemma 1 通过分块消元把原 polyhedron $P_\ell \cap P_y$ 压缩成只保留必要变量的更小 polyhedron $\mathring P$；Lemma 2 则保证区间近似在这种变量裁剪下仍然是可兼容的。

从 Zonotope 角度，这一块非常关键。因为 CZPR 的瓶颈往往不是集合线性增长本身，而是 lifted representation 中半空间和等式约束数太多。Lemma 1/2 证明：其中一部分复杂度完全可以被**精确移除**，这为后续 Proposition 4 的等价简化奠定了基础。

#### 技术块 D：Proposition 4 证明简化后 enclosure 与原主公式等价

![图5：Proposition 4，等价降复杂度 enclosure](__PUBLIC_IMAGE_PREFIX__/proposition_4.png)

Proposition 4 证明了原始主公式 (18) 与简化后的公式 (26) 是等价的。也就是说，作者不是在这里做一个“更便宜但更松”的替代版本，而是给出一个**无保守性**的简化实现。这一点直接决定了 CZPR 是否能在实际递推中落地：如果每一步都保留完整 lifted polyhedron，哪怕复杂度线性增长，也很快会变得不可算；而 Proposition 4 说明，部分 lifted constraints 可以在不损失信息的前提下被抽掉。

对所有依赖 zonotope/CZ 递推的算法而言，这类“等价而非近似”的复杂度压缩都极其珍贵，因为它不会把理论上的 tightness 偷偷换成工程上的 loose workaround。

#### 技术块 E：Algorithm 1 给出 CZPR 的完整递推流程

![图6：Algorithm 1，CZPR递推算法](__PUBLIC_IMAGE_PREFIX__/algorithm_1.png)

Algorithm 1 把上述所有模块整合为完整流程：从 $X_0$ 初始化开始，对每个时刻 $k$ 依次执行 factorable decomposition、区间域计算、逐因子 halfspace 构造、measurement equality 合并、等价降维，以及最终的 CZ order reduction。

这一步的重要性在于，它表明 CZPR 不是若干理论块的松散拼装，而是一套能够实际迭代运行的 state estimator。作者还强调，尽管 lifted polyhedral relaxation 会引入更多 halfspaces，但整体复杂度对时间步仍保持线性增长；与基于 Taylor 展开的二次增长、纯多面体算法的更高复杂度相比，这仍然是具有工程意义的折衷。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文用 3 个数值例子比较了 CZPR 与两类已有方法：

- **CZMV**：基于 Mean Value Theorem 的 constrained-zonotope 估计；
- **CZDC**：基于 DC programming principles 的 constrained-zonotope 估计；
- **CZPR**：本文提出的 polyhedral-relaxation 方法。

比较指标主要是 $\sqrt[n_x]{\mathrm{Vol}_{\square}(\hat X_k)}$ 与 $\sqrt[n_x]{\mathrm{Vol}_{\diamond}(\hat X_k)}$，也就是由 interval hull 和 zonotope hull 派生的体积指标，以及每步平均计算时间。

### 3.2 主要结果与对比说明

#### 证据块 A：Figure 2 显示 Example 1 中 CZPR 明显优于 CZMV 和 CZDC

![图7：Figure 2，Example 1 的体积指标比较](__PUBLIC_IMAGE_PREFIX__/figure_2.png)

Example 1 使用带非线性动力学和非线性测量的二维系统。Figure 2 及其对应文字说明表明，CZDC 的包络很快发散，而 CZMV 与 CZPR 都保持有界，但 CZPR 更紧。论文给出的数字是：

- CZPR-to-CZMV 的几何平均体积比 GAVR□ 约为 **49.79%**；
- CZPR-to-CZMV 的 GAVR⋄ 约为 **53.38%**。

这说明 CZPR 在这个例子里把 CZMV 的包络体积大约压到一半量级。代价是计算时间上升：文中报告 CZMV 与 CZPR 的平均每步时间分别约为 **34.4 ms** 和 **68.6 ms**。因此，Example 1 的结论是：CZPR 以额外计算代价换取了显著 tighter 的集合估计。

#### 证据块 B：Figure 7 表明 Example 2 中 CZPR 同时更紧且更快

![图8：Figure 7，Example 2 的集合投影比较](__PUBLIC_IMAGE_PREFIX__/figure_7.png)

Example 2 是连续搅拌反应釜（CSTR）模型。Figure 7 直接比较了 $k=200$ 时刻 CZMV 与 CZPR 在 $(x_1,x_2)$ 平面上的投影，可以看到 CZPR 的包络明显更小。更重要的是，该例子的 factorable representation 相对简单，因此 lifted relaxation 并没有把计算量推高。文中报告：

- CZPR-to-CZMV 的 GAVR□ 约为 **12.90%**；
- CZPR-to-CZMV 的 GAVR⋄ 约为 **23.65%**；
- CZMV 与 CZPR 的平均每步时间分别约为 **187.2 ms** 与 **50.0 ms**。

也就是说，在这个结构更适合 factorable relaxation 的例子里，CZPR 不仅更紧，而且更快。这说明本文方法的代价与收益并非固定，强烈依赖系统函数的 factorization 复杂度。

#### 证据块 C：Figure 9 说明 Example 3 中只有 CZPR 能保持有界包络

![图9：Figure 9，Example 3 的体积指标比较](__PUBLIC_IMAGE_PREFIX__/figure_9.png)

Example 3 是具有多重三角函数的平面机器人机械臂。作者指出，在该例中 CZDC 和 CZMV 都很快发散，只有 CZPR 仍能维持有界状态包络。虽然平均每步时间达到 **1.614 s**，成本明显升高，但这是文中最能体现 CZPR 方法边界价值的例子：当系统含有复杂 trig nonlinearities 时，传统线性化或 DC-based CZ 方法已经无法给出可用结果，而 CZPR 仍然能工作。

从 Zonotope 角度看，这个例子说明：只要能把 trig factors 纳入 lifted polyhedral relaxation，constrained zonotope 仍然可以作为最终状态包络的宿主，而不需要完全转向纯 polytope 或无限维提升方法。

## 4. 后续研究方向
1. 标题：针对高阶 factorable graph 的自适应 halfspace 精简  
   *核心想法：根据各个因子对最终状态包络的敏感度，动态裁剪 lifted polyhedral relaxation 中的半空间数量，减少 CZPR 在复杂 trig 系统中的计算负担。*  
   数学推导难度：高  
2. 标题：面向混合系统与切换系统的 CZPR 扩展  
   *核心想法：将 factorable relaxation 与离散模式切换逻辑结合，在每个模式内使用 CZPR，在模式间通过受约束Zonotope 的集合并或分支管理维持递推。*  
   数学推导难度：很高  
3. 标题：结合非线性不变量的 measurement-aware lifted relaxation  
   *核心想法：把质量守恒、能量守恒等非线性不变量直接写入 lifted polyhedron，与测量等式一起参与 Proposition 3 的交集，从而进一步收紧状态包络。*  
   数学推导难度：高  

## 5. 总结与评价

本文的主要贡献，在于为非线性集合值状态估计提供了一条不同于全局线性化的新路线：先把 nonlinear dynamics 和 measurements 展成 factorable graph，再在 lifted space 中做 polyhedral relaxation，最后借助 constrained zonotope 把几何信息高效地投回状态空间。这样做的结果是，CZPR 同时保留了 polyhedral relaxation 的精细非线性包络能力，以及 constrained zonotope 在线性映射、交集和复杂度控制上的优势。

从技术链条看，Proposition 2 负责 lifted enclosure，Proposition 3 负责主估计递推，Lemma 1/2 与 Proposition 4 负责无保守性降复杂度，Algorithm 1 则把整套递推固定下来。结构清晰，逻辑闭合。

从 Zonotope 发展视角看，这篇论文很有代表性：它没有试图让 zonotope 家族单独承担所有非线性建模任务，而是让 constrained zonotope 与 polyhedral relaxation 协同分工。前者负责集合递推和复杂度管理，后者负责复杂非线性图的局部包络。这种“中间 lifted polyhedron，外层 CZ 递推”的分层设计，是这篇工作的真正方法论价值。
