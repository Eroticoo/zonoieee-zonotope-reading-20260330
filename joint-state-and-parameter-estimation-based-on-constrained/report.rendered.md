# 基于受约束Zonotope的联合状态与参数估计

![论文抬头：标题与作者](__PUBLIC_IMAGE_PREFIX__/header.png)

- 关键词：受约束Zonotope；联合状态与参数估计；非线性状态估计；参数辨识；广义交集；集合计算
- DOI / 论文链接：https://doi.org/10.1016/j.automatica.2022.110425

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

本文处理的问题是：当模型参数 $p$ 未知但有界时，如何在状态估计过程中**同时**收紧状态集合与参数集合。传统做法往往把参数辨识视为离线问题，把状态估计视为在线问题，两者分离处理。这种分工在随机框架下常见，但在 set-based 框架下会损失一个非常重要的信息源，即**状态与参数之间的几何耦合关系**。

作者指出，区间方法虽然能做联合估计，但很难保存变量依赖关系，容易因为 wrapping effect 让参数和状态包络同时变松。普通 zonotope 虽然在线性变换和 Minkowski 和方面高效，但对更新交集的处理依然保守。本文的关键判断是：如果把状态和参数统一成一个增广变量，并用**受约束Zonotope**统一描述，那么参数集合就能像状态集合一样，在每次测量到来时被精确地更新和收紧。

### 1.2 方法框架与核心思路

在线性情形下，论文考虑如下离散时间系统：



$$
\begin{aligned}
x_k &= A x_{k-1} + B_u u_{k-1} + B_p p + B_w w_{k-1}, \\
y_k &= C x_k + D_u u_k + D_p p + D_v v_k,
\end{aligned}
$$



其中，$x_k$ 为状态，$u_k$ 为已知输入，$p$ 为未知但有界参数，$w_k$ 为过程扰动，$v_k$ 为测量扰动。作者把联合变量写为



$$
z_k \triangleq (x_k, p),
$$



并把联合预测-更新递推写成



$$
\bar{Z}_k \supseteq \{ (A x_{k-1} + B_u u_{k-1} + B_p p + B_w w_{k-1},\; p) : (x_{k-1}, p) \in \hat{Z}_{k-1},\; w_{k-1} \in W \},
$$





$$
\hat{Z}_k \supseteq \{ (x_k, p) \in \bar{Z}_k : C x_k + D_u u_k + D_p p + D_v v_k = y_k,\; v_k \in V \}.
$$



![图1：联合状态-参数框架与线性递推](__PUBLIC_IMAGE_PREFIX__/joint_framework.png)

在 CG-rep 中，上述递推可以直接实现为



$$
\bar{Z}_k =
\begin{bmatrix}
A & B_p \\
0 & I
\end{bmatrix}
\hat{Z}_{k-1}
\oplus
\begin{bmatrix}
B_u \\
0
\end{bmatrix} u_{k-1}
\oplus
\begin{bmatrix}
B_w \\
0
\end{bmatrix} W,
$$





$$
\hat{Z}_k = \bar{Z}_k \cap_{[\,C\;\;D_p\,]}
\Bigl((y_k - D_u u_k) \oplus (-D_v V)\Bigr).
$$



这套写法的本质非常重要：参数 $p$ 没有被当成“附加标签”挂在状态外面，而是被纳入与状态相同的集合几何对象中。这样，一次测量更新不仅会收紧状态投影 $\hat{X}_k$，也会收紧参数投影 $\hat{P}_k$，并且二者之间的依赖关系能够跨时刻传递。

对于非线性系统，作者进一步考虑



$$
x_k = f(x_{k-1}, u_{k-1}, p, w_{k-1}),
$$



并把 prediction 步写成联合形式



$$
\bar{Z}_k \supseteq
\{ (f(x_{k-1}, u_{k-1}, p, w_{k-1}),\; p) :
(x_{k-1}, p) \in \hat{Z}_{k-1},\; w_{k-1} \in W \}.
$$



随后用基于 Jacobian 区间界的外包络来传播该增广集合，而 update 步仍然保持线性，因此仍可借助 constrained zonotope 的广义交集精确处理。

### 1.3 主要创新点

**创新点 1：** 论文首次把已有的 constrained-zonotope 状态估计框架扩展成**联合状态-参数估计**框架，并显式保持状态-参数耦合关系。

**创新点 2：** 在 update 步中，参数集合不再停留在初始先验，而会随测量通过广义交集不断收紧。这一点与很多只更新状态、不更新参数的 set-based 方法不同。

**创新点 3：** 对非线性系统，作者把 2020 年论文中的 Jacobian 区间包络方法推广到增广变量 $(x,p)$ 上，使 constrained zonotope 不仅能做状态传播，也能做联合状态-参数传播。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

本文的技术路线可以概括为：

1. 用受约束Zonotope 统一表示状态集、参数集及其笛卡尔积；
2. 在线性系统中，把 $(x,p)$ 直接作为一个增广状态做 prediction 和 update；
3. 在非线性系统中，对 $f(x,u,p,w)$ 关于增广变量 $z=(x,p)$ 做 Jacobian 区间外包络；
4. 借助先前工作中的集合乘积算子与本文的 Proposition 1 / Corollary 1 完成联合预测；
5. 通过广义交集精确更新测量一致集合，从而同时收紧状态和参数。

这里最值得强调的 Zonotope 细节是：受约束Zonotope 的强项并非只有“比普通 zonotope 多了约束”，而是它允许把**依赖关系写进生成变量约束**。这正是联合估计问题所需要的，因为状态与参数的耦合一旦在某步被折断，后续任何 measurement update 都无法把这层关系重新找回来。

受约束Zonotope 的基本形式为



$$
Z = \{ c_z + G_z \xi : \|\xi\|_\infty \le 1,\; A_z \xi = b_z \},
$$



其中 $G_z$ 决定生成元方向，$c_z$ 是中心，$A_z \xi = b_z$ 则刻画隐藏在生成变量空间中的线性耦合。对于联合估计，这种耦合并不是装饰性的，它承载的正是“某些状态取值只能与某些参数取值一起成立”的几何约束。

### 2.2 关键技术块解析

#### 技术块 A：Definition 1 给出受约束Zonotope 的 CG-rep

![图2：Definition 1，受约束Zonotope定义](__PUBLIC_IMAGE_PREFIX__/definition_1.png)

Definition 1 重新引入了本文使用的基础集合对象。相比普通 zonotope，受约束Zonotope 在生成变量 $\xi$ 上加入等式约束，因此可以描述非中心对称的凸多面体，同时依然保留生成元表达的低维线性结构。

对本文而言，这一定义的作用非常具体：联合变量 $(x,p)$ 的可行集合往往不是简单的箱体或对称体，而是由状态方程、参数先验和测量一致性共同裁剪后的偏斜多面体。若仍使用普通 zonotope，很多这种几何信息会在交集外近似中丢失；受约束Zonotope 则能把这些关系留在 CG-rep 里继续传播。

#### 技术块 B：Theorem 1 提供区间矩阵作用下的集合包络算子

![图3：Theorem 1，区间Jacobian乘集合的包络算子](__PUBLIC_IMAGE_PREFIX__/theorem_1.png)

Theorem 1 来自作者此前工作，是本文非线性联合预测的直接支撑。它定义了算子 $\triangleleft(J, X)$，用来外包络区间矩阵 $J$ 与受约束Zonotope 偏移集 $X$ 的乘积结果。对本文来说，这个算子扮演的角色非常清楚：当对非线性函数做均值定理展开后，会出现 Jacobian 区间乘以增广偏移量 $(z-\gamma_z)$ 的项，Theorem 1 负责把这部分重新封装回可计算的 constrained zonotope 表达中。

如果没有这个算子，联合非线性预测只能退回到 interval 或普通 zonotope 近似。也就是说，Theorem 1 虽然不是本文首次提出，但它是本文 Proposition 1 成立所依赖的**几何操作桥梁**。

#### 技术块 C：Proposition 1 把非线性状态预测推广到联合状态-参数预测

![图4：Proposition 1，非线性联合预测的核心命题](__PUBLIC_IMAGE_PREFIX__/proposition_1.png)

Proposition 1 是本文最核心的新结果之一。它考虑 $f(x,u,p,w)$ 关于增广变量 $z=(x,p)$ 的非线性映射，并给出



$$
f(X, u, P, W) \subseteq Z_w \oplus \triangleleft(J, Z - \gamma_z),
$$



其中 $Z = X \times P$，$\gamma_z = (\gamma_x, \gamma_p)$ 是线性化参考点，$Z_w$ 包含基点处关于扰动 $W$ 的像集，$J$ 则包络 $\nabla_z f$。这个命题的关键不只是把状态预测推广到“状态加参数”，而是让参数在 Jacobian 中和状态一起出现，使得参数不确定性能够通过系统动力学被投影到状态传播中。

从 Zonotope 角度看，这一步真正保存的是**状态-参数依赖结构**。若用 interval 表达，$x$ 与 $p$ 的相关性在一次线性化后就会被拆散成独立上下界；Proposition 1 则把这种相关性保存在同一个 constrained zonotope 中。

#### 技术块 D：Corollary 1 把命题结果提升为完整联合预测公式

![图5：Corollary 1，联合预测的 CG-rep 形式](__PUBLIC_IMAGE_PREFIX__/corollary_1.png)

Corollary 1 将 Proposition 1 的状态预测包络提升到完整的联合变量 $(x_k,p)$ 预测，给出块矩阵形式的 CG-rep 递推：



$$
\bar{Z}_k =
\begin{bmatrix}
H & E
\end{bmatrix}
\hat{Z}_{k-1}
\oplus
\begin{bmatrix}
H \\
0
\end{bmatrix}
(-\gamma_z)
\oplus
\begin{bmatrix}
\hat{P} \\
0
\end{bmatrix}
B_\infty^n
\oplus
\begin{bmatrix}
I \\
0
\end{bmatrix}
Z_w.
$$



这个推导的工程意义非常强：参数 $p$ 因为被放在联合变量中，在预测步里自动以“恒定但未知”的方式穿过系统模型；在 update 步里又会和状态一起被测量方程裁剪。结果就是参数不再是一组孤立区间，而成为会被状态轨迹和测量数据共同收紧的几何对象。

#### 技术块 E：联合 update 的真正优势来自广义交集

虽然本文没有把联合 update 单独写成一个新定理块，但它的价值并不次于 Proposition 1。联合更新公式



$$
\hat{Z}_k = \bar{Z}_k \cap_{[\,C\;\;D_p\,]}
\Bigl((y_k - D_u u_k) \oplus (-D_v V)\Bigr)
$$



说明参数 $p$ 也会通过测量方程被更新。只要 $D_p$ 不为零或参数通过状态传播进入输出一致性条件，参数集合就会被持续收紧。正因为受约束Zonotope 对广义交集是闭合的，这一步才能在统一框架内精确完成。对联合估计问题而言，这一点比单纯改进 prediction 更关键，因为很多参数信息实际上是通过 repeated updates 才逐渐显现出来的。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文给出两类实验：

- **线性联合估计实验**：10 维状态、6 维参数、6 维输出，比较 CZ-J、CZ、Z-J 和 FBP。
- **非线性联合估计实验**：二维非线性系统加一维未知参数，比较 CZMV-J、CZMV、ZMV-J 和 FBP。

这里记号中的 `-J` 表示 joint estimation。也就是说，CZ 与 CZMV 是“只做状态估计”的原方法，而 CZ-J 与 CZMV-J 是“状态与参数联合估计”的新框架。

### 3.2 主要结果与对比说明

#### 证据块 A：Fig. 1 显示线性系统中参数集合会被联合框架持续收紧

![图6：Fig. 1，线性系统中状态半径与参数半径比较](__PUBLIC_IMAGE_PREFIX__/figure_1.png)

Fig. 1 上半部分比较线性例子中状态投影 $\hat{X}_k$ 的平均半径，下半部分比较参数投影 $\hat{P}_k$ 的平均半径。作者指出，CZ-J 的状态与参数集合都明显优于 CZ、Z-J 和 FBP。其原因并不复杂：

- CZ 没有在线收紧参数集合；
- Z-J 使用普通 zonotope，在交集处更保守；
- FBP 使用区间，无法有效保持状态-参数依赖。

这个图最重要的结论是：**一旦参数也进入统一的 constrained-zonotope 递推，参数先验 $P$ 会随着时间持续缩小**。这正是联合估计框架相对传统状态估计器的核心增益。

#### 证据块 B：Fig. 2 展示非线性系统中联合集合几何明显更紧

![图7：Fig. 2，非线性系统在多个时刻的联合集合比较](__PUBLIC_IMAGE_PREFIX__/figure_2.png)

Fig. 2 直接画出非线性例子在 $k \in \{3, 15, 149\}$ 时刻的 $(x_k, p)$ 联合集。作者比较了 ZMV-J、CZMV-J 和 interval arithmetic。可以看到，CZMV-J 的联合集不仅更小，而且更贴近真实变量轨迹。这说明把参数放进增广变量之后，受约束Zonotope 不只是让参数集合缩小，也让状态集合因为保留了与参数的耦合而变得更紧。

#### 证据块 C：Fig. 3 用面积比和半径比量化了联合框架优势

![图8：Fig. 3，非线性系统中状态面积与参数半径比较](__PUBLIC_IMAGE_PREFIX__/figure_3.png)

Fig. 3 汇总了非线性系统中状态投影 $\hat{X}_k$ 的面积和参数投影 $\hat{P}_k$ 的半径变化。论文给出两个关键数字：

- CZMV-J 相对 ZMV-J 的 $\hat{X}_k$ 平均面积比约为 **71%**；
- CZMV-J 相对 ZMV-J 的 $\hat{P}_k$ 平均半径比约为 **34%**。

这组结果有两个含义。第一，联合 constrained-zonotope 框架不仅改善状态估计，也大幅改善参数估计。第二，参数集合的改进幅度甚至比状态集合更显著，这说明 measurement update 通过状态-参数耦合，把额外信息有效投射到了参数空间中。对于辨识问题来说，这一点比单纯减小状态包络更有方法论价值。

## 4. 后续研究方向
1. 标题：面向时变参数的联合 constrained-zonotope 估计  
   *核心想法：把常参数假设 $p_k = p_{k-1}$ 扩展为带有有界漂移的参数动态模型，在联合变量中同时估计状态、参数及参数漂移。*  
   数学推导难度：高  
2. 标题：推广到描述符系统与代数约束联合估计  
   *核心想法：沿着论文结尾提到的 descriptor systems 方向，把受约束Zonotope 引入含代数方程的联合状态-参数估计，并研究可观测性/可辨识性条件下的有界递推。*  
   数学推导难度：很高  
3. 标题：非线性输出方程下的联合 update 精确化  
   *核心想法：将当前线性输出方程的广义交集更新推广到非线性测量方程，通过 Jacobian/Hessian 包络与局部约束拼接，构造带保证的参数-状态联合测量更新。*  
   数学推导难度：很高  

## 5. 总结与评价

这篇 Technical Communique 的价值非常集中：它把 set-based state estimation 与 parameter identification 从“两个相邻问题”变成了“一个统一的集合几何问题”。在这个统一框架下，参数不再只是独立先验区间，而是与状态一起进入同一个 constrained zonotope，并在 prediction 与 update 中被共同传播和共同裁剪。

从 Zonotope 角度看，本文最有价值的贡献是证明了受约束Zonotope 不仅适合做状态估计，也适合做**依赖关系保真**的联合估计。普通 zonotope 在交集与依赖保持上的短板，使其在 joint estimation 中更容易保守；区间方法则更容易在 wrapping effect 下丢失状态-参数耦合。本文提供的结果和图示都清楚表明：**把 $(x,p)$ 统一编码到 constrained zonotope 中，是获得更紧状态包络和更快参数收缩的关键。**
