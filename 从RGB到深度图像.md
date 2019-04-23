从RGB到深度图像
#背景与问题简介
在Visual SLAM的许多应用场景中，重建出稠密完整的三维场景有着重要的意义。在增强现实应用中深度图可以提供真实物体和虚拟物体精确的遮挡关系[1]，可以增加虚拟物体与场景的互动方式（碰撞、反弹）；在机器人应用中，稠密场景可以为碰撞规避和路径规划算法提供更精确的地图信息，同时也可以作为机械手臂Motion Planning中work space或障碍物。
既然深度图像和稠密场景地图有如此多的作用，为什么不直接用Kinect或者RealSense这样的深度摄像头获取深度呢？这个方案在很多情况下时可行的。但深度摄像头也有其相应的缺点：
1. 有限的作用距离。深度摄像头通常分为近距和远距两种，其有效距离分别为0.2～2m，0.5～6m。最新的Kinect据说最远可达近10米左右，但毕竟还是有限的。在仓库、工业区以及户外等区域，这个距离范围有时是不够的。
2. 材料敏感。深度摄像头并不是万能的。目前的深度摄像头多以结构光或ToF为主。当红外线射中金属或玻璃这种高反光材料后，得到的反射属性往往是难以预知的。因此深度图中常常会因此有不少洞和噪声。
3. 功耗巨大。相比RGB摄像头，深度摄像头通常功耗要大上许多。对于对功耗比较敏感的设备（手机，机器人）在目前的硬件条件下，装配深度摄像头并不是明智的选择。
因此有许多机器视觉科研人员致力于用RGB图像恢复深度图像，在一定程度上解决深度摄像头的一些缺点，降低深度图的获取成本。本文中将为大家介绍几篇近年来由RGB恢复深度图像方向比较重要的论文。

#监督式RGB恢复深度方法

#引入稀疏深度的监督式RGB恢复深度方法

#非监督RGB恢复深度方法


#参考文献
[1] Du, Chao, et al. "Edge snapping-based depth enhancement for dynamic occlusion handling in augmented reality." 2016 IEEE International Symposium on Mixed and Augmented Reality (ISMAR). IEEE, 2016.
[2] Zamir, Amir R., et al. "Taskonomy: Disentangling task transfer learning." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2018.
[3] Eigen, David, Christian Puhrsch, and Rob Fergus. "Depth map prediction from a single image using a multi-scale deep network." Advances in neural information processing systems. 2014.
[4] Eigen, David, and Rob Fergus. "Predicting depth, surface normals and semantic labels with a common multi-scale convolutional architecture." Proceedings of the IEEE international conference on computer vision. 2015.
[5] Laina, Iro, et al. "Deeper depth prediction with fully convolutional residual networks." 2016 Fourth international conference on 3D vision (3DV). IEEE, 2016.
[6] Mal, Fangchang, and Sertac Karaman. "Sparse-to-dense: Depth prediction from sparse depth samples and a single image." 2018 IEEE International Conference on Robotics and Automation (ICRA). IEEE, 2018.
[7] Zhang, Yinda, and Thomas Funkhouser. "Deep depth completion of a single rgb-d image." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2018.
[8] Jaritz, Maximilian, et al. "Sparse and dense data with cnns: Depth completion and semantic segmentation." 2018 International Conference on 3D Vision (3DV). IEEE, 2018.
[9] Chen, Zhao, et al. "Estimating depth from RGB and sparse sensing." Proceedings of the European Conference on Computer Vision (ECCV). 2018.
[10] Zhou, Tinghui, et al. "Unsupervised learning of depth and ego-motion from video." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2017.
[11] Vijayanarasimhan, Sudheendra, et al. "Sfm-net: Learning of structure and motion from video." arXiv preprint arXiv:1704.07804 (2017).
[12] Mahjourian, Reza, Martin Wicke, and Anelia Angelova. "Unsupervised learning of depth and ego-motion from monocular video using 3d geometric constraints." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2018.
[13] Yin, Zhichao, and Jianping Shi. "Geonet: Unsupervised learning of dense depth, optical flow and camera pose." Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2018.
[14] Tang, Chengzhou, and Ping Tan. "Ba-net: Dense bundle adjustment network." arXiv preprint arXiv:1806.04807 (2018).