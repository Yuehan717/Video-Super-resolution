# Video-Super-resolution
整理视频超分相关的基础知识，以及关于图像超分辨率和视频超分辨率(VSR)的论文和论文笔记。主要包括real-time模型和面向real world的模型。
## 基础知识
参考资料：[Digital video introduction](https://github.com/leandromoreira/digital_video_introduction)
### 基础术语
一张图像(image)可被视为一个2D矩阵(matrix)。如果考虑彩色图像，可以增加一个提供色彩的维度(dimension)，用一个3D矩阵表示。通常使用原色(Red, Green, Blue)表示彩色图像。
![三维图像矩阵](https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/3D.png)

矩阵中的每个点被称为一个像素(pixel)。像素通常是一个数值，表示其代表的颜色（原色）的强度(intensity)。图像颜色可以通过不同强度的原色的组合表示。

每个像素值需要一定位数的数字去表示，所需位数被称为位深度(bit depth)。当图像的位深度是8 bits的时候，图像的色深(color depth)是24 bit(3 planes R/G/B * 8 bits)，可以表示2的24次方种色彩。这个数值十分接近肉眼所能分辨的颜色，因此也被称为真色彩(True color)。

图像的另一个性质是**分辨率(Resolution)**,它表示一个维度上像素的个数，通常表示为宽度X高度。同时，在研究图像或视频的过程中也经常见到**长宽比(Aspect ratio)** 一词，它表示图像或者像素的宽和高的比例关系，分为显示长宽比(display aspect ratio)和像素长宽比(Pixel aspect ratio)。

最后，我们可以将视频定义为在时间维度上连续的n个帧(Frame)。n是帧速率或者每秒帧数(FPS)。

![Frames](https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/video.png)

### 颜色模型
常见的颜色模型是RGB模型，但实际上，还有其他模型也常被使用。YCbCr是一个将luma(亮度)和chrominance(颜色)分开表示的模型。这个模型使用**Y**表示亮度和另外两个色彩通道**Cb** (chroma blue)和**Cr** (chroma red)。YCbCr这RGB之间可以相互转换。Y可以用R G B的线性组合表示。

### 帧类型
+ I Frame (intra, keyframe)

也称关键帧，它不依赖于对其它帧的渲染而获得。I-frame类似一张静态的图像，视频的第一帧通常是I-frame，但是他也会规律地插在其它帧之间。
+ P Frame (predicted)

+ B Frame (bi-predictied)

### 图像超分的传统方法
#### Bicubic Interpolation
### 其它重要方法
#### 光流法（Optical Flow）
#### 运动补偿（Motion Compensation）
## 图像超分辨率
+ 综述

1. [Deep Learning for Image Super-resolution: A Survey](https://ieeexplore.ieee.org/abstract/document/9044873)
+ Classical SISR (Single Image Super-Resolution) Model

1. [(CVPR2017)Enhanced Deep Residual Networks for Single Image Super-Resolution (EDSR)](https://github.com/Yuehan717/Video-Super-resolution/blob/main/Notes/EDSR.md) NTIRE2017 Champion
2. [(ECCV2018)Image Super-Resolution Using Very Deep Residual Channel Attention Networks (RCAN)](https://github.com/yulunzhang/RCAN)
3. [(CVPR2018)Deep Back-Projection Networks For Super-Resolution (DBPN)](https://openaccess.thecvf.com/content_cvpr_2018/html/Haris_Deep_Back-Projection_Networks_CVPR_2018_paper.html) NTIRE2018 Champion
+ Real-time Model

1. [(CVPR2016)Real-Time Single Image and Video Super-Resolution Using an Efficient Sub-Pixel Convolutional Neural Network (Sub-pixel)](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Shi_Real-Time_Single_Image_CVPR_2016_paper.pdf)
+ Model Targeting Real-world Cases

## 视频超分辨率
+ 综述

1. [Video Super Resolution Based on Deep Learning: A comprehensive survey](https://arxiv.org/abs/2007.12928)
+ Real-time Model

1. [(CVPR2017)Real-Time Video Super-Resolution with Spatio-Temporal Networks and Motion Compensation (Sub-pixel in video)](https://openaccess.thecvf.com/content_cvpr_2017/html/Caballero_Real-Time_Video_Super-Resolution_CVPR_2017_paper.html)
2. [(CVPR2019)Recurrent Back-Projection Network for Video Super-Resolution (RBPN)](https://openaccess.thecvf.com/content_CVPR_2019/html/Haris_Recurrent_Back-Projection_Network_for_Video_Super-Resolution_CVPR_2019_paper.html)

## NTIRE (New Trends in Image Restoration and Enhancement) CHALLENGE
### NTIRE2017
**SISR**
[NTIRE 2017 Challenge on Single Image Super-Resolution: Dataset and Study](https://openaccess.thecvf.com/content_cvpr_2017_workshops/w12/html/Agustsson_NTIRE_2017_Challenge_CVPR_2017_paper.html)  
[NTIRE 2017 Challenge on Single Image Super-Resolution: Methods and Results](https://openaccess.thecvf.com/content_cvpr_2017_workshops/w12/html/Timofte_NTIRE_2017_Challenge_CVPR_2017_paper.html)
### NTIRE2018
**SISR**
[NTIRE 2018 Challenge on Single Image Super-Resolution: Methods and Results](https://openaccess.thecvf.com/content_cvpr_2018_workshops/w13/html/Timofte_NTIRE_2018_Challenge_CVPR_2018_paper.html)  
**Image dehazing**
[NTIRE 2018 Challenge on Image Dehazing: Methods and Results](https://openaccess.thecvf.com/content_cvpr_2018_workshops/w13/html/Ancuti_NTIRE_2018_Challenge_CVPR_2018_paper.html)
### NTIRE2019
2019年设置了更多赛道，方向包括：
+ **real image SR** (之前使用的数据集都是用特定方法下采样得到的LR图像，在这样的数据集上进行训练的模型不能保证在真实低分辨图像上有好的表现)
[NTIRE 2019 Challenge on Real Image Super-Resolution: Methods and Results](https://openaccess.thecvf.com/content_CVPRW_2019/html/NTIRE/Cai_NTIRE_2019_Challenge_on_Real_Image_Super-Resolution_Methods_and_Results_CVPRW_2019_paper.html)
+ **video deblurring and SR**
[NTIRE 2019 Challenge on Video Deblurring and Super-Resolution: Dataset and Study](https://openaccess.thecvf.com/content_CVPRW_2019/html/NTIRE/Nah_NTIRE_2019_Challenge_on_Video_Deblurring_and_Super-Resolution_Dataset_and_CVPRW_2019_paper.html)
[NTIRE 2019 Challenge on Video Deblurring: Methods and Results](https://openaccess.thecvf.com/content_CVPRW_2019/html/NTIRE/Nah_NTIRE_2019_Challenge_on_Video_Deblurring_Methods_and_Results_CVPRW_2019_paper.html)
+ **real image denoising**
[NTIRE 2019 Challenge on Real Image Denoising: Methods and Results](https://openaccess.thecvf.com/content_CVPRW_2019/html/NTIRE/Abdelhamed_NTIRE_2019_Challenge_on_Real_Image_Denoising_Methods_and_Results_CVPRW_2019_paper.html)
还有**image enhancement, image colorization, image dehazing**
### NTIRE2020
2020年的赛道更丰富，除了之前已有的real-world图像和视频的超分和deblurring, 还增加了光谱重建，video quality mapping和去摩尔纹。
+ Super-resolution
[NTIRE 2020 Challenge on Real-World Image Super-Resolution: Methods and Results](https://openaccess.thecvf.com/content_CVPRW_2020/html/w31/Lugmayr_NTIRE_2020_Challenge_on_Real-World_Image_Super-Resolution_Methods_and_Results_CVPRW_2020_paper.html)  [NTIRE 2020 Challenge on Perceptual Extreme Super-Resolution: Methods and Results](https://openaccess.thecvf.com/content_CVPRW_2020/html/w31/Zhang_NTIRE_2020_Challenge_on_Perceptual_Extreme_Super-Resolution_Methods_and_Results_CVPRW_2020_paper.html) 
+ Deblurring
[NTIRE 2020 Challenge on Image and Video Deblurring](https://openaccess.thecvf.com/content_CVPRW_2020/html/w31/Nah_NTIRE_2020_Challenge_on_Image_and_Video_Deblurring_CVPRW_2020_paper.html)
+ Demoireing
[NTIRE 2020 Challenge on Image Demoireing: Methods and Results](https://openaccess.thecvf.com/content_CVPRW_2020/html/w31/Yuan_NTIRE_2020_Challenge_on_Image_Demoireing_Methods_and_Results_CVPRW_2020_paper.html)
