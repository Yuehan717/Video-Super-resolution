# 【论文笔记】Image Super-Resolution Using Very Deep Residual Channel Attention Networks
对于CNN网络来说，网络深度非常重要。但是随着网络深度增加，训练难度往往也会上升。这篇论文提出了一种方法，
可以搭建非常深的SISR模型，效果也有比较大的提升。论文关注的另外一个问题是，低分辨率的输入图像和他们的
feature map里面包含丰富的low-frequency信息，而模型平等处理这些来自不同通道的特征，这会阻碍网络的表示能力。
RCAN提出了一种通道注意力机制（channel attention）来解决这个问题。
## 主要贡献
论文提出了一个可以完成高精度图像超分的深度网络（RCAN）。
+ Proposed residual in residual structure to construct very deep trainable networks.
+ Propoesd channel attention (CA) mechanism to adaptively rescale features by considering interdependencies
among feature channels.
## 模型
### 网络架构
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/RCAN.png width=80%> </img>  
RCAN包含四个部分：**shallow feature extraction, RIR, upsample, reconstruction**. 第一部分特征提取和最后的重建
都由一个卷积层构成，upsample可以选择的方法有很多，论文当中没有明确给出，需要看作者给的代码实现。RCAN尽量将处理任务放在RIR
中，简化模型的其他部分。
### Residual in Residual (RIR)
RIR结构由G个**residual group (RG)** 组成。RIR的首尾由long skip connection（LSC）连接。LSC可以减轻模型向前传递低频信息的负担，同时让RIR能学习到粗略的残差信息。  
每一个RG有B个RCAB和末尾的一个卷积层组成。RG的首尾由short skip connection（SSC）连接。SSC让网络的主要部分关注残差信息。  
LSC和SSC整体上在模型中增加了identity-based skip connection。作者这样做的原因是，LR图像所包含的低频信息最终会出现在HR图像中，
这些信息非常丰富并且实际上不需要进行处理，增加skip connection可以直接传递这些信息，而网络的主要部分专注于学习残差信息就可以。这样的设计给网络训练减少了很多负担，也让模型的学习更有效。
### Channel Attention (CA)
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/CA.png width=60%></img>  
注意力机制经常被用在有序信息的学习和一些high-level的视觉任务当中，作者第一次把这种方法用在low-level的SR任务里面。
如何为每一个feature channel生成一个attention是关键步骤。作者最后给出了一个使用global average pooling计算attention的方法，
并且给出了这样设计的依据，原文如下，还没太理解这之间的关系。
> First, information in the LR space has abundant low-frequency and valuable high-frequency components. The low-frequency parts seem to be more complanate. 
The high-frequency components would usually be regions, being full of edges, texture, and other details. On the other hand, each filter in Conv layer operates 
with a local receptive field. Consequently, the output after convolution is unable to exploit contextual information outside of the local region.

图中的HGP可以看作一个global polling层，WD是一个进行channel-downscaling的卷积层，WD产生的特征图经过一个ReLU函数的处理
输入到WU中，WU也是一个卷积层，负责channel-upscaling，最后的f是sigmoid函数。经过这一系列的处理得到了s，s和最开始的输入相乘，对输入的各个通道进行rescale。
> WD和WU进行的down-scaling和upscaling目的是什么？


### Residual Channel Attention Block (RCAB)
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/RCAB.png width=60%></img>  
图中虚线框内就是上一部分所讲的channel attention，除此之外RCAB首先用两个卷积层对输入进行处理，然后在处理过的特征图上提取channel statistic，左后获得的这个值
和整个block的输入通过一个short cut连接起来。  
和EDSR进行对比，可以把EDSR看作是RCAN的一个特例，如果channel statistic是常数0.1， 那么RCAB中的channel statistic就相当于EDSR中每个residual block最后的scaling层。
用channel attention替代scaling层考虑了各个通道之间的相互关系。
> 原文：Although the channel-wise feature rescaling is introduced to train a very wide network, the interdependencies among channels are not considered in EDSR.
但是这里的*interdependencies*具体该怎么理解？
## 代码&实验
## 对比
