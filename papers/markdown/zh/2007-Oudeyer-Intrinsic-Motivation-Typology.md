# 2007-Oudeyer-Intrinsic-Motivation-Typology

> **来源**：../2007-Oudeyer-Intrinsic-Motivation-Typology.pdf ｜ **转换**：MinerU agent API（视觉结构恢复，含 OCR） ｜ 英文原文 markdown ｜ **中文译版**：全文翻译，术语保留英文括注，文末附术语对照表

# 什么是内在动机？一种计算方法的类型学（What is intrinsic motivation? A typology of computational approaches）

Pierre-Yves Oudeyer^1,2,\* 与 Frederic Kaplan^3

1. Sony Computer Science Laboratory Paris，Paris，France

2. INRIA Bordeaux-Sud-Ouest，France

3. Ecole Polytechnique Federale de Lausanne，EPFL-CRAFT，Lausanne，Switzerland

Edited by: Max Lungarella, University of Zurich, Switzerland

Reviewed by: Jeffrey L. Krichmar, The Neurosciences Institute, USA；Cornelius Weber, Johann Wolfgang Goethe University, Germany

内在动机（intrinsic motivation）参与了自发的探索与好奇（curiosity），是发展心理学中的关键概念，被认为是人类开放式认知发展的关键机制，近年来因此在发展机器人学（developmental robotics）研究者中引起日益增长的关注。本文的目标有三重。第一，综述心理学中内在动机的不同研究路径；第二，通过在计算强化学习框架下重新解释这些路径，论证它们并不具备可操作性（operational），甚至有时彼此不一致；第三，通过给出一套可能计算方法的形式化类型学（typology），为内在动机的系统性操作研究奠定基础。该类型学部分基于既有计算模型，同时也提出了概念化内在动机的新方式。我们论证，这种计算类型学可能有助于为心理学与发展机器人学双方的研究开辟新途径。

关键词：内在动机、认知发展、奖励（reward）、强化学习、探索（exploration）、好奇、计算建模（computational modeling）、人工智能、发展机器人学

## 引言（INTRODUCTION）

生命体（尤其是人类）中存在着极其多样的动机系统。例如，有些系统推动机体维持一定的化学能量水平（涉及进食），还有些系统推动机体将其体温或物理完整性维持在可存活的区间内。受这类动机以及（神经）动物行为学家（(neuro-)ethologists）对它们的理解的启发，机器人学家建造了被赋予类似系统的机器，以期使其具备自主性和类生命的智能属性（Arkin, 2005）。例如，受 sowbug 启发的机器人（Endo and Arkin, 2001）、螳螂机器人（Arkin et al., 1998）、类犬机器人（Fujita et al., 2001）都已被建造出来。

某些动物——在人类身上最为显著——还拥有更一般的动机，推动它们去探索、操纵或试探其环境，促成好奇以及对游戏性活动和新活动的投入。这类动机被心理学家称为内在动机（intrinsic motivation）（Ryan and Deci, 2000），它对贯穿一生的感知运动（sensorimotor）与认知发展至关重要。心理学中有大量文献解释它为何对认知成长与组织不可或缺，并考察内在动机之下实际潜在的认知过程（Berlyne, 1960; Csikszentmihalyi, 1991; Deci and Ryan, 1985; Ryan and Deci, 2000; White, 1959）。这近年来吸引了越来越多发展机器人学研究者的兴趣，若干计算模型已被开发（综述见 Barto et al., 2004; Oudeyer et al., 2007）。

然而，内在动机这一概念本身从未真正从计算的角度得到过一致而批判性的讨论。许多作者凭直觉使用它，而未追问它究竟意味着什么。因此，本文的第一个目标与贡献是先在心理学中综述这一概念，随后以计算术语对其做批判性再解释。我们将表明，心理学给出的定义实际上并不令人满意。作为后果，我们将通过给出一套可能计算方法的类型学，为内在动机的系统性操作研究奠定基础，并讨论给出单一通用的内在动机计算定义是否可能、是否有用。我们将给出的类型学部分基于既有计算模型，但也提出概念化内在动机的新方式。我们将着力考察这些模型之间的相互关系，并提出一个划分为宽泛而互不相同的类别的分类。

## 心理学家视角下的内在动机（INTRINSIC MOTIVATION FROM THE PSYCHOLOGIST'S POINT OF VIEW）

### 内在动机与工具化（Intrinsic motivation and instrumentalization）

依照 Ryan and Deci (2000)（pp.56）：

> 内在动机被定义为：因活动本身固有的满足感而从事该活动，而非为了某种可分离的结果。当一个人被内在动机驱动时，他是为了活动所蕴含的乐趣或挑战而行动，而不是因为外部产物、压力或奖励。

内在动机在幼儿身上清晰可见：他们会不断尝试去抓握、投掷、啃咬、挤压自己遇到的新物体，或对其叫喊。随着成长这种动机虽然重要性下降，人类成年人在玩填字游戏、绘画、园艺，或仅仅是阅读小说、看电影时，仍常常处于内在动机之中。然而，要更清楚地刻画内在动机，需要理解它是相对于外在动机（extrinsic motivation）被定义的：

> 外在动机是一个构念（construct），指每当一项活动是为了达成某种可分离的结果而做时便适用的动机。因此外在动机与内在动机形成对照，后者指仅仅为了活动本身带来的享受而从事该活动，而非其工具性价值。（Ryan and Deci, 2000）

我们看到，区分内在与外在动机的核心特征是工具化（instrumentalization）。我们还看到，内在动机与外在动机这对概念，不同于内部动机与外部动机（internal vs. external motivations）的区分。在计算文献中，“intrinsic”有时被当作“internal”的同义词使用，“extrinsic”被当作“external”的同义词使用。但这实际上是一种混淆。确实，存在着可以是内部的（internal）外在动机（extrinsic motivations），反之亦然。事实上，存在不同种类的工具化，可以按其自我决定（self-determined）程度高低加以分类（Ryan and Deci, 2000）。让我们举例说明以求更清楚。

例如，一个认真做家庭作业的孩子，其动机可能是为了避免若不做作业会受到的父母惩罚。此时行动的原因显然是外部的，做作业不是为了作业本身，而是为了“不受惩罚”这一可分离的结果。这里孩子是被外在且外部地驱动的。

另一方面，也可能有一个孩子认真做作业，是因为他相信这会帮助他在成年之后得到自己梦想的工作。此时行动的原因是内部产生的，但作业同样不是为了其本身而完成，而是因为孩子认为它会带来“得到好工作”这一可分离的结果。

最后，也可能有一个孩子为了乐趣本身而认真做作业，因为他从发现新知识中体验到愉悦，或例如把数学题看得和玩电子游戏一样有趣。此时，其行为是被内在（且内部）驱动的。

这些不同种类的动机有时也可以在同一全局活动中叠加或交织。例如，一个做作业的孩子很可能部分地被“考试得高分”外在驱动，部分地被“学习有趣的新东西”内在驱动，这完全是可能的。再如，设想一个孩子因打网球而具有内在动机，但必须骑自行车才能到网球场（而他并不特别喜欢骑车）。此时，骑车这一行为是一个内部的、外在驱动的行为，它源自打网球这一内在动机行为之外延。

## 是什么使一项活动具有内在激励性？（What makes an activity intrinsically motivating?）

鉴于内在动机与外在动机的这一宽泛区分，心理学家尝试建立关于活动的哪些特征使它们对某些人具有内在激励性的理论（且并非对所有人在所有时刻都成立——同一活动可能在某时刻对某人有内在激励性，之后不再如此）。他们研究了这些动机如何在机体（尤其人类）中被功能性实现，并提出了若干理论方向。

**操纵驱力、探索驱力（Drives to manipulate, drives to explore）。** 二十世纪五十年代，心理学家最初尝试在驱力理论（theory of drives）（Hull, 1943）的基础上解释内在动机与探索活动，驱力即饥饿或疼痛这类机体试图降低的特定组织亏缺。例如，(Montgomery, 1954) 提出了一种探索驱力，(Harlow, 1950) 提出了一种操纵驱力。这种以驱力命名的路径有许多缺陷，White (1959) 对此有详细批评：内在动机驱动的探索活动具有根本不同的动力学。事实上，它们不是稳态的（homeostatic）：探索的一般倾向并不是对机体身体受到应激性扰动的完成性反应（consummatory response）。

**认知失调的降低（Reduction of cognitive dissonance）。** 一些研究者随后提出了另一种概念化。Festinger 的认知失调理论（Festinger, 1957）断言，机体有动机去降低失调（dissonance），即内部认知结构与当前被知觉情境之间的不兼容性。十五年后，Kagan 表述了一个相关的观点：人类的一种基本动机是不确定性（uncertainty）的降低，即“（两个或多个）认知结构之间、认知结构与经验之间、或结构与行为之间的不兼容性”的降低（Kagan, 1972）。然而，这些理论受到如下批评：许多人类行为同样意在增加不确定性，而不仅是降低它。人类似乎在完全不确定与完全确定的情境之间寻找某种最优形式。

**最优失谐（Optimal incongruity）。** 1965 年，Hunt 提出了儿童与成人都在寻找最优失谐（optimal incongruity）的思想（Hunt, 1965）。他将儿童视为信息加工系统，并指出有趣的刺激是那些被知觉水平与标准水平之间存在差异的刺激。对 Dember 和 Earl 而言，内在动机行为中的失谐或差异存在于人的预期与刺激的属性之间（Dember and Earl, 1957）。Berlyne 发展了类似的观念，他观察到最有奖励性的情境是那些新异性（novelty）水平居中的情境，介于已经熟悉与全新未知的情境之间（Berlyne, 1960）。

**对效能、个人因果、能力与自我决定的动机（Motivation for effectance, personal causation, competence and self-determination）。** 最后，另一组研究者更倾向于挑战（challenge）的概念而非最优失谐的概念。这些研究者宣称，驱动人类行为的是对效能（effectance）（White, 1959）、个人因果（personal causation）（De Charms, 1968）、能力与自我决定（competence and self-determination）（Deci and Ryan, 1985）的动机。基本上，这些路径主张，激励人们的是他们能对他人、外部物体和自身拥有的控制程度，或者换句话说，是有效交互（effective interaction）的总量。以类似的方式，最优挑战（optimal challenge）的概念也被提出，例如在“心流”（Flow）理论中（Csikszentmihalyi, 1991）。

## 计算系统中的动机：外在 vs. 内在，外部 vs. 内部（MOTIVATION IN COMPUTATIONAL SYSTEMS: EXTRINSIC VS. INTRINSIC AND EXTERNAL VS. INTERNAL）

在宽泛地综述了心理学中的内在动机之后，我们在此开始采取计算的视角。首先，我们将描述在计算机与机器人架构中，一般动机是如何被构想与使用的。然后，我们将给出一组重要的区分维度——其中包括内在—外在之分——它们有助于组织可能的动机系统的空间。

**动机变量与驱力（Motivational variables and drives）。** 虽然动机在简单机器人架构中有时以隐式方式实现，例如 Braitenberg 车辆（Braitenberg vehicles）中的趋光性（Braitenberg, 1984），如今更为常见的做法是直接且显式地以一个模块的形式实现它：该模块追踪若干内部“动机”变量的取值，并向架构的其余部分发送信号（Arkin, 2005; Breazeal, 2002; Huang and Weng, 2004; Konidaris and Barto, 2006）。例如，人们经常遇到一个能量水平变量，它与一个舒适区（即一个取值范围）相关联，当该变量超出这一区间时，系统向架构的其余部分、特别是动作选择（action selection）模块发送信号，使机器人尽快找到充电站。这种稳态系统也可以实现为 Hull 式驱力（Hullian drive）（Hull, 1943; Konidaris and Barto, 2006），能量水平是一个从 0（完全未满足）到 1（饱足）取值的变量，并持续向动作选择系统发送其数值，以使之尽可能接近 1。

**计算强化学习与奖励（Computational Reinforcement Learning and rewards）。** 在机器人系统中，经常出现的情况是：使动机变量尽可能保持饱足的动作策略既不是固定的、也不是最初手工编码的，而是应当被学习出来的。发生这种情况的标准框架是“计算强化学习（computational reinforcement learning, CRL）”（Sutton and Barto, 1998）。这一框架引入了许多算法，目标是找到最大化“奖励（rewards）”的策略，奖励是 CRL 的核心概念。非常重要的一点是，CRL 中“奖励”一词的含义是在一种特定的技术意义上使用的，不同于心理学中（尤其是操作性条件反射理论（Skinner, 1953）中）“奖励”一词的含义。尽管如此，这两种含义有重叠，这在文献中造成了若干混淆。在 CRL 中，“奖励”在技术上只是一个被连续测量的数值量，用于驱动动作选择机制，使该量在未来的累积值最大化。CRL 理论对该值由什么/如何/在哪里产生完全不可知（agnostic）。回到实现动物行为学启发的动机系统的机器人，这个值可以是例如机器人内部能量水平的值。但是——这正是计算文献中 CRL 的常见用法——这个值也可以由人类工程师直接设定，或由人类工程师构建的外部程序设定。例如，在许多工程师试图建造能向前行走的机器人的实验中，使用了 CRL 算法，其奖励是来自一个外部系统（如安装在天花板上的摄像头）的值，该系统观测机器人移动得多快（或没有移动），该值即速度。正是在这类实验中，“reward”一词与操作性条件反射文献中的“reward”一词重叠，指获得外部对象/事件/属性，如金钱、食物或学校里的高分。但必须记住，在使用 CRL 的机器人中，奖励可以完全由内部定义，类似于神经递质（neurotransmitter）的实际释放。

**作为多种动机之共同通货的奖励（Rewards as a common currency for multiple motivations）。** CRL 中奖励概念的一个优点在于：作为数值量，它可以充当单一架构中若干并存动机之间的“共同通货（common currency）”（McFarland and Bosser, 1994）。确实，在一个典型的机体（自然的或人工的）中，不同且可能冲突的动机会试图把动作推向某些方向：例如，能量水平维持驱力可能与物理完整性维持驱力、睡眠驱力以及推动寻找社会伙伴的驱力并存。为了在这些动机所蕴含的可能冲突动作之间做出仲裁，人们利用数值比较与各动机相关联的期望奖励的可能性。此外，人们经常见到这样的架构：每个奖励都被赋予一个（可能是自适应的）数值权重（Konidaris and Barto, 2006）。

**内部 vs. 外部动机（Internal vs. external motivations）。** 有了这个在机器人中实现动机的架构框架，就可以考察内部动机与外部动机之间的第一种区分。这一差别关乎自主性（autonomy），在于计算/生成奖励的机制的功能位置。如果奖励——即系统要最大化的数值量——来自自主系统的外部，则称之为外部的（external）。这就是上面提到的例子：行走机器人由来自人类或安装于天花板上的带摄像头系统的奖励所驱动。如果奖励由自主系统内部计算并生成，则称之为内部的（internal）。这就是上面提到的例子：与能量维持驱力的饱足相关联的奖励。这一差别总结于图 1。然而，这一差别在机器人的情形下有时会很微妙。计算机允许我们做对人类而言不可能的操作。例如，工程师完全可以建造一台能自己监测它是否在向前走、速度多快的自主机器，并可以在机器人的内部架构中纳入一个“尽可能快地向前走”的动机。实践中，这将产生与天花板上的行走检测系统大致相同的行为，但在技术上我们得到的是一个内部奖励（然而，正如下文将看到的，它是外在的）。当然，这类操作对人类不可能，在人类身上也难找到这类“极限”例子。

<!-- image -->
[图：图 1. CRL 框架下外部动机与内部动机的差异：在外部驱动的行为中，奖励在智能体（agent）外部计算并施加于它；而在内部驱动的行为中，奖励在智能体内部计算并自我决定。本图受 Barto et al. (2004) 启发。]

**内在 vs. 外在动机（Intrinsic vs. extrinsic motivations）。** 现在来看我们如何在这个计算框架中概念化内在动机与外在动机的差异。我们此前看到，心理学中内在动机被定义为刻画那些因其自身缘故而“有趣”或“有挑战性”的活动，而外在动机刻画的是为了达到某个单独定义的特定目标而完成的活动。对计算机科学家而言，这些定义实际上相当模糊，可以以多种互不兼容的方式做计算解释。首先，使一项活动具有内在激励性的那些属性（“乐趣”、“挑战”、“新异性”、“认知失调”或“最优失谐”）似乎对内在动机的定义本身至关重要，但心理学文献中对于它们究竟是什么并没有统一的路径或共识。其次，区分内在与外在的目标或工具化概念实际上是有歧义的。确实，例如可以设想存在这样一种动机：每遇到一个新异情境就产生一个正奖励。在 CRL 框架中，系统试图最大化奖励，于是获得奖励就是一个目标！这样，对新异情境的寻找——在心理学文献中通常被呈现为内在动机行为——就是由“获得内部奖励”这一目标引导的，从而在技术上是外在驱动（extrinsically motivated）的。于是我们看到，用来定义外在动机的“可分离目标”概念过于模糊，或许应当用若干具体属性加以修正。但是，哪些属性应当刻画一个涉及外在（而非内在）动机的目标呢？精确定义这一区分的难题还在于：如上所见，内在动机与外在动机并不是互斥概念——一个给定活动可以同时既是内在又是外在驱动的。尽管如此，这段讨论再次表明，内在与外在之分不同于内部与外部之分（后者如我们所见要简单得多）。可以稳妥地说，前一段所定义的所有外部动机，无论“因其自身缘故而有趣或有挑战性的活动”作何解释，都不是内在的。同样可以稳妥地说，内部动机可以是内在的、外在的，或两者兼有。

鉴于人类动机科学理解水平较低所导致的这种混乱，从计算观点出发，对待内在动机最务实的路径也许是：避免试图建立一个单一的通用定义，而是尝试基于一系列既有的或可能的操作性方法绘制一张地图。这正是我们下一节将做的，同时识别那些不应被视为内在动机的计算定义动机的例子。尽管如此，正如我们将看到的，对这些例子的枚举将引导我们提出一个这样的内在动机通用计算定义。该定义将在讨论部分给出，我们将论证：尽管从心理学理论的观点看它反直觉，但它可能对研究的组织富有成效。

**稳态 vs. 异稳态动机（Homeostatic vs. heterostatic motivations）。** 为使动机特征的图景更完整，还必须呈现两类重要动机之间的区分：稳态（homeostatic）与异稳态（heterostatic）。在机器人中发现的、可能也是动物界中最常见的动机类型，是稳态系统：推动机体将其某些属性维持在一个“可存活的”或“舒适的”区间内。维持电池能量高于某个阈值（且必然低于一个不可逾越的最大值）的动机，或维持中等水平社会刺激的动机（Breazeal, 2002）就是例子。在 Hull 式视角下，稳态动机对应于可以被饱足的驱力（例如，进食驱力在吃够食物后被饱足）。与之相对，存在异稳态动机系统：持续把机体推离其惯常状态。稳态动机是试图补偿（外部或内部的）扰动对机体之效应的系统，而异稳态动机是试图（自我）扰动机体使之脱离平衡的系统。用 Hull 式术语说，异稳态动机是无法被饱足的驱力。例如，正如下文将看到的，可能存在一种明确推动机体寻找新异情境的动机：在 CRL 框架中，每遇到一个新异情境就提供奖励。此时，不存在该动机试图维持的平衡状态，而是机体若一再体验新异性就会持续获得奖励（但注意，可以设想这样一种动机系统：仅当新异性以中等频率被体验到时才提供奖励，此时它就变成了稳态动机）。

**固定 vs. 自适应动机（Fixed vs. adaptive motivations）。** 最后一个同样重要的区分是动机系统的固定（fixed）与自适应（adaptive）属性。用心理学术语说，固定的动机系统是在个体整个生命周期内始终以相同方式评价同一感知运动情境的系统。在 CRL 框架中，固定的动机系统是在个体生命周期内对同一感知运动情境始终提供相同奖励的系统¹。相反，自适应的动机系统会随时间推移对同一情境做出不同评价（或在 CRL 框架中，它不一定会随时间推移对同一情境提供相同奖励）。例如，能量维持动机可以是固定的（若能量舒适区始终保持不变），也可以是自适应的（若例如个体身体随时间长大，且动机的实现方式使舒适区边界相应移动）。如果个体能够记住它已经历过的情境，那么新异性驱力（novelty drive）就是自适应的：某个时刻曾经新异从而有吸引力的情境，在经历之后将不再如此。

## 内在动机计算方法的类型学（A TYPOLOGY OF COMPUTATIONAL APPROACHES OF INTRINSIC MOTIVATION）

文献中已经开发出相当数量包含特定内在动机模型的认知架构（cognitive architectures）（例如 Barto and Simsek, 2005; Bonarini et al., 2006; Huang and Weng, 2002; Kaplan and Oudeyer, 2003; Marshall et al., 2004; Merrick and Maher, 2008; Oudeyer et al., 2005, 2007; Schmidhuber, 1991; Thrun, 1995）。然而，它们大多是特设的（ad hoc），很难理解它们彼此之间以及与心理学文献的一般概念之间的关系。正如下文将展示的，一大批潜在有趣的计算方法也尚未被实现与研究。

本节的目标是给出一个类型学的、形式化的框架，使研究者能够更好地理解并绘制可能模型的空间。这一类型学是多年理论发展与内在动机系统计算模型实际实践的产物（Kaplan and Oudeyer, 2003, 2007a,b; Oudeyer and Kaplan, 2006; Oudeyer et al., 2005, 2007）。它植根于对心理学文献与既有计算模型的了解，但既试图超越前者的模糊性，也试图对特殊的机器人实现加以推广。这一类型学的一个底层假设是：我们把自己定位在计算强化学习（CRL）框架之内。因此，该类型学依赖于对各种可被视为定义内在动机系统的奖励计算类型的形式化描述。类型学聚焦于奖励的定义，而有意不指定其可插入的特定 CRL 算法（如 Q-learning 或 Sarsa，可能算法的介绍见 Sutton and Barto, 1998），因为我们认为那是一个正交的研究问题。

此外，虽然我们此处聚焦于与内在动机相关的奖励定义，但隐含的是：在某个特定机器人上，这些内在奖励可能与其他类型的奖励系统（如饥饿、社会在场等）整合在一起。还应注意的是，当我们给出总结每一大类的图示时，图中只画出与内在动机系统直接相关的认知回路，但隐含的是，在某个特定机器人完整的认知架构中，可能还有许多其他模块在并发运行。

在这一类型学中，某些内在奖励模型已在文献中被实现和测试。由这些模型出发，我们提出若干变体。其中一些变体是对基本模型的必要改进，来自与机器人实际实验；另一些变体是自然的形式变体，因而在实现上极为相似，但有趣的是，它们直觉上对应着一些心理学中通常不被视为内在动机的人类动机。这在“内在动机应当如何被概念化”上的含义将在讨论部分展开。最后，我们还提出内在动机的新的形式模型，它们对应于心理学中的重要路径，但似乎从未在计算框架中被操作性地研究过。

据我们所知，这样的类型学是首次被给出，我们希望它有助于组织未来的研究。然而，同样重要的是理解这一类型学不打算成为什么：

1. 我们并不宣称这份清单是穷尽的，也不主张将这些方法组织成类型别无他法。

2. 我们给出的形式方法清单并不打算包含在特定机器人上编程特定方程的方法。对于某些类型奖励的计算，这已经在文献中别处完成；对于另一些，则是未来研究的课题。不过，在相关之处，我们提供了描述在特定机器人上实现某一特定方法的实用方法与架构的论文引文。

3. 这一类型学不是对既有内在动机计算模型的综述——综述见 Oudeyer et al. (2007)——而是一个大型形式化框架的呈现，既有与未来的模型都可以在其中定位。

4. 这一类型学对于“当所呈现的某一形式模型被实现在机器人中时会出现何种行为”以及“它在多大程度上能被用作开放式（open-ended）发展的基础”不作任何断言：事实上，所呈现的若干模型在行为上是明确相互矛盾的，但它们都被纳入，或因为它们已照此在文献中被使用，或因为它们形式上的相似性。因此还应注意，这一类型学、以及我们所提议的内在动机的通用概念化，是基于所涉机制（mechanisms）而非它们所产生的实际结果。

在下文中，我们把内在动机计算模型的空间组织为三大类，它们都共享同一个形式观念：机器人所经验的一个感知运动流（sensorimotor flow）。我们假设典型机器人由若干感觉通道（sensory channels），记作 $s_i$，与若干运动通道（motor channels），记作 $m_i$，其取值随时间连续流动，故有记法 $s_i(t)$ 与 $m_i(t)$（见图 2）。时刻 t 所有感知运动取值的向量记作 SM(t)。对于以下的计算模型，三个特征是重要的：

1. 这些通道可以对应机器人的任何一种物理变量或内部变量（例如来自红外传感器、麦克风传感器、像人脸存在检测器这样的虚拟内部传感器、手臂的低层关节取值、身体的整体运动方向等）；

2. 这些感觉通道实际上是什么，即它们的“意义”，不被纳入考量；

3. 在某一情境的内在动机度量中被纳入考量的感知运动通道的集合，可能小于机器人可用的全部感知运动通道的集合。

## 基于知识的内在动机模型（Knowledge-based models of intrinsic motivation）

内在动机的第一种计算路径，基于机器人所经验情境与机器人关于这些情境的知识和预期之间的失调（dissonances）（或共振，resonances）的度量。这里“情境”一词既可指被动的观察活动——机器人不做别的，只是把注意聚焦于环境的某个特定方面——也可指主动的活动——机器人执行动作，并将其动作的实际结果与其关于这些动作的知识和预期进行比较。

在这一路径内部，依据知识与预期的表示方式，存在两个子路径：信息论/分布式的（information theoretic/distributional）与预测式的（predictive）。

<!-- image -->
[图：图 2. 机器人由其感觉通道与运动通道取值的连续流刻画，记作 SM(t)。]

**信息论与分布式模型（Information theoretic and distributional models）。** 这一路径基于机器人构建的表示，这些表示估计在特定情境下观察到某些事件 $e^k$ 的概率分布，情境被定义为感知运动流中的数学构型。这样的事件有多种类型，但被测量的概率通常是：在感知运动流中观察到某个状态 $SM^k$ 的概率，记作 $P(SM^k)$；或观察到的状态间特定转移的概率，如 $P(SM^k(t), SM^l(t+1))$；或在观察到给定状态之后观察到特定状态的概率 $P(SM^k(t+1)|SM^l(t))$。这里状态 $SM^k$ 既可以直接是数值原型（prototypes），也可以是感知运动空间中的完整区域（这可能涉及一种对空间离散化的机制）。在下文中，我们将认为所有这些可能性皆成立，并只使用一般记法 $P(e^k)$。我们将假设机器人拥有一种机制，使其能够在经验世界的过程中，对可能事件空间 E 中事件概率分布的估计进行内部构建（但可能事件空间不是预先定义的，也应由机器人发现，故典型地这是一个初始为空、随经验增长的空间）。最后，我们对离散空间使用刻画分布函数形状的熵（entropy）概念：

$$
H(E) = - \sum_{e^k \in E} P(e^k) \mathsf{ln}(P(e^k))\tag{1}
$$

对连续空间：

$$
H(E) = - \int_{e^k \in E} P(e^k) \mathsf{ln}(P(e^k))\tag{2}
$$

图 3 总结了内在动机信息论路径的一般架构。

**不确定性动机（Uncertainty motivation, UM）。** 被新异性内在吸引的倾向常被用作内在动机文献中的例子。以计算方式实现它的一个直接方式，是构建这样一个系统：对每一个实际观察到的事件 $e^k$，生成一个与其观察概率成反比的奖励 $r(e^k)$：

$$
r(e^k, t) = C \cdot (1 - P(e^k, t))\tag{3}
$$

其中 C 是常数。这一奖励计算机制随后可以整合进一个 CRL 架构，后者将选择动作以最大化这些奖励在未来期望累积和。实际上，这在以下所有定义中都是隐含的，故正文集中于定义和计算奖励的显式机制。基于 UM 类机制的多种模型已在计算文献中被实现（例如 Huang and Weng, 2004）。

<!-- image -->
[图：图 3. 基于信息论/分布式知识的内在动机计算方法的一般架构。]

**信息增益动机（Information gain motivation, IGM）。** 心理学与教育学中还常提出，人类有学习与同化（assimilate）的自然倾向（Ryan and Deci, 2000）。用信息论术语说，这一同化观念或“学习之乐”可以用事件 $e^k$ 发生之后机器人对世界的知识的降低的不确定性来建模：

$$
r(e^k, t) = C \cdot (H(E, t) - H(E, t+1))\tag{4}
$$

这种信息增益动机的实现例子可见于例如 Fedorov (1972) 与 Roy and McCallum (2001)（但注意这些论文中并未使用“动机系统”一词）。应当指出，在实践中这在连续空间中未必可行（tractable）。实际上，这是所有分布式路径潜在共有的问题。

**分布式惊讶动机（Distributional surprise motivation, DSM）。** 体验惊讶（surprise）之乐有时也被提出。惊讶通常被理解为观察到一个强烈违反预期的事件，即一个发生了却被强烈预期不会发生的事件。数学上，可以将其建模为：

$$
r(e^k, t) = C \cdot \frac{1 - P(e^k, t)}{P(e^k, t)}\tag{5}
$$

其中 $C$ 是常数。注意这与 UM 有些不同：随着新异性增加，奖励呈非线性增长。一个事件对 UM 而言可以高度新异且有奖励，但若人们并未更强烈地预期另一个事件取代它发生，则它并不很令人惊讶（例如，平坦均匀分布中的任何随机事件对 UM 是新异且有奖励的，但对 DSM 并不令人惊讶、奖励很低）。

**分布式熟悉性动机（Distributional familiarity motivation, DFM）。** 在心理学文献中，内在动机一般指的是推动机体探索其环境的机制。然而，前述可能系统存在直接变体，它们既简单又直觉上对应着既有人类动机形式。例如，把 UM 的符号反过来，就建模了一种寻找被非常频繁观察到的、从而熟悉的（familiar）情境的动机：

$$
r(e^k) = C \cdot P(e^k)\tag{6}
$$

我们是否应将其视为一种内在动机，将在下文讨论。

<!-- image -->
[图：图 4. 基于预测式知识的内在动机计算方法的一般架构。]

**预测式模型（Predictive models）。** 通常，机器人中的知识与预期不是用完整的概率分布表示的，而是基于预测器（predictors）的使用，如神经网络或支持向量机（support vector machines），它们对未来事件做出直接预测（见图 4）。在这类架构中，同样可以对各种形式的内在动机做计算定义。这些预测器记作 Π，典型地用于在给定当前感知运动情境 SM(t)（可能还有过去感知运动情境）的条件下，预测将在（近或远的）未来发生的某些属性 $Pr^k$ 或感知运动状态 SM。与上文类似，我们将用一般记法 $e^k$ 表示所有属性与状态。我们还将用记法 $SM(t)$ 表示一个编码了当前感知运动情境、可能还有过去情境的结构。于是，一个系统的一般预测记作：

$$
\Pi(SM(t)) = \tilde{e}^k(t+1)\tag{7}
$$

然后我们定义 $E_r(t)$ 为该预测的误差（error），即被预测事件 $\tilde{e}^k(t+1)$ 与实际发生事件 $e^k(t+1)$ 之间的距离：

$$
E_r(t) = \|\tilde{e}^k(t+1) - e^k(t+1)\|\tag{8}
$$

图 4 总结了基于预测式知识的内在动机计算方法的一般架构。

**预测式新异性动机（Predictive novelty motivation, NM）。** 接着很自然地可以提出在这一框架中建模新异性动机的第一种方式。有趣的情境是那些预测误差（prediction error）最高的情境：

$$
r(SM(t)) = C \cdot E_r(t)\tag{9}
$$

其中 C 是常数。这类动机系统的实现例子可见于例如 Barto et al. (2004) 与 Thrun (1995)。

**中间水平新异性动机（Intermediate level of novelty motivation, ILNM）。** 依照提出人类被中等/最优失谐情境吸引的心理学家的观点，可以在前述机制中引入一个定义这一中等新异性水平的阈值 $E_r^{\sigma}$：

$$
r(SM(t)) = C_1 \cdot e^{-C_2 \cdot \|E_r(t) - E_r^{\sigma}\|^2}\tag{10}
$$

其中 $C_1$ 与 $C_2$ 是常数。然而，这一定义的缺点是把阈值的整定留给了人类工程师的直觉。事实上，对整个感知运动空间只用单一阈值在实践中甚至可能相当成问题，因为新异性与相似性的概念在该空间的不同部分可能差异很大，而开发自动自适应阈值机制是一个困难问题。

**学习进步动机（Learning progress motivation, LPM）。** 若干研究者提出了另一种建模最优失谐的方式，它避免了设定阈值的问题，且与上文本论节中描述的信息增益度量相关。它在于用这样一个系统建模内在动机：当预测随时间改善时，该系统生成奖励。于是，系统将试图最大化预测进步（prediction progress），即预测误差的降低。预测进步在 Oudeyer et al. (2007) 中也被称为“学习进步（learning progress）”。要得到形式模型，需要精确而细致地规定这一降低如何计算。确实，正如 Oudeyer et al. (2007) 所论证的，一种可能的天真实现——比较 t 时刻附近的时间窗与 t-θ 时刻附近的时间窗之间的预测误差——实际上是无意义的：例如，它可能把“机器人试图预测风中叶子运动（高度不可预测）的情境”到“机器人只是盯着白墙试图预测墙的颜色是否会变（高度可预测）的情境”之间的转移赋予高奖励。系统不应试图比较非常不同的感知运动情境与质性不同的预测。因此，一种可能是使用一种机制，使机器人能把相似情境归组到区域（regions）$\mathcal{R}_n$ 之中，在区域内比较才有意义。这些区域的数量与边界典型地被自适应更新。然后，对每个这样的区域，机器人监测预测误差的演化，并对它们在过去时间中的整体导数（derivative）建立模型，这就定义了这些区域中的学习进步，从而定义奖励。数学上：

$$
r(SM(t)) = \langle E_r^{\mathcal{R}_n}(t-\Theta) \rangle - \langle E_r^{\mathcal{R}_n}(t) \rangle\tag{11}
$$

其中 SM(t) 属于区域 $\mathcal{R}_n$，而 $\langle E_r^{\mathcal{R}_n}(t) \rangle$ 是预测器在最近 t 次关于属于区域 $\mathcal{R}_n$ 的感知运动情境 SM(t) 的预测中所犯预测误差的均值。关于如何实现这样一个系统的详细研究见 Oudeyer et al. (2007)。

另一种计算学习进步的方式在 Schmidhuber (1991) 中被提出。它在于测量预测器 Π 关于同一感知运动情境 $SM(t)$ 在第一次预测与第二次预测之间的预测误差之差，第二次预测是在预测器刚被一条学习规则更新之后做出的：

$$
r(SM(t)) = E_r(t) - E_r'(t)\tag{12}
$$

其中

$$
E_r'(t) = \|\Pi'(SM(t)) - e^k(t+1)\|\tag{13}
$$

其中 Π′ 是由于预测 $\Pi(SM(t))$ 与对实际结果 $e^k(t+1)$ 的知觉而经历学习更新之后已被更新的预测器。

**预测式惊讶动机（Predictive surprise motivation, SM）。** 与 DSM 类比，也可以用基于预测式知识的框架来建模惊讶动机。如上所解释的，惊讶可以被理解为一个被强烈预期不会发生的事件的发生，或一个被强烈预期发生的事件的不发生。这里，与前几段相反，由于惊讶关联到一个时间跨度很短的特定事件，有必要有一个在每个时间步都显式建模预测（即预期）强度的机制。因此，我们需要引入一个元预测器（metapredictor）MetaΠ，它试图在时刻 t 预测 Π 在时刻 t 的误差 $E_r(t)$ 将会是多少：

$$
\mathsf{Meta}\Pi(SM(t)) = \widetilde{E_r(t)}\tag{14}
$$

其中 $\widetilde{E_r(t)}$ 是 Π 的被预测绝对误差。在技术上，MetaΠ 是与 Π 同类的机器，例如可以是神经网络或支持向量机。它在每个时间步、当实际 $E_r(t)$ 被测量之后被更新。或者，MetaΠ 也可以简单地实现为计算最近的过去中对同一预测的近期误差均值。然后我们可以定义一个对高度惊讶情境提供高奖励的系统，基于预测中的实际误差与预测误差的期望水平之间的比值（惊讶的情境是那些实际预测误差很高、但被预期的误差水平很低的情境）：

$$
r(SM(t)) = C \cdot \frac{E_r(t)}{\mathsf{Meta}\Pi(SM(t))}\tag{15}
$$

其中 C 是常数。

**预测式熟悉性动机（Predictive familiarity motivation, FM）。** 如在信息论模型中那样，上述预测式模型的结构可以用来实现体验熟悉（familiar）情境的动机：

$$
r(SM(t)) = \frac{C}{E_r(t)}\tag{16}
$$

其中 C 是常数。然而这一实现可能易受噪声影响，在真实世界中未必有用，因为它只基于时空上局部（local）的预测。要得到对熟悉性更稳健的系统，一种可能是计算当前感知运动情境邻域中过去预测的一个平滑（smoothed）误差。可以使用 LPM 段中引入的区域（region）概念：

$$
r(SM(t)) = \frac{C}{\langle E_r^{\mathcal{R}_n}(t) \rangle}\tag{17}
$$

其中 $SM(t)$ 落入感知运动区域 $\mathcal{R}_n$。如同 LPM，这一架构假设一种允许渐进构建 $\mathcal{R}_n$ 区域的机制。这一机制可以基于如 Oudeyer et al. (2007) 中的迭代区域分裂（region splitting），或简单地基于与 $SM(t)$ 的距离的一个（可能是自适应的）阈值 $T_f$：

$$
\mathcal{R}_n(SM_i(t)) = \{SM_j(t) \mid \mathsf{dist}(SM_j(t), SM_i(t)) < T_f\}\tag{18}
$$

其中 dist(·,·) 是一个距离度量。

## 基于能力的内在动机模型（Competence-based models of intrinsic motivation）

内在动机的第二种主要计算路径，基于智能体（agent）在达成自我决定的结果或目标方面的能力（competence）的度量。有趣的是，这一路径尚未在计算文献中被研究，但我们认为它蕴含未来研究的巨大潜力。确实，它直接受启发于关于效能（effectance）（White, 1959）、个人因果（personal causation）（De Charms, 1968）、能力与自我决定（competence and self-determination）（Deci and Ryan, 1985）以及“心流（Flow）”（Csikszentmihalyi, 1991）的重要心理学理论。这里的核心是“挑战（challenge）”概念，连同其难度度量与实际表现度量。这里的挑战将是任何被个体自行设定、并试图通过行动去达成的感知运动构型 SM，或任何感知运动构型的属性集合 $\{P_k\}$。于是，挑战在这里是一个自我决定的目标（self-determined goal），记作 $g^k$。决定相关活动有趣性（interestingness）水平的，是达成过程的各种属性，而非被达成的特定目标的“意义”。虽然前几节使用的预测机制或概率模型可以用于目标达成（goal-reaching）架构，但它们不是必需的（例如，可以实现一些通过 Q-learning 试图达成自我生成目标、而从不显式预测未来感知运动情境的系统）。此外，虽然在某些情形下，某些基于能力的与基于知识的内在动机模型可能多少等价，它们往往会产生非常不同的行为。确实，预测一个情境中会发生什么的能力，与为达成一个给定的自我决定目标而改变该情境的能力，只是松散耦合的。

更技术性地，我们在此假设一个认知架构，其中有一个“专知（know-how）”模块 $KH(t_g)$，负责规划动作以达成自我决定的目标 $g^k$，并通过经验学习。还有一个动机模块，将基于 $KH(t_g)$ 的表现赋予奖励。这一架构中有两个时间尺度：对应于原子动作的传统物理时间尺度，记作 $t$；以及与目标达成情节（goal-reaching episodes）的序列相关的抽象时间尺度，记作 $t_g$。一个目标达成情节由在时刻 $t_g$ 设定目标 $g^k(t_g)$ 开始，随后是由 $KH(t_g)$ 决定的一个动作序列以试图达成 $g^k(t_g)$，其时长受超时（timeout）阈值 $T_g$ 约束。在目标被达成或超时终止了 $KH(t_g)$ 之后，新的目标达成情节可以在抽象时刻 $t_g+1$ 开始。在每个情节结束时，已被达成的感知运动构型，记作 $\widehat{g_k(t_g)}$，与初始目标 $g_k(t_g)$ 相比较，以计算 $g^k$ 的（未）达成水平（level of (mis-)achievement）$l_a(g_k, t_g)$：

$$
l_a(g_k, t_g) = \|\widehat{g_k(t_g)} - g_k(t_g)\|\tag{19}
$$

这一达成水平随后将是计算内部奖励的基础，从而也是评价相关目标有趣性水平的基础。最后，有一个负责恰当选择将提供最大奖励的目标的模块，它典型地可以用 CRL 框架中开发的算法实现。图 5 总结了基于能力的内在动机方法的一般架构。

<!-- image -->
[图：图 5. 基于能力的内在动机计算方法的一般架构。]

情节与选项理论（option theory）中的时间上延展的动作相关（Sutton et al., 1999）。然而，据我们所知，本文给出了基于能力的内在动机模型的首次描述。

我们现在给出若干示例系统，它们以奖励的计算方式相区分。

**最大化无能动机（Maximizing incompetence motivation, IM）。** 第一种基于能力的内在动机方法可以是这样一个系统：推动机器人去设定其表现最差的挑战/目标。这是一种对最大难度挑战的动机。可以实现为：

$$
r(SM(t), g_k, t_g) = C \cdot l_a(g_k, t_g)\tag{20}
$$

注意这里以及在基于能力的路径的所有地方，奖励只在情节结束时生成。前一方程只度量单一尝试/情节中试图达成一个给定目标的无能，从而度量有趣性。构建一个把机器人此前情节中关于同一目标的表现纳入考量的奖励系统可能是有用的，尤其对那些表现方差很大的目标。方程为：

$$
r(SM(t), g_k, t_g) = C \cdot \langle l_a(g_k, t_g) \rangle\tag{21}
$$

其中 $\langle l_a(g_k, t_g) \rangle$ 表示在设定了该目标的最近 t 个情节中试图达成 $g_k$ 的表现均值。这一奖励系统还可以进一步更新，以允许在计算一个目标的有趣性时进行泛化（generalization）。在前两个方程中，一个给定目标 $g_k$ 的有趣性不依赖于机器人在相似目标上的表现。然而，这可能是一个有用的特性：试想一个玩弄自己手臂的机器人，发现试着抓取前方桌上 30 cm 处的一个物体是有趣的。若机器人能推断出试着抓取 35 cm 处的物体同样有趣、而无需从头重算有趣性水平，将是有潜在用处的。为实现这一点，一个可能的解是使用如下类型的方程：

$$
r(SM(t), g_k, t_g) = C \cdot \langle l_a(g_k^{\sigma g}, t_g) \rangle\tag{22}
$$

其中 $\langle l_a(g_k^{\mathcal{R}_n}, t_g) \rangle$ 表示试图达成满足 dist$(g_k, g_k^{\sigma g}) < \sigma_g$ 的目标 $g_k^{\sigma g}$ 的表现均值，dist(·,·) 是距离函数，$\sigma_g$ 是数值阈值。于是，用这一公式，在计算有趣性时，与当前目标距离小于给定阈值的所有目标都被视为等价于当前目标。

**最大化能力进步动机——又称心流动机（Maximizing competence progress - aka Flow motivation, CPM）。** 最大化无能并不能很好地建模由 (Csikszentmihalyi, 1991) 提出的最优挑战与“心流（flow）”的心理学模型。心流指与难度最优的活动相关联的愉悦状态：既不太容易也不太困难。由于一个目标的难度可以用达成该目标的（平均）表现来建模，建模心流的一种可能方式是引入两个阈值来定义最优难度区间。然而，阈值的使用可能相当脆弱，需要手工整定，且可能需要复杂的自适应机制在机器人生命周期中更新这些阈值。可以采取另一条避免使用阈值的路径。它在于把一个挑战的有趣性定义为机器人反复尝试达成它的过程中所体验到的能力进步（competence progress）。于是，一个机器人起初不擅长、但正迅速变好的挑战将具有高奖励。于是，实现 CPM 的第一种方式是：

$$
r(SM(t), g_k, t_g) = C \cdot (l_a(g_k, t_g - \Theta) - l_a(g_k, t_g))\tag{23}
$$

对应于任务 $g_k$ 的当前表现与上一次尝试 $g_k$（在记作 $t_g - \theta$ 的时刻）时的表现之差。同样，由于目标达成可能存在高方差，可以使用平滑后的差值：

$$
r(SM(t), g_k, t_g) = C \cdot (\langle l_a(g_k, t_g - \theta) \rangle - \langle l_a(g_k, t_g) \rangle)\tag{24}
$$

其中 $\langle l_a(g_k, t_g) \rangle$ 是最近 t 个对应情节中试图达成 $g_k$ 的平均表现，$\langle l_a(g_k, t_g - \Theta) \rangle$ 是在情节 $t_g - \theta - \tau$ 与 $t_g - \theta$ 之间试图达成 $g_k$ 的平均表现。同样，这一公式不包含泛化机制，在连续感知运动空间中可能显得低效。可以用与 IM 中相同的机制更新它：

$$
r(SM(t), g_k, t_g) = C \cdot (\langle l_a(g_k^{\sigma g}, t_g - \Theta) \rangle - \langle l_a(g_k^{\sigma g}, t_g) \rangle)\tag{25}
$$

记号与 IM 中相同。区域的概念（见 LPM）同样可以在此使用。

**最大化能力动机（Maximizing competence, CM）。** 在这一基于能力的形式框架中，也可以实现一种推动机器人去体验已被熟练掌握活动的动机。可以使用如下公式：

$$
r(SM(t), g_k, t_g) = \frac{C}{\langle l_a(g_k, t_g) \rangle^{\mathcal{R}_n(g_k)}}\tag{26}
$$

其中 $g_k$ 落入目标空间的区域 $\mathcal{R}_n$。这一架构假设一种允许渐进构建 $\mathcal{R}_n$ 区域的机制。这一机制可以基于如 Oudeyer et al. (2007) 中的迭代区域分裂，或简单地基于与 $g_k$ 的距离的一个（可能是自适应的）阈值 $\sigma_g$：

$$
\mathcal{R}_n(g_k) = \{g_l \mid \mathsf{dist}(g_k, g_l) < \sigma_g\}\tag{27}
$$

其中 dist(·,·) 是一个距离度量。

## 内在动机的形态学模型（Morphological models of intrinsic motivation）

前两种动机计算路径基于刻画认知学习系统与感知运动取值流之间关系的度量。可以采取的第三种路径则只基于感知运动取值流的数学/形态学（morphological）属性，而不论内部认知系统可能预测或掌握什么。图 6 总结了内在动机形态学计算方法的一般架构。我们现在给出可能的内在动机形态学计算模型的两个例子。

**同步性动机（Synchronicity motivation, SyncM）。** 这里给出的同步性动机基于若干感知运动通道之间短期相关（或约化信息距离，reduced information distance）的信息论度量。有了这样一种动机，对尽可能多的感知运动通道之间存在高短期相关的情境，会显得非常有趣。可以用如下方式形式化。

让我们把感知运动空间 SM 视为 n 个信息源 {SMᵢ} 的集合，这些信息源的可能取值通常对应于属于任意数量箱子（bins）的元素。在每个时刻 t，一个元素 SMʲ 对应信息源 SMⱼ，可使用如下记法：SMⱼ(t) = smⱼ。

两个信息源 SMᵢ 与 SMⱼ 之间的条件熵（conditional entropy）可以计算为：

$$
H(SM_j \mid SM_i) = - \sum_{sm_i} \sum_{sm_j} p(sm_i, sm_j) \mathsf{log}_2 p(sm_i, sm_j)\tag{28}
$$

其中 p(smⱼ|smᵢ) = p(smᵢ, smⱼ)/p(smᵢ)。

<!-- image -->
[图：图 6. 内在动机的形态学计算方法的一般架构。]

$H(SM_j \mid SM_i)$ 传统上被解释为：在 SMᵢ 的取值已知的情况下与 SMⱼ 相关联的不确定性。

我们可以用多种方式度量两个信息源之间的同步性 s(SMᵢ, SMⱼ)。

两个信息源之间的 Crutchfield 归一化信息距离（normalized information distance）（它是一个度量，metric）定义为（Crutchfield, 1990）：

$$
d(SM_j \mid SM_i) = \frac{H(SM_i \mid SM_j) + H(SM_j \mid SM_i)}{H(SM_i \mid SM_j)}\tag{29}
$$

基于这一定义我们可以把同步性定义为

$$
s_1(SM_j, SM_i) = \frac{C}{d(SM_j, SM_i)}\tag{30}
$$

或者，我们可以把同步性同化为互信息（mutual information）：

$$
\begin{array}{c}
{s_2(SM_j, SM_i) = MI(SM_i, SM_j)} \\
{\qquad = H(SM_i) + H(SM_j) - H(SM_i, SM_j)}
\end{array}\tag{31}
$$

我们也可以度量两个时间序列之间的相关：

$$
s_2(SM_j, SM_i) = \frac{\sum_t (sm_i(t) - sm_i) \cdot (sm_j(t) - sm_j)}{\sqrt{\sum_t (sm_i(t) - sm_i)^2} \cdot \sqrt{\sum_t (sm_j(t) - sm_j)^2}}\tag{32}
$$

无论使用何种度量，我们都可以把与给定的近期时间窗相关联的奖励定义为：

$$
r(SM(t)) = C \cdot (\sum_i \sum_j s(SM_j, SM_i))\tag{33}
$$

两个（或多个）信息源之间的同步（synchrony）检测被认为是婴儿学习与认知发展的关键机制（例如，物体交互技能 Watson, 1972；自我建模 Rochat and Striano, 2000；词汇学习 Gogate and Bahrick, 1998）。虽然一般不作为动机变量，同步性度量已被用于若干近期的形式模型（例如 Hershey and Movellan, 2000; Prince et al., 2003）。

**稳定性动机（Stability motivation, StabM）与方差动机（Variance motivation, VarM）。** 稳定性动机推动机体行动以使感知运动流保持接近其平均值：

$$
r(SM(t)) = \frac{C}{SM(t) - \langle SM(t) \rangle_\tau}\tag{34}
$$

其中 ⟨SM(t)⟩_τ 是感知运动向量在最近 τ 个时间步上的平均值。

与稳定性动机相反，方差动机奖励那些感知运动通道取值具有高方差的情境：

$$
r(SM(t)) = C \cdot (\|SM(t) - \langle SM(t) \rangle_\tau\|)\tag{35}
$$

其中 ⟨SM(t)⟩_τ 是感知运动向量在最近 τ 个时间步上的平均值。

在某些情境中，两者都可被视为合理的（rational）策略。稳定性使人能够通过行动降低知觉固有的不稳定性，例如可以导致跟踪行为（Kaplan and Oudeyer, 2003）。相反，方差动机可以导致探索远离平衡的未知感知运动因果性（sensorimotor contingencies）。

## 非内在动机系统的计算模型示例（EXAMPLES OF COMPUTATIONAL MODELS OF NON-INTRINSIC MOTIVATION SYSTEMS）

为清晰起见，我们将在本节简短地给出一些非内在的（non-intrinsic）、然而却是内部（internal）的动机系统的计算模型。

例如，设想人们想建造一台具有社会在场动机（social presence motivation）的机器人，且这台机器人能识别其环境中的人脸。如果机器人没有看到足够多的人脸，它应当表现得像孤独一样并寻找社会互动；如果看到的太多，它应当感到不堪重负并试图回避新的社会互动。如果我们把最近 t 个时间帧内看到人脸的平均数定义为 F(t)，把最优平均人脸数定义为 $F_\tau^{\sigma}$，则社会平衡交互（socially balanced interaction, SocM）的奖励可以定义为（$C_1$ 与 $C_2$ 为待定的常数）：

$$
r(SM(t)) = C_1 \cdot e^{-C_2 \|F_\tau(t) - F_\tau^{\sigma}\|^2}\tag{36}
$$

类似地，我们可以为能量维持（energy maintenance）编写奖励，推动机器人把能量维持在一个中间水平（EnerM）（介于饥饿与过食之间），方法是把时刻 t 的能量定义为 E(t)、最优能量水平定义为 E^σ，并使用如下奖励公式：

$$
r(SM(t)) = C_1 \cdot e^{-C_2 \|E(t) - E^{\sigma}\|^2}\tag{37}
$$

这类动机系统已被许多研究者研究（例如相关的一系列例子见 Breazeal, 2002）。它们非常适于模拟自然的、复杂的平衡行为。

然而，它们不应被视为内在动机系统，因为它们是基于与特定感觉通道（能量水平、看到的人脸数）相关的度量来定义的。

## 讨论（DISCUSSION）

尽管我们所呈现的内在动机计算路径多种多样，它们之间存在一个收敛点。所描述的每一个模型都以感知运动取值流的属性、及其与系统的知识和专知（know-how）的关系之术语，给出了对内在动机的某种解释，而与所涉及感觉通道的意义无关。这一定义与基于行为观察的定义（除活动本身之外没有明显目标的活动）形成鲜明对照，起初看起来可能反直觉，因为其行为后果只能通过计算建模与机器人实验加以探索。此外，这些内在动机系统的简单变体不会推动系统去探索（例如 FM、CM 或 StabM 会推动机器人静止不动），但我们相信，把它们也概念化为内在动机在形式上更为一致，即使某些心理学家不会这样做。事实上，我们相信，这种对内在动机的系统性计算路径，可以在组织围绕其定义本身、以及其在行为、学习与发展中的角色的辩论中扮演关键角色，特别是因为它使得人们能够在清晰定义的共同基础之上讨论各种假说。

图 7 的表格呈现了本文讨论的全部模型及其所属家族（内在 vs. 外在、自适应 vs. 固定、基于知识/基于能力/形态学、信息论或预测式、稳态 vs. 异稳态）。对每个模型，我们给出其探索潜力（这种动机在多大程度上可能导致探索与研究行为）与组织潜力（这种动机在多大程度上可能导致有结构、有组织的行为）的粗略估计。我们还估计了每类模型的计算成本与迄今已有的计算模型数量。这张表有助于厘清内在动机模型的图景，展示某些家族的潜力与尚少有人研究的区域。确实，我们相信大部分挑战仍在前方。

<table><tr><td rowspan=1 colspan=5></td><td rowspan=1 colspan=1>稳态(-) vs 异稳态(+)</td><td rowspan=1 colspan=1>动机</td><td rowspan=1 colspan=1>探索潜力</td><td rowspan=1 colspan=1>组织潜力</td><td rowspan=1 colspan=1>计算成本</td><td rowspan=1 colspan=1>既有模型</td></tr><tr><td rowspan=17 colspan=1>内部</td><td rowspan=15 colspan=1>内在</td><td rowspan=12 colspan=1>自适应</td><td rowspan=9 colspan=1>基于知识</td><td rowspan=4 colspan=1>信息论</td><td rowspan=3 colspan=1>+</td><td rowspan=1 colspan=1>UM</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>IGM</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>DSM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>DFM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=5 colspan=1>预测式</td><td rowspan=1 colspan=1>+</td><td rowspan=1 colspan=1>NM</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>***</td></tr><tr><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>ILNM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=2 colspan=1>+</td><td rowspan=1 colspan=1>LPM</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>?</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>SM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>?</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>FM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>□（原文符号在转换中丢失，无法复原）</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=3 colspan=2>基于能力</td><td rowspan=2 colspan=1>+</td><td rowspan=1 colspan=1>IM</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>?</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>CPM</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>***</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>CM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>□（原文符号在转换中丢失，无法复原）</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=3 colspan=1>固定</td><td rowspan=3 colspan=2>形态学</td><td rowspan=2 colspan=1>1</td><td rowspan=1 colspan=1>SyncM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>StabM</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=1 colspan=1>+</td><td rowspan=1 colspan=1>VarM</td><td rowspan=1 colspan=1>**</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>*</td></tr><tr><td rowspan=2 colspan=4>外在</td><td rowspan=1 colspan=1>二</td><td rowspan=1 colspan=1>SocM</td><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>**</td></tr><tr><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>EnerM</td><td rowspan=1 colspan=1>/</td><td rowspan=1 colspan=1>1</td><td rowspan=1 colspan=1>*</td><td rowspan=1 colspan=1>***</td></tr></table>

[图：图 7. 该表呈现本文讨论的全部模型及其所属家族。对每个模型，我们给出其探索潜力与组织潜力、以及计算成本和各类别既有计算模型数量的粗略估计。]

第一，现在至关重要的是理解：这类对所涉感觉通道“不可知（agnostic）”的离身计算机架构，当与特定的身体化（embodiment）相结合并被置于特定环境中时，如何能导致特定的行为组织。同一个内在动机系统，取决于它所连接的物理或虚拟系统的类型，可以导致非常不同的结果。尤其有趣的是，这类架构允许把身体化视为一个与控制系统清晰分离的、可控制的变量。

第二，这一类型学可作为一种邀请，邀请人们以系统的方式研究：在我们所考察的这些内在动机系统中，哪几类能够在某些方面导致与儿童发展中观察到的相类似的开放式发展轨迹（open-ended developmental trajectories）。我们过去的研究与实验提供了若干线索，表明内在的、异稳态的、自适应的动机模型是前景最大的，因为它们能同时兼具高探索潜力与高组织潜力（例如，信息增益动机——IGM、最大化学习进步动机——LPM、最大化能力进步（心流）动机——CPM）。这类动机系统推动机器人以一种渐进的、有组织的方式探索其世界，避开在其发展的给定阶段过于容易或过于困难的情境或目标。例如，在 Oudeyer et al. (2007) 中，我们给出了操场实验（Playground Experiment），其中 LPM 模型的一个实现被表明能够使一条复杂的发展轨迹自组织（self-organization）。在这一实验中，机器人对其身体与环境知之甚少：它基本上只知道其传感器与电机的无标签清单（但例如并不知道其中一些与视觉相关、另一些与触觉相关）。我们已经表明，学习进步动机（Learning Progress Motivation）与一个恰当的区域分裂机制相结合，使机器人能够自举（bootstrap）出宽泛的感知运动类别及相关行为。典型地，机器人从一个随机身体咿呀（body babbling）阶段开始，随后进入一个聚焦地玩弄身体各部位的阶段，随后进入一个对物体尝试不同种类动作的阶段，随后进入一个机器人发现动作与物体之间特定可供性（affordances）的阶段（例如，机器人反复尝试咬一个可咬的物体，或对一个远处的“成人”机器人发声）。

这些既有实现都只聚焦于整合在特定机器人与特定环境中的某一特定种类的动机。现在的一个巨大挑战是理解哪种行为轨迹与每个系统相关联，并推进我们对它们在认知开放式发展中所起作用的理解。此外，我们在本文中识别出的其它类型的内在动机系统也很可能在某些情境中同样有趣，带来相关的行为或新的学习机会。

第三，带内在动机系统的机器人或仿真实验应当能够为心理学与神经生理学两方面的数据带来新的启示。我们已经讨论过这些模型、特别是 LPM 模型与发展心理学中某些研究辩论的相关性（例如语言习得（Oudeyer and Kaplan, 2006）、模仿的发展（Kaplan and Oudeyer, 2007b）），并对假定的潜在神经回路提出了一些假说（Kaplan and Oudeyer, 2007a）。然而，由于在这些领域中实际处理内在动机的实验工作极少，在大多数情况下，这些新模型是对开展新实验的邀请。

最后，我们必须研究这些系统在实践上的可应用方面。具有内在动机的机器令人着迷。然而，在某些应用情境中，它们的内在开放性是一种弱点。学习如何设计这类新型机器，使其巨大潜力能在实践中被释放，是我们仍须应对的主要挑战之一。

## 利益冲突声明（CONFLICT OF INTEREST STATEMENT）

作者声明，本研究是在不存在任何可被解释为潜在利益冲突的商业或财务关系的情况下进行的。

## 致谢（ACKNOWLEDGEMENTS）

作者感谢审稿人的有益意见。

## 参考文献（REFERENCES）

Arkin, R. (2005). Moving up the food chain: motivation and emotion in behavior based robots. In Who Needs Emotions: The Brain Meets the Robot, J. Fellous and M. Arbib, eds (Oxford University Press), pp. 245-270.

Arkin, R., Cervantes-Perez, F., and Weitzenfeld, A. (1998). Ecological robotics: a schematheoretic approach. In Intelligent Robots: Sensing, Modelling and Planning, R. Bolles, H. Bunke and H. Noltemeier, eds (Singapore, World Scientific), pp. 377-393.

Barto, A., and Simsek, O. (2005). Intrinsic motivation for reinforcement learning system. In Proceedings of the Thirteenth Yale Workshop on Adaptive and Learning Systems, New Haven, CT, Yale University.

Barto et al. (2004). Intrinsically motivated learning of hierarchical collections of skills. In Proceedings of the 3rd International Conference on Development and Learning (ICDL 2004). Salk Institute, San Diego.

Berlyne, D. (1960). Conflict, Arousal and Curiosity. New York, NY, McGraw-Hill.

Bonarini, A., Lazaric, A., and Restelli, M. (2006). Self-development frame work for reinforcement learning agents. Proceedings of the Fifth International Conference on Development and Learning, Bloomington, IN, USA.

Braitenberg, V. (1984). Vehicles: Experiments in Synthetic Psychology. Cambridge, MA, Bradford Books/MIT Press.

Breazeal, C. (2002). Designing Sociable Robots. Cambridge, MA, Bradford Books/MIT Press.

Crutchfield, J.P. (1990). Information and its metric. In Nonlinear Structures in Physical Systems - Pattern Formation, Chaos, and Waves, L. Lam and H.C. Morris, eds (New York, NY, Springer Verlag), pp. 119-130.

Csikszentmihalyi, M. (1991). Flow: The Psychology of Optimal Experience. New York, NY, Harper Perennial.

De Charms, R. (1968). Personal Causation: The Internal Affective Determinants of Behavior. New York, NY, Academic Press.

Deci, E., and Ryan, R. (1985). Intrinsic Motivation and Self-Determination in Human Behavior. New York, NY, Plenum Press.

Dember, W.N., and Earl, R.W. (1957). Analysis of exploratory, manipulatory and curiosity behaviors. Psychol. Rev. 64, 91-96.

Endo, Y., and Arkin, R. (2001). Implementing tolman's schematic sowbug: behaviorbased robotics in the 1930's. Proceedings of the IEEE International Conference on Robotics and Automation, Seoul, Korea.

Fedorov, V. (1972). Theory of Optimal Experiment. New York, NY, Academic Press.

Festinger, L. (1957). A Theory of Cognitive Dissonance. Evanston, Row, Peterson.

Fujita, M., Costa, G., Takagi, T., Hasegawa, R., Yokono, J., and Shimomura, H. (2001). Experimental results of emotionally grounded symbol acquisition by four-legged robot. In Proceedings of Autonomous Agents 2001, J. Muller, ed. Montreal, Canada.

Gogate, L.J., and Bahrick, L. (1998). Intersensory redundancy of kinematic primitives forvisual speech perception facilitates learning of arbitrary relations between vowel sounds and objects in seven-month-old infants. J. Exp. Child Psychol. 69, 133-149.

Harlow, H. (1950). Learning and satiation of response in intrinsically motivated complex puzzle performances by monkeys. J. Comp. Physiol. Psychol. 43, 289-294.

Hershey, J., and Movellan, J. (2000). Audio-vision: using audio-visual synchrony to locate sounds. In Advances in Neural Information Processing Systems 12, T. Solla and K.-R. Muller, eds (Cambridge, MA, MIT Press).

Huang, X., and Weng, J. (2002). Novelty and reinforcement learning in the value system of developmental robots. In Proceedings of the 2nd International Workshop on Epigenetic Robotics: Modeling Cognitive Development in Robotic Systems, Lund University Cognitive Studies, Vol. 94, C. Prince, Y. Demiris, Y. Marom, H. Kozima and C. Balkenius, eds (Edinburgh, Scotland, Lund University), pp. 47-55.

Huang, X., and Weng, J. (2004). Motivational system for human-robot interaction in Proceedings of the ECCV International Workshop on Human-Computer Interaction, Prague.

Hull, C.L. (1943). Principles of Behavior: An Introduction to Behavior Theory. New York, NY, Appleton-Century-Croft.

Hunt, J.M. (1965). Intrinsic motivation and its role in psychological development. Nebr. Symp. Motiv. 13, 189-282.

Kagan, J. (1972). Motives and development. J. Pers. Soc. Psychol. 22, 51-66.

Kaplan, F., and Oudeyer, P.-Y. (2003). Motivational principles for visual know-how development. In Proceedings of the 3rd International Workshop on Epigenetic Robotics: Modeling Cognitive Development in Robotic Systems, Lund University Cognitive Studies, Vol. 101, C. Prince, L. Berthouze, H. Kozima, D. Bullock, G. Stojanov and C. Balkenius, eds (Boston, USA, Lund University), pp. 73-80.

Kaplan, F., and Oudeyer, P.-Y. (2007a). In search of the neural circuits of intrinsic motivation. Front. Neurosci. 1, 225-236.

Kaplan, F., and Oudeyer, P.-Y. (2007b). The progress-drive hypothesis: an interpretationof early imitation. In Models and Mechanisms of Imitation and Social Learning: Behavioural, Social and Communication Dimensions, C. Nehaniv and K. Dautenhahn, eds (New York, Cambridge University Press), pp. 361-377.

Konidaris, G., and Barto, A. (2006). An adaptive robot motivational system. In From Animals to Animats 9: Proceedings of the 9th International Conference on Simulation of Adaptive Behavior (Roma, Italy, SAB-06).

Marshall, J., Blank, D., and Meeden, L. (2004). Anemergentframework for self-motivation in developmental robotics. In Proceedings of the 3rd International Conference on Development and Learning (CDL 2004). Salk Institute, San Diego.

McFarland, D., and Bosser, T. (1994). Inteligent Behavior in Animals and Robots. Cambridge, MA, MIT Press.

Merrick, K., and Maher, M.-L. (2008). Motivated learning from interesting events: adaptive, multitask learning agents for complex environments. Adapt. Behav. (in press).

Montgomery, K. (1954). The role of exploratory drive in learning. J. Comp. Physiol. Psychol. 47, 60-64.

Oudeyer, P.-Y., and Kaplan, F. (2006). Discovering communication. Connect. Sci. 18, 189-206.

Oudeyer, P.-Y., Kaplan, F., and Hafner, V. (2007). Intrinsic motivation systems forautonomous mental development. IEEE Trans. Evol. Comput. 11, 265-286.

Oudeyer, P.-Y., Kaplan, F., Hafner, V.V., and Whyte, A. (2005). The playground experiment: task-independent development of a curious robot. In Proceedings of the AAAI Spring Symposium on Developmental Robotics, 2005, D. Bank and L. Meeden, eds (Stanford, AAAI), pp. 42-47.

Prince, C., Hollch, G., Helder, N, Mislivec, E., Reddy, A., Salunke, S., and Memon, N. (2003). Taking synchrony seriously: a perceptual-level model of infant synchrony detection.

In Proceedings of the Fourth International Workshop on Epigenetic Robotics: Modeling Cognitive Development in Robotic Systems, Lund University Cognitive Studies, Vol. 117, L. Berthouze, H. Kozima, C. Prince, G. Sandini, G. Stojanov, G. Metta and C. Balkenius, eds (Edinburgh, Scotland, Lund University).

Rochat, P., and Striano, T. (2000). Perceived self in infancy. Infant Behav. Dev. 23, 513-530.

Roy, N., and McCallum, A. (2001). Towards optimal active learning through sampling estimation of error reduction. In Proceedings of the 18th International Conference on Machine Learning. Williamstown, MA, USA, Morgan Kaufmann Publishers Inc.

Ryan, R.M., and Deci, E.L. (2000). Intrinsic and extrinsic motivations: classic definitions and new directions. Contemp. Educ. Psychol. 25, 54-67.

Schmidhuber, J. (1991). Curious model-building control systems. In Proceedings of theInternational JointConferenceonNeuralNetworks, Vol.2. ingapore, Epp. 1458-1463.

Skinner, B. (1953). Science and Human Behavior. New York, NY, Macmillan.

Suton, R., and Barto, A. (1998). Reinforcement Learning: An Introduction. Cambridge, MA, MIT Press.

Sutton, R., Precup, D., and Singh, S. (1999). Between MDPs and semi-MDPs: a framework for temporal abstraction in reinforcement learning. Artif. Intell. 112, 181-211.

Thrun, S. (1995). Exploration in active learning. In Handbook of Brain Science and Neural Networks, M. Arbib, ed (Cambridge, MA, MIT Press).

White, R. (1959). Motivation reconsidered: the concept of competence. Psychol. Rev. 66, 297-333.

Watson, J.S. (1972). Smiling, cooing, and the game. Merril Palmer Q. 18, 23-339.

## 笔记配图

![img_001](./2007-Oudeyer-Intrinsic-Motivation-Typology.assets/img_001.jpeg)
![img_002](./2007-Oudeyer-Intrinsic-Motivation-Typology.assets/img_002.png)
![img_003](./2007-Oudeyer-Intrinsic-Motivation-Typology.assets/img_003.jpeg)
![img_004](./2007-Oudeyer-Intrinsic-Motivation-Typology.assets/img_004.jpeg)
![img_005](./2007-Oudeyer-Intrinsic-Motivation-Typology.assets/img_005.jpeg)
![img_006](./2007-Oudeyer-Intrinsic-Motivation-Typology.assets/img_006.jpeg)

## 术语对照表

| 英文 | 中文 |
| --- | --- |
| intrinsic motivation | 内在动机 |
| extrinsic motivation | 外在动机 |
| internal motivation | 内部动机 |
| external motivation | 外部动机 |
| typology | 类型学 |
| curiosity | 好奇 |
| novelty | 新异性 |
| prediction error | 预测误差 |
| learning progress | 学习进步 |
| reward | 奖励 |
| computational reinforcement learning (CRL) | 计算强化学习 |
| sensorimotor | 感知运动 |
| sensorimotor flow | 感知运动流 |
| cognitive dissonance | 认知失调 |
| optimal incongruity | 最优失谐 |
| effectance | 效能（感） |
| personal causation | 个人因果 |
| competence | 能力 |
| self-determination | 自我决定 |
| self-determined goal | 自我决定的目标 |
| flow | 心流 |
| challenge | 挑战 |
| drive | 驱力 |
| homeostatic | 稳态的 |
| heterostatic | 异稳态的 |
| instrumentalization | 工具化 |
| knowledge-based models | 基于知识的模型 |
| competence-based models | 基于能力的模型 |
| morphological models | 形态学模型 |
| information theoretic / distributional models | 信息论/分布式模型 |
| predictive models | 预测式模型 |
| predictor | 预测器 |
| metapredictor | 元预测器 |
| uncertainty motivation (UM) | 不确定性动机 |
| information gain motivation (IGM) | 信息增益动机 |
| distributional surprise motivation (DSM) | 分布式惊讶动机 |
| distributional familiarity motivation (DFM) | 分布式熟悉性动机 |
| predictive novelty motivation (NM) | 预测式新异性动机 |
| intermediate level of novelty motivation (ILNM) | 中间水平新异性动机 |
| learning progress motivation (LPM) | 学习进步动机 |
| predictive surprise motivation (SM) | 预测式惊讶动机 |
| predictive familiarity motivation (FM) | 预测式熟悉性动机 |
| maximizing incompetence motivation (IM) | 最大化无能动机 |
| maximizing competence progress motivation (CPM) | 最大化能力进步动机 |
| maximizing competence (CM) | 最大化能力动机 |
| synchronicity motivation (SyncM) | 同步性动机 |
| stability motivation (StabM) | 稳定性动机 |
| variance motivation (VarM) | 方差动机 |
| social presence motivation | 社会在场动机 |
| energy maintenance motivation (EnerM) | 能量维持动机 |
| interestingness | 有趣性 |
| exploration potential | 探索潜力 |
| organization potential | 组织潜力 |
| open-ended development | 开放式发展 |
| developmental robotics | 发展机器人学 |
| embodiment | 身体化 |
| affordance | 可供性 |
| body babbling | 身体咿呀 |
| bootstrap | 自举 |
| region splitting | 区域分裂 |
| action selection | 动作选择 |
| entropy | 熵 |
| mutual information | 互信息 |
| normalized information distance | 归一化信息距离 |
| goal-reaching episode | 目标达成情节 |
| sensorimotor contingencies | 感知运动因果性 |
| consummatory response | 完成性反应 |
| satiation / satiated | 饱足 |
| adaptive / fixed motivation system | 自适应/固定动机系统 |
| operational | 可操作的 |
