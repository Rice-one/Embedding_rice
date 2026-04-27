# EMBEDDING-IS-ALMOST-ALL-YOU-NEED

项目功能概述
本文件主要实现了基于 demo_coding_vs_intergenomic_seqs 基因组数据集的分类任务，探索了**“大语言模型嵌入 + 传统生物学特征”**的混合检索与分类方法。核心功能模块包括：

数据集获取与预处理： 使用 genomic-benchmarks 库自动下载并加载编码区与基因组间区序列数据集，将数据划分为训练集（75,000 条）和测试集（25,000 条），并转换为 Pandas DataFrame 以便于后续的数据清洗与操作。

HyenaDNA 序列嵌入提取： 从**本地目录**加载预训练的 Transformer 模型（HyenaDNA）和分词器。通过前向传播提取 token 级别的隐藏层状态，并使用平均池化（Mean Pooling）将变长的 DNA 序列转化为固定维度的特征向量（Embeddings）。

基础 KNN 检索分类： 利用 FAISS 库构建基于 L2 距离的向量索引，通过 K-近邻（KNN，K=5）检索和多数投票机制（Majority Voting）建立基础的序列分类 Baseline。

多维度生物学特征提取： 实现了一套模块化的传统 DNA 特征提取管道，涵盖了序列的重要生物物理指标，包括 GC 含量（GC Content）、Z-曲线参数（zCurve）、AT/GC 比率（AT/GC Ratio）、累积偏斜度（Cumulative Skew）以及伪 K-核苷酸组成（pseudoKNC，K=3）。

特征融合与降维： 利用 MinMaxScaler 对人工提取的生物学特征进行归一化处理。随后将模型生成的稠密嵌入向量与传统生物学特征进行加权拼接（例如嵌入权重设定为 0.8，传统特征设定为 0.2），构建更全面的序列表示。

动态检索与加权投票策略： 设计了一种高级的 FAISS 检索系统。该系统基于内积（等效于归一化向量的余弦相似度），通过设定动态相似度阈值来决定每个查询是否需要扩展检索的邻居数量（Dynamic K），并基于距离计算指数权重，实现软投票（Soft Voting）分类。

数据结果持久化： 实验运行结束后，将包含原始序列、真实标签以及计算所得 Embedding 的训练集和测试集数据导出并保存为本地的 CSV 文件，为后续实验提供直接的数据支持。
