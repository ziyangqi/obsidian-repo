mmECG：利用毫米波监测驾驶环境中的人体心动周期


## 摘要

    近年来，汽车旅行时间的不断增加使人们越来越关注道路上驾驶员的身心健康。作为关键的生命体征之一，心跳是驾驶员健康状况的重要指标。大多数现有的心跳监测研究要么需要传感器连接，要么只能提供粗略的心率。此外，大多数方法要求受试者保持静止或安静的测量环境，这很难应用于动态驾驶环境。在本文中，我们提出了一种非接触式心动周期监测系统，mmECG，它利用商用现成的毫米波雷达来估计移动车辆中驾驶员的细粒度心脏运动。通过探索基于mmWave信号的传感原理，我们首先在静态环境中进行研究，并发现细粒度的心脏运动，表示为重复心动周期中心房和心室的阶段，可以通过基于FMCW的mmWave雷达捕获为信号的相位变化。而在驾驶环境中，这种相位变化不仅由驾驶员的心跳引起和影响，而且还由驾驶操作和车辆动力学引起和影响。为了进一步提取驾驶员心脏的微小运动并消除相位变化中的其他影响，我们构建了运动混合模型来表示不同运动引起的相位变化，并进一步设计了分层变分模式分解（VMD）方法来提取和估计毫米波信号中的基本心脏运动。最后，基于提取的相位变化，mmECG通过利用基于模板的优化方法估计心房和心室的细粒度运动来重建心动周期。25名驾驶员在真实的驾驶场景中的实验结果表明，mmECG不仅可以准确地估计驾驶员在真实的驾驶环境中的心率，还可以准确地估计驾驶员的心动周期。


## I. INTRODUCTION

    人体的心跳，尤其是以心房、心室在心动周期中的重复运动为代表的细粒度心脏运动，不仅能够反映人体的生理状况，而且能够反映人体的生理状态 如困倦[2]、疲劳[3]等状况，还包括人的情绪和心理变化[4] [5]。因此，在驾驶环境中监测驾驶员的心动周期可以有助于丰富驾驶辅助应用和医疗保健服务的种类。例如，基于心动周期监测的疲劳检测可以通过智能手机App向驾驶员发出警告，而从监测到的心跳变化中检测突然的攻击性情绪可以帮助车辆的控制系统采取行动（例如，减速），防止事故隐患。因此，非常希望为驾驶环境中的驾驶员提供细粒度的心动周期监测。

*As one of the most important vital indicators of human health, the heartbeats of human, especially the fine-grained heart move-ments represented as the repetitive movements of atria and ventricles in cardiac cycles, can not only reflect human body

*For instance, the detection of fatigue based on cardiac cycle monitoring could raise warnings to the driver through a smartphone App, and the detection of sudden aggressive emotion from monitored heartbeat changes could help the control system of vehicles taking actions (e.g.,slow down the speed) to prevent potential accidents. Therefore,it is highly desirable to provide fine-grained cardiac cycle monitoring for drivers in driving environments.*


当前的一些设备的缺点

    当前的心动周期监测解决方案通常需要日常不可用且侵入性的基础设施（例如，心电图）。为了释放硬件需求，最近的研究采用了商用现货（COTS）设备，例如WiFi [6]、RFID [7] [4]、音频设备[8]等，用于低成本和非接触式心跳监测。但是由于感测分辨率的限制，这些方法只能估计粗略的心率，远远达不到医学水平的监测要求（即，心动周期）。而且，由于缺乏有效的干扰消除方案，所有这些工作都要求目标用户相对静止（例如，静坐或睡觉）和环境清洁（例如，家庭），这两个都不能在驾驶环境中实现。


我们的目标是设计一个非接触和低成本的驾驶环境中的心动周期监测系统。在各种非接触式和低成本传感模式中，毫米波因其短波长和大带宽的细粒度传感能力而脱颖而出。为此，我们的目标是研究利用毫米波信号来检测心跳运动的可行性，以便在驾驶环境中进行心动周期监测。要实现基于毫米波的心动周期监测，我们在实践中面临着一些挑战。首先，毫米波信号应被设计为能够捕获人类的微小心脏运动。第二，驾驶环境中的干扰，包括驾驶操作和车辆动力学，应该从毫米波信号中消除。



## II PRELIMINARY

### A 人类心跳的背景


心电图和心脏运动之间的紧密相关性表明，心房和心室的运动带来了可以被毫米波捕获的胸部振动，能够为医疗监测提供人体心跳的深刻信息。

*The tight correlation between ECG and heart movements indicates that the movement of atria and ventricles, which bring chest vibrations that could be captured by mmWave, are able to provide the profound information of human heartbeats for healthcare monitoring.*


### B 利用毫米波检测心脏活动的原理


*Millimeter wave (mmWave)-based sensing technology is developing rapidly in recent years. Because of the small wavelength, mmWave is able to sense minute movements,e.g., human heartbeat activities. Specifically, a mmWave-based FMCW radar continuously transmits chirp signals to an object, and collects the received signal. Then, the displacement Δd
of the object can be calculated as Δd = λΔφ 4π , where λ is the wavelength associated with the average frequency of the transmitted mmWave, Δφ is the phase change caused by the
displacement of object in the dechirped signal between the received and transmitted signals. It can be seen that a smaller wavelength (i.e., higher frequency) of the transmitted wave-
form leads to a better resolution for displacement detection. Thus, we leverage a 77 ∼ 81GHz off-the-shelf mmWave radar (with one of the highest frequency range in COTS mmWave sensors) to build the system, the resolution of displacement is about 1mm per π phase changes in the dechirped signal, which can achieve sub-millimeter level resolution for the movement detection.*

![[Pasted image 20240910104658.png]]


    现有研究[12]表明，毫米波可以很容易地穿透大多数种类的布，但几乎不能穿透或被人体皮肤吸收。因此，从理论上讲，心脏运动引起的胸部振动（大致在毫米级）可以使用毫米波信号捕获。图2说明了基于毫米波的细粒度心跳活动检测的基本思想。毫米波雷达通过发射器端（Tx）向人体胸部区域连续发射FMCW信号。大部分毫米波信号被胸部反射，并进一步被毫米波雷达的接收端（Rx）捕获。在这个过程中，由心房和心室的运动引起的胸部的微小振动被FMCW雷达捕获。

`Existing study [12] shows that mmWave can easily penetrate most kinds of cloth, but can hardly penetrate or be absorbed by the skin of human. So theoretically, the chest vibrations induced by heart movements, which is roughly in millimeter-level, can be captured using mmWave signal. Figure 2 illus-trates the basic idea of mmWave-based fine-grained heartbeat activities detection. A mmWave radar continuously transmits FMCW signal through the transmitter end (Tx) to the region of human chest. Most of the mmWave signal is reflected by the chest, and further captured by the receiver end (Rx) of mmWave radar. During this process, the minute vibrations of chest caused by the movements of atria and ventricles are captured by the FMCW radar.`

    图3显示了2s内mmWave测量和相应ECG测量的归一化相位变化，其包含志愿者的三个完整心动周期。从图中可以看出，mmWave和ECG测量之间存在明确的时间关系。以第二心动周期为例，有三种典型的时间关系。首先，在毫米波测量中第二增加趋势的开始时间，即，t1，与心电图P波的中部有关，是心房收缩的开始。二、毫米波测量中最大值出现的时间，即：t2对应于ECG测量中的Q波。由于QRS波群在心电图测量中是作为一个组合，Q波和R波之间的时间间隔通常小于0.03s [13]，因此t2可以大致代表心房收缩的结束和心室收缩的开始。三、毫米波测量中最小值出现的时间，即：t3，对应于ECG测量中T波的结束，这是心室收缩的结束。基于上述分析，由mmWave捕获的心房和心室的细粒度运动可与ECG测量值大致匹配，以显示心动周期。因此，去杠杆是可行的，基于毫米波的FMCW雷达，用于捕获细粒度的心脏运动，并进一步确定心房和心室的诊断阶段。
    
![[Pasted image 20240910110146.png]]

`Figure 3 shows the normalized phase changes of mmWave measurement and corresponding ECG measurement in 2s, which contains three complete cardiac cycles of the volunteer. It can be seen from the figure that there are clear time relationships between mmWave and ECG measurements.Taking the second cardiac cycle as an example, there are three typical time relationships. First, the beginning time of the second increase trend in mmWave measurement, i.e., t1, relates to the middle part of P wave in ECG, which is the beginning of atrial systole. Second, the time of the maximum value in mmWave measurement, i.e., t2, corresponds to Q wave in ECG measurement. Since QRS complex is taken as a combination in ECG measurement and the time interval between Q wave and R wave is normally less than 0.03s [13], t2 could roughly represent the end of atrial systole and the beginning of ventricular systole. Third, the time of the minimum value in mmWave measurement, i.e., t3, corresponds to the end of T wave in ECG measurement, which is the end of ventricular systole. Based on the aforementioned analysis, the fine-grained movements of atria and ventricles captured by mmWave could roughly match the ECG measurement to exhibit the cardiac cycles. Therefore, it is feasible to leverage mmWave-based FMCW radar for capturing the fine-grained heart movements, and further determine the stages of atria and ventricles for the diagnosis.`



## III SYSTEM DESIGN

### A System Overview

    我们设计了一个细粒度的心动周期监测系统，mmECG，它估计分钟的心脏运动的驾驶员在驾驶环境中利用毫米波信号。图4显示了mmECG的系统架构。整个系统包括三个步骤：毫米波信号预处理、基本心脏运动估计和心动周期重建，在毫米波信号预处理中，我们设计了chirp毫米波信号，以获得满意的心动周期监测距离和时间分辨率。设计的毫米波信号被传输到驱动器，反射信号被同一雷达捕获。然后，根据接收到的信号计算由驾驶环境中的所有运动引起的毫米波信号的相位变化。然后，在基本心脏运动估计中，构建运动混合模型来表示驾驶环境中所有运动引起的毫米波信号的相位变化。在此基础上，设计了一种分层变分模式分解（VMD）方法，根据干扰信号的频率特性，将干扰信号从心脏运动引起的相位变化中分离出来。最后，在心动周期重建中，mmECG利用mmWave信号中心跳的形状相似性将提取的相位变化分割成心动周期，并进一步利用基于模板的优化方法来重建每个心动周期中的心房和心室阶段。
    
![[Pasted image 20240910112042.png]]

### B mmWave Signal Pre-processing
    mmECG首先对mmWave信号进行预处理，以检测物体的细微运动。

**Designing Sensing Signal**

    mmECG采用基于mmWavebased的雷达来检测由心跳引起的人体胸部的皮肤振动，用于心动周期监测。具体地，雷达利用FMCW技术从发射天线发射具有线性增加的频率的啁啾，然后利用接收天线收集反射信号。之后，雷达将混合发送和接收的FMCW信号以生成中频（IF）信号，该中频信号嵌入雷达感测到的所有信息，包括心动周期。

**Subtle Movements Detection.**

    为了捕获心动周期的细微运动，mmECG从IF信号中导出mmWave信号的相位变化。具体地，mmECG对IF信号执行FFT操作（Range-FFT）以获得目标的Range-Profile。 
    
依据于距离剖面，通过计算FMCW（调频连续波）信号中连续脉冲的相位变化来检测细微的心脏运动。从理论上讲，经过FFT操作后的采样毫米波信号$s(d,k)$ 可以表示为 $S(d,k)=r(d,k)+i(d,k)⋅j$，其中 $r(d,k)$ 和 $i(d,k)$ 分别是毫米波信号的实部和虚部，$d$ 和 $k$ 分别是信号的距离单元和脉冲标签，$j$ 是虚数单位。因此，相位变化 $Δϕ(d,k)$被推导为：
![[Pasted image 20240910143555.png]]

$$Δϕ(d,k)=arctan(r(d,k)i(d,k)​)$$

因此，通过相位变化，mmECG可以实现每个距离单元中亚毫米级别的运动检测。

**Basic Heart Movements Estimation**

    由于mmECG在信号预处理之后实现了亚毫米级运动检测，因此驾驶员在驾驶环境中的所有运动，包括驾驶操作（例如，转向和制动），细微的身体运动（例如，呼吸和心跳）和驾驶带来的振动，可以通过毫米波信号的相位变化来捕获。然而，由位于某个距离仓中的移动引起的相位变化可能影响若干相邻的距离仓。换句话说，当同时存在多个移动时，每个距离箱包含由移动的混合引起的相位变化。因此，即使相对于毫米波传感器在不同距离仓中发生不同的运动，我们也不能根据距离直接区分每个运动。
    为了从毫米波信号的相位变化中提取心脏运动，我们首先构建一个运动混合模型来表示所有运动带来的相位变化的传递性。然后，基于所构建的模型，我们设计了一种分层VMD方法来从毫米波信号的相位变化中分离不同的运动，并进一步估计基本的心脏运动。
    1)我们首先利用毫米波信号中相位变化的传递性。作为电磁波，毫米波信号经过毫米波传感器传输后，在传感范围内的空间中形成毫米波场，毫米波场是毫米波能量的物理场。当空间中存在运动时，它会搅动毫米波场并导致毫米波信号的传播，就像涟漪的传播一样。
    因此，由运动引起的毫米波信号的相位变化可以在不同的距离单元之间传递。基于相位变化的传递性，我们进一步将每个距离单元中的相位变化建模为不同运动的线性混合。具体来说，假设在一个时间单位内，
有 $( N )$个距离单元，并存在 $( K )$ 种运动 $( M_1, M_2,..., M_K )$，每种运动在相应运动单元中的相位变化为 $( C_1, C_2, \dots, C_K )$。在距离单元 $( B_n )$（其中 $( n \in 1, 2, \dots, N )$）中的相位变化表示为

$$( P_{B_n} = \sum_{i=1}^{K} \alpha_{in} C_i )      (2)$$
其中$αin是由距离仓Bn中的运动Mi引起的相位变化的衰减系数，其满足αin ∈ [0，1]$。因此，从毫米波信号提取的相位变化可以被建模为由不同运动引起的相位变化的混合，其可以由下式表示：

$$P_B​(N×1)=α(N×K)×C(K×1)                (3)$$

通过衰减系数矩阵 $\alpha$ 调制。

    2) 利用层次VMD分离运动：

    根据构建的运动混合模型，从毫米波信号中估计驾驶员的心脏运动，就是要找到驾驶员胸部相关的距离单元中由心脏运动引起的相位变化。虽然我们可以根据运动混合模型从毫米波信号中获得每个距离单元中所有运动的总体相位变化，

即 $( P_B )$，但是由于衰减矩阵 $( \alpha )$ 是未知的，无法通过公式 (3) 计算出相位变化 $( C )$。

    为了获得由驾驶员心脏运动引起的相位变化，我们需要在驾驶环境中分离毫米波传感器感知范围内的所有运动。通常，在驾驶环境中存在四类主要的独立运动：1) 驾驶员的操作，指的是驾驶操作的运动（如转向和刹车），运动范围较大（亚米级）且频率较低（0.1-2Hz）。2) 驾驶员的呼吸，运动范围适中（厘米级），频率中等（0.16-0.6Hz）[14]。3) 驾驶员的心跳，运动范围较小（毫米级和亚毫米级），频率较高（0.7-3.5Hz）[15。4) 驾驶环境中的振动，其范围和频率根据振动的程度而不同。由于这四类运动的频率范围不同但有重叠，仅仅应用带通滤波器可能无法有效分离不同的运动。因此，我们考虑应用变分模态分解（VMD）方法[16]，该方法作为一种自适应滤波器，将混合信号分解为不同模态，以满足毫米波信号中运动分离的要求。

    在应用VMD方法时，需要提前确定关键参数以确保分解性能，包括成分的数量 $( k )$、成分的带宽限制 $(\alpha )$ 和每个成分的初始频率  $f_0$ 。此外，由于这四类运动在幅度和频率上有很大的差异，几乎不可能一次找到一组参数能够很好地分离所有四种运动。为了解决这一问题并提取与心脏运动相关的相位变化，我们设计了一个层次VMD方法，逐步分离运动，如图5所示。层次VMD方法中有两个VMD模块，第一模块用于分离导致毫米波信号中大幅度相位变化的运动，包括驾驶员行为和车辆的大振动。第二模块则用于分离带来小幅度相位变化的运动，如呼吸和车辆的小振动，留下心脏运动的相位变化。对于这两个模块，成分数量  $k$  均设为2，用于从毫米波信号中分离出一个成分。成分的带宽限制  $\alpha$  和目标成分的初始频率 $f_0$是由参数生成引擎自动确定的，参数生成引擎以毫米波信号的相位变化为输入，输出对应VMD模块的参数 $\alpha$ 和  $f_0$ 。

![[Pasted image 20240910145930.png]]


    图5（B）显示了参数生成引擎的详细架构。该引擎具有两条流水线，分别用于生成初始频率f0和分量α的带宽限制。对于初始频率f0，在接收到毫米波信号中的相位变化作为输入之后，引擎对随时间的相位变化执行FFT，以将相位变化转换到频域。然后，引擎分析频率分量，并选择频域中具有最大值的分量作为VMD块的初始频率f0。直观的感觉是VMD模块总是分离出毫米波信号中具有最大相位变化幅度的分量，并且初始频率应设置为接近该分量以保证分离性能。
    至于带宽限制参数α，该参数描述了对分离分量的带宽限制。如果α太小，则信号将被过度分离;如果α太大，则信号将被分离不足。然而，在分离之前很难确定信号是过分离还是分离不足。因此，为了确定参数α，我们提取了统计特征，包括幅度、方差等，从输入信号中训练SVM回归器以确定α。SVM回归器用10小时毫米波数据以及从真实的驾驶环境中的5个参与者收集的相应呼吸和心跳地面实况数据进行训练。然后，在给定输入信号的情况下，参数生成引擎可以自动生成参数f0和α。
    基于所生成的参数，分层VMD方法可以将驾驶员的行为和呼吸以及车辆振动从毫米波信号的相位变化中分离出来，并提取心脏运动。图6示出了在真实的驾驶环境中收集的10 s毫米波信号上的分层VMD的结果。身体运动的信号由第一VMD块从原始信号中分离，如图6（a）所示。可以看出，分离后的信号明显是周期性的，因为它主要包含呼吸运动和心脏运动的信号。如图6（B）所示，使用分离后的信号作为第二VMD块的输入，分离呼吸运动并估计心脏运动的信号。



4) **心动周期重建 

       在从毫米波信号的相位变化中提取基本心脏运动之后，mmECG重构心脏运动背后的隐含心动周期以估计心房和心室的细粒度运动。
       1)分割心动周期：为了重建心动周期，mmECG首先通过将mmWave信号的提取相位变化分割成片段来估计每个心动周期的长度。每个片段与心动周期有关。关键的见解是，尽管振幅、频率等有微妙的差异，在驾驶员的每个心跳中，连续的人类心跳的一般形状在毫米波信号的相位变化中是相似的。因此，心动周期分割问题可以被构造为一个优化问题[4]。具体地，mmECG旨在找到分割S = {s1，s2，...，sn，.}每个段的长度{|S1|, |S2|,...,|SN|,...}最大化每个片段的相似性，其可以表示为：
       
       ![[Pasted image 20240913095024.png]]
       
       其中μ是毫米波信号相位变化中心动周期形状的模板。ω（μ，|si|）将模板μ重新采样到段si的长度，并且cor（）是模板μ和段si之间的相关系数。与[4]类似，我们利用动态规划来解决优化问题，并获得心动周期的分割，如图7所示。可以看出，除了在等式4中由于缺少对心动周期的校准而导致的时移之外，段的长度非常接近于相应ECG测量的基本事实。
       
       ![[Pasted image 20240913095233.png]]

       2)重建心房和心室分期：在心动周期分割的基础上，mmECG通过确定每个心动周期的关键时间点，即心房收缩开始时间t1、心房收缩结束时间和心室收缩开始时间t2、心室收缩结束时间t3，进一步重建心房和心室分期，如2.3节所述。
       为了实现这一判定，首先设计了一个在mmWave信号相位变化中的心房和心室运动模板，然后对该模板应用一种优化方法，找到了mmWave信号中每个心动周期的关键时间点t1、t2和t3。为了设计模板，mmECG基于峰值检测方法对具有纯心脏运动的mmWave信号中的相位变化进行分段，并将每个分段重新采样为平均长度。之后，将这些段平均为代表典型心动周期的模板，如图8所示。可以看出，t3被确定为心动周期的结束时间，而t1和t2是心动周期内的中间时间点。由于t1和t2的相对时间在每个心动周期中可以是不同的，所以我们假设t1和t2可以分别在范围R1和R2内移动。在t1和t2的移动过程中，模板的总长度保持固定，t1和t2旁边的部分将相应地缩放。
       基于该模板，我们将第i个心动周期Lci的模板长度设置为与图7中的第i个分段心动周期的长度相同，然后我们将模板tempi滑动到图7中的第i个心动周期ci的校准t3。在模板tempi内，mmECG进一步搜索使心动周期与模板之间的相似性最大化的t1和t2的最佳位置，
       
$$argmaxt1​,t2​∈R1​,R2​​cor(ci​,tempi​)   (5)$$
       其中，**cor(ci, tempi)** 表示心动周期 cic_ici​ 与动态模板 tempitemp_itempi​ 之间的相关系数。通过分别在 R1R_1R1​ 和 R2R_2R2​ 的范围内搜索 t1t_1t1​ 和 t2t_2t2​，**mmECG** 获得了 t1t_1t1​ 和 t2t_2t2​ 的最优解。结合 t3t_3t3​，这三个关键时间点用于确定心房和心室阶段，由 **mmECG** 完成。
       通过重建心房和心室阶段，**mmECG** 能够获取驾驶员的医疗级心跳信息，这对于道路上驾驶员的健康管理非常有帮助。



## IV EVALUATION

    在本节中，我们根据从真实的驾驶环境中的25名不同志愿者收集的数据评估mmECG的性能。

#### A. Experimental Setup and Methodologies 实验设置和方法
使用设备

    我们使用商业的 TI AWR1642 毫米波雷达 [17] 作为感测前端，配合 Dell XPS 13 笔记本电脑作为数据处理后端来实现 mmECG。毫米波雷达配备了两个板载发射天线和四个接收天线，并通过 TI DCA1000EVM 数据捕捉卡 [18] 连接，实现毫米波雷达与笔记本电脑之间的高速数据传输。另一方面，我们使用 Heal Force PC-80B 心电监测仪 [19] 来捕捉心电图（ECG）测量的真实数据。收集的数据和真实数据配对后，被分割成 30 个样本，以便进一步处理和评估。
    在实验中，毫米波雷达被放置在汽车仪表盘上志愿驾驶员的前方。具体来说，如图9所示，传感器有9个不同的位置进行放置。在这9个位置中，毫米波雷达与志愿者胸部之间的距离在40厘米到80厘米之间。对应的角度范围为[−20°，20°]。在实验过程中，我们招募了25名志愿者（14名男性，11名女性，年龄在19岁到52岁之间），并使用了3种不同的车辆（雷克萨斯RX350、宝马X3、本田雅阁）。在实验期间，每位志愿者随机驾驶一辆车，行驶在包括平路、鹅卵石街道和颠簸路面等不同的道路条件下，这些条件会产生不同类型的动态。我们在上述设置下使用mmECG收集测量数据。总共，我们收集了大约200小时的数据，这相当于24000个样本。
    
![[Pasted image 20240913101235.png]]


我们为评估定义了几个指标


• **心率（HR）估计误差**：估计的心率 $R_E$ 与通过心电图（ECG）设备测量的真实心率 $R_G$ 之间的误差，定义为 $R_E$ 和 $R_G$ 之间的绝对差值，即 $\Delta HR = |R_E - R_G|$。
• **心率变异性（HRV）估计误差**：估计的心率变异性指标 RMSSD\(_E\) 与通过心电图（ECG）设备测量的真实心率变异性指标 RMSSD\(_G\) 之间的误差，即 $$ \Delta HRV = |RMSSD_E - RMSSD_G| $$其中
$$
RMSSD = \sqrt{\frac{1}{N} \sum_{i=2}^{N} (IBI_i - IBI_{i-1})^2}
$$
在上述公式中，\( IBI_i \) 表示第 \( i \) 个心动周期的持续时间*。

- 心动周期（CC）估计误差：估计的关键时间点的RMSSD的误差（即，t1、t2和t3）。例如，对于时间点t1，误差为$$ \Delta t1 = |RMSSD_E - RMSSD_G| $$对于所有三个时间点，总体心动周期估计误差是三个单独时间点估计误差之和的平均值。

#### B 整体性能
    我们评估了不同志愿者组的mmECG的总体性能。由于HR直接表现出心脏运动估计的粗粒度结果，因此我们首先基于mmECG中的分段心动周期来估计HR。图10显示了不同性别和年龄组下mmECG的心率（HR）估计误差。可以看出，所有性别和年龄组的HR估计误差均低于0.6bpm，表明mmECG可以在驾驶环境中实现准确的HR估计。图11示出了mmECG的心率变化（HRV）估计误差。可以看出，所有性别和年龄组的HRV估计误差均低于14ms，表明mmECG可以准确估计驾驶环境中驾驶员的HRV。图12最后显示了mmECG的心动周期（CC）估计误差。我们可以观察到，所有性别和年龄组的CC估计误差均低于9ms。此外，对于心动周期中的三个关键时间点t1、t2和t3中的每一个，估计结果在图13中示出。可以观察到，所有三个关键时间点的估计误差都低于10ms，这足以提供驾驶员的细粒度心脏运动信息作为健康指标。从图13中还可以看出，t3的估计误差低于t1和t2，这是因为t3作为毫米波信号中每个心动周期的结束时间点具有更鲁棒的形状特征。

![[Pasted image 20240913102606.png]]


    此外，女性HR、HRV和CC的估计误差略高于男性，如图10、11和12所示，因为女性的平均心率高于男性[21]。同时，年轻志愿者对HR估计的估计误差（如图10所示）略高于老年志愿者，但对HRV估计的估计误差（如图11所示）和CC估计的估计误差（如图12所示）略低于老年志愿者。原因是年轻人的HR通常高于老年人[9]，这给HR估计带来了更多的变化。对于HRV和心动周期，较高的心率带来每个心动周期的短时间，从而导致较低的估计误差。总体而言，mmECG可以实现对所有三种测量的准确估计，即，HR、HRV和CC估计。由于与HR和HRV相比，CC估计表现出更细粒度的心动周期结果，因此以下评价侧重于显示用于评价mmECG性能的CC估计误差。

#### C.毫米波雷达布局的影响

    图14（a）示出了毫米波雷达相对于驱动器的不同角度下的平均CC估计误差。可以看出，在0 °角处实现了最低的估计误差。当角度远离0 °时，误差增大，特别是在角度为±20 °后。这是因为毫米波的小波长引起窄的主波束，指示毫米波雷达的高度定向的视场（即，在角度范围内[-15，15] [17]）。当驾驶员的身体接近视野边界时，毫米波信号的边带具有小得多的功率，导致显著的性能下降。图14（B）进一步示出了不同角度下CC估计误差的CDF。可以看出，当角度在[-15 °，15 °]范围内时，80%以上的误差小于14 ms。考虑到毫米波雷达可以覆盖的宽度为46 cm（即，角度为[−15 °，15 °]，距离为40 cm），远大于人体宽度，表明mmECG可以通过合理的设置准确监测驾驶员的心动周期。
    
    ![[Pasted image 20240913103222.png]]

    我们还评估了毫米波雷达与人体胸部之间的距离对毫米心电图的影响。图15（a）示出了毫米波雷达相对于驾驶员在不同距离下的平均CC估计误差，图15（B）中绘出了CC估计误差的对应CDF。可以观察到，当距离在80cm内时，这在驾驶环境中是自然的，平均CC估计误差不超过12ms，并且对于超过90%的情况，CC估计误差低于20ms。结果表明，在驾驶环境中，mmECG能够在有效距离范围内准确监测心动周期。

![[Pasted image 20240913103635.png]]


#### D.心率影响

    由于心率可以直接影响每个心动周期的持续时间，因此我们评估了驾驶员心率对mmECG性能的影响。图16（a）示出了在不同驾驶员心跳下的平均CC估计误差，图16（B）绘制了相应的CDFCC估计误差。可以看出，mmECG在驾驶员的所有心率下都可以实现低于11ms的平均CC估计误差，超过80%的样本的CC估计误差小于20ms，表明mmECG可以准确地估计驾驶员在不同心率下的心动周期。此外，当驾驶员的心率从缓慢（即，<=60bpm）到快速（即，> 120bpm）时，平均CC估计误差先减小后增大。如我们所知，心率越高，每个心动周期的持续时间越短，从而导致由变化带来的估计误差越小。当驾驶员的心率高于100 bpm时，误差增加的原因是因为当心率很高时，呼吸运动通常变得沉重，这降低了mmWave信号中心脏运动的SNR，并进一步增加了mmECG的估计误差。

 ![[Pasted image 20240913104220.png]]

#### E.交通条件的影响

    交通状况可能会影响驾驶员的驾驶行为和车辆状况，从而影响mmECG的性能。我们分析了不同的交通条件（在高峰时间和非高峰时间）和不同的道路类型（在当地道路和高速公路），分别收集的痕迹。图17（a）和17（B）分别示出了在道路类型和交通状况的所有四种组合下的平均CC估计误差和CC估计误差的CDF。可以看出，在道路类型和交通状况的任何组合下，对于超过80%的样本，平均CC估计误差低于11ms，并且CC估计误差低于18ms。此外，在高峰时段，估计误差略大，因为驾驶员在交通繁忙时可能会进行更多的驾驶操作，这会给mmECG带来干扰。对于不同的道路类型，局部道路上的估计误差稍大，因为在局部道路上，车辆可能遭受恶劣的道路条件，例如颠簸的道路，这带来了额外的干扰。
    
 ![[Pasted image 20240913104237.png]]


#### F.异常检测性能

    我们还评估了mmECG对异常心跳检测（如房颤）的性能。对于这个实验，我们开发了一个额外的功能来检测异常心跳。如第III-D节所述，mmECG采用通过预先收集的正常心跳构建的模板进行心动周期重建。mmECG基于正常心跳的模板，通过计算检测到的心动周期与模板之间的相关性来检测异常心跳。如果该相关性小于阈值δ，则检测到的心动周期将被认为是异常的。此外，为了收集地面真相，我们邀请专业医生对收集的ECG样本进行诊断，以找出异常心跳。在收集的24万个样本中，有381个被认为是异常的。
    图18显示了在不同阈值δ下，mmECG对异常心跳检测的性能。可以观察到，随着δ从0.1增加到1，异常心跳检测的准确率在δ小于0.32时首先保持为100%，然后下降到0，而异常心跳检测的召回率从0增加到100%。而F1-score（结合了精确度和召回率）则先增加，直到δ = 0.55，然后下降。因此，mmECG将阈值选择设为δ = 0.55，对应于异常心跳检测的F1评分为88.9%。结果表明，mmECG能够有效地监测驾驶员的健康状态。

## V RELATED WORK
    在本节中，我们回顾了毫米波传感和生命体征检测的关键研究。
    毫米波感知背景。毫米波技术已用于感测人类手势[22]、[23]、室内定位和楼层地图构建[24]-[27]，从而实现室内环境的精确导航功能。进一步的研究还调查了使用毫米波验证用户身份[28]或唇阅读[29]的可行性。所有这些研究都证明了将毫米波扩展到感知区域的可行性。沿着这个方向，我们致力于利用毫米波技术实现细粒度的心跳监测。
    生命体征检测。一些研究监测粗粒度的呼吸用于健康监测，例如分别用于日常和睡眠监测的呼吸率和事件。例如，一些工作[30]，[31]采用智能手表来跟踪一个人的呼吸以进行健康监测。然而，这样的监测方式引入了侵入性体验。为了改善用户体验，其他研究使用无线信号实现呼吸监测系统，例如商业WiFi [32]，低成本RFID [33]，[34]和音频设备[35]-[37]。
    其他一些研究集中在检测心跳以进行健康监测。早期的工作[38]开发了专用的基础设施来实现心跳监测。为了降低广泛部署的成本，其他一些研究将先进的信号处理技术集成到COTS设备中，以提供心跳监测的能力。例如，一些研究使用商用WiFi [6]、低成本RFID [7]、音频设备[8]甚至mmWave [39]实现心跳监测系统。另一项工作[4]甚至通过RFID信号监测人的心跳来实现情感识别系统。然而，由于COTS设备分辨率有限，心跳监测只能实现心率测量，距离专业基础设施下的专业诊断还有很大距离。此外，所有上述研究都要求用户保持静态姿势，这几乎不支持各种条件，特别是具有一致振动的驾驶环境。最近，一些研究开始关注动态环境下的心跳监测[40] [41]，但他们仍然无法获得比心率或HRV测量更精细的信息。
    与上述研究不同的是，我们的工作旨在监测动态环境下心房和心室的收缩和舒张运动所表示的心动周期，而不是简单的心率或HRV测量，以支持健康监测。最相关的工作是使用RF信号的心震图监测[42] [43] [44]，这些工作侧重于心震图重建，这与我们的工作平行。



## VI. CONCLUSION
    在本文中，我们提出了一种心动周期监测系统，mmECG，它利用COTS毫米波雷达监测驾驶员的心动周期，在驾驶环境中提供潜在的医疗服务。mmECG首先设计了基于FMCW的毫米波信号，以检测驾驶员的心脏运动作为毫米波信号的相位变化。基于检测到的相位变化，运动混合模型的构建和心脏运动引起的相位变化提取的层次变分模式分解（VMD）的方法。最后，mmECG应用基于模板的优化，基于提取的相位变化估计心房和心室的细粒度运动。在真实的驾驶环境下的实验验证了mmECG在心动周期监测方面的性能。





以下是您提供的参考文献按照 Markdown 格式进行排版后的内容：

```markdown
[1] W. Kim, V. Anorve, and B. Tefft, “American driving survey, 2014–2017,” 2019.

[2] B. Roman, S. Pavel, P. Miroslav, V. Petr, and P. Lubomír, “Fatigue indicators of drowsy drivers based on analysis of physiological signals,” in *International Symposium on Medical Data Analysis*. Springer, 2001, pp. 62–68.

[3] N. Egelund, “Spectral analysis of heart rate variability as an indicator of driver fatigue,” *Ergonomics*, vol. 25, no. 7, pp. 663–672, 1982.

[4] M. Zhao, F. Adib, and D. Katabi, “Emotion recognition using wireless signals,” in *Proceedings of ACM MobiCom*, New York City, NY, USA, 10 2016, pp. 95–108.

[5] X. Zhang, W. Li, X. Chen, and S. Lu, “Moodexplorer: Towards compound emotion detection via smartphone sensing,” *Proceedings of the ACM on Interactive, Mobile, Wearable and Ubiquitous Technologies*, vol. 1, no. 4, pp. 1–30, 2018.

[6] J. Liu, Y. Wang, Y. Chen, J. Yang, X. Chen, and J. Cheng, “Tracking vital signs during sleep leveraging off-the-shelf wifi,” in *Proceedings of ACM MobiHoc*, Hangzhou, China, 6 2015, pp. 267–276.

[7] F. Adib, H. Mao, Z. Kabelac, D. Katabi, and R. C. Miller, “Smart homes that monitor breathing and heart rate,” in *Proceedings of ACM CHI*, Seoul, Republic of Korea, 4 2015, pp. 837–846.

[8] K. Qian, C. Wu, F. Xiao, Y. Zheng, Y. Zhang, Z. Yang, and Y. Liu, “Acousticcardiogram: Monitoring heartbeats using acoustic signals on smart devices,” in *Proceedings of IEEE INFOCOM*, Honolulu, HI, USA, 4 2018, pp. 1574–1582.

[9] K. E. Barrett, *Ganong’s review of medical physiology*. McGraw Hill Education, 2019, no. 1.

[10] L. S. Lilly, *Pathophysiology of heart disease: a collaborative project of medical students and faculty*. Lippincott Williams & Wilkins, 2012.

[11] G. J. Tortora and B. H. Derrickson, *Principles of anatomy and physiology*. John Wiley & Sons, 2018.

[12] T. Wu, T. S. Rappaport, and C. M. Collins, “The human body and millimeter-wave wireless communication systems: Interactions and implications,” in *2015 IEEE International Conference on Communications (ICC)*. IEEE, 2015, pp. 2423–2429.

[13] F. G. Yanowitz, “Introduction to ecg interpretation,” in *LDS Hospital & Intermountain Medical Center*, Salt Lake City, 2012.

[14] J. P. Mortola, “Breathing around the clock: an overview of the circadian pattern of respiration,” *European journal of applied physiology*, vol. 91, no. 2-3, pp. 119–129, 2004.

[15] B. Nes, I. Janszky, U. Wisløff, A. Støylen, and T. Karlsen, “Age-predicted maximal heart rate in healthy subjects: The hunt fitness study,” *Scandinavian journal of medicine & science in sports*, vol. 23, no. 6, pp. 697–704, 2013.

[16] K. Dragomiretskiy and D. Zosso, “Variational mode decomposition,” *IEEE transactions on signal processing*, vol. 62, no. 3, pp. 531–544, 2013.

[17] TI, “Awr1642 single-chip 77- and 79-ghz fmcw radar sensor,” [Online]. Available: https://www.ti.com/lit/ds/symlink/awr1642.pdf, 2019.

[18] TI, “Real-time data-capture adapter for radar sensing evaluation module,” [Online]. Available: https://www.ti.com/tool/DCA1000EVM?HQS=TI-null-null-digikeymode-df-pf-null-wwe, 2019.

[19] H. Force, “Pc-80b,” [Online]. Available: http://www.healforce.com/cn/index.php?ac=article, 2020.

[20] F. Shaffer and J. Ginsberg, “An overview of heart rate variability metrics and norms,” *Frontiers in public health*, vol. 5, p. 258, 2017.

[21] K. Prabhavathi, K. T. Selvi, K. Poornima, and A. Sarvanan, “Role of biological sex in normal cardiac function and in its disease outcome–a review,” *Journal of clinical and diagnostic research: JCDR*, vol. 8, no. 8, p. BE01, 2014.

[22] J. Lien, N. Gillian, M. E. Karagozler, P. Amihood, C. Schwesig, E. Olson, H. Raja, and I. Poupyrev, “Soli: ubiquitous gesture sensing with millimeter wave radar,” *ACM Transactions on Graphs*, vol. 35, no. 4, pp. 142:1–142:19, 7 2016.

[23] T. Wei and X. Zhang, “mtrack: High-precision passive tracking using millimeter wave radios,” in *Proceedings of ACM MobiCom*, Paris, France, 9 2015, pp. 117–129.

[24] J. Palacios, G. Bielsa, P. Casari, and J. Widmer, “Communication-driven localization and mapping for millimeter wave networks,” in *Proceedings of IEEE INFOCOM*, Honolulu, HI, USA, 4 2018, pp. 2402–2410.

[25] J. Palacios, P. Casari, H. Assasa, and J. Widmer, “LEAP: location estimation and predictive handover with consumer-grade mmwave devices,” in *Proceedings of IEEE INFOCOM*, Paris, France, 4 2019, pp. 2377–2385.

[26] J. Palacios, P. Casari, and J. Widmer, “JADE: zero-knowledge device localization and environment mapping for millimeter wave systems,” in *Proceedings of IEEE INFOCOM*, Atlanta, GA, USA, 5 2017, pp. 1–9.

[27] A. Zhou, S. Yang, Y. Yang, Y. Fan, and H. Ma, “Autonomous environment mapping using commodity millimeter-wave network device,” in *Proceedings of IEEE INFOCOM*, Paris, France, 4 2019, pp. 1126–1134.

[28] P. Zhao, C. X. Lu, J. Wang, C. Chen, W. Wang, N. Trigoni, and A. Markham, “mid: Tracking and identifying people with millimeter wave radar,” in *Proceedings of DCOSS*, Santorini, Greece, 5 2019, pp. 33–40.

[29] C. Xu, Z. Li, H. Zhang, A. S. Rathore, H. Li, C. Song, K. Wang, and W. Xu, “Waveear: Exploring a mmwave-based noise-resistant speech sensing for voice-user interface,” in *Proceedings of ACM MobiSys*, Seoul, Republic of Korea, 6 2019, pp. 14–26.

[30] X. Sun, L. Qiu, Y. Wu, Y. Tang, and G. Cao, “Sleepmonitor: Monitoring respiratory rate and body position during sleep using smartwatch,” *Proceedings of the ACM on Interactive, Mobile, Wearable and Ubiquitous Technologies*, vol. 1, no. 3, pp. 104:1–104:22, 9 2017.

[31] Y. Zhang, Z. Yang, Z. Zhang, P. Li, D. Cao, X. Liu, J. Zheng, Q. Yuan, and J. Pan, “Breathing disorder detection using wearable electrocardiogram and oxygen saturation,” in *Proceedings of ACM SenSys*, Shenzhen, China, 11 2018, pp. 313–314.

[32] P. Nguyen, X. Zhang, A. C. Halbower, and T. Vu, “Continuous and fine-grained breathing volume monitoring from afar using wireless signals,” in *Proceedings of IEEE INFOCOM*, San Francisco, CA, USA, 4 2016, pp. 1–9.

[33] Y. Hou, Y. Wang, and Y. Zheng, “Tagbreathe: Monitor breathing with commodity RFID systems,” in *Proceedings of IEEE ICDCS*, Atlanta, GA, USA, 6 2017, pp. 404–413.

[34] R. Zhao, D. Wang, Q. Zhang, H. Chen, and A. Huang, “CRH: A contactless respiration and heartbeat monitoring system with COTS RFID tags,” in *Proceedings of IEEE SECON*, Hong Kong, China, 6 2018, pp. 325–333.

[35] H. E. Romero, N. Ma, G. J. Brown, A. V. Beeston, and M. Hasan, “Deep learning features for robust detection of acoustic events in sleep-disordered breathing,” in *Proceedings of IEEE ICASSP*, Brighton, United Kingdom, 5 2019, pp. 810–814.

[36] X. Xu, J. Yu, Y. Chen, Y. Zhu, L. Kong, and M. Li, “Breathlistener: Fine-grained breathing monitoring in driving environments utilizing acoustic signals,” in *Proceedings of ACM MobiSys*, Seoul, Republic of Korea, 6 2019, pp. 54–66.

[37] Y. Ren, C. Wang, Y. Chen, J. Yang, and H. Li, “Noninvasive fine-grained sleep monitoring leveraging smartphones,” *IEEE Internet of Things Journal*, vol. 6, no. 5, pp. 8248–8261, 6 2019.

[38] N. Jähne-Raden, M. Marschollek, U. Kulau, and L. C. Wolf, “Heartbeat the odds: A novel digital ballistocardiographic sensor system,” in *Proceedings of ACM SenSys*, Delft, Netherlands, 11 2017, pp. 59:1–59:2.

[39] Z. Yang, P. H. Pathak, Y. Zeng, X. Liran, and P. Mohapatra, “Monitoring vital signs using millimeter wave,” in *Proceedings of ACM MobiHoc*, Paderborn, Germany, 7 2016, pp. 211–220.

[40] Z. Chen, T. Zheng, C. Cai, and J. Luo, “MoVi-Fi: Motion-robust Vital Signs Waveform Recovery via Deep Interpreted RF Sensing,” in *Proceedings of the 27th ACM MobiCom*, 2021, pp. 392–405.

[41] T. Zheng, Z. Chen, C. Cai, J. Luo, and X. Zhang, “V2ifi: in-vehicle vital sign monitoring via compact RF sensing,” in *Proceedings of the 22nd ACM UbiComp*, 2020, pp. 70:1–27.

[42] Y. Li, Z. Xia, and Y. Zhang, “Standalone systolic profile detection of non-contact scg signal with lstm network,” *IEEE Sensors Journal*, vol. 20, no. 6, pp. 3123–3131, 2019.

[43] C. Will, K. Shi, S. Schellenberger, T. Steigleder, F. Michler, J. Fuchs, R. Weigel, C. Ostgathe, and A. Koelpin, “Radar-based heart sound detection,” *Scientific reports*, vol. 8, no. 1, pp. 1–14, 2018.

[44] U. Ha, S. Assana, and F. Adib, “Contactless seismocardiography via deep learning radars,” in *Proceedings of the 26th Annual International Conference on Mobile Computing and Networking*, 2020, pp. 1–14.

```