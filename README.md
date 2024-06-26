## YOLOV3算法剪枝和量化学习

### 1.模型剪枝

##### 1.参考文章及项目

```
Learning Efficient Convolutional Networks through Network Slimming
```

```
https://github.com/coldlarry/YOLOv3-complete-pruning
```

```
https://mp.weixin.qq.com/s/nqEXUBXHedNImabZboSk3Q
https://zhuanlan.zhihu.com/p/153496637?utm_id=0
```

##### 2. 主要思路

```
大多数网络结构中都会设置bn层，bn可以加快训练速度，提高网络泛化能力。BN层实现过程如下图，计算样本均值方差，样本数据标准化处理，最后进行平移和缩放处理。注意BN层引入了γ , β 这两个可学习重构参数，让网络可以学习数据样本分布。
```

```
简单来说BN层可以表示为：y = γx + b
缩放因子γ与通道的输出相乘，所以γ的大小直接影响bn层输出的大小。若bn层输出越小，那么通过激活函数后的输出就越接近与0，后续的输出均近似0，因此该channel对整体网络的增益微乎其微。
	
```

$$
L=\sum_{(x, y)} l(f(x, W), y)+\lambda \sum_{\gamma \in \Gamma} g(\gamma)
$$

```
稀疏化训练：
	上面是通用的稀疏化训练损失函数，(x,y)是模型的损失函数，g(r)是稀疏化的惩罚项，采用正则化惩罚后，会使得权重更偏向0或1。与不加稀疏性惩罚相比，缩放因子γ扮演选择的角色γ的值更加接近0，压缩移除这些channel对网络整体影响不大。
```

##### 3. 项目训练部署

数据准备  
[DATA](https://pan.baidu.com/s/1zQHmo4HZINV4MH79ARBRXA?pwd=4bj3)   提取码：4bj3
```
1） 稀疏化训练
2） 常规剪枝
	1. 设置剪切比率percent
	2. 统计模型的bn层，以及需要剪切的bn层
	3. 遍历需要剪枝的bn层，根据bn层的|γ|排序
	4. 设置剪枝阈值：num(bn) * percent
	5. 低于阈值的bn层权重设为0
	6. 重构模型，将bn层权重为0的通道数删去
	7. 计算剪枝后的map
3)	规整剪枝
	故名思义，规整剪枝是要比较工整。规整剪枝就是让网络层的通道数剪枝之后仍然是预先设定的2的N次方这种形式，有利于模型的推理速度提升以及内存占用降低。
4） 极限剪枝
	在常规剪枝的基础上，对shortcut层进行剪枝。
```

##### 4. 实验结果

|   模型   | 参数量(M) | 模型体积(MB) | 压缩率(%) | 耗时(s) |  mAP   |
| :------: | :-------: | :----------: | :-------: | :-----: | :----: |
| baseline |   61.5    |    235.8     |     0     | 0.0511  | 0.7805 |
| 正常剪枝 |   9.32    |     35.7     |    83     | 0.0314  | 0.7734 |
| 规整剪枝 |   14.9    |     57.3     |    75     | 0.0401  | 0.7802 |
| 极限剪枝 |   7.06    |     26.8     |    89     | 0.0392  | 0.7456 |

### 2. 模型量化

##### 1. 参考文献

```
DOREFA-NET: TRAINING LOW BITWIDTH CONVOLUTIONAL NEURAL NETWORKS WITH LOW BITWIDTH
GRADIENTS
```

##### 2. 主要思路

```
STE(Straight-through estimator) 直通估计器
```

##### 3.项目训练部署

```
指定量化激活方式
量化训练
```

##### 4.实验结果

|    模型     | mAP(%) |
| :---------: | :----: |
|    Base     | 82.15  |
| 全16bit量化 | 81.87  |
| 全8bit量化  | 80.64  |

### 3. 权重分享  
(https://pan.baidu.com/s/1tuHRoTh1fPoHuNax3dse-A?pwd=805p)
提取码：805p 

	
