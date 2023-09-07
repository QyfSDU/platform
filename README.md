# 抗衰减直流分量相量测量算法综合测试平台

## 项目背景

信号中的衰减直流分量（Decaying DC Component，DDC）严重影响相量测量算法的精度。目前，虽然已有大量的抗DDC相量测量算法，但缺乏高效的算法测试软件。可再生能源大规模并网、柔性负荷接入的背景下，电气信号呈现频谱特征复杂、噪声大与动态过程复杂的特征，现有单一的评估方案已难以覆盖新型电力系统的信号形态。因此，建立了抗DDC相量测量算法综合测试平台——MATPEAT，提高算法测试效率。



## 主要功能
- MEATPEAT是一个开源的抗DDC相量测量算法综合测试软件，用于评估此类算法的综合性能。
- 目前，MEATPEAT提供DFT,ADFT[^1],TDFT[^2],DDFT[^3],DLES[^4]与Prony[^5]六种对比算法,并提供算法导入接口。
- MEATPEAT支持抗干扰性、敏感度、动态性能测试，可以根据特定需求增添测试环节。

   1. 抗干扰性测试：
      - [x] 基础信号测试
      - [x] 抗多个DDCs干扰测试
      - [x] 抗噪声干扰测试
      - [x] 抗谐波干扰测试
      - [x] 抗间谐波干扰测试
      - [x] 抗信号混叠干扰测试

   2. 敏感度测试：
      - [x] 对时间常数敏感度测试
      - [x] 对系统运行频率敏感度测试
      - [x] 对采样频率敏感度测试

   3. 动态性能测试
      - [x] 上升时间
      - [x] 暂态时间

   4. 算法运算复杂度（以算法运行时间替代）

- MATPEAT能够有效提高算法测试效率，是一个可靠的研究与教育工具。我们鼓励您加入我们，进一步开发、完善测试平台。

## 如何部署
**注:** MATPEAT仅适用于MATLAB 2020a及以上版本。

1. 将Algorithm_test_platform.mlapp中代码savefloder='XXX'中xxx修改为**文件绝对保存路径**。
2. 使用MATLAB appdesigner的软件打包功能将文件打包为一个APP。
   - 点击Algorithm_test_platform.mlapp中“共享”，可选择将文件打包为MATLAB APP，Web APP或独立桌面APP（需一同打包 MATLAB Runtime）。
   - 将主文件与通过分析而包含的文件外的文件添加至辅助文件，确保没有遗漏，点击点击右侧“打包”。
3. 安装打包生成的.mlappinstall文件。

## 使用方法
![软件页面](https://github.com/QyfSDU/platform/assets/144082078/fb3337e4-2769-48c7-b2f0-0ab4ae88637d)

1. ①为算法导入与选择区，用户可导入并选择待测算法（算法代码编写注意事项见 算法代码编写注意事项），无误后点击"Confirm"。
2. ②为测试环节选择与参数输入区，用户在相应测试项目中输入合理参数，无误后点击"Confirm"。
3. ③为测试结果显示区，分别显示算法的幅值、相位估计结果与算法稳定后的最大误差。

**注：** 

- 为保证估计结果曲线易于分辨，同时测试的算法不应超过五个。
- 故障前后工频分量幅值分别为0.1 p.u.与1 p.u.，相位分别为 $\frac{\pi}{3}$ rad与  $\frac{\pi}{2}$ rad.
- 谐波测试环节中，根据IEEE-519标准[^6]在2-10、11-16等谐波区间各随机选择三个谐波分量，按最大谐波电流失真上限设置测试信号幅值，相位随机选取。
- 用户也可以根据需求，修改Signal.m,Signal_sensitivity.m与Signal_sensitivity_SNR.m文件，以生成满足需求的测试信号。

## 算法函数编写注意事项
- 算法仅支持以Matlab函数形式编写，并且要求文件名称必须与函数名称相同。
- 函数需要有三个输入，分别为测试信号、系统额定频率与采样频率。
- 函数需要有三个输出，分别为相量幅值估计值、相位估计值与算法所需的超出一个周期的采样点数目。
- 测试信号长度不应小于八个工频周期。
- 算法所需的超出一个周期的采样点数目（即参考代码中的delay）需根据算法原理人为设置。

为给算法编写提供参考，下面提供了传统DFT算法代码。
```MATLAB
% Traditional DFT function
% The inputs are the test signal, the rated frequency and the sampling frequency
% The outputs are the estimated amplitude, the estimated phase and the number of sampling points beyond a cycle
function [amp_X_DFT,phase_X_DFT,delay] = DFT(x,f0,fs)

 N=fs/50; % Number of sampling points in one cycle
 signal_length=ceil(8*N);
 amp_X_DFT=zeros(1,signal_length);
 phase_X_DFT=zeros(1,signal_length);

 m=0:N-1;
 for window=1:8*N
    X_DFT=sum(x(window+m).*exp(-1j*2*pi*m/N))*2/N;
    amp_X_DFT(window)=abs(X_DFT);
    phase_X_DFT(window)=rem(angle(X_DFT)-2*pi*(window+N)*(f0/50)/N,pi);
 end

 delay=0;
end
```

## 版权说明

代码内容采用 新



[^1]:M. R. Dadash Zadeh and Z. Zhang, “A new DFT-based current phasor estimation for numerical protective relaying,” IEEE Trans. Power Del., vol. 28, no. 4, pp. 2172–2179, Oct. 2013, doi: 10.1109/TPWRD.2013.2266513.

[^2]:S. Afrandideh, M. R. Arabshahi, and S. M. Fazeli, “Two modi-fied DFT‐based algorithms for fundamental phasor estimation,” IET Gener. Transm. Distrib., vol. 16, no. 16, pp. 3218–3229, Aug. 2022, doi: 10.1049/gtd2.12516.

[^3]:H. Yu, Z. Jin, H. Zhang, and V. Terzija, “A phasor estimation al-gorithm robust to decaying DC component,” IEEE Trans. Power Del., vol. 37, no. 2, pp. 860–870, Apr. 2022, doi: 10.1109/TPWRD.2021.3073135.

[^4]:J. K. Hwang and C. S. Lee, “Fault current phasor estimation be-low one cycle using Fourier analysis of decaying DC compo-nent,” IEEE Trans. Power Del., vol. 37, no. 5, pp. 3657–3668, Oct. 2022, doi: 10.1109/TPWRD.2021.3134086.

[^5]:Q. Zhang, X.Y. Bian, X,Y. Xu, R.M. Huang, H.E. Li, “Research on factors influencing subsynchronous oscillation damping characteristics based on SVD-Prony and principal component regression,” J. Electr. Eng. Technol., vol. 37 no. 17, pp. 4364–4376, Sep. 2022, doi: 10.19595/j.cnki.1000-6753.tces.211085.

[^6]:IEEE Standard for Harmonic Control in Electric Power Systems, IEEE Standard 519, 2022.
