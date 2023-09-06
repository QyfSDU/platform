# 抗衰减直流分量相量测量算法综合测试平台
## 一、主要功能

MEATPEAT是一个开源的抗衰减直流分量（Decaying DC Component，DDC）相量测量算法综合测试软件，用于评估此类算法的综合性能。

目前，MEATPEAT提供DFT,ADFT[^1],TDFT[^2],DDFT[^3],DLES[^4]与Prony[^5]六种对比算法。

MEATPEAT支持抗干扰性、敏感度、动态性能测试。另外，计算复杂度通过算法计算耗时实现。

1. 抗干扰性测试：
- [x] 基础信号测试
- [x] 抗多个DDCs干扰测试
- [x] 抗噪声干扰测试
- [x] 抗谐波干扰测试
- [x] 抗间谐波干扰测试
- [x] 对信号混叠敏感度测试

2. 敏感度测试：
- [x] 对时间常数敏感度测试
- [x] 对系统运行频率敏感度测试
- [x] 对采样频率敏感度测试

3. 动态性能测试
- [x] 上升时间
- [x] 暂态时间

4. 算法运算复杂度（以算法运行时间替代）

MATPEAT能够有效提高算法测试效率，是一个可靠的研究与教育工具。我们鼓励您加入我们，进一步开发、完善测试平台。

## 二、如何安装


## 三、使用方法


## 四、算法代码编写注意事项
- 算法仅支持以Matlab函数形式编写，并且要求文件名称必须与函数名称相同。
- 函数需要有三个输入，分别为测试信号、系统额定频率与采样频率。
- 函数需要有三个输出，分别为相量幅值估计值、相位估计值与算法所需的超出一个周期的采样点数目。
- 算法所需的超出一个周期的采样点数目（即参考代码中的delay）需根据算法原理人为设置。

下面是传统DFT算法代码，为新的算法函数编写提供参考。
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




[^1]:M. R. Dadash Zadeh and Z. Zhang, “A new DFT-based current phasor estimation for numerical protective relaying,” IEEE Trans. Power Del., vol. 28, no. 4, pp. 2172–2179, Oct. 2013, doi: 10.1109/TPWRD.2013.2266513.

[^2]:S. Afrandideh, M. R. Arabshahi, and S. M. Fazeli, “Two modi-fied DFT‐based algorithms for fundamental phasor estimation,” IET Gener. Transm. Distrib., vol. 16, no. 16, pp. 3218–3229, Aug. 2022, doi: 10.1049/gtd2.12516.

[^3]:H. Yu, Z. Jin, H. Zhang, and V. Terzija, “A phasor estimation al-gorithm robust to decaying DC component,” IEEE Trans. Power Del., vol. 37, no. 2, pp. 860–870, Apr. 2022, doi: 10.1109/TPWRD.2021.3073135.

[^4]:J. K. Hwang and C. S. Lee, “Fault current phasor estimation be-low one cycle using Fourier analysis of decaying DC compo-nent,” IEEE Trans. Power Del., vol. 37, no. 5, pp. 3657–3668, Oct. 2022, doi: 10.1109/TPWRD.2021.3134086.

[^5]:Q. Zhang, X.Y. Bian, X,Y. Xu, R.M. Huang, H.E. Li, “Research on factors influencing subsynchronous oscillation damping characteristics based on SVD-Prony and principal component regression,” J. Electr. Eng. Technol., vol. 37 no. 17, pp. 4364–4376, Sep. 2022, doi: 10.19595/j.cnki.1000-6753.tces.211085.
