# Visual SLAM前端技术（上）

上一篇文章里，我和大伙儿分享了一下个人对Visual SLAM整体上的一些看法。这篇我主要想讲一下前端的技术。不过Visual SLAM前端技术分支很多很杂，预计需要分上下两篇。上篇里我会主要通过介绍不同的技术分类方法来给大家比较全面的一个Visual SLAM技术发展情况。而下篇我会挑一种比较典型的Visual SLAM技术做一下介绍。这些分类方法对科研方面比较有帮助，但对实际工程作用不大。大家如果对典型SLAM技术比较感兴趣，可以略过此篇跳到下篇

## 四象分类法
Visual SLAM前端技术有很多分类方法，大家一般比较熟知的是稀疏（Sparse）vs.  稠密(Dense)。稀疏方法，顾名思义就是只抓取图片序列中的少量点，大多数时候是特征点，来进行匹配、三角化以及后续的优化。稠密方法则把整张图糊在另一张图上，然后不断微（揉）调（搓），达到最优的匹配效果。而作者个人则比较喜欢在这个基础上进一步做成两个维度四个象限，又叫做四象分类法（自己琢磨分类当然起名字就要随意一些(･ิω･ิ)）。这两个象限分别是时间（或相机运动轨迹所在的六维空间SE3）和图像空间（R2）。在图像空间上进行离散采样和连续采样分别对应传统的稀疏方法和稠密方法。而在时间（运动轨迹）维度上，区分方法为是否采用时间连续（time coherence）。这样划分的话，大多数Visual SLAM和SFM的工作就都可以乖乖的归类在此了。尽管这里讨论的是前端技术，后端优化时的模型可以和前端相同或完全不属于同一个象限。因此，后端技术也可以用这个分类方法进行归类。

<img src="https://github.com/yzxsunshine/zhihublogDLVSLAM/blob/master/four_quardrant_front_end.png" alt="四象分类法" width="640px"/>

### 相机轨迹离散 + 图像空间离散
这类方法是比较传统的SfM三维重建方法。这类方法因为是离线处理，所以一般也不需要分前后端。
比较典型的流程是：
1. 采集视频流，提取灰度图
2. 提取关键帧
3. 提取关键帧特征（例如，SIFT、SURF、ORB）
4. 特征匹配（一般用kd-tree或者vocabular tree存储和检索特征）
5. 计算摄像机姿态
6. Bundle Adjustment

这类方法因为没有空间连续和时间连续的假设，得到的相机姿态有时候会有些抖。相机姿态的精度取决于特征点或点云的精度。

### 相机轨迹连续 + 图像空间离散
这一象限里的方法就是大家通常所说的基于特征的Visual SLAM方法，也是Visual SLAM初学者接触最多的一类方法。这类方法引入了相机运动连续的假设，就可以利用相邻帧信息帮助计算相机姿态（temporal coherence），最大的好处就是可以带入运动模型以及对相机轨迹求导了。通过采用各种优化方法（Gradient Decent，Gaussian-Newton, LM, dogleg）去优化摄像机模型。比较典型的是大名鼎鼎的PTAM。
这些方法如果是单目的话，一般会有个初始化过程。初始化步骤的流程类似于1，得到几个初始的相机位置和一部分点云信息。初始化之后的步骤流程大致如下：
1. 利用相机运动模型给出一个预测的相机姿态\xi*
2. 根据\xi*把点云投影到图像空间
3. 在图像空间提取特征点x'
4. 匹配投影点x与附近的特征点x'找到最匹配的特征点
5. 修正三维点云在图像空间的投影位置（x = x'）
6. 代入到公式，最小化反投影误差之和\sum{r_i}，其中变量为\xi。
7. 将得到的新\xi更新相机运动模型

图片

PTAM在上述流程的进行过程中，会开另一个线程用提取出的关键帧和点云做Bundle Adjustment来优化相机位置和点云信息。

这类方法得到的相机姿态比较平滑，效果也不错，前几年比较流行。需要掌握的数学部分包括：
a) SO3和SE3的求导问题。稍微学一些简单的黎曼几何之后，这层窗户纸捅破了，实现很容易。
b) 高斯牛顿迭代，或者Levenberg–Marquardt最小二乘（就是Gauss-Newton加了一项\lambda I）
其他的部分OpenCV里面都有实现，Bundle Adjustment可以用一下bundler或者sba的实现。（bundler封装了sba）。
剩下的部分都是自由发挥或者偏工程实现的部分了。
具体的一些算法和流程，我会在下篇里介绍一下

### 相机轨迹连续 + 图像空间连续
这一象限是最近几年比较热门的Dense/Direct方法。之所以叫Dense（稠密）方法，是因为相比提取特征的稀疏方法，这类方法假设图像是对场景的观测结果，可以表示为一个连续的函数I(x)。那么当场景是静态场景，光照相对恒定的时候，那么相机相对位置求解的问题就可以转化为两张图片对齐的问题。把一张图像糊在另一张上（image warping）在机器视觉领域里已经有很成熟的方法了，求解相机姿态的方式有Forward-addition, inverse-composition等等，都是最小化photometric error （公式见图2）。公式里I是图像的intensity（灰度/亮度），\pi是一个三维到二维的投影函数（x和y分别除z），T是刚体在三维空间的变换函数，D是深度（p所对应的三维点投影之后z的值），p是二维点，\xi是6 DOF的相机姿态。这部分的计算和上一个分类的大同小异，只是求导的链式法则里多了一个乘图像灰度的项。比较好的参考资料是 [13, 14]，里面有具体的公式和求导过程。

这类方法虽然在理论上要更先进一些，不太需要担心特征点稀少的问题。但实际操作时候有不少问题。
1. 需要非常好的相机姿态初始值才能避免迭代到局部最优解。因此相机帧率不能太低，但因为点多计算量又很大，如果没有很好的工程优化进行CPU或者GPU加速（最好GPU）的话，很难跑在线SLAM。
2. 这一类方法大多数没有很好的重定位（relocalization）解决方案，大多数工作要么压根不管这个，要么用一些传统的人工智能方法（Random Forest, Fern等）来找最近的关键帧。
3. 这类方法通常缺乏比较好的全局约束，大多数都是靠相机姿态图优化（Pose Graph Optimization）。不像稀疏方法可以用三维点-相机姿态的共视约束用地图（Map）信息优化姿态。作者的论文PSM-SLAM[12]就是在使用Dense方法时候建立少量且可靠的共视约束，并且使用photometric bundle adjustment做全局优化方面做了一些尝试。

### 相机轨迹离散 + 图像空间连续
看了上一个分类，我们知道当图像空间连续的时候（Dense/Direct方法），每一帧都需要很好的相机初始值。因此此类方法在没有相机模型的情况下，很难进行求解。通常都是先用SfM方法得到初步的相机姿态以后，再进行photometric bundle adjustment进行全局优化，得到稠密的模型。[10]这篇文章尝试使用滑动窗口的local photometric bundle adjustment做了一些SLAM方面的尝试，但是在实验结果上说服力并不强。


## 帧到帧 vs 帧到模型
除了稀疏、稠密或四象分类之外，还有一种分类法，大致可以把这些工作分成帧到帧(frame-to-frame)以及帧到模型两类(frame-to-model)。当然大多数提到这种分类的时候指得都是稠密方法。因为对于稀疏方法来说，帧到帧这种方式因为重建的地图（Map）利用率太低，并不是实用。举个例子的话，一个简单的使用RGBD相机的稀疏帧到帧方法可以采用以下流程
1. 提取上一帧特征（例如，SIFT、SURF、ORB，甚至随机选一些一阶灰度不为0的点）
2. 将特征点反投影到三维空间形成临时三位点云
3. 在当前帧找上一帧匹配点
4. 解PnP得到当前帧相机位置
这样做很明显丧失了全局的地图共视约束，只能用相机姿态图优化，效果自然不好。
而稠密方法（Dense/Direct）中，大多数都是把最近关键帧的点云用于求解当前帧相机位置，误差也会不断累积下去。直到触发回环检测，换掉最近关键帧之后才能更正误差。好在这类方法每一帧的相机位置误差比较小，所以大多数时候效果还可以。

帧到模型这类方法是大多数稀疏方法采用的方案，既把地图中的三维点（model）投影到图像空间（frame）进行姿态求解。这类方法有更多的全局约束，可以利用地图信息和共视原理进行姿态优化，修正一些累积误差。而部分稠密方法，比如KinectFusion，同样是将Volume投影到一个虚拟相机的位置做ICP，所以也属于frame-to-model这一类。但是这些稠密的帧到模型(dense frame-to-model)方法一般都是不断更新模型而没有建立共视约束，所以并不存在最终的优化过程。同样，PSM-SLAM[12]也是在从这个角度触发，尝试给稠密方法加入可以和稀疏方法帧到模型类似的全局共视约束优化。

## 总结
说了这么多，就是希望大家能对Visual SLAM的各种方法和策略有一个更全面的了解。通过不同的角度看问题，说不定会发现一些新的研究点。这也是作者当年这两篇论文的尝试方向。希望这种分析问题的方式能给大家带来一些启发。下一章我会介绍一些稀疏方法的基本知识。同时也会总结一下CVPR2018的workshop: Deep Learning for Visual SLAM。尽早的引入一些深度学习的内容，不等Visual SLAM写完后再加了。

## 参考文献
[1] Davison, Andrew J., et al. "MonoSLAM: Real-time single camera SLAM." IEEE transactions on pattern analysis and machine intelligence 29.6 (2007): 1052-1067.
[2] Klein, Georg, and David Murray. "Parallel tracking and mapping for small AR workspaces." Mixed and Augmented Reality, 2007. ISMAR 2007. 6th IEEE and ACM International Symposium on. IEEE, 2007.
[3] Mur-Artal, Raul, Jose Maria Martinez Montiel, and Juan D. Tardos. "ORB-SLAM: a versatile and accurate monocular SLAM system." IEEE Transactions on Robotics 31.5 (2015): 1147-1163.
[4] Engel, Jakob, Jurgen Sturm, and Daniel Cremers. "Semi-dense visual odometry for a monocular camera." Proceedings of the IEEE international conference on computer vision. 2013.
[5] Babu, Benzun Wisely, et al. "σ-dvo: Sensor noise model meets dense visual odometry." Mixed and Augmented Reality (ISMAR), 2016 IEEE International Symposium on. IEEE, 2016.
[6] Newcombe, Richard A., et al. "KinectFusion: Real-time dense surface mapping and tracking." Mixed and augmented reality (ISMAR), 2011 10th IEEE international symposium on. IEEE, 2011.
[7] Whelan, Thomas, et al. "ElasticFusion: Dense SLAM without a pose graph." Robotics: Science and Systems, 2015.
[8] Engel, Jakob, Vladlen Koltun, and Daniel Cremers. "Direct sparse odometry." IEEE transactions on pattern analysis and machine intelligence 40.3 (2018): 611-625.
[9] Forster, Christian, Matia Pizzoli, and Davide Scaramuzza. "SVO: Fast semi-direct monocular visual odometry." Robotics and Automation (ICRA), 2014 IEEE International Conference on. IEEE, 2014.
[10] Alismail, Hatem, Brett Browning, and Simon Lucey. "Photometric bundle adjustment for vision-based slam." Asian Conference on Computer Vision. Springer, Cham, 2016.
[11] Delaunoy, Amaël, and Marc Pollefeys. "Photometric bundle adjustment for dense multi-view 3d modeling." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2014.
[12] Yan, Zhixin, Mao Ye, and Liu Ren. "Dense Visual SLAM with Probabilistic Surfel Map." IEEE transactions on visualization and computer graphics 23.11 (2017): 2389-2398.
[13] Baker, Simon, and Iain Matthews. "Lucas-kanade 20 years on: A unifying framework." International journal of computer vision 56.3 (2004): 221-255.
[14] Kerl, Christian. "Odometry from rgb-d cameras for autonomous quadrocopters." Master's Thesis, Technical University (2012).