# 2017-Kirkpatrick-EWC

> **来源**：../2017-Kirkpatrick-EWC.pdf ｜ **转换**：markitdown（文本提取，标题层级待恢复） ｜ 英文原文 markdown ｜ **中文译版**：全文翻译，术语采用项目惯用译法，文末附术语对照表

# 克服神经网络中的灾难性遗忘
Overcoming catastrophic forgetting in neural networks

James Kirkpatrickᵃ, Razvan Pascanuᵃ, Neil Rabinowitzᵃ, Joel Venessᵃ, Guillaume Desjardinsᵃ, Andrei A. Rusuᵃ, Kieran Milanᵃ, John Quanᵃ, Tiago Ramalhoᵃ, Agnieszka Grabska-Barwinskaᵃ, Demis Hassabisᵃ, Claudia Clopathᵇ, Dharshan Kumaranᵃ, and Raia Hadsellᵃ

ᵃ DeepMind, London, N1C 4AG, United Kingdom

ᵇ Bioengineering department, Imperial College London, SW7 2AZ, London, United Kingdom

## 摘要

以序列方式学习多个任务的能力对人工智能的发展至关重要。总体而言，神经网络并不具备这种能力，且人们长期以来普遍认为，灾难性遗忘（catastrophic forgetting）是联结主义模型（connectionist models）不可避免的特性。我们证明，这一限制是可以克服的：可以训练出即使长时间未再经历某些任务、仍能保持对这些任务的专业能力的网络。我们的方法通过选择性地减慢对旧任务重要权重的学习来记住旧任务。我们通过解决一组基于 MNIST 手写数字数据集的分类任务，以及按序列学习多款 Atari 2600 游戏，证明了该方法的可扩展性与有效性。

## 1 引言

实现通用人工智能（artificial general intelligence）要求智能体能够学习并记住许多不同的任务 [Legg and Hutter, 2007]。这在现实世界环境中尤其困难：任务序列可能没有被显式标注，任务可能不可预测地切换，且单个任务可能在长时间间隔内不再出现。因此，关键在于智能体必须展现出持续学习（continual learning）的能力，即在学习连续到来的任务的同时，不遗忘如何执行先前训练过的任务。

由于在吸收与当前任务（例如任务 B）相关的信息时，先前学习任务（例如任务 A）的知识往往会被突然丢失，持续学习对人工神经网络构成了特殊的挑战。这一现象被称为灾难性遗忘 [French, 1999; McCloskey and Cohen, 1989; McClelland et al., 1995; Ratcliff, 1990]，它特指网络按序列在多个任务上训练时发生的现象，因为网络中对任务 A 重要的权重会被改变以满足任务 B 的目标。尽管机器学习特别是深度神经网络的近期进展在多个领域带来了显著的性能提升（例如 [Krizhevsky et al., 2012; LeCun et al., 2015]），但在实现持续学习方面进展甚微。当前的方法通常确保所有任务的数据在训练期间同时可用。通过在学习过程中交错（interleaving）多个任务的数据，遗忘不会发生，因为网络权重可以针对所有任务上的性能被联合优化。在这种机制下——通常称为多任务学习（multitask learning）范式——深度学习技术已被用于训练能成功玩多款 Atari 游戏的单个智能体 [Rusu et al., 2015; Parisotto et al., 2015]。如果任务按序列呈现，则只有当数据被情景记忆（episodic memory）系统记录下来并在训练期间回放给网络时，才能使用多任务学习。这一方法（通常称为系统级巩固 [McClelland et al., 1995]）对学习大量任务并不现实，因为在我们的设定下，它要求存储和回放的记忆量与任务数量成正比。因此，缺乏支持持续学习的算法仍然是通用人工智能发展的一大关键障碍。

与人工神经网络形成鲜明对比的是，人类和其它动物似乎能够以持续的方式学习 [Cichon and Gan, 2015]。近期证据表明，哺乳动物大脑或许通过在新皮层回路（neocortical circuits）中保护先前获得的知识来避免灾难性遗忘 [Cichon and Gan, 2015; Hayashi-Takagi et al., 2015; Yang et al., 2009, 2014]。当小鼠习得一项新技能时，一部分兴奋性突触（excitatory synapses）会被强化；这表现为神经元单个树突棘（dendritic spines）体积的增加 [Yang et al., 2009]。关键的是，即使随后学习了其它任务，这些增大的树突棘仍会持续存在，这解释了数月后任务表现仍得以保持的现象 [Yang et al., 2009]。当这些树突棘被选择性「擦除」时，相应的技能便被遗忘 [Hayashi-Takagi et al., 2015; Cichon and Gan, 2015]。这提供了因果证据：支持保护这些强化突触的神经机制对任务表现的保持至关重要。总之，这些实验发现——连同神经生物学模型 [Fusi et al., 2005; Benna and Fusi, 2016]——表明哺乳动物新皮层中的持续学习依赖于一种任务特异性的突触巩固（synaptic consolidation）过程：关于如何执行先前习得任务的知识被持久地编码在一部分突触中，这些突触的可塑性被降低，从而在长时间尺度上保持稳定。

在本工作中，我们证明任务特异性的突触巩固为人工智能的持续学习问题提供了一种新颖的解决方案。我们为人工神经网络开发了一种与突触巩固相类似的算法，称之为弹性权重巩固（elastic weight consolidation，简称 EWC）。该算法依据某些权重对先前见过任务的重要性来减慢其上的学习。我们展示了 EWC 如何用于监督学习和强化学习问题，按序列训练多个任务而不遗忘旧任务，这与以往的深度学习技术形成鲜明对比。

## 2 弹性权重巩固

在大脑中，突触巩固通过降低对先前学习任务至关重要的突触的可塑性（plasticity）来实现持续学习。我们实现了一种在人工神经网络中执行类似操作的算法：约束重要参数，使其保持在旧值附近。本节中，我们解释为什么我们预期会在旧任务解的邻域内找到新任务的解、如何实现该约束，以及最后如何确定哪些参数是重要的。

深度神经网络由多层线性投影及其后接的逐元素非线性组成。学习一个任务就是调整线性投影的权重与偏置集合 θ，以优化性能。θ 的许多配置会产生相同的性能 [Nielsen, 1989; Sussmann, 1992]；这与 EWC 相关：过参数化（over-parameterization）使得任务 $\mathbf { B }$ 存在解 $\theta _ { B } ^ { \ast }$ 且该解很可能接近于此前为任务 $\mathbf { A }$ 找到的解 $\theta _ { A } ^ { * }$。因此，在学习任务 B 时，EWC 通过约束参数停留在以 $\theta _ { A } ^ { * }$ 为中心的任务 A 低误差区域内，来保护任务 A 的性能，如图 1 示意所示。该约束以二次惩罚（quadratic penalty）的形式实现，因此可以想象为一根把参数锚定到旧解的弹簧，这正是「弹性」一词的由来。重要的是，这根弹簧的刚度不应对所有参数都相同；相反，对任务 A 期间性能影响最大的那些参数，其刚度应更大。

为了论证这一约束选择的合理性并定义哪些权重对任务最重要，从概率视角考察神经网络训练是有益的。从这一视角看，优化参数等价于在给定某数据 $\mathcal { D }$ 的情况下寻找参数最可能的取值。我们可以利用贝叶斯法则，由参数的先验概率 $p ( \theta )$ 和数据的概率 $p ( \mathcal { D } | \theta )$ 计算这一条件概率 $p ( \theta | \mathcal { D } )$：

$$
\log p ( \theta | \mathcal { D } ) = \log p ( \mathcal { D } | \theta ) + \log p ( \theta ) - \log p ( \mathcal { D } )\tag{1}
$$

注意，给定参数时数据的对数概率 $\log p ( \mathcal { D } | \theta )$ 就是当前问题损失函数的负值 $- \mathcal { L } ( \boldsymbol { \theta } )$。假设数据被划分为两个独立部分，一部分定义任务 $\mathbf { A } \left( \mathcal { D } _ { A } \right)$，另一部分定义任务 $\mathbf { B } ( \mathcal { D } _ { B } )$。于是，我们可以把公式 1 重排为：

$$
\begin{array} { r } { \log p ( \theta | \mathcal { D } ) = \log p ( \mathcal { D } _ { B } | \theta ) + \log p ( \theta | \mathcal { D } _ { A } ) - \log p ( \mathcal { D } _ { B } ) } \end{array}\tag{2}
$$

注意，左侧仍然描述给定整个数据集时参数的后验概率，而右侧只依赖于任务 B 的损失函数 $\log p ( \mathcal { D } _ { B } | \theta )$

[图：图 1 EWC 示意图]
图 1：弹性权重巩固（EWC）确保在训练任务 B 的同时任务 A 仍被记住。训练轨迹在示意性的参数空间中展示，其中灰色区域表示在任务 A 上取得良好性能的参数区域，米色区域表示任务 B 上的良好性能区域。学完第一个任务后，参数位于 $\theta _ { A } ^ { * }$。如果仅依据任务 B 进行梯度下降（蓝色箭头），我们会最小化任务 B 的损失，但会破坏已为任务 A 学到的内容。另一方面，如果用相同的系数约束每个权重（绿色箭头），所施加的限制就过于严格，我们只能记住任务 A，代价是学不会任务 B。相反，EWC 通过显式计算各权重对任务 A 的重要程度，在不给任务 A 带来显著损失的情况下找到任务 B 的解（红色箭头）。

因此，关于任务 A 的全部信息必定已被吸收进后验分布 $p ( \theta | \mathcal { D } _ { A } )$ 之中。该后验概率必然包含关于哪些参数对任务 A 重要的信息，因而是实现 EWC 的关键。真实的后验概率难以计算（intractable），因此，沿用 MacKay 的拉普拉斯近似（Laplace approximation）工作 [MacKay, 1992]，我们将后验近似为一个高斯分布：其均值由参数 $\theta _ { A } ^ { * }$ 给出，其对角精度（diagonal precision）由 Fisher 信息矩阵 F 的对角元给出。F 有三个关键性质 [Pascanu and Bengio, 2013]：(a) 它等价于极小值附近损失的二阶导数；(b) 它仅由一阶导数即可算出，因而即使对大模型也易于计算；(c) 它保证是半正定的。注意，该方法类似于期望传播（expectation propagation），其中每个子任务被视为后验的一个因子 [Eskin et al., 2004]。在这一近似下，EWC 所最小化的函数 $\mathcal { L }$ 为：

$$
\mathcal { L } ( \theta ) = \mathcal { L } _ { B } ( \theta ) + \sum _ { i } \frac { \lambda } { 2 } F _ { i } ( \theta _ { i } - \theta _ { A , i } ^ { * } ) ^ { 2 }\tag{3}
$$

其中 $\mathcal { L } _ { B } ( \boldsymbol { \theta } )$ 是仅针对任务 B 的损失，λ 设定旧任务相对于新任务的重要性，i 标记每个参数。

当转向第三个任务（任务 C）时，EWC 会尽量让网络参数同时接近任务 A 与任务 B 已学得的参数。这既可以用两个相互独立的惩罚来实现，也可以合并为一个惩罚，只需注意到两个二次惩罚之和本身仍是二次惩罚。

## 2.1 EWC 在监督学习场景下实现持续学习

我们首先研究这一问题：弹性权重巩固能否让深度神经网络在不发生灾难性遗忘的情况下学习一组复杂任务。具体而言，我们用一个全连接多层神经网络按序列训练多个监督学习任务。在每个任务内部，我们以传统方式训练神经网络，即打乱数据并按小批次（minibatch）处理。但对每个任务完成固定量的训练后，我们不再允许在该任务的数据集上进一步训练。

我们从 MNIST [LeCun et al., 1998] 手写数字分类问题出发构建任务集合，采用的方案此前已用于持续学习文献 [Srivastava et al., 2013; Goodfellow et al., 2014]。对每个任务，我们生成一个固定且随机的置换（permutation），按此置换打乱所有图像的输入像素。因此每个任务与原始 MNIST 问题难度相同，但各自需要不同的解。所用设置的详细描述见附录 4.1。

如图 2A 所示，用朴素随机梯度下降（stochastic gradient descent, SGD）在这一任务序列上训练会引发灾难性遗忘。蓝色曲线展示了在两个不同任务测试集上的表现。当训练机制从第一个任务（A）切换到第二个任务（B）时，任务 B 的表现迅速下降，而任务 A 的表现急剧攀升。任务 A 的遗忘随着训练时间的增加和后续任务的加入而进一步加剧。用对每个权重固定的二次约束来正则化网络（绿色曲线，L2 正则化）也无法解决这一问题：此时任务 A 的性能退化要轻得多，但任务 B 无法被妥善学习，因为该约束同等地保护所有权重，留给学习 B 的冗余容量很少。然而，当我们使用 EWC、即考虑每个权重对任务 A 的重要性时，网络可以在不遗忘任务 A 的情况下学好任务 B（红色曲线）。这正如图 1 以图示方式描述的预期行为。

[图：图 2A 训练曲线]

[图：图 2B 平均表现曲线]

[图：图 2C Fisher 信息矩阵相似度曲线]
图 2：置换 MNIST 任务上的结果。A：使用 EWC（红色）、L2 正则化（绿色）和朴素 SGD（蓝色）在三个随机置换 A、B 和 C 上的训练曲线。注意只有 EWC 能在保持学习新任务能力的同时，在旧任务上维持高性能。B：使用 EWC（红色）或带 dropout 正则化的 SGD（蓝色）在所有任务上的平均表现。虚线表示仅在单个任务上的表现。C：两种不同置换程度下 Fisher 信息矩阵的相似度随网络深度的变化。灰色为仅置换图像中央 8x8 像素小方块的情形，黑色为置换 26x26 像素大方块的情形。注意任务之间差异越大，早期层中 Fisher 信息矩阵的重叠越小。

以往解决深度神经网络持续学习问题的尝试，依赖于精心选择网络超参数并结合其它标准正则化方法，来缓解灾难性遗忘。然而在该任务上，它们最多在两个随机置换上取得了尚可的结果 [Srivastava et al., 2013; Goodfellow et al., 2014]。我们采用与 [Goodfellow et al., 2014] 类似的交叉验证超参数搜索，将传统 dropout 正则化与 EWC 进行了比较。我们发现，仅带 dropout 正则化的随机梯度下降效果有限，且无法扩展到更多任务（图 2B）。相比之下，EWC 允许按序列学习大量任务，且错误率仅温和增长。

鉴于 EWC 使网络能在固定容量的网络中有效容纳更多功能，我们可能会问：它为每个任务分配的是完全独立的网络部分，还是通过共享表征更高效地利用容量？为评估这一点，我们通过测量成对任务各自 Fisher 信息矩阵之间的重叠，来确定每个任务是否依赖相同的权重集合（见附录 4.3）。重叠小意味着两个任务依赖不同的权重集合（即 EWC 为不同任务划分网络的权重）；重叠大则表明权重被两个任务共同使用（即 EWC 实现了表征共享）。图 2C 展示了重叠随深度的变化。作为一个简单对照，当网络在两个彼此非常相似的任务上训练时（两个仅置换少量像素的 MNIST 版本），在整个网络中两个任务都依赖相似的权重集合（灰色曲线）。而当两个任务彼此差异更大时，网络开始为这两个任务分配独立的容量（即权重）（黑色曲线）。尽管如此，即使对于大置换，网络靠近输出的那些层确实仍被两个任务复用。这反映出如下事实：置换使输入域差异很大，但输出域（即类别标签）是共享的。

## 2.2 EWC 在强化学习场景下实现持续学习

接下来，我们检验弹性权重巩固能否在要求严苛得多的强化学习（reinforcement learning, RL）领域支持持续学习。在 RL 中，智能体与环境动态交互，以发展出最大化未来累积奖励的策略。我们探究 Deep Q Networks（DQN）——一种在此类高难度 RL 环境中取得显著成功的架构 [Mnih et al., 2015]——能否与 EWC 结合，在经典的 Atari 2600 任务集 [Bellemare et al., 2013] 上成功支持持续学习。具体而言，每个实验由若干游戏组成，这些游戏从 DQN 能达到人类水平或以上的游戏中随机选取十款。训练时，智能体在每款游戏上经历长时间的体验。游戏的呈现顺序是随机的，并允许多次回到同一款游戏。我们还会定期测试智能体在这十款游戏上的得分，期间不允许智能体在它们上面训练（图 3A）。

值得注意的是，以往面向持续学习的强化学习方法，要么依赖为网络增加容量 [Ring, 1998; Rusu et al., 2016]，要么在相互独立的网络上分别学习每个任务，再用它们训练一个能玩所有游戏的单一网络 [Rusu et al., 2015; Parisotto et al., 2015]。相比之下，本文提出的 EWC 方法使用具有固定资源（即网络容量）的单一网络，且计算开销极小。

除使用 EWC 保护先前获得的知识外，我们还借助 RL 领域来处理成功的持续学习系统所需的更广泛的一组要求：特别地，需要更高层的机制来推断当前正在执行哪个任务、在遇到新任务时将其检测并纳入，以及支持任务间快速而灵活的切换 [Collins and Frank, 2013]。在灵长类大脑中，前额叶皮层（prefrontal cortex）被广泛认为通过维持任务情境的神经表征来支持这些能力，这些表征对低层区域的感官处理、工作记忆（working memory）和动作选择施加自上而下的门控影响 [O'Reilly and Frank, 2006; Mante et al., 2013; Miller and Cohen, 2001; Doya et al., 2002]。

受这些证据启发，我们使用了与 [van Hasselt et al., 2016] 所述非常相似的智能体，仅有少许不同：(a) 参数更多的网络，(b) 更小的转移表，(c) 每层的任务特定偏置与增益，(d) Atari 的完整动作集，(e) 任务识别模型，以及 (e)（译注：原文印误，按序应为 (f)）EWC 惩罚。超参数的完整细节见附录 app:atari（译注：原文印作 app:atari，为未解析的排版标签，实指附录 4.2）。这里我们简要描述对智能体的两项最重要修改：任务识别模块与 EWC 惩罚的实现。

我们将任务情境（task context）视为一个隐马尔可夫模型（Hidden Markov Model）的潜变量。因此，每个任务都关联到一个潜在的观测生成模型。我们方法的主要独特之处在于：如果新生成模型对近期数据的解释优于现有模型池，我们允许将其加入，这通过一个受 forget me not 过程 [Kieran et al., 2016] 启发的训练程序实现（见附录 4.2）。

为了应用 EWC，我们在每次任务切换时计算 Fisher 信息矩阵。对每个任务，加入一个惩罚项：锚点由参数当前值给出，权重由 Fisher 信息矩阵乘以一个经超参数搜索优化的缩放因子 λ 给出。我们只对经历过至少 2000 万帧的游戏添加 EWC 惩罚。

我们还允许 DQN 智能体为每个推断出的任务维护独立的短期记忆缓冲区：这使得每个任务的动作价值可以通过经验回放（experience replay）机制以离策略（off-policy）方式学习 [Mnih et al., 2015]。如此一来，整个系统在两个时间尺度上拥有记忆：在短时间尺度上，经验回放机制使 DQN 的学习建立在交错且不相关的经验之上 [Mnih et al., 2015]；在较长时间尺度上，跨任务的技能知识通过 EWC 得到巩固。最后，我们允许少量网络参数为游戏专属而非跨游戏共享。具体而言，我们允许网络的每一层拥有针对每款游戏特定的偏置和逐元素乘性增益。

在图 3 中，我们比较了使用 EWC 的智能体（红色）与不使用 EWC 的智能体（蓝色）在多组十款游戏上的表现。我们将表现度量为十款游戏上人类归一化得分（human-normalized score）的总和。我们在随机种子以及十款游戏的具体选择上取平均（见附录 4.2）。我们还将每款游戏的人类归一化得分截断为 1。因此，我们的表现度量是一个最大值为 10 的数字（所有游戏均至少达到人类水平），0 表示智能体与随机智能体相当。若如 [Mnih et al., 2015] 那样依赖朴素梯度下降方法，智能体永远学不会玩多于一款游戏，且遗忘旧游戏造成的损害使得总的人类归一化得分始终低于 1。而使用 EWC 后，智能体确实学会了玩多款游戏。作为对照，我们还考察了显式为智能体提供真实任务标签（图 3B，棕色）而非依赖 FMN 算法学得的任务识别（红色）时对智能体的收益。此处提升幅度有限。

[图：图 3A 游戏训练日程]

[图：图 3B 总分曲线]

[图：图 3C 权重扰动敏感性曲线]
图 3：Atari 任务上的结果。A：游戏日程。黑色条表示每款游戏的顺序训练时段（片段）。每个训练片段结束后，测量在所有游戏上的表现。只有当智能体在某款游戏上经历过 2000 万帧后，EWC 约束才会被激活以保护智能体在该游戏上的表现。B：各方法在所有游戏上的总分。红色曲线表示用 Forget Me Not 算法推断任务标签的网络；棕色曲线是给定任务标签的网络。当受 EWC 保护的游戏再次开始被游玩时，EWC 与 SGD 曲线开始分化。C：在 Breakout 上训练的单游戏 DQN 对其权重所加噪声的敏感性。Breakout 上的表现作为权重扰动幅度（标准差）的函数展示。权重扰动取自零均值高斯分布，其协方差或为均匀（黑色；即同等对待所有权重），或为逆 Fisher（$( F + \lambda I ) ^ { - 1 }$；蓝色；即模拟 EWC 所允许的权重变化），或为 Fisher 零空间（nullspace）内的均匀扰动（橙色；即针对 Fisher 估计网络输出对其完全不变的权重）。为评估得分，我们让智能体运行十个完整游戏回合，每个时间步都重新抽取一个新的随机权重扰动。

用 EWC 增强 DQN 智能体虽然使其能够按序列学习多款游戏而不遭受灾难性遗忘，但未能达到训练十个独立 DQN 所能获得的分数（见附录 4.2 图 1）。一个可能的原因是，我们对每款游戏权重的巩固基于参数不确定性的一个可计算的近似——Fisher 信息。因此我们试图以实证方式检验我们估计的质量。为此，我们在单款游戏上训练智能体，并测量扰动网络参数如何影响智能体的得分。无论智能体在哪款游戏上训练，我们观察到的模式都相同，如图 3C 所示。首先，相比于均匀扰动（黑色），智能体对由 Fisher 信息对角逆所塑形的参数扰动（蓝色）始终更鲁棒。这验证了 Fisher 的对角元是对某参数重要程度的良好估计。在我们的近似内，在零空间中扰动应当对性能完全没有影响。然而实证上，我们观察到在该空间中扰动（橙色）与在逆 Fisher 空间中扰动效果相同。这表明我们对某些参数不重要的判断过于自信：因此当前实现的主要局限很可能在于低估了参数不确定性。

## 3 讨论

我们提出了一种新颖的算法——弹性权重巩固，以应对持续学习给神经网络带来的重大问题。EWC 使先前任务的知识在新学习过程中得到保护，从而避免旧能力的灾难性遗忘。它通过选择性地降低权重的可塑性来实现这一点，因而与突触巩固的神经生物学模型有相通之处。我们将 EWC 实现为一种软性二次约束：每个权重被拉回其旧值的程度，与其对先前学习任务表现的重要性成正比。在任务共享结构的范围内，用 EWC 训练的网络会复用网络中的共享组件。我们进一步表明，EWC 能与深度神经网络有效结合，在具有挑战性的强化学习场景（如 Atari 2600 游戏）中支持持续学习。

EWC 算法可以立足于贝叶斯学习方法。形式上，当有新任务要学习时，网络参数受一个先验的调节，该先验即给定先前任务数据时参数的后验分布。这使得被先前任务弱约束的参数能以较快的学习率学习，而关键参数则以较慢的学习率学习。

此前已有工作 [French and Chater, 2002; Eaton and Ruvolo, 2013] 使用二次惩罚来近似数据集的旧有部分，但这些应用仅限于小模型。具体而言，[French and Chater, 2002] 使用随机输入计算能量曲面的二次近似。他们的方法很慢，因为需要在每个样本处重新计算曲率。[Eaton and Ruvolo, 2013] 描述的 ELLA 算法需要计算并求逆维度等于被优化参数数量的矩阵，因此主要应用于线性回归和逻辑回归。相比之下，EWC 的运行时间在参数数量和训练样本数量上都是线性的。我们只有通过若干简化才能达到如此低的计算复杂度，最显著的是：用因子化的高斯分布来近似任务上参数的后验分布（即权重不确定性），以及通过 Fisher 信息矩阵的对角元、用参数的点估计来计算其方差。尽管计算成本低且实证上成功——即便在具有挑战性的 RL 领域——我们对后验方差使用点估计（如拉普拉斯近似中那样）确实构成一个显著的弱点（见图 4C，译注：原文印作图 4C，按上下文应指图 3C）。我们的初步探索提示，可以通过贝叶斯神经网络 [Blundell et al., 2015] 改进这一局部估计。

虽然本文的主要焦点是基于神经生物学观察构建算法，但反过来考察该算法的成功能否回馈于我们对大脑的理解，也颇具启发意义。特别地，我们看到 EWC 与两种突触可塑性计算理论之间存在相当多的平行之处。

在这方面，我们在此提出的视角与近期的一个提议相一致：每个突触不仅存储其当前权重，还存储对该权重不确定性的隐式表征 [Aitchison and Latham, 2015]。这一想法基于如下观察：突触后电位在幅度上高度可变（提示计算过程中从权重后验中采样），而那些更具可变性的突触更易于被强化或弱化（提示权重后验的更新）。虽然我们在此并未探究从后验采样的计算益处，但我们的工作与「权重不确定性应当为学习率提供信息」这一观念相一致。我们更进一步强调：巩固高精度权重使得长时间尺度上的持续学习成为可能。使用 EWC 时，每个突触需存储三个值：权重本身、其方差与其均值。有趣的是，大脑中的突触也携带多于一条信息。例如，短时程可塑性的状态可以携带方差信息 [Aitchison and Latham, 2015; Pfister et al., 2010]。可塑性早期阶段的权重 [Clopath et al., 2008] 可以编码当前突触强度，而与可塑性晚期阶段（即巩固阶段）相关联的权重则可以编码平均权重。

接续学习多个任务而不遗忘的能力，是生物智能与人工智能的核心组成部分。在本工作中，我们表明一种支持持续学习的算法——其灵感来自突触巩固的神经生物学模型——能够与深度神经网络结合，在一系列具有挑战性的领域取得成功表现。由此，我们证明了当前关于突触巩固的神经生物学理论确实能够扩展至大规模学习系统。这提供了初步证据：这些原理可能是大脑中学习与记忆的基本要素。

致谢。我们感谢 P. Dayan、D. Wierstra、S. Mohamed、Yee Whye Teh 和 K. Kavukcuoglu。

## References

Laurence Aitchison and Peter E Latham. Synaptic sampling: A connection between psp variability and uncertainty explains neurophysiological observations.arXiv preprint arXiv:1505.04544,2015.

Marc G Bellemare, Yavar Naddaf, Joel Veness,and Michael Bowling. The arcade learning environment: An evaluation platform for general agents. Journal of Artificial Intelligence Research, 47: 253-279,2013.

Marcus K Benna and Stefano Fusi. Computational principles of synaptic memory consolidation. Nature neuroscience,2016.

Charles Blundell, Julien Cornebise, Koray Kavukcuoglu,and Daan Wierstra. Weight uncertainty in neural network. In Proceedings of The 32nd International Conference on Machine Learning, pages 1613-1622,2015.

Joseph Cichon and Wen-Biao Gan. Branch-specific dendritic ca2+ spikes cause persistent synaptic plasticity. Nature, 520(7546):180-185,2015.

Claudia Clopath,Lorric Ziegler,Eleni Vasilaki,Lars Büsing,and Wulfram Gerstner. Tag-triggerconsolidation: a model of early and late long-term-potentiation and depression. PLoS Comput Biol, 4(12):e1000248,2008.

Anne GE Collins and Michael JFrank. Cognitive control over learning: creating, clustering,and generalizing task-set structure.Psychological review,12O(1):190, 2013.

DC Dowson and BV Landau. The fréchet distance between multivariate normal distributions. Journal of multivariate analysis,12(3):450-455,1982.

Kenji Doya, Kazuyuki Samejima, Ken-ichi Katagiri,and Mitsuo Kawato. Multiple model-based reinforcement learning. Neural computation,14(6):1347-1369,2002.

Eric Eaton and Paul L Ruvolo. Ella: An efcient lifelong learning algorithm. In International Conference on Machine Learning, pages 507-515,2013.

Eleazar Eskin, Alex J. Smola, and S.v.n. Vishwanathan. Laplace propagation. In Advances in Neural Information Processing Systems 16, pages 441-448. MIT Press, 2004. URL http: //papers. nips.cc/paper/2444-laplace-propagation.pdf.

Robert MFrench. Catastrophic forgetting in connectionist networks. Trends in cognitive sciences, 3 (4):128-135, 1999.

Robert MFrench and Nick Chater. Using noise to compute error surfaces in connectionist networks: a novel means of reducing catastrophic forgetting. Neural computation,14(7):1755-1769, 2002.

Stefano Fusi, Patrick J Drew,and LF Abbott. Cascade models of synaptically stored memories. Neuron,45(4):599-611,2005.

Ian JGoodfellow,Mehdi Mirza,Da Xiao,Aaron Courville,and Yoshua Bengio.An empirical investigation of catastrophic forgeting in gradient-based neural networks. Int'l Conf. on Learning Representations (ICLR),2014.

Akiko Hayashi-Takagi, Sho Yagishita, Mayumi Nakamura, Fukutoshi Shirai, Yi I. Wu, Amanda L. Loshbaugh, Brian Kuhlman, Klaus M. Hahn, and Haruo Kasai. Labelling and optical erasure of synaptic memory traces in the motor cortex. Nature, 525(7569):333-338,09 2015. URL http://dx.doi.org/10.1038/nature15257.

Milan Kieran,Joel Veness,Michael Bowling, James Kirkpatrick,Anna Koop,and Demis Hassabis. The forget me not process. In Advances in Neural Information Processing Systems 26, page accepted for publication,2016.

Alex Krizhevsky, Ilya Sutskever, and Geoffrey E Hinton. Imagenet classification with deep convolutional neural networks. In NIPS, pages 1097-1105,2012.

Yann LeCun, Corinna Cortes,and Christopher JC Burges. The mnist database of handwriten digits, 1998.

Yann LeCun, Yoshua Bengio, and Geoffrey Hinton. Deep learning. Nature,521(7553):436-444, 2015.

Shane Legg and Marcus Hutter. Universal intelligence: A definition of machine intelligence. Minds and Machines,17(4):391-444,2007.

David JC MacKay. A practical bayesian framework for backpropagation networks. Neural computation,4(3):448-472, 1992.

Valerio Mante,David Sussillo, Krishna V Shenoy,and William T Newsome. Context-dependent computation by recurrent dynamics in prefrontal cortex. Nature, 503(7474):78-84,2013.

James L McClelland, Bruce L McNaughton, and Randall C O'Reilly. Why there are complementary learning systems in the hippocampus and neocortex: insights from the successes and failures of connectionist models of learning and memory. Psychological review,102(3):419, 1995.

Michael McCloskey and Neal J Cohen. Catastrophic interference in connectionist networks: The sequential learning problem. The psychology of learning and motivation,24(109-165):92,1989.

Earl K Miler and Jonathan D Cohen. An integrative theory of prefrontal cortex function. Annual review of neuroscience,24(1):167-202,2001.

Volodymyr Mnih, Koray Kavukcuoglu,David Silver, Andrei A Rusu, Joel Veness, Marc G Bellmare, Alex Graves,Martin Riedmiler, Andreas K Fidjeland, Georg Ostrovski, et al. Human-level control through deep reinforcement learning. Nature, 518(7540):529-533, 2015.

Robert H. Nielsen. Theory of the backpropagation neural network. In Proceedings of the International Joint Conference on Neural Networks, volume I, pages 593-6O5.Piscataway,NJ: IEEE,1989.

Randall C O'Reilly and Michael JFrank. Making working memory work: A computational model of learning in the prefrontal cortex and basal ganglia. Neural computation,18(2):283-328,2006.

Emilio Parisoto,Jimmy Lei Ba,and Ruslan Salakhutdinov. Actor-mimic: Deep multitask and transfer reinforcement learning. arXiv preprint arXiv:1511.06342,2015.

Razvan Pascanu and Yoshua Bengio. Revisiting natural gradient for deep networks. arXiv preprint arXiv:1301.3584,2013.

Jean-Pascal Pfister,Peter Dayan,and Maté Lengyel. Synapses with short-term plasticity are optimal estimators of presynaptic membrane potentials. Nature neuroscience,13(10):1271-1275,2010.

Roger Ratcliff. Connectionist models of recognition memory: constraints imposed by learning and forgetting functions. Psychological review, 97(2):285,1990.

Mark B Ring. Child: A frst step towards continual learning. In Learning to learn, pages 261-292. Springer, 1998.

Andrei A Rusu, Sergio Gomez Colmenarejo, Caglar Gulcehre, Guillaume Desjardins, James Kirkpatrick， Razvan Pascanu, Volodymyr Mnih, Koray Kavukcuoglu, and Raia Hadsell.Policy distillation. arXiv preprint arXiv:15i1.06295,2015.

Andrei A Rusu, Neil C Rabinowitz, Guillaume Desjardins, Hubert Soyer, James Kirkpatrick, Koray Kavukcuoglu, Razvan Pascanu, and Raia Hadsell. Progressive neural networks. arXiv preprint arXiv:1606.04671,2016.

Rupesh K Srivastava, Jonathan Masci, Sohrob Kazerounian,Faustino Gomez,and Juergen Schmidhuber. Compete to compute. In Advances in Neural Information Processng Systems 26, pages 2310-2318. Curran Associates, Inc., 2013. URL http://papers.nips.cc/paper/ 5059-compete-to-compute.pdf.

Héctor J. Sussmann. Uniqueness of the weights for minimal feedforward nets with a given inputoutput map. Neural Networks,5:589-593,1992.

Hado van Hasselt, Arthur Guez, and David Silver. Deep reinforcement learning with double q-learning. Proceedings of the Thirtieth AAAI Conference on Artificial Intelligence, pages 2094-2100,2016.

Joel Veness, Kee Siong Ng, Marcus Hutter, and Michael Bowling. Context tree switching. In 2012 Data compression conference., pages 327-336. IEEE,2012.

Guang Yang, Feng Pan, and Wen-Biao Gan. Stably maintained dendritic spines are associated with lifelong memories. Nature,462(7275):920-924,2009.

Guang Yang, Cora Sau Wan Lai, Joseph Cichon,Lei Ma, Wei Li,and Wen-Biao Gan. Sleep promotes branch-specific formation of dendritic spines after learning. Science, 344(6188):1173-1178,2014.

<table><tr><td rowspan="2">超参数</td><td colspan="3">对应图</td></tr><tr><td>3A</td><td>3B</td><td>3C</td></tr><tr><td>学习率（learning rate）</td><td>10^-3</td><td>10^-5–10^-3</td><td>10^-3</td></tr><tr><td>dropout</td><td>否</td><td>是</td><td>否</td></tr><tr><td>早停（early stopping）</td><td>否</td><td>是</td><td>否</td></tr><tr><td>隐层数（n. hidden layers）</td><td>2</td><td>2</td><td>6</td></tr><tr><td>隐层宽度（width hidden layers）</td><td>400</td><td>400–2000</td><td>100</td></tr><tr><td>每数据集 epoch 数（epochs/dataset）</td><td>20</td><td>100</td><td>100</td></tr></table>

表 1：MNIST 各图所用的超参数

## 4 附录

## 4.1 MNIST 实验

我们使用带修正线性单元（rectified linear unit）的全连接网络完成了所有 MNIST 实验。为了复现 [Goodfellow et al., 2014] 的结果，我们与使用 dropout 正则化所得的结果进行了比较。如 [Goodfellow et al., 2014] 所建议，我们对输入以 0.2 的概率、对其它隐层以 0.5 的概率应用 dropout。为了给带 dropout 的 SGD 最好的机会，我们还使用了早停。早停的实现方式是：在迄今所有见过的像素置换上计算验证集的测试误差。若验证误差被观察到在随后超过五步中持续上升，我们就终止该训练片段并进入下一个数据集；此时，我们将网络权重重置为在所有先前数据集上平均验证误差最低时的值。表 1 列出了生成正文图 3 中三幅图所用的全部超参数（译注：原文印作图 3，MNIST 结果实为正文图 2）。凡给出范围之处，该参数为随机变化，所报告的结果取自最佳超参数设置。使用随机超参数搜索时，每个实验尝试了 50 组参数组合。

## 4.2 Atari 实验

所用智能体架构与 [van Hasselt et al., 2016] 中使用的几乎完全相同。本节提供所用全部参数的细节。

图像的预处理与 [Mnih et al., 2015] 相同，即使用双线性插值将来自 Atari 模拟器的 210x160 图像下采样为 84x84。然后我们将 RGB 图像转换为 YUV 并仅使用灰度通道。智能体使用的状态由最近四帧下采样后的灰度观测拼接而成。

所用网络结构与 [Mnih et al., 2015] 中的类似，即三个卷积层后接一个全连接层。第一个卷积的卷积核大小为 8、步幅 4、32 个滤波器。第二个卷积的卷积核大小为 4、步幅 2、64 个滤波器。最后一个卷积的卷积核大小为 3、步幅 1、128 个滤波器。全连接层有 1024 个单元。注意，由于全连接单元数量是两倍且最后一个卷积的滤波器数量也是两倍，该网络的参数量约为标准网络的四倍。与标准网络的另一处不同是，允许每层拥有任务特定的增益与偏置。对每一层，网络所计算的变换 x → y 因此为：

$$
y _ { i } = \left( \sum _ { j } W _ { i j } x _ { j } + b _ { i } ^ { c } \right) g _ { i } ^ { c }\tag{4}
$$

其中 $b$ 为偏置，$g$ 为增益。网络权重与偏置的初始化方式，是在 -σ 与 σ 之间按均匀分布随机取值，其中 σ 对线性层设为输入隐单元数的平方根，对卷积层设为卷积核面积乘以输入滤波器数量。偏置与增益分别初始化为 0 和 1。

我们采用 ε-贪心探索策略，其中选择随机动作的概率 ε 随训练时间衰减。我们为每个任务保留了各自独立的计时器。前 $5 \times 1 0 ^ { 4 }$ 个时间步设 ε = 1，随后在接下来 $1 0 ^ { 6 }$ 个时间步内将其线性衰减至 0.01。

我们用 Double Q-learning 算法 [van Hasselt et al., 2016] 训练网络。每四步在 32 条经验的小批次上执行一次训练步。目标网络每 $3 \times 1 0 ^ { 4 }$ 个时间步更新一次。我们使用 RMSProp 训练：动量为 0.、衰减为 0.95、学习率为 $2 . 5 \times 1 0 ^ { - 4 }$、最大学习率为 $2 . 5 \times 1 0 ^ { - 3 }$。

相对于参考实现，我们修改的其它超参数为：1) 使用更小的回放缓冲区（$5 \times 1 0 ^ { 5 }$ 条过往经验）；2) EWC 惩罚的缩放因子为 400。另一处细微差别是，我们在 Atari 模拟器中使用了完整动作集。事实上，尽管许多游戏只支持 18 种可能动作中的一小部分，为了让所有游戏具有统一的网络结构，我们在每款游戏中都使用了 18 个动作。

每个实验的 10 款游戏从 19 款 Atari 游戏中随机选取，这些游戏上独立 DQN 可在 $5 0 \times 1 0 ^ { 6 }$ 帧内达到人类水平表现。图 4 展示了这些游戏上基线算法、EWC 与朴素 SGD 训练的得分随该游戏中所玩步数变化的函数。为获得平均性能，我们选取了 10 组各 10 款游戏，并对每组运行 4 个不同的随机种子。

与已发表模型最显著的差异是任务的自动判定。我们用环境的一个生成模型为每个任务建模。本工作中为简单起见，我们只对当前观测建模。当前任务被建模为一个类别型情境 c，它作为解释观测的隐马尔可夫模型中的隐变量。在这样的模型中，时刻 t 处于特定情境 c 的概率按如下方式演化：

$$
\begin{array} { l } { { p ( c , t + 1 ) = \displaystyle \sum _ { c ^ { \prime } } p ( c ^ { \prime } , t ) \Gamma ( c , c ^ { \prime } ) } } \\ { { \mathrm { } } } \\ { { \Gamma ( c , c ^ { \prime } ) = \delta ( c , c ^ { \prime } ) ( 1 - \alpha ) + ( 1 - \delta ( c , c ^ { \prime } ) ) \alpha } } \end{array}
$$

其中 δ 是 Kronecker delta 函数，α 是切换情境的概率。任务情境随后条件化一个预测观测概率 $p ( o | c , t )$ 的生成模型。给定这样的生成模型，时刻 t 处于某个任务情境（task set）的概率可由迄今所见的观测推断为：

$$
p ( c | o _ { 1 } . . . o _ { t } ) \propto \sum _ { c ^ { \prime } } \Gamma ( c , c ^ { \prime } ) p ( c ^ { \prime } , t - 1 ) p ( o | c , t )
$$

概率最大的情境随即被当作当前任务标签。

在我们的实现中，生成模型由因子化的多项分布组成，解释观测空间中每个像素状态的概率。该模型是一个参数化的 Dirichlet 分布，用贝叶斯更新概括迄今所见的数据。为促使每个模型专门化，我们按如下方式训练这些模型。我们将时间划分为宽度为特定值 W 的时间窗。在每个窗口内，所有 Dirichlet 先验都用迄今所见的证据更新。窗口结束时，选出与当前任务情境最匹配的模型。由于该模型对解释当前数据最有用，它保留自己的先验，而所有其它先验都回退到时间窗开始时的状态。我们确保始终有一个留出的均匀（即未初始化的）Dirichlet-多项模型可用。每当这个留出模型被选中，就创建一个新的生成模型，从而创建一个新的任务情境。该模型是贝叶斯的，含义是数据被用来维持对生成模型先验的信念；同时是非参数的，含义是模型可以随观测数据而增长。它可以视为 [Kieran et al., 2016] 所描述的 flat forget me not 算法的一种实现。参数 α 不是学得的。我们转而使用 [Veness et al., 2012] 的结果：若任务数量以 o $\scriptstyle \left( { \frac { n } { \log n } } \right)$ 的速度增长，则时间衰减的切换率 $\alpha = 1 / t$ 可保证良好的最坏情形渐近性能。

表 2 概括了 Atari 实验所用的全部超参数。除与 EWC 算法相关的参数（Fisher 乘子、Fisher 样本数、EWC 启动）以及与任务识别模型相关的参数（模型更新周期、模型降尺度与窗口大小）外，所有参数值均与 [van Hasselt et al., 2016] 中的相同，且未针对这些实验进行调优。

<table><tr><td>超参数</td><td>值</td><td>简要说明</td></tr><tr><td>动作重复（action repeat）</td><td>4</td><td>连续四帧重复同一动作。智能体每步发生在每第四帧。</td></tr><tr><td>折扣因子（discount factor）</td><td>0.99</td><td>Q-learning 算法中使用的折扣因子。</td></tr><tr><td>最大空操作数（no-op max）</td><td>30</td><td>每个训练回合开始时执行的最大「无操作」次数，以提供多样化的训练集。</td></tr><tr><td>最大奖励（max. reward）</td><td>1</td><td>奖励被截断为 1。</td></tr><tr><td>缩放输入（scaled input）</td><td>84x84</td><td>输入图像经双线性插值缩放为 84x84。</td></tr><tr><td>优化算法（optimization algorithm）</td><td>RMSprop</td><td>所用的优化算法。</td></tr><tr><td>学习率（learning rate）</td><td>0.00025</td><td>RMSprop 中的学习率。</td></tr><tr><td>最大学习率（max. learning rate）</td><td>0.0025</td><td>RMSprop 将应用的最大学习率。</td></tr><tr><td>动量（momentum）</td><td>0.</td><td>RMSprop 中使用的动量。</td></tr><tr><td>衰减（decay）</td><td>0.95</td><td>RMSProp 中使用的衰减。</td></tr><tr><td>裁剪 δ（clip δ）</td><td>1.</td><td>Q-learning 的每个梯度被裁剪到 ±1。</td></tr><tr><td>最大范数（max. norm）</td><td>50.</td><td>裁剪后，若梯度的范数大于 50.，则将梯度重新归一化到 50.</td></tr><tr><td>历史长度（history length）</td><td>4</td><td>取最近经历的四帧构成 Q-learning 的一个状态。</td></tr><tr><td>小批次大小（minibatch size）</td><td>32</td><td>从回放缓冲区中取出以构成一个小批次训练样本的元素数量。</td></tr><tr><td>回放周期（replay period）</td><td>4</td><td>每 4 步（含动作重复共 16 帧）从回放缓冲区载入一个小批次。</td></tr><tr><td>记忆容量（memory size）</td><td>50000</td><td>回放记忆存储最近经历的五万次转移。</td></tr><tr><td>目标更新周期（target update period）</td><td>7500</td><td>Q-learning 中的目标网络每 7500 步更新为策略网络。</td></tr><tr><td>最小历史（min. history）</td><td>50000</td><td>只有在五万次转移被存入记忆后，智能体才开始学习。</td></tr><tr><td>初始探索（initial exploration）</td><td>1.</td><td>初始探索率的值。</td></tr><tr><td>探索衰减起点（exploration decay start）</td><td>50000</td><td>探索率将在五万帧后开始衰减。</td></tr><tr><td>探索衰减终点（exploration decay end）</td><td>1050000</td><td>探索率将在一百万帧内衰减。</td></tr><tr><td>最终探索（final exploration）</td><td>0.01</td><td>最终探索率的值。</td></tr><tr><td>模型更新周期（model update period）</td><td>4</td><td>Dirichlet 模型每四步更新一次。</td></tr><tr><td>模型降尺度（model downscaling）</td><td>2</td><td>Dirichlet 模型按因子 2 降尺度，即建模的是 42x42 大小的图像。</td></tr><tr><td>窗口大小（size window）</td><td>4</td><td>任务识别模型学习的窗口大小。</td></tr><tr><td>Fisher 样本数（num. samples Fisher）</td><td>100</td><td>每当为某任务重算 Fisher 对角元时，从回放缓冲区抽取一百个小批次。</td></tr><tr><td>Fisher 乘子（Fisher multiplier）</td><td>400</td><td>Fisher 乘以该数以构成 EWC 惩罚。</td></tr><tr><td>启动 EWC（start EWC）</td><td>20E6</td><td>EWC 惩罚仅在 500 万步（2000 万帧）之后才应用。</td></tr></table>

表 2：MNIST 各图所用的超参数（原文如此，实为 Atari 实验超参数）

[图：图 4 各单款游戏得分曲线]
图 4：各单款游戏中的得分随该游戏中所玩步数的变化。黑色基线曲线表示仅在单款游戏上单独学习的结果。

## 4.3 Fisher 重叠

为评估在同一网络中求解的不同任务是否使用相似的权重集合（正文图 3C），我们测量了两个任务的 Fisher 矩阵之间的重叠程度。具体而言，我们计算两个任务的 Fisher 矩阵 $F _ { 1 }$ 与 $F _ { 2 }$，将其归一化为各自迹为 1 的 $\hat { F } _ { 1 }$ 与 $\hat { F } _ { 2 }$，然后计算它们的 Fréchet 距离——半正定矩阵空间上的一种度量 [Dowson and Landau, 1982]：

$$
\begin{array} { l } { { d ^ { 2 } ( \hat { F } _ { 1 } , \hat { F } _ { 2 } ) = \displaystyle \frac { 1 } { 2 } \mathrm { t r } \left( \hat { F } _ { 1 } + \hat { F } _ { 2 } - 2 ( \hat { F } _ { 1 } \hat { F } _ { 2 } ) ^ { 1 / 2 } \right) } } \\ { { \displaystyle \qquad = \frac { 1 } { 2 } | | \hat { F } _ { 1 } ^ { 1 / 2 } - \hat { F } _ { 2 } ^ { 1 / 2 } | | _ { F } } } \end{array}
$$

该值介于 0 与 1 之间。我们随后将重叠定义为 $1 - d ^ { 2 }$：取值为 0 表示两个任务依赖互不重叠的权重集合，取值为 1 表示存在某个 $\alpha > 0$ 使得 $F _ { 1 } = \alpha F _ { 2 }$

---

## 术语对照表

- catastrophic forgetting → 灾难性遗忘
- elastic weight consolidation (EWC) → 弹性权重巩固（EWC）
- continual learning → 持续学习
- task → 任务
- Fisher information (matrix) → Fisher 信息（矩阵）
- synaptic consolidation → 突触巩固
- system-level consolidation → 系统级巩固
- connectionist models → 联结主义模型
- multitask learning → 多任务学习
- supervised learning → 监督学习
- reinforcement learning (RL) → 强化学习（RL）
- policy → 策略
- cumulative future reward → 未来累积奖励
- agent → 智能体
- stochastic gradient descent (SGD) → 随机梯度下降（SGD）
- L2 regularization → L2 正则化
- dropout regularization → dropout 正则化
- early stopping → 早停
- weight → 权重
- bias → 偏置
- gain → 增益
- plasticity → 可塑性
- quadratic penalty → 二次惩罚
- over-parameterization → 过参数化
- posterior probability / posterior distribution → 后验概率 / 后验分布
- prior probability → 先验概率
- Bayes' rule → 贝叶斯法则
- Laplace approximation → 拉普拉斯近似
- Gaussian distribution → 高斯分布
- (diagonal) precision → （对角）精度
- positive semi-definite → 半正定
- expectation propagation → 期望传播
- loss function → 损失函数
- permutation → 置换
- permuted MNIST → 置换 MNIST
- episodic memory → 情景记忆
- experience replay → 经验回放
- replay buffer → 回放缓冲区
- off-policy → 离策略
- target network → 目标网络
- minibatch → 小批次
- hyperparameter → 超参数
- cross-validated hyperparameter search → 交叉验证超参数搜索
- dendritic spine → 树突棘
- excitatory synapse → 兴奋性突触
- neocortex / neocortical circuits → 新皮层 / 新皮层回路
- prefrontal cortex → 前额叶皮层
- top-down gating → 自上而下门控
- working memory → 工作记忆
- task context → 任务情境
- task recognition → 任务识别
- Hidden Markov Model → 隐马尔可夫模型
- generative model → 生成模型
- forget me not process → forget me not 过程（勿忘我过程）
- Dirichlet distribution → Dirichlet 分布
- multinomial distribution → 多项分布
- Kronecker delta function → Kronecker delta 函数
- nullspace → 零空间
- Fréchet distance → Fréchet 距离
- unit trace → 单位迹
- factorized Gaussian → 因子化高斯分布
- point estimate → 点估计
- weight uncertainty → 权重不确定性
- parameter uncertainty → 参数不确定性
- Bayesian neural network → 贝叶斯神经网络
- rectified linear unit → 修正线性单元
- human-normalized score → 人类归一化得分
- short-term plasticity → 短时程可塑性
- potentiation / depression → 强化 / 弱化
- Deep Q Network (DQN) → 深度 Q 网络（DQN）
- anchor point → 锚点
- artificial general intelligence → 通用人工智能
