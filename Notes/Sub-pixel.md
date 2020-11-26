#【论文笔记】Real-Time Single Image and Video Super-Resolution Using an Efficient Sub-Pixel Convolutional Neural Network
[论文地址](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Shi_Real-Time_Single_Image_CVPR_2016_paper.pdf)

[代码地址](https://github.com/atriumlts/subpixel)

这篇论文的主要贡献是提出了一种先在low-resolution下进行图像处理，再进行upsampling的方法。此前的深度模型基本是先使用插值方法把LR的图像upsampling到HR空间，再对HR的图像进行卷积等处理，重建细节。处理HR图像会消耗较大的计算资源，而使用这篇文章的方法则可以避免此项处理，在LR空间下完成，从而降低了对资源的要求，是的实时处理成为可能。
## 模型

## 实验
## 不足
