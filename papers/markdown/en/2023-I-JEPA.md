# 2023-I-JEPA

> **来源**：../2023-I-JEPA.pdf ｜ **转换**：MinerU agent API（视觉结构恢复，含 OCR） ｜ 英文原文 markdown

# Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture

Mahmoud Assran1,2,3 \* Quentin Duval1Ishan Misra1Piotr Bojanowski1   
Pascal Vincent1Michael Rabbat1,3 Yann LeCun1,4Nicolas Ballas1

1Meta AI(FAIR)²McGil University3 Mila, Quebec AI Institute4New York University

## Abstract

This paper demonstrates an approach for learning highly semantic image representations without relying on hand-crafted data-augmentations. We introduce the Imagebased Joint-Embedding Predictive Architecture (I-JEPA),a non-generative approach for self-supervised learning from images. The idea behind I-JEPA is simple: from a single context block,predict the representations of various target blocks in the same image. A core design choice to guide I-JEPA towards producing semantic representations is the masking strategy;specifically,it is crucial to (a) sample target blocks with sufficiently large scale (semantic),and to (b) use asufficiently informative(spatially distributed) context block. Empirically,when combined with Vision Transformers,we findI-JEPA to be highly scalable.For instance,we train a ViT-Huge/l4 on ImageNet using l6 Al0O GPUs in under 72 hours to achieve strong downstream performance across a wide range of tasks,from linear classification to object counting and depth prediction.

## 1. Introduction

In computer vision,there are two common families of approaches for self-supervised learning from images: invariance-based methods [1,4,10,17,18,24,35,37,74] and generative methods [8,28,36,57].

Invariance-based pretraining methods optimize an encoder to produce similar embeddings for two or more views of the same image [15,20],with image views typically constructed using a set of hand-crafted data augmentations, such as random scaling,cropping,and color jittering [20], amongst others [35]. These pretraining methods can produce representations of a high semantic level [4,18], but they also introduce strong biases that may be detrimental for certain downstream tasks or even for pretraining tasks with different data distributions [2]. Often, it is unclear how to generalize these biases for tasks requiring different levels of abstraction.For example, image classification and instance segmentation do not require the same invariances [11]. Additionally, it is not straightforward to generalize these image-specific augmentations to other modalities such as audio.

<!-- image-->  
Figure 1.ImageNet Linear Evaluation. The I-JEPA method learns semantic image representations without using any view data augmentations during pretraining.By predicting in representation space,I-JEPA produces semantic representations while using less compute than previous methods.

Cognitive learning theories have suggested that a driving mechanism behind representation learning in biological systems is the adaptation of an internal model to predict sensory input responses [31,59]. This idea is at the core of self-supervised generative methods,which remove or corrupt portions of the input and learn to predict the corrupted content [9,36,57,67, 68,71]. In particular, maskdenoising approaches learn representations by reconstructing randomly masked patches from an input, either at the pixel or token level.Masked pretraining tasks require less prior knowledge than view-invariance approaches and easily generalize beyond the image modality [8]. However, the resulting representations are typically of a lower semantic level and underperform invariance-based pretraining in offthe-shelf evaluations (e.g.，linear-probing） and in transfer settings with limited supervision for semantic classification tasks [4]. Consequently,a more involved adaptation mechanism (e.g.,end-to-end fine-tuning) is required to reap the full advantage of these methods.

<!-- image-->  
Figure2.Commonarchitecturesforself-supervisedlearing,inwhichthesystemleas tocapturethrelationsipsbetwenitsinputs. Theobjectiveistassahignergy(argesalervauetoicompatible inputsdtosiglwnergyowalervae)patibleinputs.(a)JointEmbeddingArcitecturesatooutputsilarembeddingsforompatibleinputs,yanddisiilarebeddings foricompatibleinputs.(b)GenerativeArchitecturesleatodirectlyreconstructasignalyfromacompatiblesignalx,usingadecoder networkthatisonditiododitioal(ossblyatentarblestoacilitateecostructi.(cointmbedgPrdicticite turesleartopredicttheembeddingsofasignalyfromacompatiblesignalx,usingapredictrnetwork thatisconditionedonaditional (possibly latent) variables z to facilitate prediction.

In this work,we explore how to improve the semantic level of self-supervised representations without using extra prior knowledge encoded through image transformations. To that end, we introduce a joint-embedding predictive architecture [48] for images (I-JEPA).An illustration of the method is provided in Figure 3.The idea behindI-JEPA is to predict missing information in an abstract representation space; e.g.，given a single context block,predict the representations of various target blocks in the same im-age, where target representations are computed by a learned target-encoder network.

Compared to generative methods that predict in pixel/token space, I-JEPA makes use of abstract prediction targets for which unnecessary pixel-level details are potentially eliminated, thereby leading the model to learn more semantic features. Another core design choice to guide I-JEPA towards producing semantic representations is the proposed multi-block masking strategy. Specifically,we demonstrate the importance of predicting sufficiently large target blocks in the image,using an informative (spatially distributed) context block.

Through an extensive empirical evaluation, we demonstrate that:

·I-JEPA learns strong off-the-shelf representations without the use of hand-crafted view augmentations (cf.Fig.1). I-JEPA outperforms pixel-reconstruction methods such as MAE [36] on ImageNet-1K linear probing，semi-supervised 1% ImageNet-1K,and semantic transfer tasks.

· I-JEPA is competitive with view-invariant pretraining approaches on semantic tasks and achieves better performance on low-level visions tasks such as object counting and depth prediction (Sections 5 and 6). By using a simpler model with less rigid inductive bias, I-JEPA is applicable to a wider set of tasks.

· I-JEPA is also scalable and effcient (Section 7).Pretraining a ViT-H/14 on ImageNet requires less than 1200 GPU hours,which is over 2.5× faster than a ViT-S/16 pretrained with iBOT [79] and over 10× more efficient thana ViT-H/14 pretrained with MAE.Predicting in representation space significantly reduces the total computation needed for self-supervised pretraining.

## 2. Background

Self-supervised learning is an approach to representation learning in which a system learns to capture the relationships between its inputs. This objective can be readilydescribed using the framework of Energy-Based Models (EBMs)[49] in which the self-supervised objective is to as-sign a high energy to incompatible inputs,and to assign a low energy to compatible inputs. Many existing generative and non-generative approaches to self-supervised learning can indeed be cast in this framework; see Figure 2.

Joint-Embedding Architectures. Invariance-based pretraining can be cast in the framework of EBMs using a Joint-Embedding Architecture (JEA),which learns to out-put similar embeddings for compatible inputs,x, y,and dissimilar embeddings for incompatible inputs; see Figure 2a. In the context of image-based pretraining, compatible x, y pairs are typically constructed by randomly applying handcrafted data augmentations to the same input image [20].

The main challenge with JEAs is representation collapse, wherein the energy landscape is flat (i.e., the encoder produces a constant output regardless of the input). During the past few years,several approaches have been investi-gated to prevent representation collapse, such as contrastive losses that explicitly push apart embeddings of negative examples [15,24,37], non-contrastive losses that minimize the informational redundancy across embeddings [1O,74],and clustering-based approaches that maximize the entropy of the average embedding [4,5,18]. There are also heuristic approaches that leverage an asymmetric architectural design between the x-encoder and y-encoder to avoid collapse [8,24,35].

Generative Architectures. Reconstruction-based methods for self-supervised learning can also be cast in the framework of EBMs using Generative Architectures；see Figure 2b. Generative Architectures learn to directly reconstruct a signal y from a compatible signal x,using a decoder network that is conditioned on an additional (possibly latent) variable z to facilitate reconstruction.In the context of image-based pretraining,one common approach in computer vision is to produce compatible x,y pairs using masking [9,38] where x is a copy of the image y, but with some of the patches masked. The conditioning variable z then corresponds to a set of (possibly learnable) mask and position tokens,that specifies to the decoder which image patches to reconstruct. Representation collapse is not a concern with these architectures as long as the informational capacity of z is low compared to the signal y.

Joint-Embedding Predictive Architectures. As shown in Figure 2c, Joint-Embedding Predictive Architectures [48] are conceptually similar to Generative Architectures; however,a key difference is that the loss function is applied in embedding space,not input space.JEPAs learn to predict the embeddings of a signal y from a compatible signal x, using a predictor network that is conditioned on an additional (possibly latent） variable z to facilitate prediction. Our proposed I-JEPA provides an instantiation of this architecture in the context of images using masking; see Figure 3.

In contrast to Joint-Embedding Architectures, JEPAs do not seek representations invariant to a set of hand-crafted data augmentations,but instead seek representations that are predictive of each other when conditioned on additional information z. However, as with Joint-Embedding Architectures, representation collapse is also a concern with JEPAs;we leverage an asymmetric architecture between the x- and y-encoders to avoid representation collapse.

## 3. Method

We now describe the proposed Image-based Joint-Embedding Predictive Architecture (I-JEPA), illustrated in Figure 3. The overall objective is as follows: given a context block,predict the representations of various target blocks in the same image. We use a Vision Transformer [29,63] (ViT） architecture for the context-encoder, target-encoder, and predictor. A ViT is composed of a stack of transformer layers, each consisting of a self-attention [66] operation followed by a fully-connected MLP. Our encoder/predictor architecture is reminiscent of the generative masked autoencoders (MAE) [36] method. However,one key difference is that the I-JEPA method is non-generative and the predictions are made in representation space.

<!-- image-->  
Figure 3.I-JEPA.The Image-based Joint-Embedding Predictive Architecture uses a single context block to predict the representations of various target blocks originating from the same image. The context encoder is a Vision Transformer (ViT),which only processes the visible context patches.The predictor is a narrow ViT that takes the context encoder output and,conditioned on positional tokens (shown in color),predicts the representations of a target block at a specific location. The target representations correspond to the outputs of the target-encoder, the weights of which are updated at each iteration via an exponential moving average of the context encoder weights.

Targets. We first describe how we produce the targets in the I-JEPA framework:in I-JEPA, the targets correspond to the representations of image blocks. Given an input image y, we convert it into a sequence of N non-overlapping patches,and feed this through the target-encoder $f _ { \bar { \theta } }$ to obtain a corresponding patch-level representation $\begin{array} { r l } { s _ { y } } & { { } = } \end{array}$ $\{ \pmb { s } _ { y _ { 1 } } , \dotsc , \pmb { s } _ { y _ { N } } \}$ where $\boldsymbol { s } _ { y _ { k } }$ is the representation associated with the $k ^ { \mathrm { { t h } } }$ patch.To obtain the targets for our loss,we randomly sample M (possibly overlapping) blocks from the target representations $s _ { y }$ ，We denote by $B _ { i }$ the mask corresponding of the $i ^ { \mathrm { t h } }$ block and by $\pmb { s } _ { y } ( i ) = \{ \pmb { s } _ { y _ { j } } \} _ { j \in B _ { i } }$ its patch-level representation. Typically,we set M equal to 4,and sample the blocks with a random aspect ratio in the range (O.75,1.5) and random scale in the range (0.15, 0.2). Note that the target blocks are obtained by masking the output of the target-encoder, not the input. This distinction is crucial to ensure target representations of a high semantic level; see, e.g., [8].

<!-- image-->  
Figure 4. Examples of our context and target-masking strategy. Given an image,we randomly sample 4 target blocks with scale in the range (O.15,O.2) and aspect ratio in the range (0.75,1.5). Next, we randomly sample a context block with scale in the range (0.85,1.O) and remove any overlapping target blocks. Under this strategy, the target-blocks are relatively semantic,and the contextblock is informative,yet sparse (eficient to process).

Context. Recall, the goal behind I-JEPA is to predict the target block representations from a single context block. To obtain the context in I-JEPA,we first sample a single block x from the image with a random scale in the range (0.85,1.O) and unit aspect ratio. We denote by $B _ { x }$ the mask associated with the context block x. Since the target blocks are sampled independently from the context block,there may be significant overlap. To ensure a non-trivial predic-tion task, we remove any overlapping regions from the context block. Figure 4 shows examples of various context and target blocks in practice.Next, the masked context block, x,is fed through the context encoder $f _ { \theta }$ to obtain a corresponding patch-level representation $\pmb { s } _ { x } = \{ \pmb { s } _ { x _ { j } } \} _ { j \in B _ { x } }$

Prediction. Given the output of the context encoder, $s _ { x } ,$ we wish to predict the M target block representations $\pmb { s } _ { y } ( 1 ) , \dots , \pmb { s } _ { y } ( M )$ ．To that end, for a given target block $\begin{array} { r } { { \pmb s } _ { y } ( i ) } \end{array}$ corresponding to a target mask $B _ { i }$ ，the predictor $g _ { \phi } ( \cdot , \cdot )$ takes as input the output of the context encoder ${ \pmb s } _ { x }$ and a mask token for each patch we wish to predict, $\{ m _ { j } \} _ { j \in B _ { i } }$ ，and outputs a patch-level prediction $\begin{array} { r } { \hat { s } _ { y } ( i ) = } \end{array}$ $\{ \hat { \pmb { s } } _ { y _ { j } } \} _ { j \in B _ { i } } \ = \ g _ { \phi } ( \pmb { s } _ { x } , \{ m _ { j } \} _ { j \in B _ { i } } )$ ．The mask tokens are parameterized by a shared learnable vector with an added positional embedding. Since we wish to make predictions for M target blocks,we apply our predictor M times, each time conditioning on the mask tokens corresponding to the target-block locations we wish to predict,and obtain predictions $\hat { \pmb { s } } _ { y } ( 1 ) , \dots , \hat { \pmb { s } } _ { y } ( M )$ ：

Loss. The loss is simply the average $L _ { 2 }$ distance between the predicted patch-level representations $\hat { s } _ { y } ( i )$ and the target patch-level representation $s _ { y } ( i ) ; \mathrm { i . e . }$ ，

$$
\frac { 1 } { M } \sum _ { i = 1 } ^ { M } D \left( \hat { \pmb { s } } _ { y } ( i ) , { \pmb { s } } _ { y } ( i ) \right) = \frac { 1 } { M } \sum _ { i = 1 } ^ { M } \sum _ { j \in B _ { i } } \| \hat { \pmb { s } } _ { y _ { j } } - { \pmb { s } } _ { y _ { j } } \| _ { 2 } ^ { 2 } .
$$

The parameters of the predictor, $\phi ,$ and context encoder, $\theta ,$ are learned through gradient-based optimization,while the parameters of the target encoder $\bar { \theta }$ are updated via an exponential moving average of the context-encoder parameters. The use of an exponential moving average target-encoder has proven essential for training JEAs with Vision Transformers [18,25,79], we find the same to be true for I-JEPA.

## 4. Related Work

A long line of work has explored visual representation learning by predicting the values of missing or corrupted sensory inputs.Denoising autoencoders use random noise as input corruption [67]. Context encoders regress an entire image region based on its surrounding [57]. Other works cast image colorization as a denoising task [46,47,77].

The idea of image denoising has recently been revis-ited in the context of masked image modelling [9,36,71], where a Vision Transformer [29] is used to reconstruct missing input patches. The work on Masked Autoencoders (MAE) [36] proposed an efficient architecture that only requires the encoder to process visible image patches. By reconstructing missing patches in pixels space,MAE achieves strong performance when fine-tuned end-to-end on large labeled datasets and exhibits good scaling properties. BEiT [9] predicts the value of missing patches in a tokenized space； specifically, tokenizing image patches using a frozen discreteVAE,which is trained on a dataset containing 250 million images [58]. Yet, pixel-level pre-training has been shown to outperform BEiT for fine-tuning [36]. Another work, SimMIM[71], explores reconstruction targets based on the classic Histogram of Gradients [27] feature space,and demonstrates some advantage over pixel space reconstruction. Different from those works,our representation space is learned during training through a Joint-Embedding Predictive Architecture. Our goal is to learn semantic representations that do not require extensive finetuning on downstream tasks.

Closest to our work is data2vec [8] and Context Autoencoders [25]. The data2vec method learns to predict the representation of missing patches computed through an online target encoder; by avoiding handcrafted augmentations, the method can be applied to diverse modalities with promising results in vision,text and speech.Context Autoencoders use an encoder/decoder architecture optimized via the sum of a reconstruction loss and an alignment constraint, which enforces predictability of missing patches in representation space. Compared to these methods,I-JEPA exhibits significant improvements in computational efficiency and learns more semantic off-the-shelf representations. Concurrent to our work,data2vec-v2 [7] explores efficient architectures for learning with various modalities.

<table><tr><td>Method</td><td>Arch.</td><td>Epochs</td><td>Top-1</td></tr><tr><td colspan="3">Methods without view data augmentations</td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>1600</td><td>77.3</td></tr><tr><td rowspan="3">MAE [36]</td><td>ViT-B/16</td><td>1600</td><td>68.0</td></tr><tr><td>ViT-L/16</td><td>1600</td><td>76.0</td></tr><tr><td>ViT-H/14</td><td>1600</td><td>77.2</td></tr><tr><td rowspan="2">CAE [22]</td><td>ViT-B/16</td><td>1600</td><td>70.4</td></tr><tr><td>ViT-L/16</td><td>1600</td><td>78.1</td></tr><tr><td rowspan="4">I-JEPA</td><td>ViT-B/16</td><td>600</td><td>72.9</td></tr><tr><td>ViT-L/16</td><td>600</td><td>77.5</td></tr><tr><td>ViT-H/14</td><td>300</td><td>79.3</td></tr><tr><td> $\mathrm { V i T - H / 1 6 _ { 4 4 8 } }$ </td><td>300</td><td>81.1</td></tr><tr><td colspan="4">Methods using extra view data augmentations</td></tr><tr><td>SimCLR v2 [21]</td><td>RN152 (2×)</td><td>800</td><td>79.1</td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>300</td><td>80.1</td></tr><tr><td></td><td></td><td></td><td></td></tr><tr><td>iBOT[79]</td><td>ViT-L/16</td><td>250</td><td>81.0</td></tr></table>

Table 1.ImageNet. Linear-evaluation on ImageNet-1k (the ViT-$\mathrm { H } / 1 6 _ { 4 4 8 }$ is pretrained at at a resolution of 448 × 448).I-JEPA improves linear probing performance compared to other methods that do not rely on hand-crafted view data-augmentations during pretraining.Moreover,I-JEPA demonstrates good scalability — the larger I-JEPA model matches the performance of view-invariance approaches without requiring view data-augmentations.

We also compare I-JEPA with various methods based on joint-embedding architectures; e.g.,DINO [18], MSN [4] and iBOT [79]. Theses methods rely on hand-crafted data augmentations during pretraining to learn semantic image representations. The work on MSN [4],uses masking as an additional data-augmentation during pretraining,while iBOT combines a data2vec-style patch-level reconstruction loss with the DINO view-invariance loss. Common to these approaches is the need to process multiple usergenerated views of each input image,thereby hindering scalability. By contrast, I-JEPA only requires processing a single view of each image. We find that a ViT-Huge/14 trained with I-JEPA requires less computational effort than a ViT-Small/16 trained with iBOT.

<table><tr><td>Method</td><td>Arch.</td><td>Epochs</td><td>Top-1</td></tr><tr><td colspan="4">Methods without view data augmentations</td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>1600</td><td>73.3</td></tr><tr><td>MAE [36]</td><td>ViT-L/16 ViT-H/14</td><td>1600 1600</td><td>67.1 71.5</td></tr><tr><td>I-JEPA</td><td>ViT-L/16 ViT-H/14  $\mathrm { V i T  – H / 1 6 _ { 4 4 8 } }$ </td><td>600 300 300</td><td>69.4 73.3 77.3</td></tr><tr><td>iBOT [79]</td><td>Methods using extra view data augmentations ViT-B/16</td><td>400</td><td>69.7</td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>300</td><td>70.0</td></tr><tr><td>SimCLR v2 [35]</td><td>RN151 (2×)</td><td>800</td><td>70.2</td></tr><tr><td></td><td></td><td></td><td></td></tr><tr><td>BYOL [35]</td><td>RN200 (2×)</td><td>800</td><td>71.2</td></tr><tr><td>MSN [4]</td><td>ViT-B/4</td><td>300</td><td>75.7</td></tr></table>

Table 2.ImageNet-1%.Semi-supervised evaluation on ImageNet-1K using only 1% of the available labels.Models are adapted via fine-tuning or linear-probing,depending on whichever works best for each respective method. $\mathrm { V i T - H } / 1 6 _ { 4 4 8 }$ is pretrained at at a resolution of 448 × 448.I-JEPA pretraining outperforms MAE which also does not rely on hand-crafted data-augmentations during pretraining.Moreover, I-JEPA benefits from scale.A ViT-H/16 trained at resolution 448 surpasses previous methods including methods that leverage extra hand-crafted data-augmentations.

## 5. Image Classification

To demonstrate that I-JEPA learns high-level representations without relying on hand-crafted data-augmentations, we report results on various image classification tasks using the linear probing and partial fine-tuning protocols.In this section,we consider self-supervised models that have been pretrained on the ImageNet-1K dataset [6O]. Pretraining and evaluation implementation details are described in the Appendix A.All I-JEPA models are trained at resolution $2 2 4 \times 2 2 4$ pixels,unless stated otherwise.

ImageNet-1K. Table 1 shows performance on the common ImageNet-1K linear-evaluation benchmark.After selfsupervised pretraining,the model weights are frozen and a linear classifier is trained on top using the full ImageNet-1K training set. Compared to popular methods such as Masked Autoencoders (MAE) [36], Context Autoencoders (CAE) [22],and data2vec [8], which also do not rely on ex-tensive hand-crafted data-augmentations during pretraining, we see that I-JEPA significantly improves linear probing performance,while using less computational effort (see section 7). By leveraging the improved efficiency of I-JEPA, we can train larger models that outperform the best CAE model while using a fraction of the compute.I-JEPA also benefits from scale; in particular, a ViT-H/16 trained at resolution 448 × 448 pixels matches the performance of viewinvariant approaches such as iBOT [79], despite avoiding the use of hand-crafted data-augmentations.

<table><tr><td>Method</td><td>Arch.</td><td>CIFAR100</td><td>Places205</td><td>iNat18</td></tr><tr><td colspan="5">Methods without view data augmentations</td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>81.6</td><td>54.6</td><td>28.1</td></tr><tr><td rowspan="2">MAE [36] I-JEPA</td><td>ViT-H/14</td><td>77.3</td><td>55.0</td><td>32.9</td></tr><tr><td>ViT-H/14</td><td>87.5</td><td>58.4</td><td>47.6</td></tr><tr><td colspan="5">Methods using extra view data augmentations</td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>84.9</td><td>57.9</td><td>55.9</td></tr><tr><td>iBOT [79]</td><td>ViT-L/16</td><td>88.3</td><td>60.4</td><td>57.3</td></tr></table>

Table 3.Linear-probe transfer for image classification.Linearevaluation on downstream image classification tasks.I-JEPA significantly outperforms previous methods that also do not use augmentations (MAE and data2vec),and decreases the gap with the best view-invariance-based methods that leverage hand-crafted data augmentations during pretraining.

Low-Shot ImageNet-1K. Table 2 shows performance on the 1% ImageNet benchmark. Here the idea is to adapt the pretrained models for ImageNet classification using only1% of the available ImageNet labels,corresponding to roughly 12 or13 images per class.Models are adapted via fine-tuning or linear-probing,depending on whichever works best for each respective method. I-JEPA outperforms MAE while requiring less pretraining epochs when using a similar encoder architecture.I-JEPA,using a ViT-H/14 architecture,matches the performance of a ViT-L/16 pretrained with data2vec [8],while using significantly less computational effort (see Section 7).By increasing the image input resolution,I-JEPA outperforms previous methods including joint-embedding methods that do leverage extra hand-crafted data-augmentations during pretraining, such as MSN [4],DINO [17],and iBOT[79].

Transfer learning. Table 3 shows performance on various downstream image classification tasks using a linear probe.I-JEPA significantly outperforms previous methods that do not use augmentations (MAE and data2vec),and decreases the gap with the best view-invariance-based methods,which leverage hand-crafted data augmentations during pretraining, even surpassing the popular DINO [18] on CIFAR100 and Place2O5 with a linear probe.

## 6.Local Prediction Tasks

As demonstrated in Section 5, I-JEPA learns semantic image representations that significantly improve the downstream image classification performance of previous methods,such as MAE and data2vec.Additionally, I-JEPA benefits from scale and can close the gap,and even surpass, view-invariance based methods that leverage extra handcrafted data augmentations. In this section,we find that I-JEPA also learns local image features and surpasses viewinvariance based methods on low-level and dense prediction tasks,such as object counting and depth prediction.

<table><tr><td>Method</td><td>Arch.</td><td>Clevr/Count</td><td>Clevr/Dist</td></tr><tr><td colspan="3">Methods without view data augmentations</td><td></td></tr><tr><td>data2vec [8]</td><td>ViT-L/16</td><td>85.3</td><td>71.3</td></tr><tr><td>MAE [36]</td><td>ViT-H/14</td><td>90.5</td><td>72.4</td></tr><tr><td>I-JEPA</td><td>ViT-H/14</td><td>86.7</td><td>72.4</td></tr><tr><td colspan="3">Methods using extra data augmentations</td><td></td></tr><tr><td>DINO [18]</td><td>ViT-B/8</td><td>86.6</td><td>53.4</td></tr><tr><td>iBOT[79]</td><td>ViT-L/16</td><td>85.7</td><td>62.8</td></tr></table>

Table 4．Linear-probe transfer for low-level tasks.Linearevaluation on downstream low-level tasks consisting of object counting (Clevr/Count) and depth prediction (Clevr/Dist). The I-JEPA method effectively captures low-level image features during pretraining and outperforms view-invariance based methods on tasks such object counting and depth prediction.

Table 4 shows performance on various low-level tasks using a linear probe.After pretraining, the encoder weights are frozen and a linear model is trained on top to perform object-counting and depth prediction on the Clevr dataset [43]. Compared to view-invariance methods such as DINO and iBOT,the I-JEPA method effectively captures low-level image features during pretraining and outperforms them in object counting(Clevr/Count) and (by a large margin) depth prediction (Clevr/Dist).

## 7. Scalability

Model Efficiency.I-JEPA is highly scalable compared to previous approaches.Figure 5 shows semi-supervised evaluation on 1% ImageNet-1K as a function of GPU hours. I-JEPA requires less compute than previous methods and achieves strong performance without relying on handcrafted data-augmentations. Compared to reconstructionbased methods, such as MAE,which directly use pixels as targets,I-JEPA introduces extra overhead by computing targets in representation space (about 7% slower time per iteration). However, since I-JEPA converges in roughly 5× fewer iterations,we still see significant compute savings in practice. Compared to view-invariance based methods, such as iBOT,which rely on hand-crafted data augmentations to create and process multiple views of each image,I-JEPA also runs significantly faster. In particular, a huge I-JEPA model (ViT-H/14) requires less compute than a small iBOT model (ViT-S/16).

Scaling data size. We also find I-JEPA to benefit from pretraining with larger datasets. Table 5 shows transfer learning performance on semantic and low level tasks when increasing the size of the pretraining dataset (IN1K versus IN22K). Transfer learning performance on these conceptually different tasks improves when pretraining on a larger more diverse dataset.

<table><tr><td>Pretrain</td><td>Arch.</td><td>CIFAR100</td><td>Place205</td><td>INat18</td><td>Clevr/Count</td><td>Clevr/Dist</td></tr><tr><td>IN1k</td><td>ViT-H/14</td><td>87.5</td><td>58.4</td><td>47.6</td><td>86.7</td><td>72.4</td></tr><tr><td>IN22k</td><td>ViT-H/14</td><td>89.5</td><td>57.8</td><td>50.5</td><td>88.6</td><td>75.0</td></tr><tr><td>IN22k</td><td>ViT-G/16</td><td>89.5</td><td>59.1</td><td>55.3</td><td>86.7</td><td>73.0</td></tr></table>

Table5.Ablatingdataset and modelsize.Evaluating impactof pre-trainingdataset sizeand model sizeontransfertasks.I-JEPA benefitsfromlargermorediversedatasets.Whenincreasingthesizeofthepretrainingdataset (I1kversusIN2k)weseeanpeformance improvementforthe Vi-H/14model.Weobserveafurtherperformanceimprovementonsemantictasks bytrainingalarger model Vi-G/16 modelonImageNet-22k.The ViT-H/14 is trainedfor300epochsonIN1kandtheequivalentof900IN1KepochsonIN22k.The ViT-H/16 is trained for the equivalent of 6Oo IN1k epochs.

<!-- image-->  
Figure 5.Scaling. Semi-supervised evaluation on ImageNet-1K 1% asa function of pretraining GPU hours. I-JEPA requires less compute than previous methods to achieve strong performance. Compared to MAE and data2vec,I-JEPA obtains a significant speedup by requiring fewer pretraining epochs. Compared to iBOT,which relies on hand-crafted data-augmentations,a huge I-JEPA model(ViT-H/14) requires less compute than their smallest model (ViT-S/16).

Scaling model size. Table 5 also shows that I-JEPA benefit from larger model size when pretraining on IN22K. Pretraining a ViT-G/16 significantly improves the downstream performances on image classification tasks such as Place205 and INat18 compared to a ViT-H/14 model, but does not improve performance on low-level downstream tasks — the ViT-G/16 uses larger input patches,which can be detrimental for the local prediction tasks.

## 8. Predictor Visualizations

The role of the predictor in I-JEPA is to take the output of the context encoder and,conditioned on positional mask tokens,to predict the representations of a target black at the location specified by the mask tokens. One natural question is whether the predictor conditioned on the positional mask tokens is learning to correctly capture positional uncertainty in the target. To qualitatively investigate this question, we visualize the outputs of the predictor. We use the following visualization approach to enable the research community to independently reproduce our findings.After pretraining, we freeze the context-encoder and predictor weights,and train a decoder following the RCDM framework[13] to map the average-pool of the predictor outputs back to pixel space. Figure 6 shows decoder outputs for various random seeds. Qualities that are common across samples represent information that is contained in the average-pooled predictor representation． The I-JEPA predictor correctly captures positional uncertainty and produces high-level object parts with the correct pose (e.g., back of the bird and top of the car).

## 9. Ablations

Predicting in representation space. Table 7 compares low-shot performance on 1% ImageNet-1K using a linear probe when the loss is computed in pixel-space versus representation space. We conjecture that a crucial component of I-JEPA is that the loss is computed entirely in representation space,thereby giving the target encoder the ability to produce abstract prediction targets,for which irrelevant pixel-level details are eliminated.From Table 7,it is clear that predicting in pixel-space leads to a significant degradation in the linear probing performance.

Masking strategy. Table 6 compare our multi-block maskingwithothermaskingstrategiessuchas rasterized masking,where the image is split into four large quadrants,and the goal is to use one quadrant as a context to predict the other three quadrants,and the traditional block and random masking typically used in reconstruction-based methods.In block masking, the target is a single image block and the context is the image complement. In random masking,the target is a set of random patches and the context is the image complement. Note that there is no overlap between the context and target blocks in all considered strategies. We find multi-block masking helpful for guiding I-JEPA to learning semantic representations.Additional ablations on multi-block masking can be found in Appendix C.

<!-- image-->  
Figure6.Visualizationof-JEPApredictorrepresentations.Foreach image:firstcolumncontains teoriginal imagescondcolum contains tecontextimage,whichisprocesedbyapretrainedI-JEPAViT-H/14encoderGreenbounding boxesinsubsequentcous containsamplesfromagenerativemodeldecodingtheoutputofthepretraiedI-JEPApredictor,whichisconditionedonpositionalmask tokenscorresponding tothelocationofthe greenbounding box.Qualities thatarecommonacrossamplesrepresent informationthat containedis intheI-JEPApredictio.TeI-JPApredictorisoectlycapturigpositionalucertaintyandproducinghg-evelbject parts withacorrectpose (e.g,tebackofthebirdandtopofacar).Quaitiesthatvaryacrossamplesrepresent informationthatis not containedinteepresetatioInisae,tJEApredictrdscadstepreiselow-vedtailsasellsackgrodfoatio

<table><tr><td rowspan="2">Mask</td><td colspan="2">Targets</td><td colspan="2">Context</td><td rowspan="2">Top-1</td></tr><tr><td>Type</td><td>Freq.</td><td>Type</td><td>Avg. Ratio*</td></tr><tr><td>multi-block</td><td>Block(0.15, 0.2)</td><td>4</td><td> Block(0.85,1.0) × Complement</td><td>0.25</td><td> 54.2</td></tr><tr><td>rasterized</td><td>Quadrant</td><td>3</td><td>Complement</td><td>0.25</td><td>15.5</td></tr><tr><td>block</td><td>Block(0.6)</td><td>1</td><td>Complement</td><td>0.4</td><td>20.2</td></tr><tr><td>random</td><td>Random(0.6)</td><td>1</td><td>Complement</td><td>0.4</td><td>17.6</td></tr></table>

\*Avg. Ratio is the average number ofpatches in thecontext block relativeto the totalnumberofpatches inthe image.

Table 6.AblatingmaskingstrategyLinearevaluationoImageNet-1Kusingonly1%of teavailablelabelsafter-JEPApretrainingof aViT-B/16for300 epochs.Comparisonofproposedmulti-block masking strategy.Inrasterizedmasking the image issplitito fourlargequadants;onequadrantisusedasacontexttopredicteother trquadrants.Inblck maskingthetargetisasinglemage block andthecontextis theimagecomplement.Inrandom masking,thetargetisasetofrandomimagepatchesand thecontextis the imagecomplement.The proposed multi-blockmasking strategyishelpfulforguidingI-JEPA tolearn semanticrepresentations.
<table><tr><td>Targets</td><td>Arch.</td><td>Epochs</td><td>Top-1</td></tr><tr><td> Target-Encoder Output</td><td>ViT-L/16</td><td>500</td><td>66.9</td></tr><tr><td>Pixels</td><td>ViT-L/16</td><td>800</td><td>40.7</td></tr></table>

Table 7.Ablating targets.Linear evaluation on ImageNet-1K using only 1% of the available labels.The semantic level of the I-JEPA representations degrades significantly when the loss is applied in pixel space,rather than representation space, highlighting the importance of the target-encoder during pretraining.

## 10. Conclusion

We proposed I-JEPA,a simple and efficient method for learning semantic image representations without relying on hand-crafted data augmentations. We show that by predicting in representation space,I-JEPA converges faster than pixel reconstruction methods and learns representations of a high semantic level. In contrast to view-invariance based methods,I-JEPA highlights a path for learning general representations with joint-embedding architectures,without relying on hand-crafted view augmentations.

## References

[1] Yuki Markus Asano,Christian Rupprecht,and Andrea Vedaldi. Self-labelling via simultaneous clustering and representation learning. Internatinoal Conference on Learning Representations,2020.1

[2] Mahmoud Assran,Randall Balestriero, Quentin Duval, Florian Bordes,Ishan Misra,Piotr Bojanowski,Pascal Vincent, Michael Rabbat,and Nicolas Ballas．The hidden uniform cluster prior in self-supervised learning. International Conference on Learning Representations,2023.1,13

[3] Mahmoud Assran，Nicolas Ballas，Lluis Castrejon，and Michael Rabbat. Supervision accelerates pre-training in contrastive semi-supervised learning of visual representations. NeurIPS Workshop on Self-Supervised Learning,2020.13

[4] Mahmoud Assran,Mathilde Caron,Ishan Misra,Piotr Bo-janowski,Florian Bordes,Pascal Vincent,Armand Joulin, Michael Rabbat,and Nicolas Ballas.Masked siamese networks for label-effcient learning. European Conference on Computer Vision,2022.1,2,3,5,6,12,13,16,17

[5] Mahmoud Assran,Mathilde Caron,Ishan Misra,Piotr Bo-janowski,Armand Joulin,Nicolas Ballas,and Michael Rabbat.Semi-supervised learning of visual features by nonparametrically predicting view assignments with support samples.IEEE/CVF International Conference on Computer Vision,2021.3,13

[6] Philip Bachman,R Devon Hjelm,and William Buchwalter. Learning representations by maximizing mutual information across views. Advances in neural information processing systems,32,2019.13

[7] Alexei Baevski,Arun Babu,Wei-Ning Hsu,and Michael Auli.Efficient self-supervised learning with contextualized target representations for vision,speech and language.arXiv preprint arXiv:2212.07525,2022. 5

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

## A.Implementation Details

## A.1. Pretraining

Architectures.ForI-JEPA pretraining,weuse Vision Transformer29](ViT)architectures for thecontext-encoder,targetencoder,and the predictor.Whilethecontext-encodersand target-encoders corespond tostandard ViTarchitectures,the predictor is designed as alight-weight (narrow） ViT architecture.Specifically，we fix the embedding dimensionof the predictor to384,while keeping the number ofself-atention heads equal to that of the backbone context-encoder.For the smaller ViT-B/16context-encoder,wesetthe depthof the predictorto6.For ViT-L/16,ViT-H/16,andViT-H/14contextencoders,wesetthedepthof the predictorto12.Finaly,the ViT-G/16usesapredictorof depth16.I-JEPA is pretrained without a[cls]token.We use the target-encoder for evaluation and average pool its output to produce a global image representation.

Optimization.We use AdamW[51] to optimize thecontext-encoder and predictor weights.Our default batch-size is 2048, and the learning rate is linearly increased from $1 0 ^ { - 4 }$ to $1 0 ^ { - 3 }$ during the first 15 epochs of pretraining,and decayed to $1 0 ^ { - 6 }$ followingacosineschedulethereafter.Following[4,18],the weight-decayis inearlyincreased from O.O4to0.4throughout pretraining.The target-encoder weightsare identical tothecontext-encoder weights atinitialization,andupdated viaan exponential moving average thereafter[4,18,23,35,37,61]. Weuse a momentumvalue of 0.996,and linearlyincrease this value to 1.0 throughout pretraining, following [4,18].

Masking.By default, we sample 4 posibly overlapping target blocks masks with random scale in the range (O15,0.2)and aspectratio in therange(0.75,1.5).We sample1 context block mask with random scale in therange (0.85,1.O)and unit aspectratio.We subsequentlyeliminate anyregions inthecontext block maskthat overlap with anyof the 4target block masks.The context-block mask and target-block masks are sampled independently for each image in the mini-batch.To ensureefficient batchprocessing,werestrict thesizeofallcontext masksonaco-located GPUtobe identical.Similarlywe restrict thesizeof alltarget masksonaco-located GPUtobe identical.Themask-sampleris effciently implemented inonly afewlines ofcode inPyTorch[56]usingabatch-collatorfunction,whichruns inthedataloaderprocesses.Inshort,ineach iteration,thedataloaderreturns amini-batchofimages andasetofcontext and target masks foreach image,identifying the patch indices to keep for the context and target views.

## A.2.Downstream Tasks

Linear evaluation.When evaluating methods such as iBOT[79],DINO[18]orMAE[36], which leverage Vision Transformers [29] with an aditional[cls]token,we use the default configurationsofVISSL[34]toevaluate all themodels on iNaturalist18[65],CIFAR100[45], Clevr/Count[42,75], Clevr/Dist [42,75],andPlaces205[78].We freze the encoder and return the best number among the following representations:1)the[cls]token representation ofthe lastlayer,2)the concatenationofthe last4layersof the[cls]token.Foreachrepresentation,we trytwodifferent heads:1)alinearhead, or 2)alinear head precededbya batch normalization,and returnthe best number.Weuse the default data augmentations of VISSL [34]:random resize cropping and horizontal flippng,with the exception of Clevr/Count and Clevr/Dist,where we onlyuse centercrop and horizontalfipping,as random cropping interferes with the capabilityof counting objects and estimating distance,removing critical objects from the scene.For CIFAR100,weresize the images to 224 ×224 pixels,so as to keep the number of patches equal to that used during pretraining.

Because our I-JEPA implementation uses Vision Transformer architectures without a[cls]token,we adapt the default VISSL evaluationrecipe to utilize the average-pooled patch representation insteadofthe[cls］token. We therefore report the best linear evaluation number among the folowing representations:1）the average-ooled patch representation of the last layer,2)the concatenation of the last 4layers of the average-pooled patch representations.We otherwise keep the linear-probing recipe identical.

ImageNet evaluations.To evaluate the I-JEPA on ImageNet[6O], weadaptthe VISSLrecipe to use average pooled epre-sentations instead ofthe[cls]token.Follwing MAE[36], we use the LARS[72]optimizer with abatch-sizeof 16384, andtrain the linear probefor 5Oepochs.Weusealearning rate with astep-wise decay,dividing it byafactorof 1O every15 epochs,and sweep threediferentreference learning rates [O.O1,0.05,0.0o1],andtwo weight decayvalues [0.0005,0.0].

Low-shot evaluation.To evaluate our model on the ImageNet-1% low-shottask,weadapt the fine-tuning protocol of MAE[36].We fine-tune our ViT-L/Hmodels for 50epochs onImageNet-1% with the AdamWoptimizerand acosine learning rate scheduler.Weuseabatch sizeof 512,alearming rate layer decayofO.75andO.1label smoothing.We use the default randaugment data-augmentations as in MAE.In contrast tothe fine-tuning done with MAE,we do not use mixup,cutmix, random erasing or drop path. For the I-JEPA, we use a learning rate /weight decay of $3 \mathrm { e } ^ { - 5 } / 5 \mathrm { e } ^ { - 2 }$ for the $\mathrm { V i T - L } / 1 6 , 3 \mathrm { e } ^ { - 5 } / 4 \mathrm { e } ^ { - 1 }$ for the ViT-H/14 and $\bar { 3 } \mathrm { e } ^ { - 5 } / 4 \mathrm { e } ^ { - 1 }$ for the $\mathrm { V i T - H } / 1 6 _ { 4 4 8 }$ . Similar fine-tuning strategy for low-shot learning has been explored by Semi-VIT in the context of semi-supervised learning [16].

## B.Broader Related Work

Self-supervised learning of visual representations with joint-embeding architectures is an active line of research [3, 10,12,18,23,24,35,37,54,69,79].Theseapproaches train a pair ofencoders tooutput similarembedings for twoor more views of the same image.To avoid pathological solution,many popular joint-embedding appoaches use explicit regularization[5,10,18,20]orarchitecturalconstraints[24,35].Collpse-preventionbasedonarchitecturalcostraints leverage specific network design choices toavoid collapse,for example,by stopping the gradient fow in one of the jointembedding branches[20],using amomentum encoder inone of the joint-embeddng branches [35],orusing anasymmetric prediction head[8,20,35].Recent work[62]attmpts totheoreticallunderstand (incertainsimplifedseings)howjointembedding methods with architectural constraints avoid representation collapse without explicit regularization.

Typical regularization-based approaches tocollapse prevention in joint-embeding architectures try to maximize the volume of space occupied by the representations.This is often motivated through the InfoMax [52] principle.Indeed,alongstanding conviction in unsupervised representation learning is thattheresulting representations should be both maximally informativeaboutheinputs,whilealsosatisfyingcertainsimplicityconstraints [33,5O].Thefrmerobjectiveisoftenefered to as the information-maximization principle (InfoMax),whilethe latter is sometimes refered toas the parsimony principle[52]. Suchappoaches toepresentationlearninghavebeenproposedfordecades (e.g.,14]),where,historicalysimplicityconstraints were enforced byencouragingthe learedrepresentations tobesparse,low-dimensional,ordisentangled,ie, the individual dimensions of therepresentation vectorshouldbestatistically independent [33]. Modern approaches enforce the simplicityconstraints coupled with InfoMaxregularization through self-supervised loss terms [6,40,41,44,55,64]. One example is the widespreadview-invariance penalty[53],oftencoupled withwithindependence[10,74]orlow-dimensioality constraints,e.g,byprojectingrepresentationsontheunithypersphere[2O,35,37].However,dspiteitsproliferation,there have also been manycriticisms of the IfoMax principle,speciallysince it is doesnot discriminate between different types of information(e.g,noise and semantics)[2]. Indeed,the sets offeatures we wishthe model tocapture arenotalways those with the highest marginal entropy (maximal information content).

Orthogonal to thecontributions of invariance-based pretraining,another line of work attempts to learn representations by artificially maskingpartsofthe input and traininganetwork toreconstruct the hiddencontent [67].Autoregresive models, and denoising autoencoders inparticular,predictclean visual inputs from noisyviews[8,9,19,36,67].Typicalythegoal is to predict missing inputs ata pixel level [29,36,70],or ata patch token-level,usingatokenizer[9,68].Whilethese works demonstrate impressive scalability,they usually learn features at a low-level of semantic abstraction compared to joint-embedding approaches [4].

More recently,a set of approaches attempt to combine both joint-embedding architectures and reconstruction based approaches [30],wherein theycombine an invariance pretraining losswith a patch-level reconstruction loss,as inthe iBOT method [79]. Since view-invariance based approaches are typically biased towards learning global image representations, therebylimiting their applicabilitytoother computer vision tasks,the ideais that addng local loss terms can improve performance on other popular tasks incomputer vision[11,26,32].Theframework ofcontrastive predictive coding[55] is also closelyrelated tothis lineof work onlocallossterms.Inthecontext ofimages39],herethe ideais touseacontrastive objectivecombined withaconvolutional network todiscriminate between overlapping image patch representations.Specificaly,the goal is toencourage therepresentations ofanimage patch tobe predictableofthe image patches directlybelow it,while pushing away the representations of other patch views.In contrast to that work,the proposed I-JEPA method is non-contrastive and does notseek to discriminate between image patches.Rather,the goal is to predictthe representations of various target blocks from asinglecontext block.This is achieved with aJoint-Embedding Predictive Architecture,using a predictor network that is conditionedon positional embeddings coresponding to the location of the target block inthe image.Qualitative experiments in Section 8 showthatthe predictor network inour architecture learns to corrctly perform this local-to-local region feature mapping, and learns to correctly capture positional uncertainty in the image.

<table><tr><td colspan="2">Targets</td><td>Context</td><td></td></tr><tr><td>Scale</td><td>Freq.</td><td>Scale</td><td>Top-1</td></tr><tr><td>(0.075, 0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>19.2</td></tr><tr><td>(0.1, 0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>39.2</td></tr><tr><td>(0.125, 0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>42.4</td></tr><tr><td>(0.15, 0.2)</td><td>4</td><td>(0.85,1.0)</td><td>54.2</td></tr><tr><td>(0.2, 0.25)</td><td>4</td><td>(0.85, 1.0)</td><td>38.9</td></tr><tr><td>(0.2,0.3)</td><td>4</td><td>(0.85, 1.0)</td><td>33.6</td></tr></table>

Table8.Ablationof the target block sizeformulti-block masking.Linear evaluationon1%ImageNet-1K(usingonly1%of the availablelabels);ablatingtheulti-blockagetsizeduringI-JEPprtraiingofaVi-B/6forepohs.Predictinglarger(satic) blocks improves the low-shot accuracy as long as the context is sufficiently informative.

<table><tr><td colspan="2">Targets</td><td>Context</td><td></td></tr><tr><td>Scale</td><td>Freq.</td><td>Scale</td><td>Top-1</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.40, 1.0)</td><td>31.2</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.65,1.0)</td><td>47.1</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.75,1.0)</td><td>49.3</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>54.2</td></tr></table>

Table 9.Ablationofthecontext size formulti-blockmasking.Linearevaluationon1%ImageNet-1K (usingonly1%oftheavailable labels);ablatingthult-locktargetszedurigIJPpretraigofVB/f3OepochReducigtheulti-blockcotextie degrades the low-shot performance.

<table><tr><td colspan="2">Targets</td><td>Context</td><td></td></tr><tr><td>Scale</td><td>Freq.</td><td>Scale</td><td>Top-1</td></tr><tr><td>(0.15,0.2)</td><td>1</td><td>(0.85,1.0)</td><td>9.0</td></tr><tr><td>(0.15,0.2)</td><td>2</td><td>(0.85, 1.0)</td><td>22.0</td></tr><tr><td>(0.15, 0.2)</td><td>3</td><td>(0.85,1.0)</td><td>48.5</td></tr><tr><td>(0.15,0.2)</td><td>4</td><td>(0.85, 1.0)</td><td>54.2</td></tr></table>

Table10.Ablationof the targets number formulti-block masking.Linearevaluationon1%ImageNet-1K (using only1%of the availablelabels);ablatingthemulti-blocknumberof targetsduringI-JEPApretrainingofaViT-B/16for3OepochsIncreasingthe number of target blocks improve the low-shot accuracy.

## C. Additional Ablations

This section folows the same experimental protocol as Section 9.We report the result of a linear probe with a frozen backbone, trained on the low-shot 1% ImageNet-1K benchmark.

Multiblock masking strategy.We present an extended ablation ofthe multiblock masking strategy where we change the targets block scale(Table 8),thecontext scale (Table 9)and the number of target blocks (Table10).We train a ViT-B/16 for 300 epochs using I-JEPA with various multi-block setings and compare performance on the 1% ImageNet-1K benchmark using alinear probe.In short,we find that it is important to predict several relatively large (semantic)target blocks,and to use a sufciently informative (spatially distributed) context block.

Masking at the output of the target-encoder.An important important design choice in I-JEPA is thatthe target blocks areobtained by masking theoutput of the target-encoder,notthe input.Table11 shows the effectof this design choice onthe smantic levelof the learned representations when pretraining a ViT-H/16using I-JEPA for 30O epochs.Inthe case where masking is apliedtothe input, we forward-propagate throughthe target-encoder once foreach targetregion. Masking the output of the target-encoder during pretraining results in more semantic prediction targets and improves linear probing performance.

<table><tr><td>Target Masking</td><td>Arch.</td><td>Epochs</td><td>Top-1</td></tr><tr><td> Output</td><td>ViT-H/16</td><td>300</td><td>67.3</td></tr><tr><td>Input</td><td>ViT-H/16</td><td>300</td><td>56.1</td></tr></table>

Table11.Ablating masking outputof targetencoder.LinearevaluationonImageNet-1Kusingonly1%oftheavailablelabels;ablating theefectof maskingthetarget-encoderoutputduringI-JEPApretrainingofaViT-H/16for3OO epochs.Maskingtheoutputof the target-encoder during pretraining significantly improves the linear probing performance of the pretrained representations.

Predictor depth.We examine the impact of the predictor depth on the downstream low-shot performance in Table 12. We pretrain a ViT-L/16 for 500 epochs using either a 6-layer predictor network ora12-layer predictor network.The model pretrained using a deeper predictor shows a significant improvement in downstream low-shot performance compared to the model pretrained with a shallower predictor.

<table><tr><td>Predictor Depth</td><td>Arch.</td><td>Epochs</td><td>Top-1</td></tr><tr><td>6</td><td>ViT-L/16</td><td>500</td><td>64.0</td></tr><tr><td>12</td><td>ViT-L/16</td><td>500</td><td>66.9</td></tr></table>

Table12.Ablatingthepredictordepth.LinearevaluationonImageNet-1Kusingonly1%oftheavailablelabels;ablatingtheefctof maskingthe predictordepthforaViT-L/16pretrainedforOOepochs.Increasingthepredictordepthleadstosignificantimprovementof the linear probe performance of the pretrained representations.

Weight decay.In Table13,we evaluate the impact of weight-decay during pretraining.We explore two weight decay strategies: linearly increase the weight-decay from O.04 toO.4or usea fix weight-decayof O.O5.Using asmaller weight decay during pretraining improves the downstream performance on ImageNet-1% when fine-tuning.However,this also leads to a degradationof performance inlinear evaluation.In themain paper,weuse the first weight decay strategyas it improves the performances in linear evaluation downstream tasks.

<table><tr><td>Weight DecayArch.</td><td></td><td></td><td></td><td>EpochsImageNet-1%ImageNet Linear-Eval</td></tr><tr><td>0.04 → 0.4</td><td>ViT-L/16</td><td>600</td><td>69.4</td><td>77.8</td></tr><tr><td>0.05</td><td>ViT-L/16</td><td>600</td><td>70.7</td><td>76.4</td></tr></table>

Table13.Ablating the pretraining weight-decay.Wecompareourdefault pretraining weightdecaystrategy where welinearlyincrease theweight-decayfromO04toO.4tousingafix weightdecayofOO5.Usingasmalerweight-decayduring pretrainingcanimprovethe fine-tuning performance on ImageNet-1%,However,it also leads to adrop of performance in linear evaluation.

Predictor width.We explore the impactof the predictor width in Table 14.We compare I-JEPA usinga ViT-L encoder anda predictor with 386channels toa similar model using apredictor with1024 channels.Note thatthe ViT-Lencoder has 1024 channels. Using a botleneck in the predictor width improves the downstream performance on ImageNet 1%.

<table><tr><td>Predictor Width</td><td>Arch.</td><td>Epochs</td><td>Top-1</td></tr><tr><td>384</td><td>ViT-L/16</td><td>600</td><td>70.7</td></tr><tr><td>1024</td><td>ViT-L/16</td><td>600</td><td>68.4</td></tr></table>

Table14.Ablating the predictoridth.WereportsresultsonImageNet-1K1%usingfine-tuning.Wecompare two predictors having a widthofeither384or24.NotetheIJEPAencoderisaViT-Lwith024channels.Havingaidthbotleneckinthepredictorimproves the downstream performances.

## D. Finetuning on the full ImageNet

In this section,we report performance on I-JEPA when fine-tuning on the fullImageNet dataset.We focus on the ViT-$\mathrm { H } / 1 6 _ { 4 4 8 }$ as this architecture achieves state-of-art performance with MAE [36].

We use a fine-tuning protocol similar to MAE. Specificaly, we fine-tune our model for 50 epochs using AdamW and a cosine learning rate schedule. The base learning rate is set to $1 0 ^ { - 4 }$ and the batch size to 528. We train using mixup [76] set to 0.8,cutmix[73]set to1.0,a drop path probabilityof O.25anda weight decay setto O.04.We also usealayer decayof 0.75.Finally, we use the same rand-augment data-augmentations as MAE,

Table 15 reports the fine-tuning results.-JEPA achieves 87.1top-1accuracy.Its performance is lessthan1%away from the best MAE model despite I-JEPA being trained for5.3times less epochs thanMAE.This result demonstrates thatI-JEPA is competitive when fine-tuning on the full ImageNet dataset.

<table><tr><td>Method</td><td>Arch.</td><td>Epochs</td><td>Top-1</td></tr><tr><td>MAE [36]</td><td> $\mathrm { V i T - H } / 1 4 _ { 4 4 8 }$ </td><td>1600</td><td>87.8</td></tr><tr><td>I-JEPA</td><td> $\mathrm { V i T  – H } / 1 6 _ { 4 4 8 }$ </td><td>300</td><td>87.1</td></tr></table>

Table15.FinetuningonthefullImageNetdataset.I-JEPAachevescompetitiveperformance.-JEPAislosetoAEappoachdespite I-JEPA being trained for 5.3 times less epochs than MAE.

## E.RCDM Visualizations

To visualize the representations of a pretrained neural network in pixel space,we use the RCDM framework[13].The RCDM framework trains a decoder network $h _ { \omega } ,$ comprising a generative diffusion model, to reconstruct an image x from the representation vector of that image ${ \pmb s } _ { x }$ and a noisy version of that image ${ \hat { \pmb { x } } } : = { \pmb { x } } + { \epsilon } ,$ where e is an additive noise vector. Concretely,the decoder objective is to minimize the loss function $\| h _ { \omega } ( \hat { \pmb x } , \pmb s _ { x } ) - \epsilon \|$ .We train each RCDM network for 300,000 iterations using the default hyperparameters [13].After training the decoder,onecan subsequentlyfeed therepresentation vector of an unseen test image $s _ { y }$ into the decoder along with various random noise vectors to generate several pixel-level visualizationsof therepresentation,thus providing insight intothe features captured intherepresentationsof the pretrained network.Qualities that are common across samples represent information that is contained in the representation.On the other hand,qualities that vary across samples represent information that is not contained in the representations

InFigure 6,the visualizationsareobtainedby feedingtheaverage-pooledoutputof the predictor,conditionedonaspecific targetregion,intothedecoder network,along withvariousrandomnoise vectors.InFigures7and 8,the visualizations are obtained by feedingthe average-pooled outputof the target-encoder into the decoder network,along with various random noise vectors.

## E.1.Encoder Visualization

InFigure7,wevisualize theaverage-pooledI-JEPA representations at theoutputofourViT-H/14 target-encoder.The first column contains the original image,while subsequent columns contain synthetic samples obtained byfeeding the averagepooled representation ofthe image into the decoder along with various random noise vectors.Figure7suggests that the I-JEPA target-encoderisable tocorectlycapture thehigh-level informationregardingobjectsand theirposes,whilediscarding low-level image details and background information.

Figure 8 shows similar visualizations,but when using an MSN [4] pretrained ViT-L/7 target-encoder to compute the image representations.The MSN method trains a context-and target-encoder using a Joint-Embeding Architecture to enforceinvarianceof global image representations to various hand crafted data augmentations and mising patches.While the MSN pretrained network isabletocapture high level semantic informationaboutthe image inthefirstcolumn,it also exhibits higher variabilityin the generatedsamples,e.g.variabilityin theobjectpose,objectscale,andnumberofinstances. In short,the MSN pretrained discards muchofthe local structure in theimage,which is instarkcontrast toI-JEPA,which retains information about much of the local structure in the input image.

<!-- image-->  
Figure7.VisualizationofI-JEPAtarget-encoderrepresetations.Foreachimage:firstcolumncontains theoriginalimage;subsequent columnscontainsamplesfromagenerativemodeldecodingtheaverage-pooledoutputof apretraied-JEPAtarget-encoder.Qualities thatareomonacrossspleseprentiforatiotatotaedisintEAepreentatio.EPAisabletetlpture thehigh-levelinformationregardingobjectsandtherposes.Quaitiesthatvaryacrosamplesrepresentinformationtatisotontaed in the representation.I-JEPA encoder discards the precise low-level details as wellas background information.

<!-- image-->  
Figure8.VisualizationofMSN target-encoderrepresentations.Foreachimage:fistcolumncontains theoriginalimage;subsequent columnscontainsamples fromagenerativemodeldecoding theoutputof afrozenMSNencoder[4].Qualities thatarecommonacross samplesrepresent informationthatisotainedinterepresetation.Quaitiesthatvaryacrosssamplespresent iformationthatis not captured byMSN.Comparedto-JEPA,MSNsamplesshowhighervariability.MSNretainslessinformationfromtheinputIarticular it discards global structure information such as the object pose or even number of instances.

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
