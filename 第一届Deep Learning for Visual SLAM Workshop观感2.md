# 第一届Deep Learning for Visual SLAM Workshop观感: 后四讲

书接上回，我们这一篇介绍1st International Workshop of Deep Learning for Visual SLAM的前三讲。这一篇介绍后续的四讲内容。

## Matthias Niessner
Niessner教授（个人主页http://www.niessnerlab.org/）目前在TUM任职，早年以利用CUDA技术改进KinectFusion[1]一举成名。目前在很多领域都有研究成果，不过最出名的还是他在三维重建方面的贡献，比如BundleFusion[2]ScanNet[3]。Niessner大佬这次演讲的主题是三维数据的演变：从三维几何到语义信息，再到图像与三维信息的结合。他介绍了他们实验室开放的数个开源数据集，包括ScanNet的两个版本以及和Matterport合作发布的Matterport3D，都在几何信息的基础上由专人标注了语义信息。他们组最新的论文3DMV[4]通过结合图像与三维几何这两类互补的信息来提高三维物体识别和分割的精度。这种融合多类信息的学习方式也是不错的研究方向。不过他们组到目前还只在用Voxel这种表示方法，不知道将来会不会尝试别的三维表示方式。

## Katerina Frakiadaki
Fragkiadaki教授（个人主页https://www.cs.cmu.edu/~katef/）任职于CMU。她师承UPenn的Jianbo Shi大牛，在往上数就是Malik大佬了，是一个比较年轻的教授。她讲的主题是SLAM与物体识别相互辅助（Objects-aware SLAM and SLAM-aware Object Recognition）。首先深度学习可以帮助解决SLAM中的如下问题：1. 物体尺寸上的二义性；2. 动态物体；3. 重建不可见的内容。而相应的SLAM可以为深度学习提供几何信息辅助物体识别。她举得例子中一个是典型的利用Photometric Loss无监督恢复深度与相机运动的工作。在这个基础上加入了物体识别和物体姿态估计。另一个则是用RGB图象序列恢复三维信息的工作。她们实验室感觉是GAN的死忠粉，疯狂的在各种问题加入GAN。由于讲的时候还呼吁大家不要毙掉她提的这些还未发表的工作，整体观感比较差。不过核心理念是没错的，利用物体识别解决动态物体和不确定的场景比例，同时在物体识别中引入位置信息提高准确率。

## Noah Snavely
Snavely大神（个人主页http://www.cs.cornell.edu/~snavely/）也无须多介绍，只要知道Photo tourism和Building rome in a day这两篇利用互联网海量图片做SfM的工作，就会知道他的影响力。他讲的主题是如何利用互联网视频进行无监督的深度和相机姿态学习，算是在Zhou et. al. [5] 的基础上再继续深挖无监督视频学习的潜力。Zhou et. al. [6] 这一篇采用Youtube视频作为训练集来训练一个能够增幅相机间距离（Stereo Magnification）的网络。训练时取连续两帧（窄基线）作为输入，而网络生成的则是以多个平面（例如前景，背景）表示的图像。采用多平面表示方法能够很方便地合成出宽基线的图片。这也是这篇论文的主要创新点。除了开发不同的图像表示方法，从视频中学习更多信息外，Noah还介绍了他们的新单目深度估计数据集MegaDepth[7]。MegaDepth包含了196个知名景点的网络数据，采用SfM+MVS以及一些depth outlier剔除方法生成，很可能会成为将来单目深度估计的标准数据集。
虽然Noah Snavely大神的工作多是SfM方面的，但是对于SLAM来说还是有很多值得借鉴的地方。总而言之，一方面是通过视频进行无监督学习，另一方面则是用单目深度估计来做稠密的场景重建。

## Vladlen Koltun
Vladlen Koltun是Intel Intelligent System Lab的director，个人主页（http://vladlen.info/）。他这次讲的主题比较哲学，探讨的是机器人导航是否需要完整且精确的SLAM结果。在引用了一些哲人名言以及神经学论文后，他给出的结论是动物（包括人类）在做导航时大多数时候并不需要建立完整的场景信息，而一些局部信息加上一些稀疏的导航点（waypoints）就够了。在[8] 这篇论文中，他们在毁灭公爵第一代（Doom）上进行训练，通过深度网络同时构建场景信息并完成导航。这个深度网络包括特征提取（传统CNN图像处理）和记忆图两个部分。CNN网络读取图片得到记忆图中的节点，然后学习记忆图中边的权重从而得到寻路的路径。虽然这篇论文训练的场景相对简单，但是结果还是很有说服力的。Vladlen Koltun很喜欢用合成数据（Synthetic Data）来做机器人导航和SLAM，他的另一篇工作MINOS[9]中就结合了合成数据集SUNCG和真实数据集Matterport3D搞了一套机器人导航的框架。

## 总结
第一届Deep Learning for Visual SLAM Workshop总得来说还是干货满满的。可以预见在将来深度学习和SLAM结合的案例会越来越多，希望大家能在这个方向里找到自己的研究方向。最后祝大家和SLAM玩得愉快！

## Reference
[1] Nießner, Matthias, et al. "Real-time 3D reconstruction at scale using voxel hashing." ACM Transactions on Graphics (ToG) 32.6 (2013): 169.
[2] Dai, Angela, et al. "Bundlefusion: Real-time globally consistent 3d reconstruction using on-the-fly surface reintegration." ACM Transactions on Graphics (TOG) 36.3 (2017): 24.
[3] Dai, Angela, et al. "Scannet: Richly-annotated 3d reconstructions of indoor scenes." Proc. IEEE Conf. on Computer Vision and Pattern Recognition (CVPR). Vol. 1. 2017.
[4] Dai, Angela, and Matthias Nießner. "3DMV: Joint 3D-Multi-View Prediction for 3D Semantic Scene Segmentation." arXiv preprint arXiv:1803.10409 (2018).
[5] Zhou, Tinghui, et al. "Unsupervised learning of depth and ego-motion from video." CVPR. Vol. 2. No. 6. 2017.
[6] Zhou, Tinghui, et al. "Stereo Magnification: Learning view synthesis using multiplane images." arXiv preprint arXiv:1805.09817 (2018).
[7] Li, Zhengqi, and Noah Snavely. "MegaDepth: Learning Single-View Depth Prediction from Internet Photos." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2018.
[8] Savinov, Nikolay, Alexey Dosovitskiy, and Vladlen Koltun. "Semi-parametric topological memory for navigation." arXiv preprint arXiv:1803.00653 (2018).