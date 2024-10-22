![[High-Precision Vital Signs Monitoring Method Using a FMCW Millimeter-Wave Sensor.pdf]]




### 摘要

主要讲述了使用毫米波雷达传感器检测人体生命体征（呼吸和心率）的方法。该方法采用77 GHz的FMCW雷达，通过提出的新型脉冲去噪操作和频谱估计决策方法，显著提高了信噪比和检测精度。实验结果表明，与智能手环的参考值相比，该方法的呼吸和心率测量误差分别为1.33%和1.96%，证明了该方案在非接触式监测中的高质量和可重复性

### 技术

主要技术是基于频率调制连续波（FMCW）毫米波雷达的信号处理技术，用于检测人体的呼吸和心跳。具体技术包括：

1. **FMCW雷达**：利用77 GHz的FMCW毫米波雷达传感器来检测人体的生命体征信号，特别是呼吸和心跳。

2. **脉冲去噪**：使用迭代的变分模态分解（VMD）结合小波间隔阈值法（Wavelet-Interval-Thresholding）来减少信号中的脉冲噪声。这种方法在去噪的同时保留了信号的关键特征。

3. **频谱估计方法**：采用混合的快速傅里叶变换-切比雪夫多项式（FFT-CZT）算法来进行频谱估计，从而获得更加精确的呼吸和心跳频率。此外，还结合了基于时间域的峰值搜索来提高检测精度。

4. **多普勒效应和相位信息提取**：通过雷达接收到的相位信息，提取心跳和呼吸引起的微小位移变化，从而实现对人体生命体征的高精度检测。

这些技术的结合使得毫米波雷达能够有效地检测生命体征，并且在噪声环境中也能够保持较高的检测准确性。