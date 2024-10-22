论文连接: https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10447570




### 论文中的定义
#### 心脏时空信号构建的含义

1. **时空信号的定义**：
    
    - **时域（Temporal）**：指信号随时间变化的特性。在心率变异性（HRV）监测中，时域信号反映了心跳的时间间隔和频率变化。
    - **空间域（Spatial）**：指信号在空间上的分布和变化。在雷达感测中，空间域信号涉及身体不同部位反射回来的雷达信号，这些信号可能因心脏活动而产生微小的运动。
2. **信号构建的过程**：
    
    - **数据采集**：利用毫米波雷达（如IWR1843BOOST）从人体不同部位采集反射信号。这些信号包含了由心脏活动引起的微小运动信息。
    - **信号分离与提取**：从多个反射信号中分离出与心跳相关的部分。这可能涉及滤波、去噪和特征提取等步骤。
    - **时空融合**：将来自不同空间位置的信号进行整合，建立一个全面的时空信号模型。这有助于更准确地捕捉心脏活动的全貌，而不仅仅依赖于单一位置的信号。
    - **特征表示**：将构建的时空信号转换为适合深度学习模型（如卷积-变压器混合网络）处理的格式，以便进一步进行心率变异性的分析和监测。
3. **重要性**：
    
    - **提高准确性**：通过综合考虑全身多个部位的反射信号，可以更准确地检测和分析心跳，从而提升HRV监测的精度。
    - **减少误差**：单一位置的信号可能受到局部干扰或噪声的影响，而多位置的信号融合可以有效减少这些误差，提高系统的鲁棒性。
    - **捕捉复杂模式**：心脏活动不仅仅在时间上有规律变化，在空间上也可能呈现复杂的运动模式。时空信号构建能够更好地捕捉这些复杂的模式，为后续的深度学习分析提供丰富的信息。
###  摘要

雷达传感一直是非接触式心率变异性（HRV）监测的一种有前途的解决方案，心率变异性是心血管和自主神经系统的重要指标。然而，现有的工作忽略了心跳驱动的身体表面运动的空间变化，这限制了他们的准确性，在识别细网格连续的心跳时序和整体HRV性能。在本文中，*我们提出利用全身反射，并通过深度神经网络对这些反射与心跳之间的内在时空关系进行建模，以实现非接触式HRV监测*。具体而言，一个混合卷积变换为基础的网络被设计成一个高效的序列建模过程中复杂的多维时空建模问题。实验结果表明，该方法优于基线方法，IBI估计误差中值为12ms（w.r.t.98.47%的准确度），RMSDD误差为7.3ms，SDRR误差为2.9ms，pNN 50误差为5.5%。


### 1 介绍

*心率变异性（HRV），即心跳间隔（IBI）之间的周期变化，被认为是个体整体健康状况的重要指标[1，2]*。HRV分析提供了对心脏健康和自主神经系统（ANS）的见解[2，3]。因此，开发更方便、舒适、有效的心率变异性监测技术一直受到学术界和工业界的高度关注。在这些技术中，由于非接触式，隐私保护和舒适的用户体验，雷达传感已成为一种有前途的解决方案[4，5，6，7，8，9，10，11]。在这些工作中，各种努力都集中在利用信号处理算法来建模RF信号和心跳运动之间的关系[6，7，8]。Wang等人[7]提出优化由胸部运动调制的通道信息的相位的分解，从而估计所述心跳信号并进一步评估所述HRV度量。此外，随着深度学习模型的快速增长，关于这个主题的相关作品数量也显着增加[4，5，9]。Zhang等人[9]提出通过利用变分编码器-解码器网络设计来分解非线性信号混合并恢复细粒度心跳波形。

通常，*基于雷达的心率变异性（HRV）感测通过从身体表面的特定部位分离反射信号，恢复信号中调制的心跳波形，最终从波形中计算HRV指标*然而，如文献[12]所强调的，心脏的惯性机械活动驱动整个躯干表面以相同的心跳节律运动，但具有空间变化的形态，而不是仅在单一身体表面部位进行一维运动。这表明，之前的方法在考虑一维运动并忽视躯干表面其他部位之间的关系时，可能在识别细粒度连续心跳时序方面存在模糊性。因此，为了实现准确的HRV监测，我们需要综合考虑来自整个身体表面的反射信号。

然而，由于两个主要挑战，在雷达感测中解决上述问题是复杂的。*首先，与容易识别皮肤区域的基于视觉的光电体积描记术不同，雷达传感难以准确地分离整个躯干表面的反射。其次，对空间变化的身体表面运动和心跳定时之间的关系进行建模是具有挑战性的，特别是考虑到无线电信号域中的显著噪声和干扰*。

在本文中，*我们提出利用全身反射信号，并通过深度学习网络对这些反射与心跳之间的内在时空关系进行建模，以实现非接触式HRV监测*  ,具体来说，我们提取粗网格体素信号周围的目标体从原始信号和构建的时空心脏信号表示，其中包括整个身体表面的心跳运动与空间冗余。然后，一个混合卷积变换为基础的网络被设计成一个高效的序列建模过程中复杂的多维时空建模问题。最后，基于由网络生成的心跳波形来计算HRV度量。为了证实我们的建议的有效性，我们收集了一个由8个不同参与者的数据组成的数据集。对国家的最先进的方法的比较分析一致证明了我们的方法所取得的上级性能。


### 2 系统设计

####  2.1.心脏时空信号的构建
首先，我们需要提取从整个身体表面反射的心脏时空信号。使用雷达传感器检测心脏微动的基本概念涉及提取从目标反射的接收信号的相位变化。相位模型在数学上定义如下：

$$
\phi(t) = \frac{2\pi d(t)}{\lambda}
$$

该公式描述了相位 $\phi(t)$ 与距离 $d(t)$ 以及波长 $\lambda$ 之间的关系。具体来说：

- $\phi(t)$：随时间变化的相位。
- $d(t)$：随时间变化的距离。
- $\lambda$：雷达信号的波长。

**公式含义**：

在雷达系统中，发射的毫米波信号遇到目标物体后会反射回来。反射信号的相位$\phi(t)$ 随着目标物体距离 $d(t)$的变化而变化。波长 $\lambda$ 是雷达信号的一个关键参数，决定了信号的分辨率和穿透能力。

通过该公式，可以将测得的相位变化 $\phi(t)$转换为目标物体的距离变化 $\phi(t)$。这在心率变异性（HRV）监测中尤为重要，因为心跳引起的胸部微小运动会导致反射信号的相位发生微小变化，从而可以通过分析相位变化来检测心跳和计算HRV指标。

**应用示例**：

假设雷达信号的波长 \(\lambda = 0.03\) 米（对应频率约为10 GHz），如果测得的相位变化 \(\phi(t) = \pi/2\)，则可以计算出距离变化：

$$
d(t) = \frac{\phi(t) \cdot \lambda}{2\pi} = \frac{\frac{\pi}{2} \cdot 0.03}{2\pi} = \frac{0.03}{4} = 0.0075 \text{ 米} = 7.5 \text{ 毫米}
$$

这表明心跳引起的胸部运动导致的距离变化约为7.5毫米。

其中$λ$表示RF信号的波长，$d（t$）表示雷达传感器与反射物体之间的距离，t表示感测时间

然而，从接收到的RF信号中精确地识别和提取这些信号是复杂的。相反，我们首先从具有空间冗余的目标中提取信号，以确保包括整个身体反射。然后，我们强调了心跳的特点，并通过时间滤波抑制这些信号中的其他干扰。这种流线型的处理方法帮助我们构建时空心脏信号表示，确保与心跳相关的所有反射都包含嵌入的时空细节.

具体而言，我们选择粗粒度的空间范围，以确保使用[14]中的波束成形和定位技术包含整个身体反射。波束成形计算表示为：


### 数学公式解释

您提供的公式如下：

$$
S(x, y, t) = \sum_{n=1}^{XN} \sum_{tf=1}^{XT} y_{n, tf} \cdot e^{j 2\pi (tf - 1) T_s k r(x, y, n)} \cdot c \cdot e^{j \frac{2\pi r(x, y, n)}{\lambda}}
$$

让我们逐项解析这个公式：

1. **\( S(x, y, t) \)**：
   - 表示在空间坐标 \((x, y)\) 和时间 \(t\) 点的雷达信号。

2. 双重求和符号 \(\sum_{n=1}^{XN} \sum_{tf=1}^{XT}\)**：
   - **\( n \)**：索引，可能代表不同的天线、传感器或空间位置，总数为 \(XN\)。
   - **\( tf \)**：时间帧索引，总数为 \(XT\)。
   - 这个双重求和表示将所有天线（或传感器）和所有时间帧的数据进行累加。

3. **\( y_{n, tf} \)**：
   - 表示第 \(n\) 个天线在第 \(tf\) 个时间帧采集到的信号强度或幅值。

4. **\( e^{j 2\pi (tf - 1) T_s k r(x, y, n)} \)**：
   - **\( j \)**：虚数单位，\(j = \sqrt{-1}\)。
   - **\( 2\pi \)**：常数，用于将角频率转换为相位。
   - **\( (tf - 1) T_s \)**：表示时间延迟，其中 \(T_s\) 是采样周期。
   - **\( k \)**：波数，通常定义为 \(k = \frac{2\pi}{\lambda}\)，其中 \(\lambda\) 是雷达信号的波长。
   - **\( r(x, y, n) \)**：从雷达到空间点 \((x, y)\) 的距离，依赖于天线索引 \(n\)。
   - 这个指数项表示由于距离和时间延迟引起的相位变化。

5. **\( c \)**：
   - 一个常数系数，可能代表信号的幅度调整因子或其他缩放参数。

6. **\( e^{j \frac{2\pi r(x, y, n)}{\lambda}} \)**：
   - 类似于前面的相位因子，表示由于距离 \(r(x, y, n)\) 导致的相位变化。


这个公式描述了如何将来自多个天线（或传感器）和多个时间帧的雷达信号综合起来，构建在空间坐标 \((x, y)\) 和时间 \(t\) 点的雷达信号 \(S(x, y, t)\)。

具体过程如下：

1. **信号累加**：
   - 对于每个天线 \(n\) 和每个时间帧 \(tf\)，将对应的信号 \(y_{n, tf}\) 进行累加。

2. **相位调整**：
   - 每个信号 \(y_{n, tf}\) 都乘以两个指数项，这些项基于距离 \(r(x, y, n)\) 和时间 \(tf\) 进行相位调整，反映了信号传播过程中的时空变化。

3. **幅度调整**：
   - 常数系数 \(c\) 对信号进行幅度上的调整，以适应系统的需求或补偿信号衰减。
   - 
其中N是虚拟通道的数量，yn，tf是tf处的接收信号（线性调频内的ADC采样点），Ts是ADC采样周期，k是频率斜率，λ是波长，r（x，y，n）是通道n中从体素（x，y）到天线对的距离，t是帧时间。


然后，我们利用二阶微分器[8]来消除噪声信号，同时保留每个体素的心跳信号。微分器定义为：

$$
s''_0 = \frac{(s_{-3} + s_{3}) + 2(s_{-2} + s_{2}) - (s_{-1} + s_{1}) - 4s_{0}}{16h^2}
$$
这里，$s''_0$指的是在特定时间采样处的二阶导数，si是远离i个采样的时间序列的值，h是连续采样之间的帧周期。

最后，我们可以将心脏时空信号构建为$X_{in} \in \mathbb{R}^{H \times W \times L}$ 的形式。H 和 W 表示二维空间尺寸的维度，L 表示时间长度。

#### 2.2.深层时空模型

使用深度神经网络对心脏时空信号进行建模是一项挑战。这一挑战来自于高频雷达采样导致的爆炸性网络输入大小以及建模过程中对具有冗余空间大小的长持续时间信号的需求。为了解决这个问题，我们设计了一个基于卷积变换的混合神经网络架构，如图1所示。
![[Pasted image 20240918145246.png]]
图1系统概述。首先，我们从原始数据中构建心脏时空信号表示。然后，我们的深时空模型将复杂的多维问题转化为一个序列建模过程。最后，我们使用生成的心跳波形来计算HRV指标。


具体来说我们首先采用条件时域卷积网络（TCN）[15, 16]，在时间维度上进行特征提取和压缩。在TCN中，使用门控激活单元来融合历史特征的影响：

$$
z = \tanh(W_{f,k} * x + V_{f,k} * f(h)) \odot \sigma(W_{g,k} * x + V_{g,k} * f(h)),

（4）$$


其中，$*$ 表示卷积，$\odot$ 代表逐元素相乘，$\sigma(\cdot)$ 是 sigmoid 函数，$k$ 是层索引，$f$ 和 $g$ 分别对应滤波器和门控，$W$ 表示可学习的卷积滤波器，$V_{f,k}$ 和 $V_{g,k}$ 是可学习的线性投影矩阵。$f(h)$ 将历史序列映射到具有相同维度的输入特征。

然后，我们可以将整个过程建模如下：

$$
p(X_f|X_{in}) = \prod_{t=1}^{YT} p(X_f|y_1, \ldots, y_{n-1}, X_{in}),（5）
$$


其中，特征 $X_f$ 依赖于前一时间步的序列真实值和当前输入 $X_{in}$。


具体来说为了清晰利用时空信息的物理解释，受 Vision Transformers [17, 18] 的启发，我们将多维数据转换为类似序列的补丁。然后，我们使用 transformer 模型捕捉这些补丁之间的复杂关系。最后，我们将这些表示投影以获得细粒度的心跳信息。具体而言，给定 TCN 的输出为 $$X_f \in \mathbb{R}^{H \times W \times C \times T_{out}}$$，其中 $C$ 是输出通道数，$T_{out}$ 表示具有全局时间视图的特征长度，我们使用求和池化将 $T_{out}$ 维度拆分为 $P$ 个补丁。我们添加一个可学习的 token 来捕捉全局上下文，并混合时空补丁。这个过程产生了混合的时空补丁 $$X_{st} \in \mathbb{R}^{H \times W \times P+1 \times C}$$。在添加位置嵌入后，我们得到全局时空特征为：

$$
X_w = Attention(X_{st}, X_{st}, X_{st}) \in \mathbb{R}^C.（6）
$$



随后，使用一个简单的两层 MLP：

$$
X_{out} = \sigma(X_w \cdot W_1^T + b_1) \cdot W_2^T + b_2,(7)
$$


其中，$W_1$ 和 $W_2$ 是可学习矩阵，$\sigma$ 表示激活函数。$X_{out}$ 表示最终输出。我们的任务涉及精确的心跳时序，而不是生理波形重建。受 [19] 的启发，我们使用心跳波形作为地面真值，该波形通过用高斯分布插值对应心跳事件（心电图中的 R 峰）的时间点生成，如图 2 所示。对于最终输出，使用一个简单的峰值检测过程来定位心跳时序。


### 3实验和结果

#### 3.1 实现

 我们的非接触式 HRV 监测系统采用了 TI 的 AWR1843 毫米波雷达，配备了 2 个发射器和 4 个接收器。该雷达以 100 Hz 运行，具有 65 MHz/μs 的信号斜率和 3.32 GHz 的带宽。地面真值 ECG 数据使用 TI 的 ADS1292 评估板获取。我们的系统在 PyTorch 中实现，使用 Adam 优化器和 L1 损失进行优化，采用学习率为 $0.001$ 和小批量大小为 $32$。关键超参数包括 $L = 640$，$C = 128$，$T_{out} = 128$，$X_{out}$ 维度为 $100$，$H = W = 9$（一个 $0.5 \times 0.5$ 米的区域），以及具有扩张因子 $2$ 的 $9$ 层堆叠 TCN。在 Transformer 块中，我们采用了注意力维度为 $32$ 和 $4$ 个注意力头。

为了评估我们的系统，我们对 $8$ 名参与者进行了实验。雷达数据和 ECG 信号同时收集。在实验过程中，要求参与者坐下并自然呼吸。我们在参与者距离雷达不同的位置进行测量，具体为 $1m$、$2m$、$3m$ 和 $4m$。在每个距离处，我们收集三个数据段，总计九分钟。总共，我们收集了近 $5$ 小时的数据。最后，$75\%$ 的数据（$6$ 名参与者）用于训练模型，剩余 $2$ 名参与者的数据用于测试模型。

基线方法:我们选择 mmHRV [7] 和 VED [9] 作为比较的基线方法。mmHRV 是通过信号处理设计实现的最先进（SOTA）的基于雷达的 HRV 分析方法。VED 通过使用一种新颖的深度生成模型，在恢复细粒度心跳波形方面表现出有希望的性能。

评估指标

我们使用以下指标：

• **绝对 IBI 错误**：预测的 IBI 与其对应的地面真值之间的绝对差异。

• **相对误差**：绝对 IBI 错误与地面真值的比率。

• **RMSSD**：连续差异的均方根。

• **SDRR**：所有 IBI 的标准差。

• **pNN50**：连续 IBI 相差超过 50 毫秒的百分比测量。

为了确保公平的性能比较，并防止由于生成的心跳数与地面真值不同导致的 IBI 错误计算中的模糊性，我们沿时间采样维度而非心跳序列计算 IBI，如图 2 所示。具体来说，在每个时间点，IBI 由两个相邻波形峰之间的周期确定。对于基于周期的 HRV 指标 RMSSD、SDRR、pNN50，评估通过一个 60 秒的滑动窗口和 5 秒的步长实现。


#### 3.2 实现评价

图 2 展示了各种方法的输出。同时，图 3 说明了三种不同方法在所有距离下的整体 IBI 估计准确性。mmHRV、VED 和我们的系统实现了中位绝对 IBI 错误分别为 35ms、26ms 和 12ms，中位相对误差分别为 4.5%、3.3% 和 1.5%。我们的方法实现了最佳的整体性能。这是因为深度学习算法相比传统信号处理方法更能提取出更多的心跳特征，并且整个身体的反射信号可以通过深度时空模型处理，以提取更精确的心跳时序信息。
![[Pasted image 20240918152955.png]]
图2结果和地面真相可视化。第一行显示真实ECG信号及其相应的心跳波形地面实况。后续行显示不同方法的结果。最后一行显示沿着采样维度的IBI结果。

![[Pasted image 20240918153046.png]]
表1不同方法下不同距离的心率变异性监测性能。

表 1 展示了在各种距离下所有方法的 HRV 相关指标的平均值和中位数。可以观察到几个值得注意的现象。首先，随着距离的增加，三种方法的中位和平均 IBI 错误都呈上升趋势。这可以归因于随着距离增加信噪比的降低。值得注意的是，mmHRV 和 VED 对距离更为敏感，可能是因为当信号质量下降时，有限的身体反射效果较差。此外，我们的方法在 HRV 指标上（包括 RMSSD、SDRR 和 pNN50）优于其他方法，在不同距离下表现出更稳定的性能。同时，我们的性能在平均误差和中位误差方面表现出更大的稳定性。这凸显了我们的方法在准确提取心跳时序方面的鲁棒性。

总之，我们的系统在不同距离下始终表现出优越的性能，展示了其可靠提取复杂 HRV 相关特征的能力。

#### 3.3 消融研究

为了证明在基于雷达的 HRV 监测中考虑全身反射的重要性，而非单一部位的身体反射，我们首先通过对比使用 [9] 中描述的心脏时空信号或单一身体表面部位的一维心脏信号作为网络输入，进行消融实验。如表2所示，当使用考虑全身反射的时空信号时，性能显著提升。
![[Pasted image 20240918153517.png]]
表2心脏信号表征的消融研究

我们还分析了网络中空间建模的有效性。具体而言，我们通过沿空间维度平均 transformer 输入以模糊 Xf 中的空间信息，进行无空间建模的实验。表3中显示的结果表明，我们的网络设计有效地建模了空间信息，并实现了优越的性能。
![[Pasted image 20240918153557.png]]
表3空间建模的消融研究。


#### 4 结论

在本文中，我们提出了一种新的系统，旨在提高心率变异性监测有效地提取信息，从整个躯干表面。利用全身反射和深度时空网络，我们在公平的HRV评估方法中取得了最好的结果。此外，我们强调了使用全身反射和空间建模进行HRV分析的重要性，证明了其上级性能。该系统有望实现非接触式心率变异性监测，在心血管疾病的早期诊断和应激评估方面具有潜在的应用价值