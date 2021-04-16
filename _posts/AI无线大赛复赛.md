# AI无线大赛复赛

## 系统模型

![image-20201111155637668](../../../AppData/Roaming/Typora/typora-user-images/image-20201111155637668.png?lastModify=1618569170?lastModify=1618569206)

![image-20201111155637668](../../../AppData/Roaming/Typora/typora-user-images/image-20201111155637668.png?lastModify=1618569170)

![image-20201111155637668](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201111155637668.png)
$$
Y_j = FFT(removeCP(convlove(Clip(addCP(IFFT(mod(X_i)))), h_{j,i})))
$$

### 每一个子信道模型

![image-20201111155347727](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201111155347727.png)

## 基于传统的信道估计+MIMO检测

### 对于循环CP+无非线性影响的信道模型

![image-20201111160714171](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201111160714171.png)

其中，$y_k(i)$表示第$k$个OFDM符号的时域接收信号，$x_k(i)$表示第$k$个OFDM符号的时域发送信号，$h0,h1,\cdots,h_{L-1} $为时域冲击响应。对于循环CP以及CP长度大于冲击响应长度的情况(本题中OFDM符号是一发一收，不存在OFDM符号间干扰)，可以得到如下结论：

![image-20201111161255917](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201111161255917.png)

两边同时FFT变换即可得到：
$$
Y=\Lambda X




$$
对于MIMO-OFDM系统，则在每一个子载波上存在一个等效MIMO信道：
$$
Y_k = H_kX_k
$$
因此可以先估计出每一个子载波上的信道矩阵$H_k$后，接着使用MIMO检测算法求解$X_k$

### 信道估计

根据上述理论将系统模型变换到频域后，由于导频排列如下：

![image-20201111165850014](AI无线大赛复赛.assets/image-20201111165850014.png)

对于第1个子载波，可以估计出$h_1=Y_1/P_1,h_2=Y_2/P_1$,同理，可以估计出第4个子载波的$h_3,h_4$,因此对于信道矩阵$H_k$中的每一个元素，可以估计出32个子载波上的信道值，其余子载波上的元素通过插值方式实现

### MIMO检测

获得每一个子载波上的信道矩阵$H_k$后，通过ML检测方式估计原始发送信号



## 测试结果

对于竞赛中提到的2种导频模式以及三种链路设置。在SNR=10dB时可以得到性如下。

|  P   |   32   |   32   |   32   |   8   |   8    |   8    |
| :--: | :----: | :----: | :----: | :---: | :----: | :----: |
| MODE |   0    |   1    |   2    |   0   |   1    |   2    |
| BER1 | 0.1614 | 0.1961 | 0.2422 |  0.5  |  0.5   |  0.5   |
| BER2 | 0.023  | 0.0591 | 0.1218 | 0.023 | 0.0591 | 0.1218 |

其中BER1表示使用上述的信道估计加插值方式的性能，BER2表示使用完美信道的性能，在8导频下使用插值方式完全不能检测出原始发送信号。



## 基于网络的信道估计+ML检测

|  P   |  32  |  32  |  32  |  8   |  8   |  8   |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| MODE |  0   |  1   |  2   |  0   |  1   |  2   |
| BER1 |      |      |      | 0.37 | 0.38 | 0.39 |

![image-20201111165633361](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201111165633361.png)

