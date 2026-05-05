# 自适应加权带状粒子滤波器及其在锂离子电池 SOC 与极化电压估计中的应用

![论文抬头：标题与作者](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/header_title_authors.png)

- 关键词：自适应加权；粒子滤波；状态估计；zonotope；未知但有界噪声；锂离子电池 SOC 估计
- DOI / 论文链接：https://doi.org/10.1109/TIE.2025.3632557

## 1. 研究背景、问题定义与核心思路

### 1.1 为何普通粒子滤波在未知但有界噪声下会失去优势

粒子滤波的强项是用加权样本近似后验分布，对非线性系统很灵活；它的弱点也来自这个随机采样机制。若噪声不是高斯型，而是只知道上下界的未知但有界噪声，粒子权重和重采样会受到异常粒子、粒子退化和错误扩散范围的共同影响。Kalman 类滤波同样会遇到这个问题，因为它往往依赖噪声协方差或局部线性化后的统计假设。

集合隶属估计给了另一种思路：不把噪声写成概率分布，而是用几何集合包住所有可能状态。zonotope 在这类问题中很自然，它能表示有界误差区域，又能在 Minkowski 和、线性映射和测量条带交运算中保持较好的计算结构。问题在于，若直接把粒子限制在固定集合里，集合收缩太快会压扁粒子分布，集合太松又会牺牲估计精度。本文的 AWZPF 正是围绕这个矛盾设计的：用滤波估计作为 zonotope 中心，用粒子分布生成一个粒子 zonotope，再把测量更新后的 zonotope 与粒子分布 zonotope 自适应加权融合，得到新的可行粒子域。

### 1.2 方法框架与核心思路

论文以一般非线性状态空间模型为起点：







$$
\begin{aligned}
x_{k+1}&=f(x_k)+Bu_k+w_k,\\
y_k&=h(x_k)+v_k,
\end{aligned}
$$







其中 $w_k$ 和 $v_k$ 是未知但有界的过程扰动与测量噪声。PF 部分仍按贝叶斯递推工作，后验密度由







$$
p(x_k|y_{1:k})
=
\frac{p(y_k|x_k)p(x_k|y_{1:k-1})}{p(y_k|y_{1:k-1})}
$$







更新，再用粒子近似







$$
p(x_{0:k}|y_{1:k})
\approx
\sum_{i=1}^{N}w_k^i\delta(x_{0:k}-x_{0:k}^i).
$$







AWZPF 在这个概率递推外面加了一个集合约束层。非线性函数在当前估计点 $\hat{x}_k$ 附近 Taylor 展开：







$$
f(x_k)=f(\hat{x}_k)+A_{k+1}(x_k-\hat{x}_k)+e_k
=f_L(x_k)+e_k,
$$







忽略线性化误差项后得到局部线性模型







$$
\begin{aligned}
x_{k+1}&=Ax_k+Bu_k+w_k,\\
y_k&=Cx_k+v_k.
\end{aligned}
$$







若 $x_0\in\langle p_0,G_0\rangle$，$w_k\in\langle 0,W\rangle$，$v_k\in\langle 0,V\rangle$，预测 zonotope 写成







$$
Z_{k-1|k}
=
\langle p_{k-1|k},H_{k-1|k}\rangle,
\qquad
p_{k-1|k}=Ap_{k-1}+Bu_{k-1},
$$













$$
H_{k-1|k}=
\begin{bmatrix}
AH_{k-1} & W
\end{bmatrix}.
$$







测量更新通过测量条带







$$
S_k=\{x\mid |Cx_k-y_k|\le V\}
$$







收缩预测集合，并得到更新后的 $Z_k=\langle p_k,H_k\rangle$。之后，AWZPF 不直接把所有粒子硬塞进 $Z_k$，而是把粒子投影到生成矩阵方向上，剔除极端投影，仅包住比例 $\gamma$ 的粒子，形成粒子分布 zonotope $Z_{par,k}$。最终可行粒子域由 $Z_k$ 与 $Z_{par,k}$ 的加权融合给出。

![AWZPF 算法流程图](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/figure_2.png)

### 1.3 主要创新点

最有分量的创新是把粒子分布本身变成一个可参与几何运算的 zonotope。传统集合粒子滤波常用固定盒、椭球或外包集合筛选粒子，但集合形状主要由模型更新决定，不一定贴合当前粒子云。本文用 $Z_{par,k}$ 描述粒子主体分布，只包住比例 $\gamma$ 的粒子，等于承认随机粒子中会有离群点，并把它们对可行域尺度的影响削弱。

自适应加权是第二个关键处理。$Z_k$ 更偏向测量约束和集合收缩，$Z_{par,k}$ 更偏向粒子多样性；论文让两者的权重随粒子覆盖率和迭代衰减因子变化。早期保持较大的粒子分布权重，避免收缩过快；后期逐渐提高更新 zonotope 的权重，使边界更紧。这比固定融合系数更适合处理状态估计的收敛过程。

应用层面的贡献是把 AWZPF 放到锂离子电池 SOC 与极化电压估计中验证。电池模型采用一阶 Thevenin 等效电路，噪声设置为含高斯成分和均匀成分的非高斯混合噪声，用 EKF、线性回归、RBPF、ESMPF 和 AWZPF 对比。实验不仅看 SOC 点估计，也看极化电压 $U_p$、可行粒子域面积、间歇放电、变倍率放电和初值偏差下的鲁棒性。

## 2. 核心方法与技术主线解析

### 2.1 整体技术路线

zonotope 的基础表示为







$$
Z=p\oplus HB^r
=
\left\{
p+\sum_{i=1}^{r}\beta_i h_i:\ -1\le \beta_i\le 1
\right\}
=\langle p,H\rangle .
$$







AWZPF 每一步先做普通粒子预测和权重更新，再用集合估计得到 $Z_k$，随后构造 $Z_{par,k}$ 并融合成 $Z_{aw,k}$，最后判断每个粒子是否落在可行粒子域中。落在域外的粒子被丢弃，并在可行域内重新生成同数量粒子，从而维持粒子数 $N$ 不变。

电池应用的系统模型必须单独看。论文选用一阶 Thevenin 模型，状态为 $SOC_k$ 与极化电压 $U_{p,k}$，输出为端电压 $U_k$：

![一阶 Thevenin 等效电路模型](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/figure_3.png)







$$
\begin{aligned}
\begin{bmatrix}
SOC_{k+1}\\
U_{p,k+1}
\end{bmatrix}
&=
\begin{bmatrix}
1 & 0\\
0 & e^{-\frac{\Delta t}{R_pC_p}}
\end{bmatrix}
\begin{bmatrix}
SOC_k\\
U_{p,k}
\end{bmatrix}\\
&\quad+
\begin{bmatrix}
-\frac{\eta\Delta t}{Q_{real}}\\
R_p\left(1-e^{-\frac{\Delta t}{R_pC_p}}\right)
\end{bmatrix}I_k+w_k,\\
U_{k+1}
&=
U_{OC,k}-U_{p,k+1}-I_{k+1}R_0+v_k.
\end{aligned}
$$







这里 $R_0$ 是欧姆内阻，$R_p$ 和 $C_p$ 描述极化支路，$Q_{real}$ 为实际容量，$\eta$ 为库仑效率。OCV-SOC 数据按 10% SOC 间隔记录，文中进一步用线性关系近似实验段内的开路电压：







$$
U_{k+1}
=0.5293\,SOC+3.5821-U_{p,k+1}-I_{k+1}R_0+v_k.
$$







这个模型把 SOC 和 $U_p$ 放在同一状态向量中，因此 AWZPF 的粒子可行域不是一维置信区间，而是 SOC-$U_p$ 平面中的二维集合。

### 2.2 关键技术块解析

**Theorem 1 给出了粒子分布 zonotope 的构造方式。** 它要求 $Z_{par,k}$ 至少包住比例 $\gamma$ 的粒子：

![Theorem 1：粒子分布 zonotope 的构造](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/theorem_1.png)







$$
x_k^i\in Z_{par,k}
=
\langle p_k,H_{par,k}\rangle,
$$













$$
p_k=\sum_{i=1}^{N}w_{k-1}^{i}x_{k-1}^{i},
$$













$$
H_{par,k}
=
\left[
(I-\tilde{\lambda}_kC)AH_{k-1}\quad
(I-\tilde{\lambda}_kC)W\quad
\tilde{\lambda}_kV
\right]G_k.
$$







其中 $G_k=\mathrm{diag}(g_k^1,\ldots,g_k^r)$ 是方向缩放矩阵。论文把粒子相对中心的偏移 $\Delta d_k=x_k^i-p_k$ 投影到 $H_k$ 的列空间：







$$
\beta_{i,k}
=
(H_k^\top H_k)^{-1}H_k^\top(x_k^i-p_k)
=
H_k^{+}(x_k^i-p_k).
$$







每个方向上排序后去掉最外侧 $(1-\gamma)N$ 个投影，再用剩余投影的最大绝对值确定缩放因子：







$$
g_k^i=\max\{|\beta_{i,k}^{min}|,\ |\beta_{i,k}^{max}|\}.
$$







这个处理的实际含义很直接：粒子域不为少数离群粒子无节制膨胀，而是围绕主粒子群调整。原文在定理后的说明还强调，这种方向独立缩放保持了 $Z_{par,k}$ 的全对称结构，所以后续还能继续用标准 zonotope 运算。

**Theorem 2 是 AWZPF 名字里“自适应加权”的来源。** 更新后的测量 zonotope $Z_k$ 和粒子分布 zonotope $Z_{par,k}$ 共享中心 $p_k$，融合只发生在生成矩阵上：

![Theorem 2：自适应加权 zonotope 融合](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/theorem_2.png)







$$
H_{aw,k}
=
\lambda_{z,k}H_k+\lambda_{par,k}H_{par,k},
$$













$$
\lambda_{z,k}
=
\lambda_{z,k-1}+w_{z,k}w_{par,k},
\qquad
\lambda_{par,k}
=
\lambda_{par,k-1}-w_{z,k}w_{par,k}.
$$







两个自适应量分别为







$$
w_{z,k}=\frac{cont_k}{N},
\qquad
w_{par,k}
=
\frac{1-\lambda_{z,k-1}}{L-k+1},
$$







其中 $cont_k=\{x_k^i\mid x_k^i\in Z_{aw,k}\}$ 表示当前被 zonotope 包住的粒子数，$L$ 是迭代次数。$w_{z,k}$ 反映粒子覆盖情况，$w_{par,k}$ 让调整幅度随迭代推进逐步减小。若早期 $Z_k$ 收缩过快，粒子覆盖会变差，融合会更多依赖 $Z_{par,k}$；当迭代稳定后，$\lambda_{z,k}$ 逐渐增大，边界随测量约束进一步收紧。

**Theorem 3 负责判定粒子是否越界。** 对粒子 $x_k^i$，定义







$$
d_k=x_k^i-p_k,
\qquad
u=\frac{d_k}{\|d_k\|}.
$$







![Theorem 3：可行粒子域的越界判定](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/theorem_3.png)

判定条件写成







$$
\|d_k\|
\le
\langle p_k,u\rangle
+
\sum_{j=1}^{r}|\langle h_j,u\rangle|.
$$







右侧是 zonotope 在方向 $u$ 上的支撑函数边界；若粒子沿自身偏移方向超过这个边界，就被认为不在可行粒子域内。这个判定比逐面检查多面体约束更贴合 zonotope 的生成矩阵表示，也解释了为什么论文能在粒子更新阶段直接执行“丢弃域外粒子、域内重采样”的操作。

复杂度分析显示，AWZPF 的四个环节分别对应构造 zonotope、生成粒子 zonotope、自适应加权和粒子更新。总复杂度给为







$$
O(Nn_xr+Nr\log N+r^3),
$$







其中 $N$ 是粒子数，$n_x$ 是状态维数，$r$ 是生成矩阵列数。这里的 $Nr\log N$ 主要来自每个生成方向上的粒子投影排序；$r^3$ 则来自测量条带更新中的矩阵求逆与相关计算。

## 3. 实验结果与对比分析

实验对象为 18650 锂离子电池，额定容量 1.5 Ah，标称电压 3.7 V，充电截止电压 4.2 V，放电截止电压 2.4 V。电池在 $25^\circ C$ 环境中以 1C 恒流放电获取 OCV-SOC 关系，OCV 表中 SOC 从 0% 到 100% 每 10% 采样一次，对应 OCV 从 2.79 V 增至 4.18 V。一阶 Thevenin 参数通过最小二乘识别得到：$R_0=0.0419\Omega$，$R_p=0.0312\Omega$，$C_p=2365.8$ F，采样时间 $\Delta t=5$ s。噪声采用混合概率密度，包含高斯项和均匀项：







$$
PDF
=
\lambda\frac{1}{\sqrt{2\pi\sigma^2}}
\exp\left(-\frac{(x-\mu)^2}{2\sigma^2}\right)
+
(1-\lambda)\frac{1}{b-a},
$$







其中 $\lambda=0.5$，$\sigma^2=0.0001$，均匀噪声区间为 $[-0.01,0.01]$。对比算法包括 EKF、线性回归 LR、RBPF、ESMPF 与 AWZPF。

![ESMPF 与 AWZPF 可行域面积对比](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/figure_6.png)

Fig. 6 比较了 ESMPF 和 AWZPF 在 $k=100,300,500,700$ 时的可行域。黑色星标是真实状态，蓝色区域对应 ESMPF，红色区域对应 AWZPF。两个方法都能包住真实状态，但 AWZPF 的可行域明显更窄，尤其在 SOC-$U_p$ 二维平面中，红色 zonotope 更贴近真实点附近的主粒子分布。这说明自适应加权不是单纯缩小边界，而是在保持真实状态可包容的同时降低保守性。

![SOC 状态估计结果对比](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/figure_8.png)

Fig. 8 给出 SOC 估计轨迹。EKF 和 LR 在 UBB 噪声下偏差较大，RBPF 和 ESMPF 明显更稳，AWZPF 的轨迹最贴近真实 SOC。局部放大图中，AWZPF 的估计线和边界收缩更紧；这与 Theorem 2 的加权机制一致，即后期更强调 $Z_k$ 的收缩能力，同时仍通过 $Z_{par,k}$ 保留足够粒子分布信息。

![极化电压 Up 状态估计结果对比](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/figure_9.png)

Fig. 9 对比极化电压 $U_p$。$U_p$ 不是实验直接测量值，而是由一阶 Thevenin 模型递推得到的内部状态，因此这个结果更依赖模型和滤波器对状态耦合的处理。AWZPF 在 $U_p$ 上仍然保持最小误差区间，说明其可行粒子域并没有只针对 SOC 轴优化，而是在二维状态空间中同时约束 SOC 和极化电压。

![五种算法 RMSE 对比](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/table_4.png)

Table IV 的数值更直接。AWZPF 的 SOC RMSE 为 0.00450，低于 EKF 的 0.0168、LR 的 0.00816、RBPF 的 0.00513 和 ESMPF 的 0.00546；$U_p$ 的 RMSE 为 0.00683，也低于其他四种方法。相对于 ESMPF，AWZPF 在 SOC 和 $U_p$ 上分别降低约 21.3% 和 16% 的 RMSE。RBPF 是强基线，它已经通过盒粒子表示缓解了有界不确定性问题，但本文的 zonotope 可行域更贴近真实状态附近的方向性分布，因此误差略低。

![zonotope 面积收敛对比](https://cdn.jsdelivr.net/gh/Eroticoo/zonoieee-zonotope-reading-20260330@c3e114a8f106a3f5d39fa4c527870d540408d265/adaptive-weighted-zonotopic-particle-filter-and-its-application-to-soc-and-polarization-voltage-estimation-of-lithium-ion-batteries/images/figure_13.png)

Fig. 13 展示可行粒子域面积变化。蓝色 zonotope 面积在约 40 次迭代内迅速收敛，红色 particle zonotope 面积则随粒子分布继续变化。这个结果支持论文对自适应融合的解释：测量更新 zonotope 提供稳定收缩边界，粒子 zonotope 保留粒子云的分布弹性。论文还用间歇放电、变倍率放电和不同初始 SOC 的结果检验鲁棒性；在初始 SOC 设为 1.0 与 0.8 时，AWZPF 能在运行后较快向真实值收敛。

实验的边界也应放在同一位置看。论文的电池验证主要在 $25^\circ C$、单节 18650 电池和一阶 Thevenin 模型上完成；不同温度、老化程度和更强动态工况下，OCV-SOC 曲线、$R_0$、$R_p$、$C_p$ 都可能变化。AWZPF 处理的是 UBB 噪声和粒子域收缩问题，并没有直接解决参数漂移或模型结构误差。

## 4. 后续研究方向

1. 如果你刚开始读这篇文章
   *核心想法：先读懂式 (16)-(18) 的粒子 zonotope 构造和式 (27)-(31) 的自适应加权，电池模型只需先抓住 SOC 与 $U_p$ 这两个状态。*
   数学推导难度：中等
2. 如果你想继续往下深挖
   *核心想法：最值得推的是把 $\gamma$、$\lambda_{z,k}$ 和 $\lambda_{par,k}$ 的经验调节改成带稳定性或覆盖概率解释的参数设计。*
   数学推导难度：较高
3. 如果你要带学生做这条线
   *核心想法：可以按 PF 复现、zonotope 运算、AWZPF 粒子筛选、电池 SOC 验证、温度/老化扩展五个阶段拆任务。*
   数学推导难度：中等到较高

## 5. 总结与评价

这篇论文的核心判断是：面对未知但有界噪声，粒子滤波不能只靠增加粒子数或重采样，必须给粒子更新一个能随分布变化的几何可行域。实验结果支持这一判断，AWZPF 在 SOC 和极化电压 RMSE 上都优于 EKF、LR、RBPF 和 ESMPF，并且可行域面积更紧。方法的主要限制是电池部分仍依赖固定 Thevenin 参数和单温度验证，参数漂移带来的模型误差还没有被纳入 AWZPF 的集合设计。若把它扩展到车载 BMS，下一步应把参数在线辨识和自适应 zonotope 融合放在同一个框架中处理。
