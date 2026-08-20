# 2023-I-JEPA

> **来源**：../2023-I-JEPA.pdf ｜ **转换**：MinerU agent API（视觉结构恢复，含 OCR）｜ 英文原文 markdown ｜ **中文译版**：全文简体中文翻译（架构名、人名、文献引用与参考文献列表保留英文；数学公式保留原样；术语对照表见文末；原文 OCR 明显讹误按论文原意径改，不另增删内容）

# Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture（基于联合嵌入预测架构的图像自监督学习）

Mahmoud Assran1,2,3 \* Quentin Duval1Ishan Misra1Piotr Bojanowski1   
Pascal Vincent1Michael Rabbat1,3 Yann LeCun1,4Nicolas Ballas1

1Meta AI(FAIR)²McGil University3 Mila, Quebec AI Institute4New York University

## 摘要

本文演示了一种无需依赖手工设计的数据增强（hand-crafted data augmentations）即可学习高语义层次图像表示的方法。我们提出基于图像的联合嵌入预测架构（Image-based Joint-Embedding Predictive Architecture, I-JEPA），一种用于图像自监督学习的非生成式方法。I-JEPA 背后的想法很简单：从单个上下文块预测同一图像中多个目标块的表示。引导 I-JEPA 产生语义表示的一项核心设计选择是掩码（masking）策略；具体而言，关键在于 (a) 以足够大的尺度采样目标块（语义的），以及 (b) 使用信息足够丰富（空间上分布）的上下文块。在实验上，与 Vision Transformer 结合后，我们发现 I-JEPA 具有高度可扩展性。例如，我们在 ImageNet 上用 16 块 A100 GPU 在 72 小时内训练了一个 ViT-Huge/14，在从线性分类到物体计数与深度预测的广泛任务上取得了强劲的下游性能。

## 1. 引言

在计算机视觉中，图像自监督学习有两类常见的方法族：基于不变性（invariance-based）的方法 [1,4,10,17,18,24,35,37,74] 与生成式（generative）方法 [8,28,36,57]。

基于不变性的预训练方法优化编码器，使其对同一图像的两个或多个视图（view）产生相似的嵌入（embedding）[15,20]，图像视图通常由一组手工设计的数据增强构造，如随机缩放、裁剪和颜色抖动（color jittering）[20] 等 [35]。这些预训练方法可以产生高语义层次的表示 [4,18]，但它们也引入了强偏置，这些偏置对某些下游任务、甚至对数据分布不同的预训练任务可能是有害的 [2]。对于需要不同抽象层次的任务，往往不清楚如何泛化这些偏置。例如，图像分类与实例分割所需的不变性并不相同 [11]。此外，将这些图像特定的增强泛化到音频等其它模态也并非易事。

[图：图 1]  
图 1．ImageNet 线性评估。I-JEPA 方法在预训练中不使用任何视图数据增强即可学习语义图像表示。通过在表示空间中预测，I-JEPA 在使用比以往方法更少计算量的情况下产生语义表示。

认知学习理论提出，生物系统中表示学习的一个驱动机制是内部模型的适应——预测感官输入的响应 [31,59]。这一思想是自监督生成式方法的核心：移除或破坏输入的部分内容，并学习预测被破坏的内容 [9,36,57,67, 68,71]。特别是，掩码去噪（mask-denoising）方法通过在像素级或 token 级重建输入中被随机掩码的 patch（图像块）来学习表示。掩码预训练任务比视图不变性方法需要更少的先验知识，并且容易泛化到图像模态之外 [8]。然而，由此得到的表示通常语义层次较低，在开箱即用（off-the-shelf）评估（如线性探测 linear probing）以及语义分类任务监督有限的迁移设定中，不如基于不变性的预训练 [4]。因此，需要一个更复杂的适应机制（如端到端微调）才能充分发挥这些方法的优势。

[图：图 2]  
图 2．自监督学习的常见架构，其中系统学习捕获其输入之间的关系。目标是为不相容的输入赋予高能量，为相容的输入赋予低能量。(a) 联合嵌入架构（Joint-Embedding Architectures）学习为相容输入 x、y 输出相似嵌入，为不相容输入输出不相似嵌入。(b) 生成式架构（Generative Architectures）学习使用解码器网络直接从相容信号 x 重建信号 y，解码器以附加的（可能是潜变量 latent 的）变量 z 为条件以促进重建。(c) 联合嵌入预测架构（Joint-Embedding Predictive Architectures）学习使用预测器网络从相容信号 x 预测信号 y 的嵌入，预测器以附加的（可能是潜变量 latent 的）变量 z 为条件以促进预测。

在本工作中，我们探索如何在不使用由图像变换编码的额外先验知识的情况下，提升自监督表示的语义层次。为此，我们提出一种用于图像的联合嵌入预测架构 [48]（I-JEPA）。方法示意见图 3。I-JEPA 背后的思想是在抽象表示空间中预测缺失信息；例如，给定单个上下文块，预测同一图像中多个目标块的表示，其中目标表示由一个可学习的目标编码器网络计算。

与在像素/token 空间中预测的生成式方法相比，I-JEPA 使用抽象的预测目标，不必要的像素级细节可能被消除，从而引导模型学习更具语义的特征。引导 I-JEPA 产生语义表示的另一项核心设计选择是我们提出的多块掩码（multi-block masking）策略。具体而言，我们证明了使用信息丰富（空间上分布）的上下文块、预测图像中足够大的目标块的重要性。

通过广泛的实证评估，我们证明：

· I-JEPA 在不使用手工设计视图增强的情况下学到强大的开箱即用表示（参见图 1）。在 ImageNet-1K 线性探测、1% ImageNet-1K 半监督以及语义迁移任务上，I-JEPA 优于 MAE [36] 等像素重建方法。

· I-JEPA 在语义任务上与视图不变性预训练方法相当，并在物体计数与深度预测等低级视觉任务上取得更好性能（第 5、6 节）。凭借更简单、归纳偏置（inductive bias）刚性更弱的模型，I-JEPA 适用于更广泛的任务集。

· I-JEPA 同时具备可扩展性与高效性（第 7 节）。在 ImageNet 上预训练一个 ViT-H/14 所需 GPU 时数不足 1200，速度为用 iBOT [79] 预训练的 ViT-S/16 的 2.5 倍以上，比用 MAE 预训练的 ViT-H/14 高效 10 倍以上。在表示空间中预测显著降低了自监督预训练所需的总计算量。

## 2. 背景

自监督学习是一种表示学习方法，系统在其中学习捕获其输入之间的关系。这一目标可以方便地用基于能量的模型（Energy-Based Models, EBM）[49] 的框架来描述：自监督目标是为不相容的输入赋予高能量，为相容的输入赋予低能量。许多现有的生成式与非生成式自监督学习方法确实都可以纳入这一框架；见图 2。

联合嵌入架构。基于不变性的预训练可以用联合嵌入架构（Joint-Embedding Architecture, JEA）纳入 EBM 框架，该架构学习为相容输入 x、y 输出相似嵌入，为不相容输入输出不相似嵌入；见图 2a。在图像预训练的语境下，相容的 x、y 对通常通过对同一输入图像随机施加手工设计的数据增强来构造 [20]。

JEA 的主要挑战是表示坍塌（representation collapse），即能量面是平坦的（编码器无论输入如何都产生常数输出）。过去几年中，人们研究了多种防止表示坍塌的方法：显式推开负样本嵌入的对比损失（contrastive loss）[15,24,37]、最小化嵌入间信息冗余的非对比损失（non-contrastive loss）[10,74]，以及最大化平均嵌入熵的基于聚类的方法 [4,5,18]。还有一些利用 x 编码器与 y 编码器之间非对称架构设计的启发式方法来避免坍塌 [8,24,35]。

生成式架构。基于重建的自监督学习方法也可以用生成式架构（Generative Architectures）纳入 EBM 框架；见图 2b。生成式架构学习使用解码器网络直接从相容信号 x 重建信号 y，解码器以附加的（可能是潜变量 latent 的）变量 z 为条件以促进重建。在图像预训练的语境下，计算机视觉中的一种常见做法是用掩码 [9,38] 构造相容的 x、y 对：x 是图像 y 的副本，但其中一些 patch 被掩码。条件变量 z 则对应一组（可能是可学习的）掩码与位置 token，向解码器指明要重建哪些图像 patch。只要 z 的信息容量相对信号 y 较低，这些架构就不存在表示坍塌问题。

联合嵌入预测架构。如图 2c 所示，联合嵌入预测架构（Joint-Embedding Predictive Architecture, JEPA）[48] 在概念上与生成式架构相似；但一个关键差别在于损失函数施加在嵌入空间而非输入空间。JEPA 学习使用预测器网络从相容信号 x 预测信号 y 的嵌入，预测器以附加的（可能是潜变量 latent 的）变量 z 为条件以促进预测。我们提出的 I-JEPA 以掩码的方式给出了该架构在图像语境下的一个实例化；见图 3。

与联合嵌入架构不同，JEPA 不追求对一组手工设计数据增强不变的表示，而是追求在以附加信息 z 为条件时彼此可预测的表示。然而，与联合嵌入架构一样，表示坍塌在 JEPA 中同样是需要关切的问题；我们利用 x 编码器与 y 编码器之间的非对称架构来避免表示坍塌。

## 3. 方法

我们现在描述所提出的基于图像的联合嵌入预测架构（I-JEPA），示意见图 3。总体目标如下：给定一个上下文块，预测同一图像中多个目标块的表示。我们为上下文编码器、目标编码器与预测器使用 Vision Transformer [29,63]（ViT）架构。ViT 由一叠 transformer 层组成，每层包含一个自注意力（self-attention）[66] 操作后接一个全连接 MLP。我们的编码器/预测器架构让人联想到生成式掩码自编码器（Masked Autoencoders, MAE）[36] 方法。但一个关键差别是，I-JEPA 方法是非生成式的，预测在表示空间中进行。

[图：图 3]  
图 3．I-JEPA。基于图像的联合嵌入预测架构使用单个上下文块预测来自同一图像的多个目标块的表示。上下文编码器是一个 Vision Transformer（ViT），只处理可见的上下文 patch。预测器是一个窄的 ViT，它接收上下文编码器的输出，并以位置 token（图中着色所示）为条件，预测特定位置处某个目标块的表示。目标表示对应目标编码器的输出，其权重在每次迭代中通过上下文编码器权重的指数移动平均（exponential moving average, EMA）来更新。

目标（Targets）。我们首先描述在 I-JEPA 框架中如何产生目标：在 I-JEPA 中，目标对应图像块的表示。给定输入图像 y，我们将其转换为 N 个互不重叠的 patch 序列，并送入目标编码器 $f _ { \bar { \theta } }$ 得到相应的 patch 级表示 $\begin{array} { r l } { s _ { y } } & { { } = } \end{array}$ $\{ \pmb { s } _ { y _ { 1 } } , \dotsc , \pmb { s } _ { y _ { N } } \}$，其中 $\boldsymbol { s } _ { y _ { k } }$ 是与第 $k ^ { \mathrm { { t h } } }$ 个 patch 相关联的表示。为得到损失所用的目标，我们从目标表示 $s _ { y }$ 中随机采样 M 个（可能重叠的）块。记 $B _ { i }$ 为第 $i ^ { \mathrm { { t h } } }$ 个块对应的掩码，$\pmb { s } _ { y } ( i ) = \{ \pmb { s } _ { y _ { j } } \} _ { j \in B _ { i } }$ 为其 patch 级表示。通常我们取 M 等于 4，并以 (0.75, 1.5) 范围内的随机长宽比（aspect ratio）和 (0.15, 0.2) 范围内的随机尺度（scale）采样这些块。注意，目标块是通过对目标编码器的输出施加掩码得到的，而不是对输入施加掩码。这一区别对于确保目标表示具有高语义层次至关重要；参见例如 [8]。

[图：图 4]  
图 4．我们的上下文与目标掩码策略示例。给定一张图像，我们随机采样 4 个目标块，尺度在 (0.15, 0.2) 范围内、长宽比在 (0.75, 1.5) 范围内。接着，我们随机采样一个尺度在 (0.85, 1.0) 范围内的上下文块，并移除任何与之重叠的目标块。在此策略下，目标块相对具有语义，上下文块信息丰富但稀疏（处理高效）。

上下文（Context）。回顾 I-JEPA 背后的目标：从单个上下文块预测目标块的表示。为在 I-JEPA 中获得上下文，我们首先从图像中采样一个单一块 x，其尺度在 (0.85, 1.0) 范围内随机、长宽比为单位值。记 $B _ { x }$ 为与上下文块 x 相关联的掩码。由于目标块独立于上下文块采样，二者可能有显著重叠。为确保预测任务非平凡，我们从上下文块中移除任何重叠区域。图 4 展示了实践中各种上下文块与目标块的示例。然后，掩码后的上下文块 x 被送入上下文编码器 $f _ { \theta }$，得到相应的 patch 级表示 $\pmb { s } _ { x } = \{ \pmb { s } _ { x _ { j } } \} _ { j \in B _ { x } }$。

预测（Prediction）。给定上下文编码器的输出 $s _ { x }$，我们希望预测 M 个目标块表示 $\pmb { s } _ { y } ( 1 ) , \dots , \pmb { s } _ { y } ( M )$。为此，对于对应目标掩码 $B _ { i }$ 的给定目标块 $\begin{array} { r } { { \pmb s } _ { y } ( i ) } \end{array}$，预测器 $g _ { \phi } ( \cdot , \cdot )$ 以上下文编码器的输出 ${ \pmb s } _ { x }$ 以及对每个待预测 patch 的掩码 token $\{ m _ { j } \} _ { j \in B _ { i } }$ 为输入，输出 patch 级预测 $\begin{array} { r } { \hat { s } _ { y } ( i ) = } \end{array}$ $\{ \hat { \pmb { s } } _ { y _ { j } } \} _ { j \in B _ { i } } \ = \ g _ { \phi } ( \pmb { s } _ { x } , \{ m _ { j } \} _ { j \in B _ { i } } )$。掩码 token 由一个共享的可学习向量加上位置嵌入（positional embedding）参数化。由于我们希望对 M 个目标块进行预测，我们将预测器应用 M 次，每次以待预测目标块位置对应的掩码 token 为条件，得到预测 $\hat { \pmb { s } } _ { y } ( 1 ) , \dots , \hat { \pmb { s } } _ { y } ( M )$。

损失（Loss）。损失就是预测的 patch 级表示 $\hat { s } _ { y } ( i )$ 与目标 patch 级表示 $s _ { y } ( i )$ 之间 $L _ { 2 }$ 距离的平均值；即

$$
\frac { 1 } { M } \sum _ { i = 1 } ^ { M } D \left( \hat { \pmb { s } } _ { y } ( i ) , { \pmb { s } } _ { y } ( i ) \right) = \frac { 1 } { M } \sum _ { i = 1 } ^ { M } \sum _ { j \in B _ { i } } \| \hat { \pmb { s } } _ { y _ { j } } - { \pmb { s } } _ { y _ { j } } \| _ { 2 } ^ { 2 } .
$$

预测器的参数 $\phi$ 与上下文编码器的参数 $\theta$ 通过基于梯度的优化学习，而目标编码器的参数 $\bar { \theta }$ 通过上下文编码器参数的指数移动平均来更新。使用指数移动平均目标编码器已被证明对用 Vision Transformer 训练 JEA 至关重要 [18,25,79]，我们发现这对 I-JEPA 同样成立。

## 4. 相关工作

长期以来，一系列工作通过预测缺失或被破坏的感官输入的取值来探索视觉表示学习。去噪自编码器（denoising autoencoder）使用随机噪声作为输入破坏 [67]。Context encoders 基于周围内容回归整块图像区域 [57]。另有一些工作将图像着色（colorization）作为去噪任务 [46,47,77]。

图像去噪的思想最近在掩码图像建模（masked image modelling）[9,36,71] 的语境下被重新审视：使用 Vision Transformer [29] 重建缺失的输入 patch。掩码自编码器（MAE）[36] 的工作提出了一种高效架构，只需编码器处理可见的图像 patch。通过在像素空间重建缺失 patch，MAE 在大型标注数据集上端到端微调时取得强劲性能，并表现出良好的扩展性质。BEiT [9] 在词元化空间中预测缺失 patch 的取值；具体而言，使用一个冻结的离散 VAE 对图像 patch 做词元化，该 VAE 在一个包含 2.5 亿张图像的数据集上训练 [58]。然而，像素级预训练已被证明在微调上优于 BEiT [36]。另一项工作 SimMIM [71] 探索了基于经典梯度方向直方图（Histogram of Gradients）[27] 特征空间的重建目标，并展示了相对像素空间重建的一些优势。与这些工作不同，我们的表示空间是在训练过程中通过联合嵌入预测架构学得的。我们的目标是学习无需在下游任务上进行大量微调的语义表示。

与我们工作最接近的是 data2vec [8] 与 Context Autoencoders [25]。data2vec 方法学习预测由在线目标编码器计算的缺失 patch 的表示；通过避免手工设计的增强，该方法可应用于多种模态，在视觉、文本与语音上均取得可观结果。Context Autoencoders 使用编码器/解码器架构，通过重建损失与对齐约束之和进行优化，后者强制缺失 patch 在表示空间中的可预测性。与这些方法相比，I-JEPA 在计算效率上有显著改进，并学到更具语义的开箱即用表示。与我们工作同期，data2vec-v2 [7] 探索了面向多模态学习的高效架构。

<table><tr><td>方法</td><td>架构</td><td>训练轮数（Epochs）</td><td>Top-1</td></tr><tr><td colspan="3">不使用视图数据增强的方法</td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>1600</td><td>77.3</td></tr><tr><td rowspan="3">MAE [36]</td><td>ViT-B/16</td><td>1600</td><td>68.0</td></tr><tr><td>ViT-L/16</td><td>1600</td><td>76.0</td></tr><tr><td>ViT-H/14</td><td>1600</td><td>77.2</td></tr><tr><td rowspan="2">CAE [22]</td><td>ViT-B/16</td><td>1600</td><td>70.4</td></tr><tr><td>ViT-L/16</td><td>1600</td><td>78.1</td></tr><tr><td rowspan="4">I-JEPA</td><td>ViT-B/16</td><td>600</td><td>72.9</td></tr><tr><td>ViT-L/16</td><td>600</td><td>77.5</td></tr><tr><td>ViT-H/14</td><td>300</td><td>79.3</td></tr><tr><td> $\mathrm { V i T - H / 1 6 _ { 4 4 8 } }$ </td><td>300</td><td>81.1</td></tr><tr><td colspan="4">使用额外视图数据增强的方法</td></tr><tr><td>SimCLR v2 [21]</td><td>RN152 (2×)</td><td>800</td><td>79.1</td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>300</td><td>80.1</td></tr><tr><td></td><td></td><td></td><td></td></tr><tr><td>iBOT[79]</td><td>ViT-L/16</td><td>250</td><td>81.0</td></tr></table>

表 1．ImageNet。ImageNet-1k 上的线性评估（ViT-$\mathrm { H } / 1 6 _ { 4 4 8 }$ 以 448 × 448 的分辨率预训练）。与其它在预训练中不依赖手工设计视图数据增强的方法相比，I-JEPA 提升了线性探测性能。此外，I-JEPA 展现出良好的可扩展性——更大的 I-JEPA 模型在不使用视图数据增强的情况下达到了视图不变性方法的性能。

我们还将 I-JEPA 与多种基于联合嵌入架构的方法进行比较；如 DINO [18]、MSN [4] 与 iBOT [79]。这些方法在预训练期间依赖手工设计的数据增强来学习语义图像表示。MSN [4] 的工作将掩码作为预训练期间的一种附加数据增强，而 iBOT 将 data2vec 风格的 patch 级重建损失与 DINO 的视图不变性损失相结合。这些方法的共同之处在于需要处理每个输入图像的多个用户生成的视图，从而阻碍了可扩展性。相比之下，I-JEPA 只需处理每张图像的单个视图。我们发现，用 I-JEPA 训练的 ViT-Huge/14 所需计算量少于用 iBOT 训练的 ViT-Small/16。

<table><tr><td>方法</td><td>架构</td><td>训练轮数（Epochs）</td><td>Top-1</td></tr><tr><td colspan="4">不使用视图数据增强的方法</td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>1600</td><td>73.3</td></tr><tr><td>MAE [36]</td><td>ViT-L/16 ViT-H/14</td><td>1600 1600</td><td>67.1 71.5</td></tr><tr><td>I-JEPA</td><td>ViT-L/16 ViT-H/14  $\mathrm { V i T  – H / 1 6 _ { 4 4 8 } }$ </td><td>600 300 300</td><td>69.4 73.3 77.3</td></tr><tr><td>iBOT [79]</td><td>使用额外视图数据增强的方法 ViT-B/16</td><td>400</td><td>69.7</td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>300</td><td>70.0</td></tr><tr><td>SimCLR v2 [35]</td><td>RN151 (2×)</td><td>800</td><td>70.2</td></tr><tr><td></td><td></td><td></td><td></td></tr><tr><td>BYOL [35]</td><td>RN200 (2×)</td><td>800</td><td>71.2</td></tr><tr><td>MSN [4]</td><td>ViT-B/4</td><td>300</td><td>75.7</td></tr></table>

表 2．ImageNet-1%。在 ImageNet-1K 上仅使用 1% 可用标签的半监督评估。模型通过微调或线性探测进行适配，以各方法中效果更好者为准。$\mathrm { V i T - H } / 1 6 _ { 4 4 8 }$ 以 448 × 448 的分辨率预训练。I-JEPA 预训练优于同样不在预训练中依赖手工设计数据增强的 MAE。此外，I-JEPA 受益于规模：在分辨率 448 下训练的 ViT-H/16 超越了以往方法，包括利用额外手工设计数据增强的方法。

## 5. 图像分类

为证明 I-JEPA 在不依赖手工设计数据增强的情况下学到高层次表示，我们在多种图像分类任务上报告使用线性探测（linear probing）与部分微调（partial fine-tuning）协议的结果。本节中，我们考虑在 ImageNet-1K 数据集 [60] 上预训练的自监督模型。预训练与评估的实现细节见附录 A。除非特别说明，所有 I-JEPA 模型均在 $2 2 4 \times 2 2 4$ 像素分辨率下训练。

ImageNet-1K。表 1 展示了常见的 ImageNet-1K 线性评估基准上的性能。自监督预训练之后，模型权重被冻结，并在其上使用完整 ImageNet-1K 训练集训练一个线性分类器。与 MAE [36]、Context Autoencoders（CAE）[22]、data2vec [8] 等同样不在预训练中依赖大量手工设计数据增强的流行方法相比，我们看到 I-JEPA 在使用更少计算量（见第 7 节）的同时显著提升了线性探测性能。凭借 I-JEPA 更高的效率，我们可以训练更大的模型，以一小部分计算量超越最好的 CAE 模型。I-JEPA 同样受益于规模；特别是，以 448 × 448 像素分辨率训练的 ViT-H/16 达到了 iBOT [79] 等视图不变性方法的性能，同时避免了手工设计数据增强的使用。

<table><tr><td>方法</td><td>架构</td><td>CIFAR100</td><td>Places205</td><td>iNat18</td></tr><tr><td colspan="5">不使用视图数据增强的方法</td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>81.6</td><td>54.6</td><td>28.1</td></tr><tr><td rowspan="2">MAE [36] I-JEPA</td><td>ViT-H/14</td><td>77.3</td><td>55.0</td><td>32.9</td></tr><tr><td>ViT-H/14</td><td>87.5</td><td>58.4</td><td>47.6</td></tr><tr><td colspan="5">使用额外视图数据增强的方法</td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>84.9</td><td>57.9</td><td>55.9</td></tr><tr><td>iBOT [79]</td><td>ViT-L/16</td><td>88.3</td><td>60.4</td><td>57.3</td></tr></table>

表 3．图像分类的线性探测迁移。下游图像分类任务上的线性评估。I-JEPA 显著优于以往同样不使用增强的方法（MAE 与 data2vec），并缩小了与在预训练中利用手工设计数据增强的最佳基于视图不变性方法的差距。

低样本 ImageNet-1K。表 2 展示了 1% ImageNet 基准上的性能。这里的想法是仅用 1% 可用的 ImageNet 标签（对应每类约 12 到 13 张图像）将预训练模型适配到 ImageNet 分类。模型通过微调或线性探测进行适配，以各方法中效果更好者为准。在使用相似编码器架构时，I-JEPA 以更少的预训练轮数超越 MAE。使用 ViT-H/14 架构的 I-JEPA 以显著更少的计算量（见第 7 节）匹配了用 data2vec [8] 预训练的 ViT-L/16 的性能。通过提高图像输入分辨率，I-JEPA 超越了以往方法，包括在预训练中确实利用额外手工设计数据增强的联合嵌入方法，如 MSN [4]、DINO [17] 与 iBOT [79]。

迁移学习。表 3 展示了线性探测下多种下游图像分类任务上的性能。I-JEPA 显著优于以往不使用增强的方法（MAE 与 data2vec），并缩小了与在预训练中利用手工设计数据增强的最佳基于视图不变性方法的差距，在 CIFAR100 与 Place205 上甚至以线性探测超越了流行的 DINO [18]。

## 6. 局部预测任务

如第 5 节所示，I-JEPA 学到的语义图像表示显著改进了以往方法（如 MAE 与 data2vec）的下游图像分类性能。此外，I-JEPA 受益于规模，可以缩小与利用额外手工设计数据增强的基于视图不变性方法的差距，甚至实现超越。在本节中，我们发现 I-JEPA 还学到了局部图像特征，并在低级与稠密预测任务（如物体计数与深度预测）上超越基于视图不变性的方法。

<table><tr><td>方法</td><td>架构</td><td>Clevr/Count</td><td>Clevr/Dist</td></tr><tr><td colspan="3">不使用视图数据增强的方法</td><td></td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>85.3</td><td>71.3</td></tr><tr><td>MAE [36]</td><td>ViT-H/14</td><td>90.5</td><td>72.4</td></tr><tr><td>I-JEPA</td><td>ViT-H/14</td><td>86.7</td><td>72.4</td></tr><tr><td colspan="3">使用额外数据增强的方法</td><td></td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>86.6</td><td>53.4</td></tr><tr><td>iBOT[79]</td><td>ViT-L/16</td><td>85.7</td><td>62.8</td></tr></table>

表 4．低级任务的线性探测迁移。由物体计数（Clevr/Count）与深度预测（Clevr/Dist）构成的下游低级任务上的线性评估。I-JEPA 方法在预训练中有效捕获低级图像特征，并在物体计数与深度预测等任务上超越基于视图不变性的方法。

表 4 展示了线性探测下多种低级任务上的性能。预训练之后，编码器权重被冻结，并在其上训练一个线性模型，在 Clevr 数据集 [43] 上执行物体计数与深度预测。与 DINO、iBOT 等视图不变性方法相比，I-JEPA 方法在预训练中有效捕获低级图像特征，并在物体计数（Clevr/Count）与深度预测（Clevr/Dist，且优势明显）上超越它们。

## 7. 可扩展性

模型效率。与以往方法相比，I-JEPA 高度可扩展。图 5 展示了 1% ImageNet-1K 半监督评估性能随 GPU 时数的变化。I-JEPA 比以往方法需要更少的计算量，且在不依赖手工设计数据增强的情况下取得强劲性能。与 MAE 等直接用像素作为目标的基于重建的方法相比，I-JEPA 通过在表示空间中计算目标引入了额外开销（每次迭代约慢 7%）。但由于 I-JEPA 收敛所需迭代次数约少 5 倍，实践中仍能看到显著的计算节省。与 iBOT 等依赖手工设计数据增强来创建并处理每张图像多个视图的基于视图不变性的方法相比，I-JEPA 的运行也显著更快。特别地，一个巨型 I-JEPA 模型（ViT-H/14）所需计算量少于一个小型 iBOT 模型（ViT-S/16）。

数据规模扩展。我们还发现 I-JEPA 受益于用更大数据集预训练。表 5 展示了增大预训练数据集（IN1K 对 IN22K）时语义任务与低级任务的迁移学习性能。在更大、更多样的数据集上预训练时，这些概念上不同的任务的迁移学习性能均有提升。

<table><tr><td>预训练数据</td><td>架构</td><td>CIFAR100</td><td>Place205</td><td>INat18</td><td>Clevr/Count</td><td>Clevr/Dist</td></tr><tr><td>IN1k</td><td>ViT-H/14</td><td>87.5</td><td>58.4</td><td>47.6</td><td>86.7</td><td>72.4</td></tr><tr><td>IN22k</td><td>ViT-H/14</td><td>89.5</td><td>57.8</td><td>50.5</td><td>88.6</td><td>75.0</td></tr><tr><td>IN22k</td><td>ViT-G/16</td><td>89.5</td><td>59.1</td><td>55.3</td><td>86.7</td><td>73.0</td></tr></table>

表 5．数据集与模型规模消融。评估预训练数据集规模与模型规模对迁移任务的影响。I-JEPA 受益于更大、更多样的数据集。当增大预训练数据集（IN1k 对 IN22k）时，我们看到 ViT-H/14 模型的性能提升。在 ImageNet-22k 上训练更大的 ViT-G/16 模型时，我们观察到语义任务上进一步的性能提升。ViT-H/14 在 IN1k 上训练 300 轮，在 IN22k 上训练相当于 900 个 IN1k 轮次。ViT-G/16 训练了相当于 600 个 IN1k 轮次。（译注：原文此处印作 ViT-H/16，系论文笔误，按表内数据径改为 ViT-G/16。）

[图：图 5]  
图 5．扩展。1% ImageNet-1K 半监督评估性能随预训练 GPU 时数的变化。I-JEPA 取得强劲性能所需计算量少于以往方法。与 MAE 与 data2vec 相比，I-JEPA 通过需要更少的预训练轮数获得显著加速。与依赖手工设计数据增强的 iBOT 相比，巨型 I-JEPA 模型（ViT-H/14）所需计算量少于其最小模型（ViT-S/16）。

模型规模扩展。表 5 还表明，在 IN22K 上预训练时 I-JEPA 受益于更大的模型规模。与 ViT-H/14 模型相比，预训练 ViT-G/16 显著提升了 Place205 与 INat18 等图像分类任务的下游性能，但并未提升低级下游任务的性能——ViT-G/16 使用更大的输入 patch，这可能对局部预测任务不利。

## 8. 预测器可视化

I-JEPA 中预测器的角色是接收上下文编码器的输出，并以位置掩码 token 为条件，预测掩码 token 所指定位置处的目标块表示。一个自然的问题是：以位置掩码 token 为条件的预测器是否学会了正确捕获目标中的位置不确定性。为定性研究这一问题，我们可视化预测器的输出。我们采用以下可视化方法，以便研究社群能够独立复现我们的发现。预训练之后，我们冻结上下文编码器与预测器的权重，并按照 RCDM 框架 [13] 训练一个解码器，将预测器输出的平均池化（average-pool）映射回像素空间。图 6 展示了不同随机种子下的解码器输出。跨样本共同的性质表示平均池化预测器表示中所包含的信息。I-JEPA 预测器正确捕获了位置不确定性，并以正确的姿态产生高层次的物体部位（如鸟的背部与车的顶部）。

## 9. 消融实验

在表示空间中预测。表 7 比较了损失在像素空间与表示空间计算时，1% ImageNet-1K 线性探测的低样本性能。我们推测 I-JEPA 的一个关键组件是损失完全在表示空间中计算，从而使目标编码器能够产生抽象的预测目标——无关的像素级细节被消除。从表 7 可见，在像素空间中预测会导致线性探测性能的显著退化。

掩码策略。表 6 将我们的多块掩码与其它掩码策略进行比较，如光栅化掩码（rasterized masking，将图像分为四个大象限，目标是用一个象限作为上下文预测其余三个象限），以及基于重建的方法常用的传统块掩码与随机掩码。在块掩码中，目标是单个图像块，上下文是图像的补集。在随机掩码中，目标是一组随机 patch，上下文是图像的补集。注意，在所有考虑的策略中上下文块与目标块均无重叠。我们发现多块掩码有助于引导 I-JEPA 学习语义表示。关于多块掩码的更多消融见附录 C。

[图：图 6]  
图 6．I-JEPA 预测器表示的可视化。对每张图像：第一列是原始图像；第二列是上下文图像，由预训练的 I-JEPA ViT-H/14 编码器处理。后续各列中，绿色边界框内是生成模型对预训练 I-JEPA 预测器输出解码得到的样本，预测器以对应绿色边界框位置的位置掩码 token 为条件。跨样本共同的性质表示 I-JEPA 预测中包含的信息。I-JEPA 预测器正确捕获位置不确定性，并以正确的姿态产生高层次的物体部位（如鸟的背部与车的顶部）。跨样本变化的性质表示表示中未包含的信息。此处，I-JEPA 预测器丢弃了精确的低级细节以及背景信息。

<table><tr><td rowspan="2">掩码</td><td colspan="2">目标</td><td colspan="2">上下文</td><td rowspan="2">Top-1</td></tr><tr><td>类型</td><td>频次</td><td>类型</td><td>平均比例*</td></tr><tr><td>multi-block（多块）</td><td>Block(0.15, 0.2)</td><td>4</td><td> Block(0.85,1.0) × Complement</td><td>0.25</td><td> 54.2</td></tr><tr><td>rasterized（光栅化）</td><td>Quadrant</td><td>3</td><td>Complement</td><td>0.25</td><td>15.5</td></tr><tr><td>block（块）</td><td>Block(0.6)</td><td>1</td><td>Complement</td><td>0.4</td><td>20.2</td></tr><tr><td>random（随机）</td><td>Random(0.6)</td><td>1</td><td>Complement</td><td>0.4</td><td>17.6</td></tr></table>

\*平均比例（Avg. Ratio）是上下文块中 patch 数量相对图像中 patch 总数的平均比例。

表 6．掩码策略消融。用 ViT-B/16 进行 300 轮 I-JEPA 预训练后，在 ImageNet-1K 上仅用 1% 可用标签做线性评估。与所提出的多块掩码策略的比较。在光栅化掩码中，图像被分为四个大象限；一个象限被用作上下文来预测其余三个象限。在块掩码中，目标是单个图像块，上下文是图像补集。在随机掩码中，目标是一组随机图像 patch，上下文是图像补集。所提出的多块掩码策略有助于引导 I-JEPA 学习语义表示。

<table><tr><td>目标</td><td>架构</td><td>训练轮数（Epochs）</td><td>Top-1</td></tr><tr><td> 目标编码器输出</td><td>ViT-L/16</td><td>500</td><td>66.9</td></tr><tr><td>像素</td><td>ViT-L/16</td><td>800</td><td>40.7</td></tr></table>

表 7．目标消融。在 ImageNet-1K 上仅用 1% 可用标签的线性评估。当损失施加在像素空间而非表示空间时，I-JEPA 表示的语义层次显著退化，凸显了预训练中目标编码器的重要性。

## 10. 结论

我们提出了 I-JEPA，一种不依赖手工设计数据增强即可学习语义图像表示的简单高效方法。我们表明，通过在表示空间中预测，I-JEPA 比像素重建方法收敛更快，并学到高语义层次的表示。与基于视图不变性的方法相比，I-JEPA 指出了一条用联合嵌入架构学习通用表示、而不依赖手工设计视图增强的路径。

## References

[1] Yuki Markus Asano,Christian Rupprecht,and Andrea Vedaldi. Self-labelling via simultaneous clustering and representation learning. Internatinoal Conference on Learning Representations,2020.1

[2] Mahmoud Assran,Randall Balestriero, Quentin Duval, Florian Bordes,Ishan Misra,Piotr Bojanowski,Pascal Vincent, Michael Rabbat,and Nicolas Ballas．The hidden uniform cluster prior in self-supervised learning. International Conference on Learning Representations,2023.1,13

[3] Mahmoud Assran，Nicolas Ballas，Lluis Castrejon，and Michael Rabbat. Supervision accelerates pre-training in contrastive semi-supervised learning of visual representations. NeurIPS Workshop on Self-Supervised Learning,2020.13

[4] Mahmoud Assran,Mathilde Caron,Ishan Misra,Piotr Bo-janowski,Florian Bordes,Pascal Vincent,Armand Joulin, Michael Rabbat,and Nicolas Ballas.Masked siamese networks for label-effcient learning. European Conference on Computer Vision,2022.1,2,3,5,6,12,13,16,17

[5] Mahmoud Assran,Mathilde Caron,Ishan Misra,Piotr Bo-janowski,Armand Joulin,Nicolas Ballas,and Michael Rabbat.Semi-supervised learning of visual features by nonparametrically predicting view assignments with support samples.IEEE/CVF International Conference on Computer Vision,2021.3,13

[6] Philip Bachman,R Devon Hjelm,and William Buchwalter. Learning representations by maximizing mutual information across views. Advances in neural information processing systems,32,2019.13

[7] Alexei Baevski,Arun Babu,Wei-Ning Hsu-and Michael Auli.Efficient self-supervised learning with contextualized target representations for vision,speech and language.arXiv preprint arXiv:2212.07525,2022. 5

[8] Alexei Baevski,Wei-Ning Hsu,Qiantong Xu,Arun Babu, Jiatao Gu,and Michael Auli.Data2vec:A general framework for self-supervised learning in speech, vision and language. arXiv preprint arXiv:2202.03555,2022.1,3,4,5,6,13

[9] Hangbo Bao,Li Dong,and Furu Wei. Beit: Bert pre-training of image transformers.arXiv preprint arXiv:2106.08254, 2021. 1, 3, 4,13

[10] Adrien Bardes，Jean Ponce,and Yann LeCun． Vicreg:Variance-invariance-covariance regularization for selfsupervised learning.arXiv preprint arXiv:2105.04906,2021. 1, 3, 13

[11] Adrien Bardes,Jean Ponce,and Yann LeCun. Vicregl: Selfsupervised learning of local visual features.arXiv preprint arXiv:2210.01571,2022. 1, 13

[12] Florian Bordes，Randall Balestriero，Quentin Garrido, Adrien Bardes,and Pascal Vincent. Guillotine regularization: Improving deep networks generalization by removing their head.arXiv preprint arXiv:2206.13378,2022.13

[13]Florian Bordes,Randall Balestriero,and Pascal Vincent. High fidelity visualization of what your self-supervised representation knows about. Transactions on Machine Learning Research,2022. 7,16

[14] John Bridle,Anthony Heading,and David MacKay.Unsupervised classifiers,mutual information and'phantom tar-

gets.Advances in neural information processing systems, 4, 1991.13

[15] Jane Bromley, James W Bentz,Léon Bottou, Isabelle Guyon, Yann LeCun,Cliff Moore,Eduard Säckinger,and Roopak Shah. Signature verification using a“siamese” time delay neural network.International Journal ofPattern Recognition and Artificial Intelligence,7(04):669-688,1993.1,3

[16] Zhaowei Cai, Avinash Ravichandran， Paolo Favaro, Manchen Wang,Davide Modolo,Rahul Bhotika, Zhuowen Tu,and Stefano Soatto. Semi-supervised vision transformers at scale.arXiv preprint arXiv:2208.05688,2022.13

[17] Mathilde Caron,Ishan Misra,Julien Mairal,Priya Goyal,Piotr Bojanowski, and Armand Joulin. Unsupervised learning of visual features by contrasting cluster assignments.arXiv preprint arXiv:2006.09882,2020. 1,6

[18] Mathilde Caron,Hugo Touvron, Ishan Misra,Hervé Jégou, Julien Mairal,Piotr Bojanowski,and Armand Joulin.Emerging properties in self-supervised vision transformers.arXiv preprint arXiv:2104.14294,2021.1,3,4,5,6,12,13

[19] Mark Chen,Alec Radford,Rewon Child, Jeffrey Wu, Heewoo Jun, David Luan,and Ilya Sutskever. Generative pretraining from pixels.In International Conference on Machine Learning,pages 1691-1703.PMLR,2020.13

[20] Ting Chen, Simon Kornblith,Mohammad Norouzi,and Geoffrey Hinton.A simple framework for contrastive learning of visual representations.preprint arXiv:2002.05709,2020. 1, 2, 13

[21] Ting Chen,Simon Kornblith,Kevin Swersky,Mohammad Norouzi,and Geoffrey Hinton．Big self-supervised models are strong semi-supervised learners.arXiv preprint arXiv:2006.10029,2020.5

[22] Xiaokang Chen,Mingyu Ding，Xiaodi Wang，Ying Xin, Shentong Mo,Yunhao Wang，Shumin Han，Ping Luo, Gang Zeng，and Jingdong Wang.Context autoencoder for self-supervised representation learning. arXiv preprint arXiv:2202.03026,2022. 5

[23] Xinlei Chen,Haoqi Fan,Ross Girshick,and Kaiming He. Improved baselines with momentum contrastive learning. arXiv preprint arXiv:2003.04297,2020. 12,13

[24] Xinlei Chen and Kaiming He. Exploring simple siamese representation learning.arXiv preprint arXiv:20l1.10566, 2020.1,3, 13

[25] Xinlei Chen,Saining Xie,and Kaiming He.An empirical study of training self-supervised vision transformers.arXiv preprint arXiv:2104.02057,2021. 4

[26] Yubei Chen,Adrien Bardes,Zengyi Li,and Yann LeCun. Intra-instance vicreg:Bag of self-supervised image patch embedding. arXiv preprint arXiv:2206.08954,2022.13

[27] Navneet Dalal and Bill Triggs.Histograms of oriented gradients for human detection.In 20o5 IEEE computer society conference on computer vision and pattern recognition (CVPR'05),volume 1,pages 886-893.Ieee,2005.4

[28] Jacob Devlin,Ming-Wei Chang,Kenton Lee,and Kristina Toutanova.Bert:Pre-training of deep bidirectional transformers for language understanding.arXiv preprint arXiv:1810.04805,2018. 1

[29] Alexey Dosovitskiy, Lucas Beyer,Alexander Kolesnikov, Dirk Weissenborn,Xiaohua Zhai， Thomas Unterthiner, Mostafa Dehghani,Matthias Minderer,Georg Heigold, Sylvain Gelly,et al．An image is worth 16xl6 words: Transformers for image recognition at scale.arXiv preprint arXiv:2010.11929,2020.3,4,12,13

[30] Alaaeldin El-Nouby,Gautier Izacard,Hugo Touvron,Ivan Laptev,Hervé Jegou,and Edouard Grave．Are large-scale datasets necessary for self-supervised pre-training?arXiv preprint arXiv:2112.10740,2021. 13

[31] Karl Friston．A theory of cortical responses．Philosophical transactions of the Royal Society B: Biological sciences, 360(1456):815-836,2005. 1

[32] Spyros Gidaris,Andrei Bursuc,Nikos Komodakis,Patrick Pérez,and Mathieu Cord.Learning representations by predicting bags of visual words.In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 6928-6938,2020. 13

[33] Ian Goodfellow, Yoshua Bengio,and Aaron Courville. Deep learning.MIT press,2016.13

[34] Priya Goyal, Quentin Duval, Jeremy Reizenstein,Matthew Leavitt,Min Xu,Benjamin Lefaudeux,Mannat Singh, Vinicius Reis,Mathilde Caron,Piotr Bojanowski,Armand Joulin,and Ishan Misra. Vissl. https : //github.com/ facebookresearch/vissl,2021.12

[35]Jean-Bastien Grill,Florian Strub,Florent Altché,Corentin Tallec,Pierre HRichemond,Elena Buchatskaya,Carl Doersch,Bernardo Avila Pires,Zhaohan Daniel Guo,Mohammad Gheshlaghi Azar,et al． Bootstrap your own latent: A new approach to self-supervised learning.arXiv preprint arXiv:2006.07733,2020.1,3,5,12,13

[36] Kaiming He,Xinlei Chen, Saining Xie,Yanghao Li,Piotr Dollar, and Ross Girshick.Masked autoencoders are scalable vision learners.IEEE/CVF Conference on Computer Vision and Pattern Recognition,2022. 1,2,3,4,5,6,12,13,15,16

[37] Kaiming He,Haoqi Fan,Yuxin Wu, Saining Xie,and Ross Girshick.Momentum contrast for unsupervised visual representation learning.arXiv preprint arXiv:1911.05722,2019. 1, 3, 12, 13

[38] Kaiming He, Xiangyu Zhang,Shaoqing Ren,and Jian Sun. Deep residual learning for image recognition. In Proceedingsof the IEEEConference on Computer Vision and Pattern Recognition, pages 770-778,2016. 3

[39] Olivier Henaff. Data-effcient image recognition with contrastive predictive coding. In International conference on machine learning,pages 4182-4192.PMLR,2020.13

[40] R Devon Hjelm,Alex Fedorov, Samuel Lavoie-Marchildon, Karan Grewal,Phil Bachman,Adam Trischler,and Yoshua Bengio.Learning deep representations by mutual information estimation and maximization.arXiv preprint arXiv:1808.06670,2018. 13

[41] Weihua Hu, Takeru Miyato,Seiya Tokui, Eiichi Matsumoto, and Masashi Sugiyama.Learning discrete representations via information maximizing self-augmented training. In International conference on machine learning,pages 1558- 1567.PMLR,2017.13

[42] JustinJohnson，Bharath Hariharan, LaurensVan Der Maaten,Li Fei-Fei,C Lawrence Zitnick,and Ross

Girshick. Clevr:A diagnostic dataset for compositional language and elementary visual reasoning.In Proceedings of the IEEE conference on computer vision and pattern recognition, pages 2901-2910,2017.12

[43] Justin Johnson,Bharath Hariharan,Laurens van der Maaten, Li Fei-Fei, C Lawrence Zitnick,and Ross Girshick. Clevr: A diagnostic dataset for compositional language and elementary visual reasoning.In CVPR,2017.6

[44] Andreas Krause,Pietro Perona,and Ryan Gomes.Discriminative clustering by regularized information maximization.Advances in neural information processing systems, 23, 2010.13

[45] Alex Krizhevsky, Geoffrey Hinton,et al.Learning multiple layers of features from tiny images.2009.12

[46] GustavLarsson,MichaelMaire，and Gregory Shakhnarovich.Learning representations for automatic colorization.2016.4

[47] GustavLarsson，MichaelMaire,andGregory Shakhnarovich．Colorization as a proxy task for visual understanding.2017.4

[48] Yann LeCun.A path towards autonomous machine intelligence version 0.9.2,2022-06-27. 2022.2,3

[49] Yann LeCun, Sumit Chopra,Raia Hadsell,M Ranzato,and Fujie Huang. A tutorial on energy-based learning. Predicting structured data,1(0),2006.2

[50] Ralph Linsker. Self-organization in a perceptual network. Computer,21(3):105-117,1988.13

[51] Ilya Loshchilov and Frank Hutter. Decoupled weight decay regularization. arXiv preprint arXiv:1711.05101,2017.12

[52] Yi Ma,Doris Tsao,and Heung-Yeung Shum.On the principles of parsimony and self-consistency for the emergence of intelligence.Frontiers of Information Technology&Electronic Engineering,pages 1-26,2022.13

[53] Ishan Misra and Laurens van der Maaten.Self-supervised learning of pretext-invariant representations.In Proceedingsof the IEEE Conference on Computer Vision and Pattern Recognition, pages 6707-6717,2020.13

[54]Jovana Mitrovic,Brian McWilliams,Jacob Walker,Lars Buesing,and Charles Blundell. Representation learning via invariant causal mechanisms. International Conference on Learning Representations,2021.13

[55] Aaron van den Oord, Yazhe Li,and Oriol Vinyals. Representation learning with contrastive predictive coding.arXiv preprint arXiv:1807.03748,2018. 13

[56] Adam Paszke, Sam Gross,Francisco Massa,Adam Lerer, James Bradbury,Gregory Chanan, Trevor Killeen, Zeming Lin,Natalia Gimelshein,Luca Antiga, et al. Pytorch: An imperative style,high-performance deep learning library. Advances in neural information processing systems,32,2019. 12

[57] Deepak Pathak,Philipp Krahenbuhl,Jeff Donahue,Trevor Darrell,and Alexei A Efros.Context encoders:Feature learning by inpainting.In Proceedings of the IEEE conference on computer vision and pattern recognition, pages 2536-2544,2016. 1,4

[58] Aditya Ramesh,Mikhail Pavlov, Gabriel Goh,Scott Gray, Chelsea Voss,Alec Radford,Mark Chen,and Ilya Sutskever.

Zero-shot text-to-image generation.In International Conference on Machine Learning,pages 8821-8831.PMLR,2021. 4

[59] Rajesh PN Rao and Dana H Ballard.Predictive coding in the visual cortex:a functional interpretation of some extra-classical receptive-field effects. Nature neuroscience, 2(1):79-87,1999. 1

[60] Olga Russakovsky, Jia Deng,Hao Su,Jonathan Krause, San-jeev Satheesh, Sean Ma, Zhiheng Huang,Andrej Karpathy, Aditya Khosla,Michael Bernstein,Alexander C.Berg,and LiFei-Fei. Imagenet large scale visual recognition challenge. International Journal of Computer Vision,115(3):211-252, 2015.5,12

[61] Antti Tarvainen and Harri Valpola．Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results.arXiv preprint arXiv:1703.01780,2017.12

[62] Yuandong Tian,Xinlei Chen，and Surya Ganguli.Understanding self-supervised learning dynamics without contrastive pairs.In International Conference on Machine Learning,pages 10268-10278.PMLR,2021.13

[63] Hugo Touvron,Matthieu Cord,Matthijs Douze,Francisco Massa, Alexandre Sablayrolles,and Hervé Jégou. Training data-efficient image transformers & distillation through attention. In International Conference on Machine Learning, pages 10347-10357.PMLR,2021.3

[64] Michael Tschannen,Josip Djolonga,Paul K Rubenstein, Sylvain Gelly,and Mario Lucic．On mutual information maximization for representation learning．arXiv preprint arXiv:1907.13625,2019.13

[65] Grant Van Horn, Oisin Mac Aodha,Yang Song,Yin Cui, Chen Sun,Alex Shepard,Hartwig Adam,Pietro Perona,and Serge Belongie.The inaturalist species classification and detection dataset.In Proceedings of the IEEE conference on computer vision and pattern recognition,pages 8769-8778, 2018.12

[66] Ashish Vaswani,Noam Shazeer,Niki Parmar,Jakob Uszkoreit,Llion Jones,Aidan N Gomez,Lukasz Kaiser,and Illia Polosukhin.Attention is all you need.In Advances in neural information processing systems,pages 5998-6008,2017.3

[67] Pascal Vincent,Hugo Larochelle,Isabelle Lajoie,Yoshua Bengio，Pierre-Antoine Manzagol, and Léon Bottou. Stacked denoising autoencoders: Learning useful representations in a deep network with a local denoising criterion. Journal of machine learning research,11(12),2010.1,4,13

[68] Chen Wei,Haoqi Fan,Saining Xie,Chao-Yuan Wu,Alan Yuille,and Christoph Feichtenhofer.Masked feature prediction for self-supervised visual pre-training.arXiv preprint arXiv:2112.09133,2021. 1,13

[69] Zhirong Wu,Yuanjun Xiong,Stella X Yu,and Dahua Lin. Unsupervised feature learning via non-parametric instance discrimination． In Proceedings of the IEEE conference on computer vision and pattern recognition,pages 3733-3742, 2018.13

[70] Qizhe Xie, Zihang Dai, Eduard Hovy, Minh-Thang Luong, and Quoc V Le.Unsupervised data augmentation. arXiv preprint arXiv:1904.12848,2019. 13

[71] Zhenda Xie, Zheng Zhang,Yue Cao,Yutong Lin, Jianmin Bao,Zhuliang Yao,Qi Dai,and Han Hu. Simmim:A simple framework for masked image modeling. arXiv preprint arXiv:2111.09886,2021.1,4

[72] Yang You,Igor Gitman,and Boris Ginsburg.Large batch training of convolutional networks,2017.12

[73] Sangdoo Yun,Dongyoon Han,Seong Joon Oh,Sanghyuk Chun,Junsuk Choe,and Youngjoon Yoo.Cutmix:Regularization strategy to train strong classifiers with localizable features.In Proceedingsof theIEEE/CVFInternational Conference on Computer Vision,pages 6023-6032,2019.16

[74] Jure Zbontar,Li Jing,Ishan Misra,Yann LeCun,and Stéphane Deny. Barlow twins:Self-supervised learning via redundancy reduction．arXiv preprint arXiv:2103.03230, 2021. 1, 3, 13

[75] Xiaohua Zhai,Joan Puigcerver,Alexander Kolesnikov, Pierre Ruyssen, Carlos Riquelme,Mario Lucic,Josip Djolonga,Andre Susano Pinto,Maxim Neumann,Alexey Dosovitskiy,Lucas Beyer, Olivier Bachem,Michael Tschannen, Marcin Michalski, Olivier Bousquet, Sylvain Gelly,and Neil Houlsby.A large-scale study of representation learning with the visual task adaptation benchmark,2O19.12

[76] Hongyi Zhang，Moustapha Cisse,Yann N Dauphin,and David Lopez-Paz.mixup:Beyond empirical risk minimization. Internatinoal Conference on Learning Representations, 2018.16

[77] Richard Zhang,Phillip Isola,and Alexei A Efros.Colorful image colorization.2016.4

[78] Bolei Zhou,Agata Lapedriza, Jianxiong Xiao,Antonio Torralba,and Aude Oliva.Learning deep features for scene recognition using places database.Advances in neural information processing systems,27,2014. 12

[79] Jinghao Zhou, Chen Wei, Huiyu Wang,Wei Shen, Cihang Xie,Alan Yuille,and Tao Kong．Ibot:Image bert pretraining with online tokenizer. International Conference on Learning Representations,2022. 2,4,5,6,12,13

## A. 实现细节

## A.1. 预训练

架构。I-JEPA 预训练中，我们为上下文编码器、目标编码器与预测器使用 Vision Transformer [29]（ViT）架构。上下文编码器与目标编码器对应标准 ViT 架构，而预测器被设计为轻量（窄）ViT 架构。具体而言，我们将预测器的嵌入维度固定为 384，同时保持自注意力头数与主干上下文编码器相同。对于较小的 ViT-B/16 上下文编码器，我们将预测器深度设为 6。对于 ViT-L/16、ViT-H/16 与 ViT-H/14 上下文编码器，预测器深度设为 12。最后，ViT-G/16 使用深度为 16 的预测器。I-JEPA 预训练不使用 [cls] token。评估时我们使用目标编码器并对其输出做平均池化，以产生全局图像表示。

优化。我们使用 AdamW [51] 优化上下文编码器与预测器的权重。默认批大小为 2048，学习率在预训练的前 15 轮内从 $1 0 ^ { - 4 }$ 线性升至 $1 0 ^ { - 3 }$，此后按余弦调度衰减至 $1 0 ^ { - 6 }$。遵循 [4,18]，权重衰减（weight decay）在整个预训练过程中从 0.04 线性升至 0.4。目标编码器权重在初始化时与上下文编码器权重相同，此后通过指数移动平均更新 [4,18,23,35,37,61]。我们使用 0.996 的动量值，并遵循 [4,18] 在整个预训练过程中将该值线性增至 1.0。

掩码。默认情况下，我们采样 4 个（可能重叠的）目标块掩码，尺度在 (0.15, 0.2) 范围内随机、长宽比在 (0.75, 1.5) 范围内随机。我们采样 1 个上下文块掩码，尺度在 (0.85, 1.0) 范围内随机、长宽比为单位值。随后我们剔除上下文块掩码中与 4 个目标块掩码中任何一个重叠的区域。上下文块掩码与目标块掩码对 mini-batch 中的每张图像独立采样。为确保高效的批处理，我们限制同一共位（co-located）GPU 上所有上下文掩码的尺寸一致。类似地，限制同一共位 GPU 上所有目标掩码的尺寸一致。掩码采样器使用 PyTorch [56] 中运行于 dataloader 进程内的 batch-collator 函数，仅用几行代码高效实现。简言之，每次迭代中，dataloader 返回一个图像的 mini-batch，以及每张图像的一组上下文与目标掩码，指明上下文视图与目标视图各自保留的 patch 索引。

## A.2. 下游任务

线性评估。在评估 iBOT [79]、DINO [18] 或 MAE [36] 等使用带附加 [cls] token 的 Vision Transformer [29] 的方法时，我们使用 VISSL [34] 的默认配置在 iNaturalist18 [65]、CIFAR100 [45]、Clevr/Count [42,75]、Clevr/Dist [42,75] 与 Places205 [78] 上评估所有模型。我们冻结编码器，并在以下表示中返回最佳数值：1) 最后一层 [cls] token 的表示；2) 最后 4 层 [cls] token 表示的拼接。对每种表示，我们尝试两种不同的头：1) 线性头，或 2) 前置批归一化（batch normalization）的线性头，并返回最佳数值。我们使用 VISSL [34] 的默认数据增强：随机缩放裁剪与水平翻转；例外是 Clevr/Count 与 Clevr/Dist，我们只使用中心裁剪与水平翻转，因为随机裁剪会干扰物体计数与距离估计的能力，会将关键物体从场景中移除。对 CIFAR100，我们将图像缩放到 224 × 224 像素，以保持 patch 数量与预训练时一致。

由于我们的 I-JEPA 实现使用不带 [cls] token 的 Vision Transformer 架构，我们调整默认 VISSL 评估配方，改用平均池化的 patch 表示而非 [cls] token。因此，我们报告以下表示中的最佳线性评估数值：1) 最后一层的平均池化 patch 表示；2) 最后 4 层平均池化 patch 表示的拼接。其余线性探测配方保持不变。

ImageNet 评估。为在 ImageNet [60] 上评估 I-JEPA，我们调整 VISSL 配方，使用平均池化表示而非 [cls] token。遵循 MAE [36]，我们使用 LARS [72] 优化器，批大小为 16384，训练线性探测头 50 轮。学习率采用阶梯式衰减，每 15 轮除以 10，并在三个不同的参考学习率 [0.01, 0.05, 0.001] 与两个权重衰减值 [0.0005, 0.0] 之间扫描。

低样本评估。为在 ImageNet-1% 低样本任务上评估我们的模型，我们调整 MAE [36] 的微调协议。我们在 ImageNet-1% 上用 AdamW 优化器与余弦学习率调度器对 ViT-L/H 模型微调 50 轮。批大小为 512，逐层学习率衰减为 0.75，标签平滑为 0.1。我们使用与 MAE 相同的默认 rand-augment 数据增强。与 MAE 的微调不同，我们不使用 mixup、cutmix、random erasing 或 drop path。对 I-JEPA，ViT-L/16 的学习率/权重衰减为 $3 \mathrm { e } ^ { - 5 } / 5 \mathrm { e } ^ { - 2 }$，ViT-H/14 为 $3 \mathrm { e } ^ { - 5 } / 4 \mathrm { e } ^ { - 1 }$，$\mathrm { V i T - H } / 1 6 _ { 4 4 8 }$ 为 $\bar { 3 } \mathrm { e } ^ { - 5 } / 4 \mathrm { e } ^ { - 1 }$。类似的低样本学习微调策略已在半监督学习语境下被 Semi-VIT 探索 [16]。

## B. 更广泛的相关工作

用联合嵌入架构进行视觉表示的自监督学习是一条活跃的研究线 [3, 10,12,18,23,24,35,37,54,69,79]。这些方法训练一对编码器，对同一图像的两个或多个视图输出相似嵌入。为避免病态解，许多流行的联合嵌入方法使用显式正则化 [5,10,18,20] 或架构约束 [24,35]。基于架构约束的防坍塌利用特定的网络设计选择来避免坍塌，例如在联合嵌入的一个分支中停止梯度流 [20]、在联合嵌入的一个分支中使用动量编码器（momentum encoder）[35]，或使用非对称预测头 [8,20,35]。近期工作 [62] 尝试在理论上理解（在某些简化设定下）带架构约束的联合嵌入方法如何在无显式正则化的情况下避免表示坍塌。

联合嵌入架构中典型的基于正则化的防坍塌方法试图最大化表示所占据的空间体积。这通常由 InfoMax [52] 原则驱动。事实上，无监督表示学习的一个长期信念是：所得表示应当对输入具有最大信息量，同时满足某些简单性约束 [33,5O]。前者常被称为信息最大化原则（InfoMax），后者有时被称为简约原则（parsimony principle）[52]。这样的表示学习方法已被提出数十年（如 [14]），历史上简单性约束通过鼓励学到的表示稀疏、低维或解耦（即表示向量的各维度应当统计独立）来强制 [33]。现代方法通过自监督损失项将简单性约束与 InfoMax 正则化相耦合 [6,40,41,44,55,64]。一个例子是广泛使用的视图不变性惩罚 [53]，常与独立性 [10,74] 或低维约束耦合，例如将表示投影到单位超球面 [20,35,37]。然而，尽管其广泛流行，InfoMax 原则也受到许多批评，特别是因为它不区分不同类型的信息（如噪声与语义）[2]。事实上，我们希望模型捕获的特征集合并不总是那些边际熵最高（信息含量最大）的特征。

与基于不变性预训练的贡献相正交，另一条工作线尝试通过人为掩蔽输入的一部分并训练网络重建隐藏内容来学习表示 [67]。自回归模型，尤其是去噪自编码器，从带噪视图预测干净的视觉输入 [8,9,19,36,67]。典型的目标是在像素级 [29,36,70]，或使用词元器（tokenizer）在 patch token 级 [9,68] 预测缺失输入。虽然这些工作展示了惊人的可扩展性，但与联合嵌入方法相比，它们通常学到语义抽象层次较低的特征 [4]。

最近，一批方法尝试将联合嵌入架构与基于重建的方法相结合 [30]，即将不变性预训练损失与 patch 级重建损失相结合，如 iBOT 方法 [79]。由于基于视图不变性的方法通常偏向学习全局图像表示，从而限制其在其它计算机视觉任务上的适用性，其想法是加入局部损失项可以提升在计算机视觉其它流行任务上的性能 [11,26,32]。对比预测编码（contrastive predictive coding）[55] 框架也与这条局部损失项的工作线密切相关。在图像语境下 [39]，其想法是用对比目标结合卷积网络来区分重叠的图像 patch 表示。具体而言，目标是鼓励某个图像 patch 的表示可预测其正下方的图像 patch，同时推开其它 patch 视图的表示。与该工作相反，所提出的 I-JEPA 方法是非对比的，不寻求区分图像 patch。其目标是从单个上下文块预测多个目标块的表示。这通过联合嵌入预测架构实现，使用一个以目标块在图像中位置对应的位置嵌入为条件的预测器网络。第 8 节的定性实验表明，我们架构中的预测器网络学会了正确执行这种局部到局部的区域特征映射，并学会正确捕获图像中的位置不确定性。

<table><tr><td colspan="2">目标</td><td>上下文</td><td></td></tr><tr><td>尺度</td><td>频次</td><td>尺度</td><td>Top-1</td></tr><tr><td>(0.075, 0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>19.2</td></tr><tr><td>(0.1, 0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>39.2</td></tr><tr><td>(0.125, 0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>42.4</td></tr><tr><td>(0.15, 0.2)</td><td>4</td><td>(0.85,1.0)</td><td>54.2</td></tr><tr><td>(0.2, 0.25)</td><td>4</td><td>(0.85, 1.0)</td><td>38.9</td></tr><tr><td>(0.2,0.3)</td><td>4</td><td>(0.85, 1.0)</td><td>33.6</td></tr></table>

表 8．多块掩码目标块尺寸的消融。1% ImageNet-1K 上的线性评估（仅用 1% 可用标签）；对 ViT-B/16 进行 300 轮 I-JEPA 预训练时消融多块目标块尺寸。只要上下文信息足够丰富，预测更大（语义的）块可提升低样本准确率。

<table><tr><td colspan="2">目标</td><td>上下文</td><td></td></tr><tr><td>尺度</td><td>频次</td><td>尺度</td><td>Top-1</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.40, 1.0)</td><td>31.2</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.65,1.0)</td><td>47.1</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.75,1.0)</td><td>49.3</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>54.2</td></tr></table>

表 9．多块掩码上下文尺寸的消融。1% ImageNet-1K 上的线性评估（仅用 1% 可用标签）；对 ViT-B/16 进行 300 轮 I-JEPA 预训练时消融多块上下文尺寸。减小多块上下文尺寸会降低低样本性能。

<table><tr><td colspan="2">目标</td><td>上下文</td><td></td></tr><tr><td>尺度</td><td>频次</td><td>尺度</td><td>Top-1</td></tr><tr><td>(0.15,0.2)</td><td>1</td><td>(0.85,1.0)</td><td>9.0</td></tr><tr><td>(0.15,0.2)</td><td>2</td><td>(0.85, 1.0)</td><td>22.0</td></tr><tr><td>(0.15, 0.2)</td><td>3</td><td>(0.85,1.0)</td><td>48.5</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>54.2</td></tr></table>

表 10．多块掩码目标数量的消融。1% ImageNet-1K 上的线性评估（仅用 1% 可用标签）；对 ViT-B/16 进行 300 轮 I-JEPA 预训练时消融多块目标的数量。增加目标块的数量可提升低样本准确率。

## C. 附加消融

本节遵循与第 9 节相同的实验协议。我们报告在低样本 1% ImageNet-1K 基准上以冻结主干训练线性探测的结果。

多块掩码策略。我们给出多块掩码策略的扩展消融，其中改变目标块尺度（表 8）、上下文尺度（表 9）与目标块数量（表 10）。我们用 I-JEPA 以各种多块设置对 ViT-B/16 训练 300 轮，并用线性探测比较在 1% ImageNet-1K 基准上的性能。简言之，我们发现：预测若干相对较大（语义的）目标块，并使用信息足够丰富（空间上分布）的上下文块，这两点都很重要。

目标编码器输出处的掩码。I-JEPA 中一个重要的设计选择是：目标块通过掩码目标编码器的输出获得，而非掩码其输入。表 11 展示了用 I-JEPA 对 ViT-H/16 预训练 300 轮时，该设计选择对所学表示语义层次的影响。在掩码施加于输入的情形，我们对每个目标区域分别通过目标编码器做一次前向传播。预训练中掩码目标编码器的输出会产生更具语义的预测目标，并提升线性探测性能。

<table><tr><td>目标掩码位置</td><td>架构</td><td>训练轮数（Epochs）</td><td>Top-1</td></tr><tr><td> 输出</td><td>ViT-H/16</td><td>300</td><td>67.3</td></tr><tr><td>输入</td><td>ViT-H/16</td><td>300</td><td>56.1</td></tr></table>

表 11．目标编码器输出掩码的消融。ImageNet-1K 上仅用 1% 可用标签的线性评估；对 ViT-H/16 进行 300 轮 I-JEPA 预训练时消融掩码目标编码器输出的效果。预训练中掩码目标编码器的输出显著提升预训练表示的线性探测性能。

预测器深度。我们在表 12 中考察预测器深度对下游低样本性能的影响。我们分别使用 6 层预测器网络与 12 层预测器网络对 ViT-L/16 预训练 500 轮。与用较浅预测器预训练的模型相比，用更深预测器预训练的模型在下游低样本性能上有显著提升。

<table><tr><td>预测器深度</td><td>架构</td><td>训练轮数（Epochs）</td><td>Top-1</td></tr><tr><td>6</td><td>ViT-L/16</td><td>500</td><td>64.0</td></tr><tr><td>12</td><td>ViT-L/16</td><td>500</td><td>66.9</td></tr></table>

表 12．预测器深度的消融。ImageNet-1K 上仅用 1% 可用标签的线性评估；对预训练 500 轮的 ViT-L/16 消融预测器深度的效果。增加预测器深度带来预训练表示线性探测性能的显著提升。

权重衰减。表 13 中，我们评估预训练中权重衰减的影响。我们探索两种权重衰减策略：从 0.04 线性升至 0.4，或使用固定的 0.05。预训练中使用较小的权重衰减可在微调时提升 ImageNet-1% 的下游性能，但也导致线性评估的性能退化。正文中我们采用第一种权重衰减策略，因为它能提升线性评估下游任务的性能。

<table><tr><td>权重衰减</td><td>架构</td><td>训练轮数（Epochs）</td><td>ImageNet-1%</td><td>ImageNet 线性评估</td></tr><tr><td>0.04 → 0.4</td><td>ViT-L/16</td><td>600</td><td>69.4</td><td>77.8</td></tr><tr><td>0.05</td><td>ViT-L/16</td><td>600</td><td>70.7</td><td>76.4</td></tr></table>

表 13．预训练权重衰减的消融。我们将默认的预训练权重衰减策略（从 0.04 线性升至 0.4）与固定为 0.05 的权重衰减进行比较。预训练中使用较小的权重衰减可以提升 ImageNet-1% 上的微调性能，但也导致线性评估的性能下降。

预测器宽度。我们在表 14 中探索预测器宽度的影响。我们将使用 ViT-L 编码器与 384 通道预测器（译注：原文印作 386，系笔误，按表 14 与附录 A.1 的 384 径改）的 I-JEPA，与使用 1024 通道预测器的类似模型进行比较。注意 ViT-L 编码器有 1024 个通道。预测器宽度上的瓶颈可提升 ImageNet 1% 的下游性能。

<table><tr><td>预测器宽度</td><td>架构</td><td>训练轮数（Epochs）</td><td>Top-1</td></tr><tr><td>384</td><td>ViT-L/16</td><td>600</td><td>70.7</td></tr><tr><td>1024</td><td>ViT-L/16</td><td>600</td><td>68.4</td></tr></table>

表 14．预测器宽度的消融。我们报告 ImageNet-1K 1% 上使用微调的结果。我们比较宽度分别为 384 与 1024 的两个预测器。注意 I-JEPA 编码器是具有 1024 个通道的 ViT-L。预测器中的宽度瓶颈可提升下游性能。

## D. 在完整 ImageNet 上微调

本节报告 I-JEPA 在完整 ImageNet 数据集上微调的性能。我们聚焦于 ViT-$\mathrm { H } / 1 6 _ { 4 4 8 }$，因为该架构在 MAE [36] 上取得了最先进的性能。

我们使用与 MAE 类似的微调协议。具体而言，我们用 AdamW 与余弦学习率调度对模型微调 50 轮。基础学习率设为 $1 0 ^ { - 4 }$，批大小为 528。训练使用设为 0.8 的 mixup [76]、设为 1.0 的 cutmix [73]、0.25 的 drop path 概率以及设为 0.04 的权重衰减。我们还使用 0.75 的逐层衰减。最后，我们使用与 MAE 相同的 rand-augment 数据增强。

表 15 报告了微调结果。I-JEPA 取得 87.1 的 top-1 准确率。尽管 I-JEPA 的训练轮数比 MAE 少 5.3 倍，其性能与最佳 MAE 模型相差不到 1%。该结果表明，I-JEPA 在完整 ImageNet 数据集上微调时具有竞争力。

<table><tr><td>方法</td><td>架构</td><td>训练轮数（Epochs）</td><td>Top-1</td></tr><tr><td>MAE [36]</td><td> $\mathrm { V i T - H } / 1 4 _ { 4 4 8 }$ </td><td>1600</td><td>87.8</td></tr><tr><td>I-JEPA</td><td> $\mathrm { V i T  – H } / 1 6 _ { 4 4 8 }$ </td><td>300</td><td>87.1</td></tr></table>

表 15．在完整 ImageNet 数据集上微调。I-JEPA 取得有竞争力的性能。尽管 I-JEPA 的训练轮数比 MAE 少 5.3 倍，I-JEPA 仍接近 MAE 方法。

## E. RCDM 可视化

为在像素空间中可视化预训练神经网络的表示，我们使用 RCDM 框架 [13]。RCDM 框架训练一个解码器网络 $h _ { \omega }$——包含一个生成式扩散模型——从图像的表示向量 ${ \pmb s } _ { x }$ 与该图像的带噪版本 ${ \hat { \pmb { x } } } : = { \pmb { x } } + { \epsilon }$ 重建图像 x，其中 $\epsilon$ 是加性噪声向量。具体地，解码器的目标是最小化损失函数 $\| h _ { \omega } ( \hat { \pmb x } , \pmb s _ { x } ) - \epsilon \|$。我们使用默认超参数 [13] 训练每个 RCDM 网络 300,000 次迭代。解码器训练完成后，可将未见测试图像的表示向量 $s _ { y }$ 连同各种随机噪声向量送入解码器，生成该表示的多个像素级可视化，从而揭示预训练网络表示中所捕获的特征。跨样本共同的性质表示表示中包含的信息；而跨样本变化的性质表示表示中未包含的信息。

在图 6 中，可视化通过将以特定目标区域为条件的预测器平均池化输出、连同各种随机噪声向量送入解码器网络得到。在图 7 与图 8 中，可视化通过将目标编码器的平均池化输出、连同各种随机噪声向量送入解码器网络得到。

## E.1. 编码器可视化

图 7 中，我们可视化 ViT-H/14 目标编码器输出处的平均池化 I-JEPA 表示。第一列是原始图像，后续各列是将该图像的平均池化表示连同各种随机噪声向量送入解码器得到的合成样本。图 7 表明，I-JEPA 目标编码器能够正确捕获物体及其姿态的高层信息，同时丢弃低级图像细节与背景信息。

图 8 展示了相似的可视化，但使用 MSN [4] 预训练的 ViT-L/7 目标编码器计算图像表示。MSN 方法使用联合嵌入架构训练上下文编码器与目标编码器，以强制全局图像表示对各种手工设计的数据增强与缺失 patch 保持不变。虽然 MSN 预训练网络能够捕获第一列图像的高层语义信息，但其生成的样本表现出更高的可变性，例如物体姿态、物体尺度与实例数量上的可变性。简言之，MSN 预训练丢弃了图像中大量的局部结构，这与 I-JEPA 形成鲜明对比——I-JEPA 保留了输入图像中大量局部结构的信息。

[图：图 7]  
图 7．I-JEPA 目标编码器表示的可视化。对每张图像：第一列是原始图像；后续各列是生成模型对预训练 I-JEPA 目标编码器的平均池化输出解码得到的样本。跨样本共同的性质表示 I-JEPA 表示中包含的信息。I-JEPA 能够正确捕获物体及其姿态的高层信息。跨样本变化的性质表示表示中未包含的信息。I-JEPA 编码器丢弃了精确的低级细节以及背景信息。

[图：图 8]  
图 8．MSN 目标编码器表示的可视化。对每张图像：第一列是原始图像；后续各列是生成模型对冻结 MSN 编码器 [4] 输出解码得到的样本。跨样本共同的性质表示表示中包含的信息。跨样本变化的性质表示 MSN 未捕获的信息。与 I-JEPA 相比，MSN 样本表现出更高的可变性。MSN 保留的输入信息更少，特别是它丢弃了全局结构信息，如物体姿态甚至实例数量。

## 笔记配图

![img_001](./2023-I-JEPA.assets/img_001.png)
![img_002](./2023-I-JEPA.assets/img_002.png)
![img_003](./2023-I-JEPA.assets/img_003.png)
![img_004](./2023-I-JEPA.assets/img_004.png)
![img_005](./2023-I-JEPA.assets/img_005.png)
![img_006](./2023-I-JEPA.assets/img_006.png)
![img_007](./2023-I-JEPA.assets/img_007.png)
![img_008](./2023-I-JEPA.assets/img_008.png)
![img_009](./2023-I-JEPA.assets/img_009.png)
![img_010](./2023-I-JEPA.assets/img_010.png)
![img_011](./2023-I-JEPA.assets/img_011.png)
![img_012](./2023-I-JEPA.assets/img_012.png)
![img_013](./2023-I-JEPA.assets/img_013.png)
![img_014](./2023-I-JEPA.assets/img_014.png)
![img_015](./2023-I-JEPA.assets/img_015.jpeg)
![img_016](./2023-I-JEPA.assets/img_016.jpeg)
![img_017](./2023-I-JEPA.assets/img_017.jpeg)
![img_018](./2023-I-JEPA.assets/img_018.jpeg)
![img_019](./2023-I-JEPA.assets/img_019.jpeg)
![img_020](./2023-I-JEPA.assets/img_020.jpeg)
![img_021](./2023-I-JEPA.assets/img_021.jpeg)
![img_022](./2023-I-JEPA.assets/img_022.jpeg)
![img_023](./2023-I-JEPA.assets/img_023.jpeg)
![img_024](./2023-I-JEPA.assets/img_024.jpeg)
![img_025](./2023-I-JEPA.assets/img_025.jpeg)
![img_026](./2023-I-JEPA.assets/img_026.jpeg)
![img_027](./2023-I-JEPA.assets/img_027.jpeg)
![img_028](./2023-I-JEPA.assets/img_028.jpeg)
![img_029](./2023-I-JEPA.assets/img_029.jpeg)
![img_030](./2023-I-JEPA.assets/img_030.jpeg)
![img_031](./2023-I-JEPA.assets/img_031.jpeg)
![img_032](./2023-I-JEPA.assets/img_032.jpeg)
![img_033](./2023-I-JEPA.assets/img_033.jpeg)
![img_034](./2023-I-JEPA.assets/img_034.jpeg)
![img_035](./2023-I-JEPA.assets/img_035.jpeg)
![img_036](./2023-I-JEPA.assets/img_036.jpeg)
![img_037](./2023-I-JEPA.assets/img_037.jpeg)
![img_038](./2023-I-JEPA.assets/img_038.jpeg)
![img_039](./2023-I-JEPA.assets/img_039.jpeg)
![img_040](./2023-I-JEPA.assets/img_040.jpeg)
![img_041](./2023-I-JEPA.assets/img_041.jpeg)
![img_042](./2023-I-JEPA.assets/img_042.jpeg)
![img_043](./2023-I-JEPA.assets/img_043.jpeg)
![img_044](./2023-I-JEPA.assets/img_044.jpeg)
![img_045](./2023-I-JEPA.assets/img_045.jpeg)
![img_046](./2023-I-JEPA.assets/img_046.jpeg)
![img_047](./2023-I-JEPA.assets/img_047.jpeg)
![img_048](./2023-I-JEPA.assets/img_048.jpeg)
![img_049](./2023-I-JEPA.assets/img_049.jpeg)
![img_050](./2023-I-JEPA.assets/img_050.jpeg)
![img_051](./2023-I-JEPA.assets/img_051.jpeg)
![img_052](./2023-I-JEPA.assets/img_052.jpeg)
![img_053](./2023-I-JEPA.assets/img_053.jpeg)
![img_054](./2023-I-JEPA.assets/img_054.jpeg)
![img_055](./2023-I-JEPA.assets/img_055.jpeg)
![img_056](./2023-I-JEPA.assets/img_056.jpeg)
![img_057](./2023-I-JEPA.assets/img_057.jpeg)
![img_058](./2023-I-JEPA.assets/img_058.jpeg)
![img_059](./2023-I-JEPA.assets/img_059.jpeg)
![img_060](./2023-I-JEPA.assets/img_060.jpeg)
![img_061](./2023-I-JEPA.assets/img_061.jpeg)
![img_062](./2023-I-JEPA.assets/img_062.jpeg)
![img_063](./2023-I-JEPA.assets/img_063.jpeg)
![img_064](./2023-I-JEPA.assets/img_064.jpeg)
![img_065](./2023-I-JEPA.assets/img_065.jpeg)
![img_066](./2023-I-JEPA.assets/img_066.jpeg)
![img_067](./2023-I-JEPA.assets/img_067.jpeg)
![img_068](./2023-I-JEPA.assets/img_068.jpeg)
![img_069](./2023-I-JEPA.assets/img_069.jpeg)
![img_070](./2023-I-JEPA.assets/img_070.jpeg)
![img_071](./2023-I-JEPA.assets/img_071.jpeg)
![img_072](./2023-I-JEPA.assets/img_072.jpeg)
![img_073](./2023-I-JEPA.assets/img_073.jpeg)
![img_074](./2023-I-JEPA.assets/img_074.jpeg)
![img_075](./2023-I-JEPA.assets/img_075.jpeg)
![img_076](./2023-I-JEPA.assets/img_076.jpeg)
![img_077](./2023-I-JEPA.assets/img_077.jpeg)
![img_078](./2023-I-JEPA.assets/img_078.jpeg)
![img_079](./2023-I-JEPA.assets/img_079.jpeg)
![img_080](./2023-I-JEPA.assets/img_080.jpeg)
![img_081](./2023-I-JEPA.assets/img_081.jpeg)
![img_082](./2023-I-JEPA.assets/img_082.jpeg)
![img_083](./2023-I-JEPA.assets/img_083.jpeg)

## 术语对照表

| 英文 | 中文 |
|---|---|
| JEPA (Joint-Embedding Predictive Architecture) | 联合嵌入预测架构（JEPA 不译） |
| I-JEPA (Image-based JEPA) | 基于图像的联合嵌入预测架构（I-JEPA 不译） |
| self-supervised learning | 自监督学习 |
| representation | 表示 |
| embedding | 嵌入 |
| latent space | 潜空间 |
| latent variable | 潜变量 |
| context encoder | 上下文编码器 |
| target encoder | 目标编码器 |
| predictor | 预测器 |
| representation collapse | 表示坍塌 |
| masking / mask | 掩码 |
| mask token | 掩码 token |
| context block | 上下文块 |
| target block | 目标块 |
| patch | patch（图像块，保留原文） |
| complement（of the image） | （图像）补集 |
| view | 视图 |
| invariance-based | 基于不变性 |
| view-invariance | 视图不变性 |
| data augmentation | 数据增强 |
| hand-crafted | 手工设计的 |
| generative / non-generative | 生成式 / 非生成式 |
| Energy-Based Model (EBM) | 基于能量的模型 |
| energy | 能量 |
| Joint-Embedding Architecture (JEA) | 联合嵌入架构 |
| compatible / incompatible inputs | 相容 / 不相容输入 |
| contrastive loss | 对比损失 |
| non-contrastive loss | 非对比损失 |
| contrastive predictive coding | 对比预测编码 |
| clustering-based | 基于聚类的 |
| exponential moving average (EMA) | 指数移动平均 |
| momentum encoder | 动量编码器 |
| Vision Transformer (ViT) | ViT（不译） |
| self-attention | 自注意力 |
| positional embedding | 位置嵌入 |
| multi-block masking | 多块掩码 |
| rasterized masking | 光栅化掩码 |
| random masking | 随机掩码 |
| masked image modeling | 掩码图像建模 |
| mask-denoising | 掩码去噪 |
| denoising autoencoder | 去噪自编码器 |
| reconstruction | 重建 |
| tokenizer / tokenized space | 词元器 / 词元化空间 |
| token | token（不译） |
| linear probing / linear evaluation | 线性探测 / 线性评估 |
| fine-tuning / partial fine-tuning | 微调 / 部分微调 |
| end-to-end fine-tuning | 端到端微调 |
| off-the-shelf | 开箱即用 |
| semi-supervised | 半监督 |
| low-shot | 低样本 |
| transfer learning | 迁移学习 |
| downstream task | 下游任务 |
| object counting | 物体计数 |
| depth prediction | 深度预测 |
| dense prediction | 稠密预测 |
| semantic | 语义的 |
| inductive bias | 归纳偏置 |
| aspect ratio | 长宽比 |
| scale | 尺度 |
| average pooling / average-pool | 平均池化 |
| batch normalization | 批归一化 |
| weight decay | 权重衰减 |
| label smoothing | 标签平滑 |
| learning rate schedule | 学习率调度 |
| layer decay | 逐层衰减 |
| InfoMax principle | 信息最大化原则 |
| parsimony principle | 简约原则 |
| scalability | 可扩展性 |
| ablation | 消融 |
