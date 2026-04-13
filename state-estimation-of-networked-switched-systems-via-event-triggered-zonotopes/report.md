# 基于事件触发 Zonotope 的网络切换系统状态估计

![论文抬头：标题与作者](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/header.png)

- 关键词：事件触发 Zonotope；网络切换系统；一般异步切换；平均驻留时间；状态估计；$L_\infty$ 性能
- DOI / 论文链接：https://doi.org/10.1109/TCNS.2024.3425641

## 1. 研究背景、问题定义与核心思路
### 1.1 研究动机与关键挑战

本文研究网络切换系统的集合状态估计问题。系统工作在网络环境中，测量输出并不持续发送，而是由事件触发机制决定何时通信；与此同时，系统模态会发生切换，而观测器增益只在触发时刻更新，因此子系统模态与观测器模态之间天然存在异步现象。对于这类问题，仅给出单点估计并不足以描述有界扰动下的真实不确定性，更合理的目标是构造一个随时间递推的状态集合，使真实状态始终被该集合包含。

本文要处理的关键难点有两个。第一，已有事件触发切换系统结果常假设每个相邻触发区间内至多发生一次系统切换，这一限制会排除更一般的异步场景；第二，即使使用 Zonotope 做集合传播，也需要给出同步区间与异步区间下生成矩阵的不同迭代方式，并进一步分析集合半径在平均驻留时间约束下是否保持有界，以及能否得到明确的 $L_\infty$ 扰动衰减指标。

### 1.2 方法框架与核心思路

论文从离散时间切换线性系统出发：

$$
\begin{aligned}
x(k+1) &= A_{\sigma(k)}x(k)+B_{\sigma(k)}u(k)+E_{\sigma(k)}\omega(k),\\
y(k) &= C_{\sigma(k)}x(k).
\end{aligned}
$$

其中，$x(k)$ 是系统状态，$u(k)$ 是控制输入，$y(k)$ 是测量输出，$\omega(k)$ 是未知但有界扰动，$\sigma(k)$ 表示当前激活的子系统模态。切换信号满足平均驻留时间约束

$$
N_\sigma(k,K)\le N_0 + \frac{K-k}{\tau_a}.
$$

![系统模型与平均驻留时间定义](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/system_model.png)

为减少通信负担，论文引入事件触发机制：

$$
\begin{aligned}
s_{\kappa+1}
&=\inf\Bigl\{k>s_\kappa \;\Bigm|\; \epsilon_{\sigma(s_\kappa)}\lvert \tilde e(s_\kappa,k)\rvert \ge \lvert y(k)\rvert,\; k\ge s_\kappa+\bar\tau\Bigr\},\\
\tilde e(s_\kappa,k) &= y(s_\kappa)-y(k),
\end{aligned}
$$

并设计观测器

$$
\hat x(k+1)=A_{\sigma(k)}\hat x(k)+B_{\sigma(k)}u(k)
+L_{\sigma(s_\kappa)}\bigl(y(s_\kappa)-C_{\sigma(k)}\hat x(k)\bigr).
$$

上式体现了本文方法的结构特征：系统矩阵随当前模态 $\sigma(k)$ 变化，但观测器增益依赖最近触发时刻的模态 $\sigma(s_\kappa)$。因此，只要在一个触发区间内发生切换，就会出现一般异步切换。作者以状态 Zonotope

$$
Z_x(k)=\langle \hat x(k), H_x(k)\rangle,\qquad
Z(k)=\langle c(k),H(k)\rangle = c(k)\oplus H(k)B^p
$$

来包络真实状态，并进一步定义半径

$$
r(k)=\max_{b\in B^{n_x}}\|H_x(k)b\|_2,\qquad
R_i(k)=\max_{b\in B^{n_x}}\|H_x(k)b\|_{P_i},
$$

从而把“集合传播”和“性能分析”统一到同一套半径函数框架中。

![事件触发机制与观测器](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/observer_trigger.png)

### 1.3 主要创新点

本文的**创新点**集中在以下三个方面。

- 提出事件触发状态 Zonotope，用于网络切换系统的集合状态估计，并显式覆盖“一次触发区间内发生多次系统切换”的一般异步情形。
- 针对同步区间与异步区间分别构造生成矩阵迭代律，使集合传播能够直接反映观测器模式滞后带来的结构差异。
- 通过构造观测器模态依赖的半径函数，把事件触发参数与观测器增益联合转化为可解的 LMI 设计问题，并在平均驻留时间条件下给出有界性与 $L_\infty$ 性能分析。

## 2. 核心方法与技术主线解析
### 2.1 整体技术路线

论文的技术路线可以概括为四步。第一，给定初始状态集与扰动集的 Zonotope 包络，利用 Zonotope 在线性映射与 Minkowski 和下的闭包规则建立集合传播基础。第二，利用事件触发规则把每个模态运行区间分解为同步段与异步段，并用 $\rho(k)$ 与 $H_\rho(k)$ 描述触发误差的可达范围。第三，借助 **Theorem 1** 给出状态 Zonotope 的一步递推公式，使状态集合在一般异步切换下依然可计算。第四，借助 **Theorem 2** 构造观测器模态依赖半径函数，把集合半径的递推不等式转化为 LMI 可解条件，并进一步推出受限切换下的有界性与 $L_\infty$ 性能。

从证明组织上看，本文并没有直接在估计误差系统上套用统一 Lyapunov 函数，而是先建立“集合生成矩阵如何递推”，再把“生成矩阵的大小”压缩为半径函数进行分析。这样做的结果是，集合包络与性能指标之间的对应关系更直接，工程解释性也更强。

### 2.2 关键技术块解析

**Theorem 1：一般异步切换下的状态 Zonotope 一步传播公式。** 该定理的前提是当前时刻真实状态满足

$$
x(k)\in Z_x(k)=\langle \hat x(k),H_x(k)\rangle.
$$

结论是在下一时刻依然有

$$
Z_x(k+1)=\langle \hat x(k+1),H_x(k+1)\rangle,
$$

并且生成矩阵按照同步区间与异步区间分别更新：

$$
H_x(k+1)=
\begin{cases}
\left[\,A_{jj}H_x(k)\;\;L_jH_\rho(k)\;\;E_jH_\omega\,\right], & k\in T_s(k_l,k_{l+1}),\\[4pt]
\left[\,A_{ji}H_x(k)\;\;L_iH_\rho(k)\;\;E_jH_\omega\,\right], & k\in T_{as}(k_l,k_{l+1}),
\end{cases}
$$

其中

$$
A_{jj}=A_j-L_jC_j,\qquad A_{ji}=A_j-L_iC_j.
$$

定理的关键意义在于：异步区间里使用的是“当前系统模态为 $j$、观测器仍停留在旧模态 $i$”的交叉矩阵 $A_{ji}$，因此该公式把一般异步切换对集合传播的影响显式地编码进了生成矩阵。证明中最关键的一步是把误差系统写为

$$
\hat e(k+1)=A_{\sigma(k)\sigma(s_\kappa)}\hat e(k)-L_{\sigma(s_\kappa)}\rho(k)+E_{\sigma(k)}\omega(k),
$$

再利用 Zonotope 的线性映射与 Minkowski 和闭包规则，把估计误差、触发误差和外部扰动三部分统一并入新的生成矩阵。

![Theorem 1：状态 Zonotope 的一步递推](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/theorem_1.png)

**Theorem 2：半径函数驱动的参数联合设计与性能分析。** 在完成集合递推之后，作者引入 $P_i>0$、$\lambda_i>1$、$F_i$ 与标量 $\gamma>0$，通过 LMI 条件同时设计触发参数与观测器增益。定理要求满足两组模态相关矩阵不等式与跨模态比较条件 $P_j\le \mu P_i$，并最终得到

$$
\epsilon_i=\lambda_i,\qquad L_i=P_i^{-1}F_i.
$$

![Theorem 2：LMI 条件与联合设计结论](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/theorem_2_part1.png)

该定理的证明逻辑是本文最核心的技术链。作者先对 LMI 使用 Schur 补，把它们改写成关于 $H_x(k+1)$、$H_x(k)$、$H_\rho(k)$ 与 $H_\omega$ 的二次型不等式；再借助事件触发条件，推出触发误差生成元受输出生成元控制，从而得到

$$
\max_{b_\rho\in B}\frac{\|H_\rho(k)b_\rho\|_2^2}{\lambda_j}
<
\max_{b_x\in B^{n_x}}\|C_jH_x(k)b_x\|_2^2.
$$

这一步把事件触发门限与观测误差项联系起来，是后续把输出信息转回集合半径分析的关键桥梁。继续代入后，作者分别在同步段、异步段和跨触发切换时刻建立三条半径递推式：

$$
\begin{aligned}
R_j(k+1) &\le \bar\alpha R_j(k)-\Gamma(k),\\
R_i(k+1) &\le \bar\beta R_i(k)-\Gamma(k),\\
R_j(s_{\kappa+1}) &\le \mu\bar\beta R_i(s_{\kappa+1}-1)-\mu\Gamma(s_{\kappa+1}-1),
\end{aligned}
$$
$$
\Gamma(k)=r(k)-\gamma^2r_\omega,\qquad
\bar\alpha=1-\alpha,\qquad
\bar\beta=1+\beta.
$$

![Theorem 2 证明中的半径递推链](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/page4_full.png)

这三条递推式分别对应“同步衰减”“异步放大”与“跨模态跳变”三种机制。最终，论文在平均驻留时间约束下给出

$$
\tau_a>-\frac{\bar\tau\ln\bar\theta+\ln\mu}{\ln\bar\alpha},\qquad
\bar\theta=\frac{\bar\beta}{\bar\alpha},
$$

从而证明状态 Zonotope 半径保持有界，并具有不超过 $\gamma$ 的 $L_\infty$ 性能上界。这里的技术价值在于，论文把复杂的事件触发异步切换问题分解成“局部一步传播 + 全局切换约束”两层分析，因而设计变量与性能指标之间具有清晰的对应关系。

此外，作者还讨论了计算复杂度问题。由于生成矩阵在每一步都会引入新的列，Zonotope 阶数会持续增长，因此文中进一步采用降阶算子 $\downarrow_g(\cdot)$ 控制最大阶数 $g$，并以区间包络形式恢复上下界。这一处理并不改变主定理的逻辑，但对工程实现是必要的。

## 3. 仿真结果与对比分析
### 3.1 仿真设置与对比对象

论文以 H-bridge rectifier 为验证对象，其连续时间模型为

$$
\begin{aligned}
C_d\frac{dU_{dc}}{dt} &= -\frac{U_{dc}}{R}+(S_1-S_2)I_L,\\
L_d\frac{dI_L}{dt} &= (S_2-S_1)U_{dc}-R_0I_L+U_s.
\end{aligned}
$$

作者取 $x=[U_{dc}\;\; I_L]^\top$、$u=U_s$、$y=U_{dc}$，并在两种开关模态下离散化得到系统矩阵 $A_1,A_2,B_1,B_2,E_1,E_2$ 与 $C_1=C_2=[1\ 0]$。参数选择为 $\alpha=0.3$、$\beta=0.1$、$\mu=1.01$、$\bar\tau=5$，由定理条件得到平均驻留时间下界

$$
\tau_a>6.3640.
$$

初始条件设置为 $x(0)=[0.1\;\;-0.1]^\top$、$\hat x(0)=[0\;\;0]^\top$，扰动为 $\omega(k)=0.01\times \mathrm{rand}(1)$，并以 $H_\omega=0.01$ 构造有界扰动集合。仿真围绕三个问题展开：参数选择对性能指标的影响、事件触发通信量的节省效果、以及不同目标阶数 $g$ 下状态包络精度的变化。

### 3.2 主要结果与对比说明

首先，参数灵敏度结果表明，半径函数设计并不是单纯的可行性条件，而是直接影响估计性能。图 4 给出了不同 $\alpha$ 与 $\mu$ 组合下的 $L_\infty$ 性能指标。文中结论是：较小的 $\alpha$ 对应更强的同步衰减，较大的 $\mu$ 放宽了跨模态比较条件 $P_j\le \mu P_i$，因此二者会共同改善性能指标与 LMI 可行性。

![图 4：不同参数下的 $L_\infty$ 性能指标](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/figure_4.png)

其次，事件触发机制有效降低了通信频率。图 5 显示，在总共 100 个采样数据包的场景下，所提方法只触发了约 50% 的通信量。这个结果说明论文并非以增加通信为代价换取集合估计精度，而是在保留状态集合包络能力的同时显著减少了网络传输负担。

![图 5：事件触发通信演化](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/figure_5.png)

再次，状态包络精度与目标阶数之间存在明确折中。文中说明 Fig. 6 中的模态不匹配区间发生在 $[0,5)$、$[25,30)$ 与 $[70,74)$，其中前两个区间内系统模态发生了多次切换，正对应本文强调的一般异步切换场景。Figs. 7 和 8 表明真实状态被所构造的状态 Zonotope 有效包含；Fig. 9 则进一步展示了 $k=50$ 时不同目标阶数 $g$ 下的区间包络。随着 $g$ 增大，区间壳更紧，说明降阶后的 Zonotope 仍保留了“阶数越高、估计越精细”的基本趋势。

![图 9：不同目标阶数下的区间壳](https://raw.githubusercontent.com/Eroticoo/zonoieee-zonotope-reading-20260330/main/state-estimation-of-networked-switched-systems-via-event-triggered-zonotopes/images/figure_9.png)

因此，这一仿真部分支持三条可以安全得出的结论：其一，所提方法确实能在一般异步切换下保持状态集合包络；其二，事件触发机制显著降低了通信量；其三，降阶参数 $g$ 提供了“精度-复杂度”之间的可调节接口。

## 4. 后续研究方向
1. 标题：面向丢包与随机时延网络的事件触发 Zonotope 估计  
   *核心想法：* 在现有一般异步切换模型上叠加有界时延与丢包机制，重新定义触发误差、可用输出和观测器增益更新时间，再分析集合传播中的附加不确定项。  
   数学推导难度：高
2. 标题：面向 LPV 与非线性切换系统的事件触发集合传播  
   *核心想法：* 用参数依赖矩阵或局部线性化模型替代固定模态矩阵，把本文同步段与异步段的生成矩阵递推推广为参数依赖或非线性包络形式。  
   数学推导难度：很高
3. 标题：面向分布式多观测器的协同触发与半径函数联合设计  
   *核心想法：* 针对多传感器网络引入多个局部观测器与一致性耦合项，研究局部触发、模态信息共享与全局集合半径有界性之间的耦合设计问题。  
   数学推导难度：很高

## 5. 总结与评价

本文围绕网络切换系统中的一般异步状态估计，建立了一套完整的事件触发 Zonotope 设计框架。其核心贡献不是单独提出某个估计器，而是把“触发通信”“异步切换”“集合传播”“性能分析”四个环节统一进同一条理论链：先由 **Theorem 1** 完成同步段与异步段的状态集合递推，再由 **Theorem 2** 把集合半径的有界性与 $L_\infty$ 性能转化为 LMI 可解条件。

从论文边界看，结果依赖线性切换模型、有界扰动描述和平均驻留时间限制；从工程价值看，这种结构化分析给出了可调的通信-精度-复杂度折中。对于需要在网络环境下进行集合状态估计的切换系统，这篇论文提供了一条逻辑清晰且可实现的设计路线。
