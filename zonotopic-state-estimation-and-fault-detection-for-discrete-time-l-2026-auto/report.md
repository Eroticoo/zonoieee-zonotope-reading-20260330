# DoS 攻击下离散时间线性系统的 zonotopic 状态估计与故障检测：一种切换控制器设计机制

![论文抬头：标题与作者](https://cdn.jsdelivr.net/gh/Eroticoo/wechat_paste@main/zonotopic-state-estimation-and-fault-detection-for-discrete-time-l-2026-auto/images/header.png)

- 关键词：Fault detection; Nonperiodic DoS attacks; Reachability analysis; Set-membership estimation; Zonotopic observer design
- DOI / 论文链接：https://doi.org/10.1016/j.automatica.2026.112833

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战
这篇论文围绕 **Zonotopic state estimation and fault detection for discrete-time linear systems under DoS attacks: A switching controller design mechanism** 所对应的问题展开。摘要开头直接给出了研究主线：Article history: This paper investigates the issues of zonotopic-based set-membership state estimation and fault Received 17 October 2024 detection for discrete-time linear systems with unknown but bounded (UBB) disturbance and noise Received in revised form 16 November 2025 under nonperiodic denial-of-service (DoS) attacks. 作者真正要解决的并不是再给一个估计器、控制器或检测器，而是在存在噪声、有界扰动、时滞、通信约束、建模误差或异常数据时，怎样给出一个仍然可计算、且能提供可靠包络解释的方法。

### 1.2 方法框架与核心思路
本文里的 **zonotope** 不是一个静态几何图形，而是攻击或故障尚未被完全辨识时的可达不确定性外包络。它用中心向量表示当前名义状态或残差水平，用生成矩阵编码扰动、攻击、噪声和触发机制带来的方向性扩张；后续检测阈值、残差判别或切换逻辑，都是在这个可传播集合之上建立的。

$$
\begin{aligned}
x_{k+1} &= A x_k + B u_k + E_w w_k,\\
y_k &= \delta_k C x_k + D_v v_k,\\
\delta_k &\in \{0,1\}
\end{aligned}
$$

这里 $\delta_k$ 用来表征 DoS 攻击导致的量测可用性切换；正因为量测链路会间歇失效，作者才需要用 zonotope 递推来追踪攻击下仍然可接受的状态与残差包络。

![technical_core_1：系统模型或方法入口](https://cdn.jsdelivr.net/gh/Eroticoo/wechat_paste@main/zonotopic-state-estimation-and-fault-detection-for-discrete-time-l-2026-auto/images/technical_core_1.png)

这张截图对应正文前半部分的系统模型、问题定义或方法入口。它的作用是把后续 theorem、lemma 与 property 的讨论放回到具体对象上：后面的证明链条并不是孤立存在，而是服务于这里给出的状态演化、观测关系、触发条件或几何对象定义。

### 1.3 主要创新点
- 把 zonotope 从辅助表示提升为方法主轴，后续设计、分析与验证都围绕集合传播展开。
- 把残差、故障估计或攻击判别与集合包络统一起来，提升了噪声存在下的可分离性解释。
- 通过 theorem、lemma 与 assumption 的链条把检测阈值、观测器设计和鲁棒性保证连成闭环。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线
如果把全文压缩成一条主线，可以概括为：**系统或问题建模 -> 用 zonotope 组织不确定性或结构对象 -> 借助 formal blocks 建立可计算的递推、判别或构造规则 -> 用图表验证主要结论**。因此 section 2 最值得读的不是单独某个 theorem，而是这些 block 之间怎样接力。

### 2.2 关键技术块解析
**Assumption 1**

它首先固定了后续结论成立的可接受模型环境。

这一块在文中更像主线入口：即便没有显式引用编号，它也通常承接前面的系统模型与噪声设定。

![assumption_1：Assumption 1 截图](https://cdn.jsdelivr.net/gh/Eroticoo/wechat_paste@main/zonotopic-state-estimation-and-fault-detection-for-discrete-time-l-2026-auto/images/assumption_1.png)

**Property 1**

这个 Property 提供的是代数工具而不是最终性能保证。它通常说明 zonotope 在线性变换、Minkowski 和或外包络近似下怎样保持可表达性，后面的递推公式正是借这个闭包性质展开。

它与前面结果之间的关系主要体现为递推承接，而不是显式编号引用。

![property_1：Property 1 截图](https://cdn.jsdelivr.net/gh/Eroticoo/wechat_paste@main/zonotopic-state-estimation-and-fault-detection-for-discrete-time-l-2026-auto/images/property_1.png)

**Theorem 1**

它是本文正式结论链上的主干结果。 从正文措辞看，这个结果直接给出稳定性、性能界或递推有界性。 这意味着滤波增益、观测器参数或控制器参数有了明确的设计准则。

与前序结果的关系是：Assumption 1 负责固定可接受的模型与噪声边界。这些引用共同把前提、工具和最终保证串成一条完整证明链。

![theorem_1：Theorem 1 截图](https://cdn.jsdelivr.net/gh/Eroticoo/wechat_paste@main/zonotopic-state-estimation-and-fault-detection-for-discrete-time-l-2026-auto/images/theorem_1.png)

把这些技术块连起来看，本文的关键证明链就是 **Assumption 1 -> Property 1 -> Theorem 1**。其中前面的 assumption、property 或 lemma 负责固定 admissible setting、提供上界或运算规则，后面的 theorem、proposition 或 algorithm 则把这些工具汇总成最终的稳定性、可行性、可检测性或构造结论。

## 3. 仿真结果与对比分析
### 3.1 结果设置与对比对象
结果部分的阅读重点不是图多不多，而是作者是否用主图和补充图共同回答了三个问题：比较对象是谁、比较指标是什么、这些证据到底能安全支撑到哪一步结论。

![figure_2：结果证据 1](https://cdn.jsdelivr.net/gh/Eroticoo/wechat_paste@main/zonotopic-state-estimation-and-fault-detection-for-discrete-time-l-2026-auto/images/figure_2.png)

这张图是结果部分的重要证据，用来观察作者声称的优势是否在可视化或表格统计上形成闭环。

![figure_3：结果证据 2](https://cdn.jsdelivr.net/gh/Eroticoo/wechat_paste@main/zonotopic-state-estimation-and-fault-detection-for-discrete-time-l-2026-auto/images/figure_3.png)

这张图是结果部分的重要证据，用来观察作者声称的优势是否在可视化或表格统计上形成闭环。

### 3.2 主要结果与对比说明
从这些图表的组合方式看，作者希望证明的不只是方法能运行，而是它在保守性、鲁棒性、可分离性、经济性能或计算代价上相对基线更有优势。需要注意的是，图表最适合证明的是在给定实验场景下确实观察到了这种趋势；至于该趋势能否无条件推广到更大规模、更强噪声或更复杂场景，仍然要回到 section 2 的 theorem 与 assumption 链条来判断。

## 4. 后续研究方向
1. 标题：多故障与多攻击耦合扩展
*核心想法：把当前单故障或单攻击的 zonotopic 判别链扩展到执行器、传感器与网络攻击同时存在的场景，并重新设计可分离阈值。*
数学推导难度：高

2. 标题：检测与容错控制联动
*核心想法：将检测得到的集合信息继续送入补偿器或切换控制器，让 zonotope 不只负责报警，还负责闭环重构。*
数学推导难度：高

3. 标题：面向弱故障的保守性压缩
*核心想法：在保持零漏检约束的前提下压缩正常包络半径，提升弱故障和早期故障的可辨识度。*
数学推导难度：中

## 5. 总结与评价
这篇论文最值得保留的价值，在于它没有把 zonotope 当成一个可有可无的几何外壳，而是把它放在了方法主线的正中央。无论是估计、控制、故障检测还是纯几何结构分析，作者都在围绕同一个核心问题展开：怎样让集合表示既承担理论证明的骨架，又承担算法实现与结果解释的接口。
