## GCoD: Graph Convolutional Network Acceleration via Dedicated Algorithm and Accelerator Co-Design

You can click here to Open the [Original Paper](../Papers/HPCA'22_GCoD.pdf),

### 一、文章定位：

### 第一篇从算法与硬件协同优化角度出发加速GCN inference任务的文章。

### 二、Overview：

![image-20230720203135632](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720203135632.png)

### 三、文章的数据集信息：

![image-20230720205711984](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720205711984.png)

### 四、文章的Motivation：（Combination-first范式）

#### GCN inference加速存在一个Dilemma： 

#### 一方面由于GCNs的邻接矩阵的不规则性,为了加速GCN推理，需要降低这种不规则性，然而，这样做不可避免地会降低推理的准确性；另一方面，保持GCNs的不规则性（从而保持其出色的准确性）会导致GCN推理的硬件成本极高，前面的一些加速器中已经证实了这一点。

#### 因此如图1所示，利用分治训练算法将整个图极化为Denser以及Sparser的两部分 => 从而将大规模的不规则性聚类到不同的classes内部，每个Classes内部具有相似的数据访问以及处理模式，并用专用的加速器进行加速。



### 五、文章的核心工作思路：

#### 1、算法层面：目标是让邻接矩阵中度相似的节点聚类到同一个Classes内部，并且集中在对角线附近。同时对非对角线的稀疏点进行剪枝（图中的Patch，这些点对最终结果影响不大）。进而得到Sparser以及Denser两个分支。

![image-20230720210641306](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720210641306.png)

#### 可视化结果：

![image-20230720210717019](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720210717019.png)

#### 2、硬件加速层面：

#### i. 结合Gathered以及Distributed两种聚合方式：

![image-20230720211215112](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720211215112.png)

![image-20230720211246568](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720211246568.png)

#### ii. 采用两种层间流水方式：

![image-20230720211336617](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720211336617.png)

![image-20230720211346209](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720211346209.png)

### 六、硬件整体架构

![image-20230720211454386](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720211454386.png)

#### 简要分析：Denser分支采用n-1个Sub-accelerators，Sparser分支1个Sub-accelerator，通过Weights Forwarding可以提高缓存利用率（Sparser与Denser访问同一列的不同行时由Sparser发出询问通知检查）。最终由Sparser分支汇总两个分支的处理结果。

### 七、总体结果

![image-20230720211952003](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720211952003.png)

#### 总结而言：GCoD相较于HyGCN以及AWB-GCN获得了7.8x以及2.5x的speedup，同时相较于HyGCN仅需要48%的off-chip memory bandwidth。

### 八、文章存在的优化空间

#### 两个分支中间存在latency以及协调的开销。（Workload unbalance）

![image-20230720211813811](C:\Users\Admin\Desktop\Reading_Report\Articles\Reading_Report\HPCA'22_GCoD.assets\image-20230720211813811.png)
