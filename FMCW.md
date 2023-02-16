# FMCW

 	该雷达主要测量前方物体的距离、速度和到达角。

## FMCW雷达运行基础知识

### chirp信号

​	下图为一个线性调频脉冲的A-t图，从一频率为$f_c$的正弦波开始，频率逐渐增大，最后以$f_c+B$结束，$B$为线性的调频脉冲的带宽。线性调频脉冲本质上是一种频率以线性方式进行调制的连续波。

![image-20230216111700604](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216111700604.png)



​	若在频率-时间图中看线性调频脉冲信号则是频率为S的斜线，竖轴代表带宽B，S和B是重要参数。

<img src="C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216112443789.png" alt="image-20230216112443789" style="zoom: 67%;" />



### 雷达工作原理

​	具有一个TX天线和RX天线，工作原理如下：

>+  生成器Synth生成一个线性调频脉冲，通过TX发射出去
>+ 遇到物体反射回来，通过RX接受
>+ TX信号与RX信号融合在一起生成IF信号，IF表示中频

![image-20230216112653663](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216112653663.png)

#### 混频器mixer

​	输入$x_1=sin[w_1t_1+\phi_1]、x_2=sin[w_2t_2+\phi_2]$得到的瞬时频率和初始相位为$x_{out}=sin[(w_1-w_2)t+(\phi_1-\phi_2)]$ 

![image-20230216144312837](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216144312837.png)

#### 单物体IF信号

RX信号是TX信号的延时副本，由于信号均为线性调频脉冲，所以RX信号和TX信号同一时刻之间的频率差值固定，由斜率和往返延迟确定，该差值即为IF信号。IF信号恒定频率为$S_{\tau}=S*\tau$ ，往返延迟$\tau=2d/c$，在AD转换时，只能从往返延迟开始到TX信号结束这段时间里。

![image-20230216144652712](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216144652712.png)



### 多物体IF信号

​	根据傅里叶变换的相关性质,当观测期越长，对于信号的解析就越好，一般观测期间隔一般高于$1/THz$。对于多物体的IF信号则经过傅里叶变换后，呈现频率与物体远近成正比。![image-20230216150029870](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216150029870.png)

#### 距离分辨率

​	两个物体距离多近仍然能在频谱中显示为两个峰值，提高雷达距离分辨率方法：

> + 增大IF信号的长度，以扩大观测器，需要更大的带宽

带宽与距离分辨率的关系为

![image-20230216150754747](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216150754747.png)

在计算过程中会对IF信号进行数字化，再通过低通滤波器，最后用于DSP，此时ADC的采样率会影响最大距离。对于以下两种连续调频信号，A的持续时间是B的两倍，说明对于同样的距离，A只需要一半的IF带宽，ADC的采样频率也更小。B则具有测量时间更短的优势。

![image-20230216151416784](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216151416784.png)

![image-20230216152747551](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216152747551.png)

### 总结

重要的有两种带宽：

>射频带宽：**是线性调频脉冲跨越的带宽，该带宽越大，距离分辨率越好**，一般为几百MHZ~几GHZ，4GHz带宽可以4cm的距离分辨率
>
>IF带宽：**可以实现更长的距离，更快的线性调频脉冲**，一般在低MHZ 

![image-20230216151900196](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216151900196.png)

![image-20230216152041445](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216152041445.png)

## IF的相位

在傅里叶变换中，峰值的相位对应于正弦波的初始相位，RX、TX、IF信号的幅值-时间图如下，IF为单个频率的正弦波。

![](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216154233729.png)

则IF的相位可由A点的相位减去B点的相位，若物体发生移动，移动时间为$\Delta\tau$，则幅度图发生变换，如下所示。此时TX发生的相位变化为$\Delta\phi=2\pi f_c \Delta\tau=4\pi\Delta d/ \lambda$，由于RX的初始相位不变，所以IF的相位变换为TX的相位变换。所以位置的微小变化体现在相位中

![image-20230216154919922](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216154919922.png)

![image-20230216155507872](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216155507872.png)

## 速度估计

### 基本原理

发射间隔$T_c$的两个线性调频脉冲，得到两个IF信号，相位与速度的关系为

![image-20230216162229912](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216162229912.png)

**同时相位也可以用于估算振动物体的幅度和周期**

有一个离散信号以w弧度的恒定速率旋转，每个向量之间都旋转了w弧度，对离散信号进行FFT变换可得到频谱图，w1为离散角频率，相位以w1旋转 

![image-20230216163707196](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216163707196.png)

当离散信号包含两相时，当序列长度越长，分辨率越高，长度为N的序列的两相的离散角频率差值需要大于$2\pi/N$,

### 最大速度

两个线性调频脉冲的相位变换必须小于$\pi$,可得![image-20230216165025063](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216165025063.png)

要得到**更快的速度**，需要脉冲更加**密集**

### 同距不同速物体估计

方案：

>+ 发射一系列等间隔的线性调频脉冲，虽然range-FFT有相同的峰值，但是峰值相位组成的离散序列有两个旋转相量，分别对应两个物体的速度，这个离散序列的doppler-FFT（多普勒FFT）将显示两个峰值。
>
>其中等间隔的线性调频脉冲脉冲序列称为帧（frame），是FMCW雷达的基本传输单位

### 速度分辨率

![image-20230216170144831](C:\Users\10425\AppData\Roaming\Typora\typora-user-images\image-20230216170144831.png)

有两个帧的帧长相同，但是线性调频脉冲数是两倍