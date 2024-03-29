# 实体对齐实验指南

## 任务引入

实体对齐旨在发现不同知识图谱中的共指实体，如百度百科的南京大学与Wikipedia中的Nanjing University。实体对齐是知识融合的重要任务。通过实体对齐集成多源知识图谱可以为下游任务提供更加全面的知识表示。

### 问题定义

本实验考虑两个知识图谱之间的实体对齐任务。每个知识图谱可以看做是三元组的集合，每个三元组（头实体、关系、尾实体）描述了一个关系事实，比如（南京大学、位于、南京市）。每个实体或关系使用统一资源标志符（URI）表示。给定源知识图谱和目标知识图谱以及它们之间的部分实体对齐作为训练数据，本实验目标是发现剩余的实体对齐。

### 数据集

本实验基于OpenEA数据集，可以通过[dataset](https://figshare.com/articles/dataset/OpenEA_dataset_v1_1/19258760/3)下载。该数据集抽取自多语言[DBpedia](https://www.dbpedia.org/), 其包含多个实体对齐任务数据集。**但本次实验只考虑EN_DE_15K_V1**，其包含以下（本实验所需的）文件：

* rel_triples_1: 源知识图谱的关系三元组，格式是（头实体 \t 关系 \t 尾实体）;
* rel_triples_2: 目标知识图谱的关系三元组，格式同上;
* 721_5fold/1/train_links: 实体对齐的训练数据，格式是（源实体 \t 等价的目标实体）;
* 721_5fold/1/valid_links: 实体对齐的验证数据，格式同上;
* 721_5fold/1/test_links: 实体对齐的测试数据，格式同上.

## 基于表示学习的实体对齐方法

基于表示学习的实体对齐方法将两个知识图谱映射到一个向量空间，期望共指的实体具有相似的向量表示，即在空间内互为最近邻。

### 基线模型

一般来说，基于表示学习的实体对齐方法有两个模块。一个是embedding learning (EL) 模块，它读取两个知识图谱的关系三元组，使用表示学习技术为实体学习向量表示。另一个是alignment learning (AL) 模块，它读取实体对齐的训练数据，为共指的实体学习相似的表示。推荐大家阅读我们的综述论文[OpenEA](http://www.vldb.org/pvldb/vol13/p2326-sun.pdf)做进一步了解。

作为参考，本实验提供了实体对齐方法[MTransE](https://www.ijcai.org/proceedings/2017/0209.pdf)的实现作为基线。该实现基于[OpenEA](https://github.com/nju-websoft/OpenEA)工具库（欢迎大家star）。mtranse.py中的self._define_embed_graph()定义了EL模块，self._define_mapping_graph()定义了AL模块，两个模块交替优化。本次课程实验的目标是是改进基线方法或重构实现新方法来获得更优的性能，推荐改进EL和AL模块。

### 模型测试

模型训练完成后，给定每一条测试数据（源实体、对应的目标实体），先读取该源实体的向量表示（如果有必要，还需要将其映射到目标知识图谱的表示空间），然后计算其和所有目标实体向量的相似度，并根据相似度对候选目标实体进行降序排列，期望对应的目标实体排在第一位（即最近邻）。

### 评价指标

Hits@k (k=1或10)、mean rank (MR)、mean reciprocal rank (MRR)是常用的实体对齐性能指标。Hits@k计算了对应目标实体排名在top k的测试数据的比例；MR是所有测试数据的对应目标实体的平均排名；MRR是这些排名的倒数的平均值。Hits@k和MRR越高且MR越低，说明模型性能越好。

## 实验要求

### 代码依赖

本实验代码基于[OpenEA](https://github.com/nju-websoft/OpenEA)（欢迎大家star），请首先参其文档安装相应依赖包和OpenEA.

### 实验步骤

- 安装[OpenEA](https://github.com/nju-websoft/OpenEA) (推荐Ubuntu系统)；
- 下载[dataset](https://figshare.com/articles/dataset/OpenEA_dataset_v1_1/19258760/3) (本实验仅要求用其中的EN_DE_15K_V1作为数据集)；
- 参照OpenEA文档运行基线方法MTransE，参数可在run/args/mtranse_args_15K.json中配置；
- 调参、改进基线方法或重构、实现新方法，达到优于基线的实体对齐性能。

基线方法在EN_DE_15K_V1数据上的表现如下：

| Hits@1 | Hits@5 | MRR |
|:--:|:--:|:--:|
| 0.309 | 0.517 | 0.409 |



## 参考文献

1. **MTransE**: [Multilingual Knowledge Graph Embeddings for Cross-lingual Knowledge Alignment](https://www.ijcai.org/proceedings/2017/0209.pdf). IJCAI 2017.

2. **OpenEA**: [A Benchmarking Study of Embedding-based Entity Alignment for Knowledge Graphs](http://www.vldb.org/pvldb/vol13/p2326-sun.pdf). VLDB 2020.
