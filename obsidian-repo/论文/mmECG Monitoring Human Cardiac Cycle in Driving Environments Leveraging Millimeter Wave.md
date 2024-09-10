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


