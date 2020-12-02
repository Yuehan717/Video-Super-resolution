# 【论文笔记】Enhanced Deep Residual Networks for Single Image Super-Resolution
[论文地址](https://openaccess.thecvf.com/content_cvpr_2017_workshops/w12/html/Lim_Enhanced_Deep_Residual_CVPR_2017_paper.html)  
[代码地址](https://github.com/thstkdgus35/EDSR-PyTorch)
## 主要贡献
这篇论文使用残差网络完成了单张图像的超分任务（SISR）。它不是第一个将残差网络运用在这一任务上的论文，但是之前的方法只是简单地讲ResNet套用在了SISR上，没有对模型进行任何调整，而这篇论文对模型架构进行了修改，
提高了模型表现。它的主要贡献包括以下几点：
+ Performance Improvement
  + Remove unnecessary modules.
  + Stabilize the training procedure.
+ **M**ulti-scale **d**eep **s**uper-**r**esolution system.
## Single-scale Model (EDSR)
### Remove unnecessary models.
EDSR把每个residual block里的BN层删去了。BN(Batch Normalize)层的作用是对特征进行归一化，论文只给了一句解释，称BN层能够”get rid of range flexibility from networks“，所以最好删去。
> 这个方法首先在研究debluring的文章：《Deep multi-scale convolutional neural network for dynamic scene deblurring》中出现，EDSR将其运用在SISR上，并且通过实验证实了它的作用。不过具体原理还没有找到。

经过修改，EDSR使用的residual block结构如下：  
<img src="https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/EDSR-residual_block.png" width=15%> </img>
### Stabilize the training procedure. 
通常，提高深度网络模型表现的最直接方法是增加参数的数目。对于CNN网络，通常用深度**B**(网络层数）和宽度**F**（特征通道数）来衡量模型占用的存储和模型的参数量。
> Memory: O(BF)  
> Parameters: O(BF<sup>2</sup>)

所以，为了在增加参数量的同时尽量降低空间占用，增加feature channel是一个较好的做法。不过增加feature channels有一个问题，在channel数大于一定数值时，模型的训练会变得非常不稳定。为了增加稳定性，
在每一个residual block的最后一层对特征的数值进行缩放，论文使用的缩放比例是0.1。
> 这个现象和解决办法是论文《Inceptionv4, inception-resnet and the impact of residual connections on learning》观察到和提出的。原文是”Also we found that if the number of filters exceeded
1000, the residual variants started to exhibit instabilities and the network has just “died” early in the training, meaning that the last layer before the average pooling started to produce
only zeros after a few tens of thousands of iterations.“ 降低学习率和增加batch normalize层都不能解决这个问题。

### Model Architecture
<img src="https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/EDSR-arch.png" width=50%> </img>  
Residual block的数目是32，所有卷积层的feature channel是256。残差块的结构和之前给出的一样，最后的Mult层用来完成scaling。超分倍数不同的时候，EDSR需要训练不同的模型，他们的区别之处在于Upsampling块的结构。
如图上所示，论文给出了X2 X3 X4三种上采样比例。完成上采样使用的shuffle层实际上就是[sub-pixel](https://github.com/Yuehan717/Video-Super-resolution/blob/main/Notes/Sub-pixel.md)中提出的方法。
这个方法现在可以在torch里面很方便的调用。
## Multi-scale Model (MDSR)
EDSR把所有不同倍数的超分问题单独训练，但是实际上尽管超分倍数不同，这些问题之间是有内在联系的。论文实验证明了使用X2 pretrained 模型来训练X4模型，相比于从头训练，收敛速度更快。所以论文进一步提出
了MDSR，把X2 X3 X4的模型整合在了一起，模型的结构如图。  
<img src="https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/MDSR-arch-m.png" width=50%> </img>

Body是所有超分倍数共用的结构，而pre-process和upsample对每个倍数单独设立参数，pre-process块由两个residual block组成，upsample块和EDSR中的设置相同。论文给出的结构中upsample后所接的卷积层也是
分开的，但是在后续的代码实现中，这个层也和body一样共用了。所有scale-specific的块都是平行设置的，在训练过程中，每次更新的时候只会对符合当前超分倍数的块进行参数更新，其它块的参数不会变化。  
Body的深度是80，feature channel采用64。
## 实验
尝试用不同的数据集（包括视频数据集）训练模型。
代码使用[EDSR-PyTorch](https://github.com/thstkdgus35/EDSR-PyTorch)。这个project整合了多个模型的代码，在这里只训练了EDSR和MDSR的部分。
在训练MDSR（同时训练多个超分scale）时发现trainer.py里有一个小bug，应该是作者洗了一个TEMP版本最后忘记修改了。原版本只对模型中负责X2放大的参数部分进行了训练。[替换代码](https://github.com/Yuehan717/Video-Super-resolution/blob/main/Code/trainer.py)
