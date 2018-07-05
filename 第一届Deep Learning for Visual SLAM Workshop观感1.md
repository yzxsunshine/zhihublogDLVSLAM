# 第一届Deep Learning for Visual SLAM Workshop观感：前言与前三讲
就在上周美帝盐湖城，第一届Deep Learning for Visual SLAM Workshop在一群大佬们的组织下蹭着CVPR 2018一同召开了。这个Workshop和我们的专栏主题如此贴近，要是专栏里不收录一下各位大佬的演讲，岂不是徒有虚名？所以接下来几篇我会先更新总结一下大佬们的演讲，然后再继续我们原定的栏目更新计划（参见文章目录）。
Deep Learning for Visual SLAM Workshop（链接http://visualslam.ai/）是由数位Visual SLAM领域内的大佬们（组织人列表http://ronnieclark.co.uk/dl4vslam/organizers.html）组织，旨在探讨深度学习与Visual SLAM两个领域之间如何相互配合借鉴，共同成长。演讲者名单如下（按演讲顺序排序）：
* Jitendra Malik, UC Berkeley
* Simon Lucey, CMU
* Jan Kautz, NVIDIA Research
* Matthias Niessner, TUM
* Katerina Frakiadaki, CMU
* Noah Snavely, Cornell/Google
* Vladlen Koltun, Intel Research
我会分上下两篇简要介绍一下每一讲的内容，希望能对大家有些启发。

## Jitendra Malik
Jitendra Malik是UC Berkeley的一名教授（个人主页https://people.eecs.berkeley.edu/~malik/），在机器视觉业内赫赫有名。 Google Scholar上统计的引用数高达142218。这位大佬在机器视觉各个领域都有所建树，荣誉太多就不介绍了。这次Malik大佬的演讲还是以熟悉的3R套路开场。3R包括Reconstruction（重建）、Recognition（识别）、Reorganization（数据重组），涵盖了计算机视觉的主要问题。在这个大背景下，Malik教授断言，机器视觉过去从二维框（Box filter）演变到现在的二维图像，而未来则是属于三维数据的。而目前三维数据在机器学习，特别是深度学习中的表示方法目前还未有定论。大多数近期的研究都采用点云（Point Cloud）或体素（Voxel），这两者各有各的问题。点云无序，而且作为一种空间表示太过于系数；而体素要考虑分辨率和内存方面的限制。因此大佬给了两个更好的三维表示方法。第一个是一种混合的方案（Factored 3D），结合了场景布局、三维体素、相机位置、包围盒等信息，为下一步的语义处理提供更多的信息（[1] https://arxiv.org/pdf/1712.01812.pdf）；而另一个则使用传统的三维网格结构（Mesh）来表示三维数据。Malik教授随后举了一个通过少量鸟类图像样本恢复三维模型的例子（[2] https://arxiv.org/pdf/1803.07549.pdf）。

个人感觉头一讲的干活是最多的。以Malik教授在机器视觉和深度学习方面的资历和权威，他指得这条路很值得去探索一下。不过和Visual SLAM方面的关联相对弱一些，更侧重于深度学习处理三维数据。

## Simon Lucey
Simon Lucey是CMU的一名比较年轻的教授（个人主页http://www.cs.cmu.edu/~slucey/），在机器视觉和机器人领域都有大量工作。他对于深度学习在Visual SLAM的应用持一种谨慎尝试的态度。主张以小模块为单位用深度学习替换掉Visual SLAM的部件，以及用SLAM来辅助深度学习，二者互补来解决问题。他幽默的打了个比方，SLAM好比一个好动的小孩儿，一切都是全新的，未经探索的，保持着一个好奇的心去了解世界。而深度学习好比是一个老人，通过经验来解决问题，但是不适合做大量的测量和尝试。将二者结合，能发挥更好的效果。当然，空口无凭，Lucey大佬也举了一个例子[3]，同时利用深度学习得到的三维形状信息和Bundle Adjustment重建出更完整、精确的三维模型。我个人还是比较赞同这种思路的，希望将来能看到更多类似的工作。

## Jan Kautz
Jan Kautz是NVIDIA Visual Computing and Machine Learning Research的高级主管。这次演讲的主题是如何使用深度学习来解决从二维光流到三维的场景流（Optical Flow and Scene Flow）计算问题。其中光流的部分是这篇PWC-Net[4]，将图像金字塔(Pyramid)，图像扭曲(Warping)和Cost Volume（不知道怎么翻译，常用于双目视觉恢复视差和深度）等经典图像处理方法结合到深度神经网络中提高网络的速度和精度。而对于场景流，Jan介绍了他们提取动态场景中静态成分的工作[5]。核心思想是在PWC-Net外单独训练一个刚体变换检测网络（Rigid Transformation Network）来计算场景中的刚体掩码图像（rigid body mask），然后通过和光流进行布尔运算就可以得到动态和静态的成分。
这部分和Visual SLAM关系相对不大，但是对于相机变换和静态成分提取对于SLAM有很好的借鉴意义。我们组内有一篇工作将在今年出现在ISMAR 2018上，同样是解决动态场景问题。等论文正式登刊后我再给大家介绍一下。

后面的四讲我会在下一篇进行总结，敬请期待！欢迎讨论！

[1] Tulsiani, Shubham, et al. "Factoring shape, pose, and layout from the 2D image of a 3D scene." arXiv preprint arXiv:1712.01812 (2017).
[2] Kanazawa, Angjoo, et al. "Learning Category-Specific Mesh Reconstruction from Image Collections." arXiv preprint arXiv:1803.07549 (2018).
[3] Zhu, Rui, et al. "Object-Centric Photometric Bundle Adjustment with Deep Shape Prior." arXiv preprint arXiv:1711.01470 (2017).
[4] Sun, Deqing, et al. "PWC-Net: CNNs for Optical Flow Using Pyramid, Warping, and Cost Volume." arXiv preprint arXiv:1709.02371 (2017).
[5] Lv, Zhaoyang, et al. "Learning Rigidity in Dynamic Scenes with a Moving Camera for 3D Motion Field Estimation." arXiv preprint arXiv:1804.04259 (2018).
