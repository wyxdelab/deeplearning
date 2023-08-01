# PreDiff: Precipitation Nowcasting with Latent Diffusion Models
## 研究背景和解决
**背景**：确定性(deterministic)的数据驱动算法进行未来降雨预测会产生模糊的预测结果，导致失去重要的细节。
**解决**：因为概率模型(probabilistic)可以展现随机系统内在的不确定性，这种模型可以获取多种可行性预测，产生多种高质量的、与真实数据更加的接近的预测结果，而扩散模型(DMs)就是概率模型的一种，其中潜扩散模型(LDMs)是对扩散模型的改良。由于扩散模型是纯数据的方法，缺少先验知识的约束，而潜扩散模型就是额外有先验知识作为约束条件。


## 研究方法
### 数据来源
N-body MNIST dataset 
SEVIR dataset
### 模型结构
两个阶段
1. variational autoencoder (VAE)
   输出结果分别输入进以下两个神经网络：
   1. Earthformer-UNet：diffusion model
   2. Earthformer-UNet：diffusion model+knowledge control network：
      1. N-body MNIST dataset :基于能量守恒定律，每一个过渡分布的能量与最终分布的能量相等。
      2. SEVIR dataset：基于预测降雨强度。
### 流程

观测序列经过variational autoencoder (VAE)编码为潜在时空序列，加入标准高斯噪音序列，一同输入进Earthformer-UNet。

经过一次Earthformer-UNet得到去噪后的高斯噪音序列，每次去噪过程都有潜在时空序列和参数化知识控制网络的约束（将输入序列分别输入到潜扩散模型和参数化知识控制网络中）。随后的每次Earthformer-UNet的输入都是高斯噪音序列+潜在时空序列，其中潜在时空序列只作为约束条件，本身不改变。

最后将高斯噪音序列decoder得到预测图像。
## 亮点
将知识控制加入神经网络，且不依赖于核心神经网络，因此当数据集变化时，只需要重新设计知识控制网络，无需重新设计核心神经网络。
