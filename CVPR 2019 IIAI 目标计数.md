## CVPR 2019 IIAI 目标计数

[我爱计算机视觉](javascript:void(0);) *4天前*

点击我爱计算机视觉标星，更快获取CVML新技术

------



本文来自起源人工智能研究院（IIAI）。



本期以Object Counting为主题，简要介绍IIAI CVPR 2019录用论文中关于Counting的两篇。



**Crowd Counting and Density Estimation by Trellis Encoder-Decoder Networks**



作者：Xiaolong Jiang, Zehao Xiao, Baochang Zhang, Xiantong Zhen, Xianbin Cao, David Doermann, Ling Shao



论文链接：

https://arxiv.org/abs/1903.00853



**Motivation**



人群计数（crowd counting）和人群密度估计（density estimation）这两项视觉任务是实现人群管理、异常行为分析、场面监视等高阶任务的基础，近年来赢得了越发广泛的关注和研究热情。传统方法通常采用基于检测（detection-based）或者直接回归的方法（regression-based）来实现人群计数。


随着深度学习和CNN技术的不断发展，基于密度估计的人群计数方法已成功取代传统方法，实现了性能的飞跃。然而，如同其他利用深度CNN网络实现逐像素回归重建（image-wise reconstruction）的任务（例如语义分割，超像素，景深估计等）一样，密度估计方法中采用的（Encoder-Decoder）编/解码网络缺乏任务相关性，表现出一定的方法盲目性和单调性。其中，encoder网络多直接采用面向分类任务的卷积神经网络，它们往往着力于实现由spatial信息向channel-wise semantic信息的转化，在前向传播过程中将特征的空间维度不断通过池化的方式压缩转化为通道维的语义depth。显然，这样构型的网络不利于保持特征的空间信息完备性（spatial fidelity），从顶层设计上就不适用于像人群密度估计这样的，逐像素回归的，具有一定空间定位目的的任务。


本文针对这一问题，提出了一种新的Trellis Encoder-Decoder networks。Trellis networks采用了创新的multi-path decoder构型，配合上面向逐像素回归的较浅少降采的encoder，有效提高了所得密度估计图中像素级定位精确性，以及最终所得人群计数的准确性。同时，我们还针对图像重建类任务，提出了两种新颖的损失函数，有效缓解了传统mean-square-error（MSE）loss的像素空间独立性假设，提高了网络的训练效果。



**Method**



针对逐像素回归重建任务（即输出一张与输入图片同尺寸的，具有特定物理意义的，概率密度估计图），基于CNN的方法多采用hourglass构型的编/解码器网络。现有此类方法存在两大弊端，导致所得估计密度图的像素级定位精度和估计准确性不足。（1）编/解码器设计沿用传统分类网络构型，导致所的特征图以及最终估计图的空间维度过度缩减，从而丧失像素级定位精度；（2）单通道hourglass构型的编/解码器网络不能实现充分的跨尺度特征融合，从而无法同时达到最优的定位精度及估计准确性。针对这两个缺陷，我们在本文中提出了如下所示的Trellis网络：

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPxU6iaibHW6PfRuKPKmoELqosxL5icibxeDjQqLibF69EWkzpgA4SoDiczafQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**编码器**



如图所示，Trellis网络的编码器部分将降采样率控制为4，达到保持特征空间信息完备性的目的。具体来讲，输入512*512的图片，在整个编码和特征提取过程中只经历两个池化操作，降低在编码过程中对空间逐像素精度的损失。另外，为了提高网络对人群计数目标尺度变化大这个特点的适应，Trellis采用类inception的多尺度编码block，在每个卷积block利用不同大小的卷积核进行特征学习。具体编码器构型如下图所示：

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUP00tCpsJlibxT4fIYW7ERPCLMc1OFX4bnxTgVCdg8Q1WY9D7Ioic3yphw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**解码器**



解码器部分（如下图所示），不同于传统hourglass编/解码器的单通道特性，Trellis网络在不同的编码阶段（如图中标黄的1，2，3，4四个encoding block）同时开启多条解码通道。每一条解码通道内利用级联的解码block构成独立的特征流。在每条解码通道的底端，形成一个中段密度估计图。而在相邻的两条解码通道之间，不同的解码block内，来自于不同通道的尺度不同的特征通过密集skip connection（红色箭头）得到系统地融合。纵横交错，在Trellis网络中我们构建了一个多尺度融合的特征“栅格”，而“Trellis”网络也由此得名。这样一来，整个Trellis网络可以看作是多个单通道hourglass编/解码器的融合，实现更为充分系统的多尺度特征融合，从而得到像素级定位性以及估计准确性都更佳的密度估计图。基于此密度估计图，我们不仅可以得到更准确的人群计数结果，同时还可以观察更精确的人群空间分布情况。

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPPVfJUWZsYHOAga1z9kVN9MzQmUibKCVEAibeMibAOk2OqjlPic0C1OoIFw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**分布式多监督**



Trellis网络多通道特性还使得其具有更好的可训练性。利用多个中段密度估计图，我们在训练Trellis时采用了multi-loss的分布式多策略，即在每条解码通道的底端都施加一个损失函数进行，分布式地直接监督中段密度估计图的生成。如此以来，在back-propagation的过程中梯度的反传就有了多个起点。同时，由于Trellis本身“栅格”化密集多通道（多skip connection）连接特性，梯度流也的传导更为通畅。综合来看，在Trellis网络的前端梯度消失的问题可以得到极大缓解。实验也证明了Trellis网络的收敛性要优于传统单通道hourglass网络。值得注意的是，我们提出的分布式监督与现有的多监督（deep-supervision）等策略是不同的。现有的多监督往往是将loss直接施加在卷积过程中的特征图上。相比之下，我们是将多个loss施加在一个network ensemble的每个子网络的中段输出密度估计图上。这样一来，每个loss有着更为直观的物理意义和监督效力。同时，不同loss的组合也能发挥更大的监督性能。



**Combinatorial损失函数**



传统密度估计网络的训练均采用MSE损失函数。这种损失函数假设像素间的独立性，不利于监督估计所得密度图（Z）与ground truth（Y）之间的局部空间相关性。对此，我们提出了新的空间抽象loss（Spatial Abstraction Loss，SAL）和空间相关性loss（Spatial Correlation Loss，SCL）。前者通过在密度图上搭建由级联的池化层（共K层）构成的金字塔，来实现在不同空间抽象层上MSE的计算（式中Nk表示第k抽象层上像素总数，φ代表池化操作）：

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPgCdibSEO5d5nL2DczpLj9ib3ZTpYEgbCBcudYR4SvicwN7jQtLv1PPYjw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

后者则是直接约束两张密度图的归一化互相关系数，从而使得估计值在整体上趋紧ground truth（式中p、q为密度图中像素横纵坐标，P、Q为横纵坐标最大值）：

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPIuic3xahOc5Q1DKWgqunOPl69ic9D8gsCurUop5U2sazuJnQOZ125kSg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在传统的image reconstruction任务中，常用的MSE损失函数逐像素计算两图间的差值再取全局平均。这样的操作假设了每个像素的独立性，完全忽视了像素间的局部空间关联性，计算所得的损失函数值无法反映两张图像的空间结构性差别。我们提出的SAL通过空间降采得到不同空间尺度上的密度图金字塔，在金字塔的各层上分别计算MSE loss从而反映在不同abstraction level（即不同空间感受野）上两图的差别。另外，SCL直接通过互相关系数的计算反映两张图的全局相似性，在更高层上监督密度估计图的生成。在实验中，SAL与SCL通过一个加权系数λ进行组合。Combinatorial loss具体实现请见下图：

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPZicMiauIVAdsg7P6t49k9wu9Q4nx7qQiaR5Elsm2DdfASic9DJD3AylQVQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**Experiments/Performance**



我们将提出的基于Trellis编解码器网络的人群计数方法在四个主要的benchmark上与多个当前最优方法（MCNN，SwitchingCNN，SANet，CSRNet，CPCNN等等）进行了对比，取得了state-of-the-art的人群计数结果（人群计数性能指标采用mean-average-error（MAE）和mean-squared-error（MSE），见Table 2，3）。同时，我们的Trellis网络相比于其他方法能够生成质量更高更的密度估计图（由PSNR/SSIM指标衡量，见Table 4）。除了Trellis网络自身的系统性密集多尺度融合之外，这种高质量密度图的生成还取决于我们采用整图估计生成的策略，摈弃了其他方法惯用的剪裁分步估计的方法（patch-wise operation），从而有效回避了由于patch拼接导致的边界效应和异物。


特别地，我们通过ablation study说明了multi-path Trellis network，分布式监督，以及combinatorial loss这三大contribution各自对计数效果和密度估计图质量带来的贡献，具体结果见Table 1.

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPQUbUfCpHuP8FvAbFwXZE8PreoGvc0AjHAgpMNYVArO3onMzDFteP7g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPkFCn4QSBFo5bpXJ4oWqSExc9GDlxL8Ubv2mDticegh99Eu875ia8xS7Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPTj7DjthDibMrvHUfxbxgxHUoOND0BgwcezwrxsvB0FzkVmC59wQkDpQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPPsFzCqDWiabmwibUQ5prN9W5aIH5m5XMo5oEdycU9H87QiaicluFgLEyjA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



具体密度估计效果和计数精度见下图：

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUPxUDcMjYR12QBauOA0c5h3bwWvib14DwCsHshcaqtRJOcvBSfGtczRMw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBknic2ldVYYN3gjh4WqKLsUP5gJplppX7bKUmHxvE6HqU96jB6Aic1g41jCUbLpqCA7wBcnkaCPefCQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**Object Counting and Instance Segmentation with Image-leve Supervision**



作者：Hisham Cholakkal, Guolei Sun, Fahad Shahbaz Khan, Ling Shao



论文链接：

https://arxiv.org/pdf/1903.02494.pdf

代码链接：

https://github.com/GuoleiSun/CountSeg



**Motivation**



common object counting，又被称generic object counting，旨在精准地预测出自然场景图片中的不同类别目标的个数。已有工作大多采用localization-based方法，或者直接用回归模型预测目标数量。后者通常具有更好的预测效果，但是只能预测出目标的全局数量，无法给出目标的位置。除了全局数量信息，per-category density map的spatial distribution在很多视觉任务里也同样是非常有用的信息。density map的spatial distribution通常在crowd counting里被深入研究，但是自然场景图像并不像crowd counting里只有人群，它具有多个类别的目标，并且多数类别在多数图片中并不会出现；而且crowd counting通常需要instance-level (point-level或bounding box) supervision。本文提出image-level supervised density map estimation方法对自然场景图像做common object counting，不但预测出目标的数量，还给出目标的spatial distribution。为此，我们引入人类数少量目标时利用holistic cues非挨个计数的属性，提出image-level lower-count (ILC) supervised density map estimation，该方法不但适用于counting，对于image-level supervised instance segmentation也同样有效。



**Method**



整个网络架构如下图所示，具有两个分支，classification branch和density branch，利用ILC supervision联合训练。classification branch预测目标是否存在，并且为训练density branch生成pseudo ground-truth。density branch的loss function中有两项，global loss和spatial loss；density branch生成density map，以预测目标的全局数量和给出spatial distribution。具体各个分支、loss function的设计请参见原文。

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBlz7mUFEYugzHzLHdVclolQkS2QKmtW67TkRibEXicIypsQ9SGicrAYKv11yE5ibKftviaXlicEeW7aRggg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**Performance**



和baseline、state-of-the-art方法在Pascal和COCO数据集上的比较分别如表1、3、4所示。Instance segmentation的表现效果如表5所示。

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBlz7mUFEYugzHzLHdVclolQ7fSY4fdgp3pDFvy9PIYm3gJJbbvsEcicJdhXjoAmdI6mxUocibibonggQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBlz7mUFEYugzHzLHdVclolQGL92fozoOeNyVUweBC0RXYaRfibfeCicsQweJibNhiatRq6eOp84SL5RIg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBlz7mUFEYugzHzLHdVclolQQKlTIAwrrI8ficVrgoTdYOKT1CHfQ94Y8x0PhvlicDTPTS2jJzovsrQw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/kF9VjRO0eBlz7mUFEYugzHzLHdVclolQw7D7hUl0gjmDzQwf3XSRsWSlXBWRwfpwHmwIjeBicAUzTemLMicfNkNg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)









IIAI主页：http://www.inceptioniai.org/



**专业交流群**



关注最新计算机视觉与机器学习技术，欢迎加入52CV专业交流群，扫码添加CV君拉你入群，



**（请务必注明:52CV）：**

**![img](https://mmbiz.qpic.cn/mmbiz_png/BJbRvwibeSTs1Ke4WXicIqN7QibMXL527MCvicgajlnePVw1mnomoLqFqL0WLf7UUpSkVGj2E1GGe83e8ZmY0G42jw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

喜欢在QQ交流的童鞋可以加52CV官方QQ群：702781905。

（不会时时在线，如果没能及时通过还请见谅）





------

![img](https://mmbiz.qpic.cn/mmbiz_png/BJbRvwibeSTvVOnJBvePcP1qFUSWpyvrjpYAWNIZTZzUA7Zq4VPlReicJWcIeozxic5VhHlwNQNAFXmKQBtKf5xAQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

长按关注我爱计算机视觉











微信扫一扫
关注该公众号