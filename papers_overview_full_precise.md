# ZonoIEEE 18 篇论文中文总清单（合并版）

> 当前版本覆盖 18 篇论文的英文标题、中文翻译标题、作者、学校与主要贡献点。
> 说明：作者保持原英文状态；学校统一翻译为中文环境对应大学 / 机构名称。

---

## 1. A novel dual-time-scale zonotopic Kalman filter integrated with optimizable convolutional long short-term memory for enhanced state of charge estimation in batteries of electric vehicles
- 中文翻译标题：一种与可优化卷积长短期记忆网络集成的新型双时间尺度 zonotopic Kalman 滤波器及其在电动汽车电池荷电状态估计中的应用
- 作者：Xin-Chen Zhou；Zi-Yun Wang；Yan Wang；Zhe-Kang Dong
- 学校：江南大学（物联网技术与应用教育部工程研究中心）；杭州电子科技大学（电子信息学院）
- 主要贡献点（精确版）：
  1. 将双时间尺度 zonotopic Kalman filter 与可优化卷积 LSTM 结合，用于电动汽车锂电池 SOC 估计。
  2. 在 unknown-but-bounded 噪声背景下，用 zonotopic 集合传播提升 SOC 估计的鲁棒性与可解释性。
  3. 通过耦合集合估计与时序学习模型，兼顾物理约束、噪声鲁棒性与估计精度。

## 2. A novel zonotopic Kalman filter-based actuator fault detection for time delay systems
- 中文翻译标题：一种基于 zonotopic Kalman 滤波器的时滞系统执行器故障检测方法
- 作者：Yu-Qing Ma；Zi-Yun Wang；Yan Wang；Ju H. Park；Zhi-Cheng Ji
- 学校：江南大学（物联网技术与应用教育部工程研究中心）；韩国岭南大学（电气工程系）
- 主要贡献点（精确版）：
  1. 面向线性离散时滞系统，提出基于 zonotopic Kalman filter 的执行器故障检测算法。
  2. 用 zonotope 表达状态、残差与故障相关不确定集合，使故障检测具备集合可分离性解释。
  3. 将时滞、噪声与故障项统一纳入递推框架，提高有界扰动下的鲁棒性。

## 3. A Zonotope-based Big Data-driven Predictive Control Approach for Nonlinear Processes
- 中文翻译标题：一种面向非线性过程的基于 zonotope 的大数据驱动预测控制方法
- 作者：Shuangyu Han；Yitao Yan；Jie Bao；Biao Huang
- 学校：新南威尔士大学（化学工程学院）；阿尔伯塔大学（化学与材料工程系）
- 主要贡献点（精确版）：
  1. 提出 zonotope-based big data-driven predictive control (BDPC) 方法，用于非线性过程控制。
  2. 通过两步层次聚类将非线性过程行为划分为多个线性子行为，构造适合在线优化的预测模型。
  3. 用 zonotope 近似未来可行轨迹集合，使 MPC 优化器面对的是可计算的凸集合而非原始样本云。

## 4. Coloring zonotopal quadrangulations of the projective space
- 中文翻译标题：射影空间中 zonotopal 四边形剖分的着色问题
- 作者：Masahiro Hachimori；Atsuhiro Nakamoto；Kenta Ozeki
- 学校：筑波大学（工程、信息与系统学院）；横滨国立大学（环境与信息科学学院）
- 主要贡献点（精确版）：
  1. 研究射影空间中 zonotopal quadrangulations 的着色性质，属于 zonotope 的组合与离散几何方向。
  2. 给出相应结构性结论，用于说明这类 zonotopal 结构的可着色规律或可构造性质。
  3. 拓展了 zonotope 在组合拓扑与离散几何中的理论边界。

## 5. Efficient zonotope reduction via hyperplane zonotopic contractors for uncertainty representation
- 中文翻译标题：基于超平面 zonotopic contractor 的高效 zonotope 降阶方法
- 作者：Rahma BENGAMRA；Soheib FERGANI；Carine JAUBERTHIE
- 学校：法国图卢兹大学 / 法国国家科学研究中心自动化与系统分析实验室（LAAS-CNRS）
- 主要贡献点（精确版）：
  1. 面向不确定性表示中的计算复杂度问题，提出新的 zonotope reduction 方法。
  2. 利用 hyperplane zonotopic contractors 改善传统 zonotope 降阶中的效率与精度权衡。
  3. 为后续集合传播、鲁棒估计和控制中的高维 zonotope 提供更轻量但仍保有关键几何信息的表达形式。

## 6. Ellipsoid-density-based set-membership global estimation for complex networked systems with absolute and relative measurements
- 中文翻译标题：面向绝对测量与相对测量复杂网络系统的基于椭球密度的集员全局估计
- 作者：Mingyang Luo；Yilian Zhang；Huaicheng Yan；Fuwen Yang
- 学校：上海海事大学（交通运输行业海洋技术与控制工程重点实验室）；华东理工大学（化工过程先进控制与优化教育部重点实验室）；格里菲斯大学（工程与建筑环境学院）
- 主要贡献点（精确版）：
  1. 研究复杂网络系统在 unknown-but-bounded 过程噪声与测量噪声下的全局状态估计问题。
  2. 同时处理绝对测量与相对测量信息，提升网络化系统中分布式 / 全局估计的表达能力。
  3. 将 set-membership estimation 与椭球密度思想结合，在保守性和估计精度之间取得更优平衡。

## 7. Fault detection based on an improved zonotopic Kalman filter with application to a wind turbine drivetrain
- 中文翻译标题：基于改进 zonotopic Kalman 滤波器的故障检测及其在风力机传动链中的应用
- 作者：Lanshuang Zhang；Zhenhua Wang；Vicenç Puig；Yi Shen
- 学校：哈尔滨工业大学（控制科学与工程系）；加泰罗尼亚理工大学（自动控制系）
- 主要贡献点（精确版）：
  1. 针对含参数不确定性的离散时间系统，提出改进的 zonotopic Kalman filter 故障检测方法。
  2. 将方法应用到风力机传动链场景，用于传感器或系统故障检测。
  3. 结合 zonotopic 集合估计与线性规划思想，提高参数不确定与有界噪声背景下的鲁棒性。

## 8. Fault-tolerant control and zonotopic interval estimation for discrete-time linear systems based on an event-triggered mechanism
- 中文翻译标题：基于事件触发机制的离散时间线性系统容错控制与 zonotopic 区间估计
- 作者：Mingliang Tian；Zhihua Guo；Xiangqing Niu；Ben Niu；Chenguang Ning；Wenqi Zhou
- 学校：山东师范大学（信息科学与工程学院）；江南大学（物联网工程学院）；东南大学（移动通信国家重点实验室）
- 主要贡献点（精确版）：
  1. 联合研究离散时间线性系统中的容错控制与 zonotopic 区间估计。
  2. 引入事件触发机制，降低通信 / 更新频率并保持闭环性能。
  3. 用 zonotope 递推跟踪触发间隔内的不确定性传播，使容错控制具备可计算的鲁棒保证。

## 9. Improved set-membership-filtering-based state estimation for target localization system
- 中文翻译标题：面向目标定位系统的改进集员滤波状态估计方法
- 作者：Xinyong Zhang；Jiongqi Wang；Haiyin Zhou；Bowen Hou
- 学校：国防科技大学
- 主要贡献点（精确版）：
  1. 研究噪声统计模型未知但噪声边界已知时的目标定位集员滤波状态估计问题。
  2. 提出改进型集员滤波方法，提高目标定位中的状态估计精度与鲁棒性。
  3. 将 zonotope 与信息融合思想结合，用于处理非线性测量背景下的定位估计问题。

## 10. Robust fault detection method based on interval neural networks optimized by ellipsoid bundles
- 中文翻译标题：基于椭球束优化区间神经网络的鲁棒故障检测方法
- 作者：Meng Zhou；Yinyue Zhang；Jing Wang；Tarek Raïssi；Vicenç Puig
- 学校：北方工业大学（电气与控制工程学院）；法国国立工艺学院（CNAM Cedric-Laetitia 实验室）；加泰罗尼亚理工大学（机器人研究所 / 高级控制系统研究组）
- 主要贡献点（精确版）：
  1. 提出面向数据驱动系统的区间预测神经网络模型，并使用椭球束进行优化。
  2. 面向未知但有界噪声与扰动场景，实现鲁棒故障检测。
  3. 通过将神经网络输出扩展为区间 / 集合形式，提高对模型误差与噪声不确定性的适应能力。

## 11. Robust tube-based economic model predictive control of nonlinear systems using linear parameter varying approach
- 中文翻译标题：基于线性参数变化方法的非线性系统鲁棒管状经济模型预测控制
- 作者：Heithem Boufrioua；Boubekeur Boukhezzar；Vicenç Puig
- 学校：阿尔及利亚康斯坦丁第一兄弟门图里大学（理学院与技术学院电子系，LARC 实验室）；加泰罗尼亚理工大学（机器人与工业信息研究所 / 监督、安全与自动控制研究中心）
- 主要贡献点（精确版）：
  1. 将线性参数变化（LPV）方法引入非线性系统的鲁棒 tube-based economic MPC 设计中。
  2. 使用 zonotope 表达鲁棒管状边界与约束收缩集合，使经济优化目标与鲁棒可行性统一在同一优化框架中。
  3. 同时关注 input-to-state stability、递归可行性与经济性能，是 zonotope 在鲁棒 EMPC 方向的典型应用。

## 12. Set-membership estimation for nonlinear systems based on zonotope analysis
- 中文翻译标题：基于 zonotope 分析的非线性系统集员估计
- 作者：Hao Yang；Huaicheng Yan；Zhichen Li；Meng Wang；Fuwen Yang
- 学校：华东理工大学（能源化工过程智能制造教育部重点实验室）；潍坊学院（机械与自动化学院）；格里菲斯大学（工程与建筑环境学院）
- 主要贡献点（精确版）：
  1. 研究非线性系统中的 zonotopic set-membership state estimation (SMSE) 问题。
  2. 将 zonotope 作为核心集合表达工具，用于构造始终包含真实状态的有界估计集合。
  3. 结合 semi-infinite programming 与 L∞ 性能指标，提升理论严谨性与可计算性。

## 13. Zonotope-based attack detection for cyber-physical systems with a dynamic event-triggered scheme
- 中文翻译标题：具有动态事件触发机制的网络物理系统基于 zonotope 的攻击检测
- 作者：Jianing Hu；Zhihua Guo；Ben Niu；Xudong Zhao；Zhenhua Wang；Wenqi Zhou
- 学校：山东师范大学（信息科学与工程学院）；大连理工大学（控制科学与工程学院）；东南大学（移动通信国家重点实验室）
- 主要贡献点（精确版）：
  1. 研究线性离散时间网络物理系统在 unknown-but-bounded 外部扰动与测量噪声下的攻击检测问题。
  2. 将动态事件触发机制与 zonotope 集合传播结合，在降低通信负担的同时保持攻击检测能力。
  3. 通过 zonotope 对残差与不确定性集合进行可达包络建模，提高攻击检测的鲁棒性。

## 14. Zonotopic distributed fusion for 2-D nonlinear systems under binary encoding schemes: An outlier-resistant approach
- 中文翻译标题：二值编码方案下二维非线性系统的 zonotopic 分布式融合：一种抗离群值方法
- 作者：Lan Lan；Guoliang Wei
- 学校：上海理工大学（理学院）；上海理工大学（商学院）
- 主要贡献点（精确版）：
  1. 研究带 unknown-but-bounded 噪声、测量离群值和带宽限制的传感网络中二维非线性系统的 zonotopic distributed fusion。
  2. 在 binary encoding / dynamic quantization 背景下，实现对二维非线性系统的分布式融合估计。
  3. 突出抗离群值特性，说明该方法不仅处理普通噪声，还面向异常测量下的鲁棒融合问题。

## 15. Zonotopic fault detection observer design for discrete-time Lipschitz nonlinear systems
- 中文翻译标题：离散时间 Lipschitz 非线性系统的 zonotopic 故障检测观测器设计
- 作者：Lanshuang Zhang；Zhenhua Wang；Vicenç Puig；Yi Shen
- 学校：哈尔滨工业大学（控制科学与工程系）；加泰罗尼亚理工大学（自动控制系）
- 主要贡献点（精确版）：
  1. 针对离散时间 Lipschitz 非线性系统，提出基于 zonotope 的故障检测观测器设计方法。
  2. 将残差生成与残差评估统一到 zonotopic 分析框架中，增强对未知有界扰动和建模误差的鲁棒性。
  3. 通过 theorem / lemma / property 链条建立观测器设计准则与理论可检测性保证。

## 16. Zonotopic set-membership state estimation for linear repetitive processes with multirate measurements under encoding-decoding mechanisms
- 中文翻译标题：编码解码机制下具有多速率测量的线性重复过程 zonotopic 集员状态估计
- 作者：Qi Li；Minghao Gao；Yufei Liu；Hongjian Liu
- 学校：杭州师范大学（信息科学与技术学院）；东北石油大学（人工智能能源研究院）；安徽工程大学（电气工程学院；教育部高端装备先进感知与智能控制重点实验室；数理与金融学院）
- 主要贡献点（精确版）：
  1. 面向带多速率测量的参数变化线性重复过程，研究 coding-decoding-based fusion estimation 问题。
  2. 在编码—解码通信机制下，引入 zonotopic set-membership estimation 处理有界噪声和不同采样率传感器带来的融合估计问题。
  3. 将线性重复过程（2-D systems）与 zonotopic 集合估计框架结合，是 zonotope 在多速率 / 编码约束环境中的扩展应用。

## 17. Zonotopic set-membership state estimation for nonlinear systems based on the deep Koopman operator
- 中文翻译标题：基于深度 Koopman 算子的非线性系统 zonotopic 集员状态估计
- 作者：Zhichao Pan；Siyu Liu；Biao Huang；Fei Liu
- 学校：浙江师范大学（计算机科学与技术学院；工学院）；阿尔伯塔大学（化学与材料工程系）；江南大学（轻工过程先进控制教育部重点实验室 / 自动化研究所）
- 主要贡献点（精确版）：
  1. 将 deep Koopman operator 引入非线性系统的 zonotopic set-membership state estimation 中。
  2. 在深度 Koopman 提供的近似线性提升空间里进行 zonotope 集合递推，从而兼顾非线性建模能力与集合估计可计算性。
  3. 让 zonotope 不仅作用于原始状态空间，也作用于 Koopman 提升空间中的潜在状态表示。

## 18. Zonotopic state estimation and fault detection for discrete-time linear systems under DoS attacks: A switching controller design mechanism
- 中文翻译标题：DoS 攻击下离散时间线性系统的 zonotopic 状态估计与故障检测：一种切换控制器设计机制
- 作者：Zhihua Guo；Xinjun Wang；Mingliang Tian；Jianing Hu
- 学校：山东师范大学（计算机科学与人工智能学院）
- 主要贡献点（精确版）：
  1. 针对存在非周期 DoS 攻击的离散时间线性系统，研究 zonotopic 集员状态估计与故障检测问题。
  2. 在未知但有界扰动和噪声背景下，用 zonotope 递推量测中断期间的可达状态与残差包络。
  3. 提出 switching controller design mechanism，使攻击下的估计、检测与控制重构形成统一框架。
