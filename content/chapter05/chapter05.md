# TensorFlow 在运行中的一些基本的例子
&emsp;&emsp;在本章中, 我们将解释 TensorFlow 背后的主要计算概念, 即计算图模型, 并实现线性回归和逻辑回归。<br>
&emsp;&emsp;本章主要介绍以下主题:<br>
&emsp;&emsp;&emsp;&emsp;1.单个神经元的容量和激活功能<br>
&emsp;&emsp;&emsp;&emsp;2.激活函数<br>
&emsp;&emsp;&emsp;&emsp;3.前馈神经网络<br>
&emsp;&emsp;&emsp;&emsp;4.多层网络的需求<br>
&emsp;&emsp;&emsp;&emsp;5.TensorFlow 的术语回顾<br>
&emsp;&emsp;&emsp;&emsp;6.线性回归模型-构建与训练<br>
&emsp;&emsp;&emsp;&emsp;7.逻辑回归模型-构建和训练<br>
&emsp;&emsp;我们将首先说明单个神经元实际上能做什么以及它的模型，并且基于这点，将出现对多层网络的需求。接下来，我们将进一步阐述在TensorFlow中使用的以及可用的一些主要概念和工具，以及如何使用这些工具来构建简单的示例，如线性回归和逻辑回归。<br>
## 单个神经元的容量
&emsp;&emsp;神经网络是一种计算模型，其灵感主要来自于人脑的生物神经网络处理输入信息的方式。神经网络在机器学习研究(特别是深度学习)和工业应用方面取得了巨大突破，例如在计算机视觉、语音识别和文本处理方面取得了突破性成果。在这一章中，我们将试着去理解一种叫做多层感知器的神经网络。<br>
## 生物运作原理和连系
&emsp;&emsp;我们大脑的基本计算单元叫做神经元, 我们的神经系统中大约有860亿个神经元, 它大约与个![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(1).gif)突触相连。<br>
&emsp;&emsp;图1显示了一个生物神经元。图2显示了相应的数学模型。在生物神经元的绘制中, 每个神经元接收来自其树突的传入信号, 然后沿轴突产生输出信号, 轴突通过它分支上的突触连接到其他神经元。<br>
&emsp;&emsp;在神经元的相应数学计算模型中, 沿轴突传播的信号![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(3).gif)与系统中另一个神经元的树突![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(4).gif)进行乘法运算，这种运算是基于该突触处的权重![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn.gif)。这个想法是, 这个的主要目的是通过网络学习，得到突触的权重或者说它的强度![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(2).gif)，它们是控制一个特定神经元对另一个神经元的影响程度。<br>
&emsp;&emsp;此外，在图2的基本计算模型中，树突将信号传送到主细胞体，并将其全部相加。如果最终结果超过某个阈值，神经元就会在计算模型中被激活。<br>
&emsp;&emsp;另外，值得一提的是我们需要控制轴突输出的峰值，所以我们使用了一种叫做激活函数的东西。实际上，一个常见的激活函数选择是sigmoid函数，因为它需要一个实值输入(求和以后的信号强度)，并将其压缩为0到1之间。我们将在下面的部分中看到这些激活函数的详细信息:<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/1.png)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 1 "大脑的计算单元"<br>
&emsp;&emsp;生物模型有相应的基本数学模型:<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/2.png)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 2 计算机中的神经元模型<br>
&emsp;&emsp;神经网络的基本计算单位是神经元，通常称为节点或单位。它从其他节点或外部源接收输入并计算输出。每个输入都有一个相关的权重(w)，它是根据相对于其他输入的重要性来分配的。该节点将函数![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(5).gif)(我们稍后定义)应用于其输入的加权和。因此，神经网络的基本计算单元一般称为神经元或者称为节点或单元。<br>
&emsp;&emsp;这个神经元从前一个神经元甚至外部源接收它的输入，然后它对这个输入做一些处理来产生所谓的激活。这个神经元的每个输入都与它自己的权重相关联，权重代表了这个神经元的强度、连接以及输入的重要性。<br>
&emsp;&emsp;因此,神经网络的的最终输出是由其权重![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(2).gif)加权的输入求和,然后神经元通过激活函数传递求和输出。<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/3.png)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 3 "单个神经元<br>
## 激活函数
&emsp;&emsp;神经元的输出如图3所示，并通过一个向输出引入非线性的激活函数进行计算。这个![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(5).gif)叫做激活函数。激活功能的主要目的是:<br>
&emsp;&emsp;&emsp;&emsp;1.在神经元的输出中引入非线性。这一点很重要, 因为大多数真实世界的数据是非线性的, 我们希望神经元学习这些<br>
&emsp;&emsp;&emsp;&emsp;&emsp;非线性表示。<br>
&emsp;&emsp;&emsp;&emsp;2.将输出压缩在一个特定范围内。<br>
&emsp;&emsp;每个激活函数 (或非线性) 都取一个数字, 并对其进行某种固定的数学运算。在实践中，您可能会遇到一些激活函数。<br>
&emsp;&emsp;接下来我们将简要介绍最常见的激活函数。<br>
### sigmoid
&emsp;&emsp;在历史上，sigmoid函数的激活功能在研究人员中广泛使用。该函数接受实值输入并将其压缩到0 - 1之间，如下图所示:<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/4.png)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 4 "sigmoid函数"<br>
### Tanh
&emsp;&emsp;Tanh 是另一个激活函数, 它容忍一些负值。Tanh 接受一个实值输入, 并将它们限制到 [-1、1]<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(6).gif)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/8.png)<br>
### ReLU
&emsp;&emsp;整流线性单元(ReLU)不能容忍负值，因为它接受实值输入并将其阈值设为零(将负值换成零):<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(1).gif)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/5.png)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 6 "Relu"激活函数<br>
&emsp;&emsp;**偏置的重要性:** 偏置的主要功能是为每个节点提供一个可训练的常量值(除了节点接收的正常输入之外)。请参阅(https://stackoverflow/com/quertions/2480650/role–of–bias–in–neural–networks) 以了解更多关于偏置在神经元中的作用。<br>
## 前馈神经网络
&emsp;&emsp;前馈神经网络是第一个也是最简单的人工神经网络。它包含多层排列的多个神经元(节点)。相邻层的节点之间有连接或边。所有这些连接都有相关的权重。<br>
&emsp;&emsp;前馈神经网络的一个例子如图7所示:<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/6.png)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 7 "前馈神经网络" 示例<br>
&emsp;&emsp;在前馈网络中，信息只以一个方向向前移动，从输入节点、通过隐藏节点(如果有的话)和输出节点。网络中没有循环 (前馈网络的这种特性不同于循环神经网络，在循环神经网络中节点之间的连接形成一个循环)。<br>
## 多层网络的需求
&emsp;&emsp;**多层感知器(MLP)** 包含一个或多个隐藏层(除了一个输入层和一个输出层)。单层感知器只能学习线性函数，而MLP也可以学习非线性函数。<br>
&emsp;&emsp;图7显示了一个隐藏层的 MLP。请注意, 所有连接都具有与之关联的权重, 但仅在图中显示三个权重 (![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(7).gif) 和![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(8).gif))。<br>
&emsp;&emsp;**输入层:** 输入层有三个节点。偏置节点的值为1。其他两个节点以X1和X2作为外部输入。如前所述，输入层不执行任何计算，因此输入层节点的输出分别为1、X1和X2，并被输入到**隐藏层**中。<br>
&emsp;&emsp;**隐藏层:隐藏层**也有三个节点，偏置节点的输出为1。**隐藏层**中其他两个节点的输出取决于**输入层**(1、X1和X2)的输出以及与连接(边)相关的权重。记住，![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(5).gif)指的是激活函数。然后将这些输出传送到输出层的节点。<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/9.png)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图"8"具有一个隐藏层的多层感知器<br>
&emsp;&emsp;**输出层:** 输出层有两个节点;它们从**隐藏层**获取输入，并对突出显示的隐藏节点执行类似的计算。这些计算结果的计算值(Y1和Y2)作为多层感知器的输出。<br>
&emsp;&emsp;给定一组特征![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(9).gif)和一个目标![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(10).gif)，多层感知器可以学习特征和目标之间的关系，进行分类或回归。<br>
&emsp;&emsp;让我们举一个例子来更好地理解多层感知器。假设我们有以下学生标记数据集：<br>
**表1-学生标记数据集示例：**<br>


学习时间|期中测试成绩|期末测试成绩
-|-|-
35|67|Pass
12|75|Fail
16|89|Pass
45|56|Pass
10|90|Fail
<br>

&emsp;&emsp;这两个输入列显示学生学习的小时数和学生获得的期中分数。最终结果列可以有两个值, 1 或 0, 表示学生是否在最后一学期通过。例如, 我们可以看到, 如果学生学习35小时, 并在期中获得了67分，他/她最终通过了最后一学期。<br>
&emsp;&emsp;现在, 假设我们想预测一个学生学习25小时, 在期中考试中得到70分是否会通过最后一学期:<br>
**表2-最后学期结果未知的样本学生:


学习时间|期中测试成绩|期末测试成绩
-|-|-
36|70|?
<br>

&emsp;&emsp;这是一个二元分类问题，MLP可以从给定的例子(训练数据)中学习，并在给定一个新的数据点时做出有根据的预测。我们将很快看到MLP如何学习这种关系。<br>
## 培训我们的 MLP-反向算法
&emsp;&emsp多层感知器学习的过程称为反向算法。我建议阅读果壳网上Hemanth Kumar给出的答案和解释, (https://www.Quora.com/How–do–you–explain–back–propagation–algorithm–to–a–beginner–in–neural–network/answer/Hemanth–Kumar–Mantri) (稍后引述)。<br>
&emsp;&emsp;**误差的反向传播**，通常缩写为BackProp是人工神经网络(ANN)训练的几种方式之一。它是一种监督训练方案，也就是说，它从标记的训练数据中学习(有一个监督者，来指导它的学习)。<br>
&emsp;&emsp;简单来说，BackProp就像“**从错误中学习**”。每当人工神经系统出现错误时，监督者就会予以纠正。<br>
&emsp;&emsp;神经网络由不同层次的节点组成;输入层、中间隐藏层和输出层。相邻层节点之间的连接具有与其相关联的“权值”。学习的目标是为这些边分配正确的权重。给定一个输入向量，这些权重决定了输出向量是什么。<br>
&emsp;&emsp;在监督学习中, 训练集被标记。这意味着, 对于某些给定的输入, 我们知道预期输出 (标签)。<br>
&emsp;&emsp;反向算法：<br>
&emsp;&emsp;最初，所有的边缘权重都是随机分配的。对于训练数据集中的每一个输入，ANN都被激活，并且它的输出可以被观察到。将此输出与我们已经知道的期望输出进行比较，错误将“传播”回前一层。注意到这个错误，并相应地“调整”权重。重复此过程，直到输出错误低于预定的阈值。<br>
&emsp;&emsp;一旦上述算法终止，我们就有了一个“完成了学习的”ANN，我们认为它已经准备好处理“新的”输入。这个ANN据说从几个例子(标记数据)和它的错误(错误传播)中学到了东西。<br>
&emsp;&emsp;hHemanth Kumar.<br> 
&emsp;&emsp;现在我们已经了解了反向传播的工作原理，让我们回到我们的学生标记数据集。<br>
&emsp;&emsp;图8中所示的MLP在输入层中有两个节点，它们占用所研究的输入小时和期中标记。它还有一个带有两个节点的隐藏层。输出层也有两个节点;上节点输出通过的概率，下节点输出失败的概率。<br>
&emsp;&emsp;在分类应用中, 我们广泛使用softmax函数(http://cs23ln.github/linear–classify/#softmax )<br>作为MLP输出层中的激活函数,以确保输出为概率,并且他们加起来等于1。softmax函数接受一个任意实值的向量，并将其压缩为一个值介于0和1之间的向量，其总和为1因此, 在这个例子中:Probability(Pass)+Probablility(Fail)=1。<br>
### 步骤1-向前传播
&emsp;&emsp;网络中的所有权值都是随机初始化的。让我们考虑一个特定的隐藏层节点，并将其称为V。假设从输入到该节点的连接的权重是![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(12).gif)和![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(13).gif)(如图所示)。<br>
&emsp;&emsp;然后网络将第一个训练样本作为输入(我们知道，对于输入35和67，通过的概率是1) <br>
&emsp;&emsp;&emsp;&emsp;网络输入= [35, 67]<br>
&emsp;&emsp;&emsp;&emsp;期望的网络输出(目标) = [1, 0]<br>
&emsp;&emsp;然后考虑节点输出V，可计算如下(![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(5).gif)为sigmoid激活函数):<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(14).gif)<br>
&emsp;&emsp;同样计算隐藏层中其他节点的输出。隐藏层中两个节点的输出作为输出层中两个节点的输入。这使我们能够计算输出层中的两个节点的输出概率。<br>
&emsp;&emsp;假设输出层中两个节点的输出概率分别为0.4和0.6(由于权值是随机分配的，输出也是随机的)。我们可以看到，计算出的概率(0.4和0.6)与期望概率(分别为1和0)相差甚远，因此，网络的输出是不正确的。<br>
## 步骤2-反向传播和更新权重
&emsp;&emsp;我们计算输出节点的总误差，通过网络传播这些误差并使用反向传播计算梯度。然后，采用梯度下降法等优化方法对网络中的权值进行调整，以减少输出层的误差。<br>
&emsp;&emsp;假设与所考虑的节点相关联的新权值是![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(16).gif)。)和![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(17).gif)(在反向传播和调整权值之后)。<br>
&emsp;&emsp;如果我们现在将相同的示例作为输入，并输入到网络中，那么网络的性能应该会比初始运行时更好，因为现在已经对权重进行了优化，以最小化预测中的错误。与前面的[0.6，-0.4]相比，输出节点的错误现在减少到[0.2，-0.2]。这意味着我们的网络已经学会正确地分类我们的第一个训练样本。<br>
&emsp;&emsp;我们对数据集中的所有其他训练样本重复这个过程。然后,我们的网络已经学会了这些例子。<br>
&emsp;&emsp;如果我们现在想要预测一个学习了25小时，在期中有70分的学生是否能通过期末考试，我们要通过正向传播步骤，找到通过和失败的输出概率。<br>
&emsp;&emsp;在这里，我避免使用数学方程和对梯度下降法等概念的解释，而是尝试为算法开发一种直觉。有关反向传播算法的更多数学讨论，请参阅以下链接: <br>
&emsp;&emsp;(http://home.agh.edu.pl/%7Evlsi/AI/backp_t_en/backprop.html) <br>
## TensorFlow术语—回顾
&emsp;&emsp;在本节中，我们将概述TensorFlow库以及基本的TensorFlow应用程序的结构。TensorFlow是一个用于创建大型机器学习应用程序的开源库;它可以在各种各样的硬件上建模计算，从android设备到异构的多gpu系统。<br>
&emsp;&emsp;TensorFlow使用一种特殊的结构，以便在不同的设备(如cpu和gpu)上执行代码。计算被定义为一个图，每个图都由operations组成，也被称为ops，所以每当我们使用TensorFlow时，我们都会在一个图中定义一系列的操作。<br>
&emsp;&emsp;要运行这些操作，我们需要将计算图启动到会话控制中。会话控制将操作转换并将其传递给执行设备。<br>
&emsp;&emsp;例如，下面的图像表示TensorFlow中的图。![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(2).gif)，![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(18).gif)和![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(19).gif)是这个图边缘上的张量。矩阵是对张量W和x的运算;在此之后，调用Add，我们将前一个操作符的结果与![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/CodeCogsEqn%20(19).gif)相加，每个操作的结果张量与下一个操作交叉，直到最后，在那里可以得到想要的结果。<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/10.png)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图"9"TensorFlow计算图<br>
&emsp;&emsp;为了使用TensorFlow，我们需要导入库;我们给它命名为UG这样我们就可以通过写入UG点来访问一个模块然后是模块的名称:<br>
```python
    import tensorflow as tf
```
&emsp;&emsp;要创建第一个图，我们将首先使用不需要任何输入的源操作。这些源操作或源操作将把它们的信息传递给其他操作，这些操作将实际运行计算。<br>
&emsp;&emsp;让我们创建两个将输出数字的源操作。我们将它们定义为 A 和 B, 您可以在下面的代码段中看到:<br>
```python
    A=tf.constant([2])
    B=tf.constant([3])
```
&emsp;&emsp;然后, 我们将定义一个简单的计算操作tf.add(),用于求和两个元素。您也可以使用C=A+B,如下面的代码所示:<br>
```python
    C = tf.add(A,B)
    #C = A + B is also a way to define the sum of the terms
```
&emsp;&emsp;由于需要在会话的上下文中执行图, 因此我们需要创建一个会话对象:<br>
```python
    session = tf.session()
```
&emsp;&emsp;为了查看图表， 让我们运行会话以从以前定义的 C 操作获取结果:<br>
```python
   result = session.run(C) print(result)
   Output:
   [5]
```
&emsp;&emsp;你可能认为仅仅把两个数字加在一起就需要做很多工作，但是理解TensorFlow的基本结构是非常重要的。一旦你这样做了，你可以定义任何你想要的计算;同样，TensorFlow的结构允许它在不同的设备(CPU或GPU)甚至集群中处理计算。如果您想了解更多这方面的信息，可以运行该方法 tf. device ().<br>
&emsp;&emsp;同样，你也可以自由地对TensorFlow的结构进行实验，以便更好地了解它是如何工作的。如果您想要得到TensorFlow支持的所有数学操作的列表，您可以查看文档。<br>
&emsp;&emsp;现在, 您应该了解 TensorFlow 的结构以及如何创建基本应用程序。<br>
## 使用 TensorFlow 定义多维数组
现在, 我们将尝试使用 TensorFlow 定义这样的一个数组:<br>
```python
    salar_var = tf.constant([4]) 
    vector_var = tf.constant([5,4,2])
    matrix_var = tf.constant([[1,2,3],[2,2,4],[3,5,5]])
    tensor = tf.constant( [ [[1,2,3],[2,3,4],[3,4,5]] , [[4,5,6],[5,6,7],[6,7,8]] , [[7,8,9],[8,9,10],[9,10,11]] ] )
    with tf.session() as session: 
        result = session.run(salar_var)
        print "Scalar (1 entry):\n %s \n" % result 
        result = session.run(vector_var)
        print "Vector (3 entries) :\n %s \n" % 
        result result = session.run(matrix_var)
        print "Matrix (3*3 entries):\n %s \n" % result
        result = session.run(tensor)
        print "Tensor (3*3*3 entries) :\n %s \n" % result
    Output:
    scalar (1 entry): 
    [2]

    Vector (3 entries) : 
    [5 6 2]

    Matrix (3*3 entries): 
    [[1 2 3]
    [2 3 4]
    [3 4 5]]

    Tensor (3*3*3 entries) : 
    [[[ 1	2	3]
    [	2	3	4]
    [	3	4	5]]
    [[	4	5	6]
    [	5	6	7]
    [	6	7	8]]
    [[	7	8	9]
    [	8	9	10]
    [	9	l0	11]]]
```
现在您已经了解了这些数据结构，我鼓励您使用以前的一些函数来了解它们的行为，根据它们的结构类型:
```python
    Matrix_one = tf.constant([[1,2,3],[2,3,4],[3,4,5]])
    Matrix_two = tf.constant([[2,2,2],[2,2,2],[2,2,2]
    first_operation = tf.add(Matrix_one, Matrix_two) 
    second_operation = Matrix_one + Matrix_two
        with tf.session() as session:
        result = session.run(first_operation)
        print "Defined using tensorflow function :" 
        print(result)
        result = session.run(second_operation) 
        print "Defined using normal expressions :" 
        print(result)

    Output:
    Defined using tensorflow function : 
    [[3 4 5]
    [4 5 6]
    [5 6 7]]
 

    Defined using normal expressions : 
    [[3 4 5]
    [4 5 6]
    [5 6 7]]

```
&emsp;&emsp;有了常规的符号定义和Tensorflow函数，我们可以得到一个元素的乘法，也称为**Hadamard乘积**。但如果我们想要正则矩阵乘积呢? 我们需要使用另一个名为tf.matmul()的TensorFlow函数<br>
```python
    Matrix_one = tf.constant([[2,3],[3,4]])
    Matrix_two = tf.constant([[2,3],[3,4]]) 
    first_operation = tf.matmul(Matrix_one, Matrix_two) with tf.3ession() as session:
    result = session.run(first_operation)
    print "Defined using tensorflow function :" 
    print(result)

    Output:
    Defined using tensorflow function : 
    [[13 18]
    [18 25]]
```
&emsp;&emsp;我们也可以自己定义这个乘法，但是有一个函数已经这样做了，所以没有必要重新定义这个过程!<br>
## 什么是张量?
&emsp;&emsp;张量结构帮助我们自由地按照我们想要的方式来塑造数据集。因为图像中的信息是编码的，所以这在处理图像时特别有用。<br>
&emsp;&emsp;考虑到图像，很容易理解它有高度和宽度，所以用二维结构(矩阵)来表示包含在其中的信息是有意义的……但是我们需要记得图像是有颜色。为了增加关于颜色的信息，我们需要另一个维度，这时张量就变得特别有用了。
图像被编码成彩色通道;图像数据以颜色通道中每个颜色在给定点上的强度表示，最常见的是RGB(表示红、蓝、绿)。图像中包含的信息是图像宽度和高度中各通道颜色的强度，如下图所示:<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/11.png)<br>
&emsp;&emsp;因此，红通道在每个点上的宽度和高度可以用矩阵表示;蓝色和绿色的通道也是如此。所以，我们最终得到了三个矩阵，当它们结合时，它们形成了一个张量。
## 变量
&emsp&emsp;现在我们对数据结构更熟悉了，接下来我们来看看TensorFlow如何处理变量。<br>
&emsp;&emsp;为了定义变量, 我们使用命令 tf.varibal()。若要能够在计算图中使用变量, 必须在会话中运行关系图之前对其进行初始化。这是通过运行tf.global_variables_initializer () 来完成的。<br>
&emsp;&emsp;要更新变量的值，我们只需运行赋值操作，该操作为变量赋值:state = tf.Variable(0)<br>
&emsp;&emsp;让我们首先创建一个简单的计数器，一个每次增加一个单位的变量:<br>
```python
    one = tf.constant(1)
    new_value = tf.add(state, one) 
    update = tf.assign(state, new_value)
```
&emsp;&emsp;在启动图之后，必须通过运行初始化操作来初始化变量。我们首先要向图中添加初始化操作:<br>
```python
    init_op = tf.global_variables_initializer()
```
&emsp;&emsp;然后, 我们启动一个会话来运行该关系图。<br>
&emsp；&emsp;我们首先初始化变量, 然后打印状态变量的初始值, 最后运行更新状态变量并在每次更新后打印结果的操作:<br>
```python
    with tf.session() as session: 
     session.run(init_op) 
     print(session.run(state)) 
     for _ in range(3):
         session.run(update) 
         print(session.run(state))

    Output:
    0
    1
    2
    3
```
## 占位符
&emsp;&emsp;现在, 我们知道如何在 TensorFlow 中操作变量, 但在 TensorFlow 模型外提供数据又如何呢？<br>
&emsp;&emsp;如果要将数据从模型外部传送到 TensorFlow 模型, 则需要使用占位符。<br>
&emsp;&emsp;那么, 这些所谓的占位符到底是什么？占位符可以被视为模型中的孔，您可以将数据传递到该孔。您可以使用tf.placeholder(datatype), 其中, 数据类型指定 (整数、浮点、字符串和布尔值) 以及其精度 (8、16、32和 64) 位。每个具有各自 Python 语法的数据类型的定义定义为:<br>
&emsp;&emsp;表 3 TensorFlow 数据类型的定义<br>
 
 
**Data type**|**Python type**|**Description**
-|-|-
DT_FLOAT|tf.float32|32-bits floating point.
DT_DOUBLE|tf.float64|64-bits floating point
DT_INT8|tf.int8|8-bits signed integer.
DT_INTl6|tf.intl6|16-bits signed integer.
DT_INT32|tf.int32|32-bits signed integer.
DT_INT64|tf.int64|64-bits signed integer.
DT_UINT8|tf.uint8|8-bits unsigned integer.
DT_3TRING|tf.string|Variable length byte arrays. Each element of a Tensor is a byte array.
DT_BOOL|tf.bool|Boolean.
DT_COMPLEX64|tf.complex64|Complex number made of two 32-bits floating points: real and imaginary parts.
DT_COMPLEXl28|tf.complexl28|Complex number made of two 64-bits floating points: real and imaginary parts.
DT_QINT8|tf.qint8|8-bits signed integer used in quantized ops.
DT_QINT32|tf.qint32|32-bits signed integer used in quantized ops.
DT_QUINT8|tf.quint8|8-bits unsigned integer used in quantized ops.
<br>


&emsp;&emsp;因此, 让我们创建一个占位符:<br>
```python
    a=tf.placeholder(tf.float32)
```
&emsp;&emsp;并定义简单乘法运算:b=a*2<br>
&emsp;&emsp;现在，我们需要定义和运行该会话，但由于我们在初始化会话时在模型中创建了一个用于传递数据的孔。我们必须将数据进行传递;否则会出现错误。<br>
&emsp;&emsp;要将数据传递给模型, 我们使用额外的参数feed_dict调用会话, 其中，我们应该传递的是一个字典，每个占位符的名称后面跟着它各自的数据，就像这样:<br>
```python
    with tf.session() as sess:
    result = sess.run(b,feed_dict=(a:3.5}) 
    print result

    Output:
    7.0
```
&emsp;&emsp;由于 TensorFlow 中的数据以多维数组的形式传递, 因此我们可以通过占位符传递任何类型的张量来获得简单乘法运算的答案: <br>
```python
    dictionary=(a: [ [ [1,2,3],[4,5,6],[7,8,9],[10,11,12] ] , [
    [13,14,15],[16,17,18],[19,20,21],[22,23,24] ] ] }
    with tf.session() as sess:
    result = sess.run(b,feed_dict=dictionary) 
    print result

    Output:
    [[[2. 4. 6.]
    [8. 10. 12.]
    [14. 16. 18.]
    [20. 22. 24.]]
    [[26. 28. 30.] 
    [32. .4. 36.]
    [38. 40. 42.]
    [44. 46. 48.]]]
```
## 运行
&emsp;&emsp;运行表示的是在图上对张量进行数学运算的节点。这些操作可以是任意类型的函数，比如加和减张量，也可以是激活函数。<br>
&emsp;&emsp;matmul, tf. add ，and tf.nn.sigmoid 是 TensorFlow 中的一些操作。这些类似于 Python 中的函数, 但可以直接在张量上运行, 每个操作都有特定的功能。<br>
&emsp;&emsp;其他操作可在以下方面轻松找到:(https://www.tensorflow.org/api_guides/python/math_ops.) <br>
&emsp;&emsp;让我们来运行一下这些操作:<br>
```python
    a = tf.constant([5]) 
    b = tf.constant([2]) 
    c = tf.add(a,b)
    d = tf.subtract(a,b)
    with tf.session() as session: 
        result = session.run(c) 
        print 'c =: %s' % result 
        result = session.run(d) 
        print 'd =: %s' % result

    Output: 
    c =: [7]
    d =: [3]
```
&emsp;&emsp;tf.nn.sigmoid 是一个激活函数: 它有点复杂, 但是这个函数可以帮助学习模型评估什么样的信息是好的，什么样的信息是不好的。<br>
## 线性回归模型–构建和训练
&emsp;&emsp;根据我们在泰坦尼克号中对线性回归的解释，在数据建模的实际操作中我们将依靠这个定义来构建一个简单的线性回归模型。<br>
&emsp;&emsp;让我们首先导入必要的包来实现此实施:<br>
```python
    import numpy as np 
    import tensorflow as tf
    import matplotlib.patches as mpatches 
    import matplotlib.pyplot as plt plt.rcParams['figure.figsize'] = (10, 6)
```
&emsp;&emsp;让我们定义一个独立的变量: <br>
```python
     input_values = np.arange(0.0, 5.0, 0.1) 
     input_values

    Output:
    array([ 0. ,	0.1,	0.2,	0.3,	0.4,	0.5,	0.6,	0.7,	0.8,	0.9,	1. ,
    1.1,	1.2,	1.3,	1.4,	1.5,	1.6,	1.7,	1.8,	1.9,	2. ,	2.1,
    2.2,	2.3,	2.4,	2.5,	2.6,	2.7,	2.8,	2.9,	3. ,	3.1,	3.2,
    3.3,	3.4,	3.5,	3.6,	3.7,	3.8,	3.9,	4. ,	4.1,	4.2,	4.3,
    4.4,	4.5,	4.6,	4.7,	4.8,	4.9])					
```
&emsp;&emsp;##您可以调整斜率和截距来验证图权值=l的变化<br>
```python
    bias=0
    output = weight*input_values + bias 
    plt.plot(input_values,output) 
    plt.ylabel('Dependent Variable')          
    plt.xlabel('Indepdendent Variable') 
    plt.show()
    Output:
```
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/12.png)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 11 "依赖变量与独立对象的可视化<br>
&emsp;&emsp;现在, 让我们看看如何将其转化为 TensorFlow 代码。<br>
## TensorFlow中的线性回归
&emsp;&emsp;对于第一部分，我们将生成随机数据点并定义一个线性关系;我们将使用TensorFlow来调整并获得正确的参数:<br>
```python
    input_values = np.random.rand(100).astype(np.float32)
```
&emsp;&emsp;本示例中使用的模型方程式为: <br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/13.png)<br>
&emsp;&emsp;这个方程没什么特别的，它只是我们用来生成数据点的模型。实际上，您可以将参数更改为任何您想要的参数，稍后您将这样做。我们在这些点上加一些高斯噪声使它更有趣：<br>
```python
    output_values = input_values * 2 + 3
    output_values = np.vectorize(lambda y: y + np.random.normal(loc=0.0, scale=0.1))(output_values)
```
&emsp;&emsp;以下是数据示例:<br>
```python
    list(zip(input_values,output_values))[5:10] 
    Output: 
    [(0.25240293, 3.474361759429548),
    (0.946697, 4.980617375175061), 
    (0.37582186, 3.650345806087635), 
    (0.64025956, 4.271037640404975), 
    (0.62555283, 4.37001850440196)]
```
&emsp;&emsp;首先，我们用任意随机猜测初始化变量Weight和Bias，然后定义线性函数: <br>
```python
    weight = tf.Variable(l.0) 
    bias = tf.Variable(0.2)
    predicted_vals = weight * input_values + bias
```
&emsp;&emsp;在一个典型的线性回归模型中，我们将我们想要调整的等式的平方差减到目标值(我们拥有的数据)，因此我们将这个等式定义为损失最小化。为了找到损失的价值, 我们使用 tf. reduce_mean ()。此函数查找多维张量的平均值, 结果可以具有不同的维度:<br>
```python
    model_loss = tf.reduce_mean(tf.square(predicted_vals – output_values))
```
&emsp;&emsp;然后，我们定义优化方法。在这里，我们将使用一个学习率为0.5的简单梯度下降法。<br>
&emsp;&emsp;现在，我们将定义我们的图的训练方法，但是我们将使用什么方法来最小化损失？我们用tf.train.GradientDescentOptimizer来实现。 minimize() 函数将最小化优化的错误函数, 从而生成更好的模型: <br>
```python
    model_optimizer = tf.train.GradientDescentOptimizer(0.5) 
    train = model_optimizer.minimize(model_loss)
```
&emsp;&emsp;在执行图之前, 不要忘记初始化变量:<br>
```python
    init = tf.global_variables_initializer() 
    sess = tf.session()
    sess.run(init)
```
&emsp;&emsp;现在, 我们准备开始优化并运行图:<br>
```python
    train_data = []
    for step in range(l00):
        evals = sess.run([train,weight,bias])[1:] 
        if step % 5 == 0:
            print(step, evals) 
            train_data.append(evals)

    Output:
    (0, [2.5176678, 2.9857566])
    (5, [2.4192538, 2.3015416])
    (10, [2.5731843, 2.221911])
    (15, [2.6890132, 2.1613526])
    (20, [2.7763696, 2.1156814])
    (25, [2.8422525, 2.0812368])
    (30, [2.8919399, 2.0552595])
    (35, [2.9294133, 2.0356679])
    (40, [2.957675, 2.0208921])
    (45, [2.9789894, 2.0097487])
    (50, [2.9950645, 2.0013444])
    (55, [3.0071881, 1.995006])
    (60, [3.0163314, 1.9902257])
    (65, [3.0232272, 1.9866205])
    (70, [3.0284278, 1.9839015])
    (75, [3.0323503, 1.9818509])
    (80, [3.0353084, 1.9803041])
    (85, [3.0375392, 1.9791379])
    (90, [3.039222, 1.9782581])
    (95, [3.0404909, 1.9775947])
```
&emsp&emsp;让我们将训练过程可视化，使其符合数据点:<br>
```python
    print('Plotting the data points with their corresponding fitted line...') 
    converter = plt.colors
    cr, cg, cb = (1.0, 1.0, 0.0)

    for f in train_data:
        cb += 1.0/ len(train_data) 
        cg –= 1.0 / len(train_data)
 

        if cb >1.0: cb = 1.0 
        if cg < 0.0: cg = 0.0
        [a, b] = f
        f_y = np.vectorize(lambda x: a*x + b)(input_values) 
        line = plt.plot(input_values, f_y)
        plt.setp(line, color=(cr,cg,cb))

    plt.plot(input_values, output_values, 'ro')
    green_line = mpatches.Patch(color='red', label='Data Points') 
    plt.legend(handles=[green_line]) 
    plt.show()
```
&emsp;&emsp;Output:<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/14.png)<br>
## 逻辑回归模型–构建和训练
&emsp;&emsp;基于我们在第2章中对逻辑回归的解释, 以及数据建模的实际应用--泰坦尼克号示例, 我们将在 TensorFlow 中实现逻辑回归算法。简单地说， 逻辑回归通过logistic或sigmoid传递输入, 然后将结果视为概率: <br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/15.png)<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;图 13 "区分0和1的两个线行可分离类<br>
## TensorFlow 中的逻辑回归
&emsp;&emsp;为了在TensorFlow中使用逻辑回归，我们首先需要导入将要使用的库。为此，您可以运行以下代码: <br>
```python
    import tensorflow as tf
    import pandas as pd
    import numpy as np 
    import time
    from sklearn.datasets import load_iris
    from sklearn.cross_validation import train_test_split 
    import matplotlib.pyplot as plt
```
&emsp;&emsp;接下来, 我们将加载要使用的数据集。在本例中, 我们使用的是内置的iris数据集。因此, 没有必要进行任何预处理, 我们可以直接跳转到操作它。我们将数据集分成 x 和 y, 然后将数据集分为训练x和y，并测试x和y，(伪)随机:<br>
```python
    iris_dataset = load_iris()
    iris_input_values, iris_output_values = iris_dataset.data[:–1,:], 
    iris_dataset.target[:–1]
    iris_output_values= pd.get_dummies(iris_output_values).values 
    train_input_values, test_input_values, train_target_values, 
    test_target_values = train_test_split(iris_input_values, 
    iris_output_values, test_size=0.33, random_state=42)
```
&emsp;&emsp;现在，我们定义x和y，这些占位符将保存我们的iris数据(包括特性和标签矩阵)，并帮助将它们传递到算法的不同部分。您可以将占位符看作是插入数据的空位。稍后，我们将通过feed_dict (提要字典)向占位符提供数据，从而将数据插入到这些占位符中: <br>
## 为什么用占位符?
&emsp;&emsp;TensorFlow的这个特性允许我们创建一个算法，它可以接受数据并知道数据的形态，而不需要知道输入的数据量。当我们在训练中插入一批数据时，我们可以很容易地调整在一个步骤中我们训练了多少个例子，而不改变整个算法:<br>
```python
    # numFeatures is the number of features in our input data.
    # In the iris dataset, this number is '4'. 
    num_explanatory_features = train_input_values.shape[1]

    # numLabels is the number of classes our data points can be in.
    # In the iris dataset, this number is '3'. num_target_values = train_target_values.shape[1]
    # Placeholders
    # 'None' means TensorFlow shouldn't expect a fixed number in that dimension 
    input_values = tf.placeholder(tf.float32, [None, num_explanatory_features])
    # Iris has 4 features, so X is a tensor to hold our data.
    output_values = tf.placeholder(tf.float32, [None, num_target_values]) 
    # This will be our correct answers matrix for 3     classes.
```
## 设置模型权重和偏差
&emsp;&emsp;就像线性回归一样，我们需要一个共享的变量权矩阵来进行逻辑回归。我们将W和b初始化为满是0的张量。因为我们要学习W和b，它们的初始值并不重要。这些变量是定义回归模型结构的对象，我们可以在它们经过训练之后保存它们，以便以后可以重用它们。<br>
&emsp;&emsp;我们将两个TensorFlow变量定义为参数。这些变量将会控制我们的逻辑回归的权重和偏差它们会在训练过程中不断更新。<br>
```python
#Randomly sample from a normal distribution with standard deviation .01
weights = tf.Variable(tf.random_normal([num_explanatory_features,num_target_values],mean=0, 
stddev=0.01, name="weights"))
biases = tf.Variable(tf.random_normal([1,num_target_values],mean=0, stddev=0.01, 
name="biases"))
```
## 逻辑回归模型
&emsp;&emsp;我们现在定义我们的operation, 以便正确运行逻辑回归。逻辑回归通常被认为是一个等式:<br>
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;![](https://github.com/computeryanjiusheng2018/infodlt/blob/master/content/chapter05/chapter05_image/2.jpg)<br>
&emsp;&emsp;然而，为了清晰起见，我们可以将其分为三个主要部分:<br>
&emsp;&emsp;&emsp;&emsp;权重乘以特征矩阵乘法运算<br>
&emsp;&emsp;&emsp;&emsp;加权特征和偏置项的总<br>
&emsp;&emsp;&emsp;&emsp;sigmoid函数的应用。<br>
&emsp;&emsp;因此，您将会发现这些组件被定义为三个单独的操作:<br>
```python
    # Three–component breakdown of the Logistic Regression equation.
    # Note that these feed into each other.
    apply_weights = tf.matmul(input_values, weights, name="apply_weights") 
    add_bias = tf.add(apply_weights, biases, name="add_bias") 
    activation_output = tf.nn.sigmoid(add_bias, name="activation")
```
&emsp;&emsp;正如我们之前看到的，我们将要使用的函数是logistic函数，它是在应用权值和偏差后输入的数据。在TensorFlow，该功能被实现为nn.sigmoid功能。实际上，它将带有偏差的加权输入放入0- 100%的曲线中，这就是我们想要的概率函数。<br>
## 训练
&emsp;&emsp;学习算法是我们如何搜索最优权向量(w)。这个搜索是一个优化问题，寻找优化误差/成本度量的假设。<br>
&emsp;&emsp;因此, 模型的成本或损失函数将告诉我们模型是坏的, 我们需要最小化这个函数。您可以遵循不同的损失或成本标准。在此实现中, 我们将使用均方误差 (MSE) 作为损耗函数。<br>
&emsp;&emsp;为了完成最小化损失函数的任务, 我们将使用梯度下降算法。<br>
## 成本函数
&emsp;&emsp;在定义我们的成本函数之前，我们需要定义我们要训练多久以及我们应该如何定义学习率: <br>
```python
    #Number of training epochs 
    num_epochs = 700
    # Defining our learning rate iterations (decay)
    learning_rate = tf.train.exponential_decay(learning_rate=0.0008,global_step=1,
    decay_steps=train_input_values.shape[0],
    decay_rate=0.95,staircase=True)
    # Defining our cost function – 3quared Mean Error
    model_cost = tf.nn.12_loss(activation_output – output_values, name="squared_error_cost")
    # Defining our Gradient Descent model_train =
    tf.train.GradientDescentOptimizer(learning_rate).minimize(model_cost)
```
&emsp;&emsp;现在, 是时候通过会话变量执行我们的计算图了。首先, 我们需要使用零或随机值初始化我们的权重和偏差, tf.initialize_all_variables ()这个初始化步骤将成为我们计算图中的一个节点, 当我们将图中放入一个会话中时, 该操作将运行并创建变量:  <br>
```python
# tensorflow session sess = tf.session()

# Initialize our variables.
init = tf.global_variables_initializer() sess.run(init)

#We also want some additional operations to keep track of our model's efficiency over time. 
We can do this like so:
# argmax(activation_output, 1) returns the label with the most probability
# argmax(output_values, 1) is the correct label correct_predictions =
tf.equal(tf.argmax(activation_output,1),tf.argmax(output_values,1))

# If every false prediction is 0 and every true prediction is 1, the average returns 
us the accuracy
model_accuracy = tf.reduce_mean(tf.cast(correct_predictions, "float"))

# summary op for regression output
activation_summary = tf.summary.histogram("output", activation_output)

# summary op for accuracy
accuracy_summary = tf.summary.scalar("accuracy", model_accuracy)

# summary op for cost
cost_summary = tf.summary.scalar("cost", model_cost)

# summary ops to check how variables weights and biases are updating after each 
iteration to be visualized in TensorBoard
weight_summary = tf.summary.histogram("weights", weights.eval(session=sess))
 

bias_summary = tf.summary.histogram("biases", biases.eval(session=sess))

merged = tf.summary.merge([activation_summary, accuracy_summary, cost_summary, 
weight_summary, bias_summary])
writer = tf.summary.FileWriter("summary_logs", sess.graph)

#Now we can define and run the actual training loop, like this:
# Initialize reporting variables

inital_cost = 0 diff = 1 epoch_vals = [] accuracy_vals = [] costs = []

# Training epochs
for i in range(num_epochs):
if i > 1 and diff < .0001:
print("change in cost %g; convergence."%diff) break

else:
# Run training step
step = sess.run(model_train, feed_dict=(input_values: train_input_values, 
output_values: train_target_values})

# Report some stats evert 10 epochs if i % 10 == 0:

# Add epoch to epoch_values epoch_vals.append(i)

# Generate the accuracy stats of the model train_accuracy, new_cost = 
sess.run([model_accuracy,
model_cost], feed_dict=(input_values: train_input_values, output_values: 
train_target_values})
# Add accuracy to live graphing variable accuracy_vals.append(train_accuracy)

# Add cost to live graphing variable costs.append(new_cost)
>
# Re–assign values for variables diff = abs(new_cost – inital_cost) cost = new_cost
print("Training step %d, accuracy %g, cost %g, cost change %g"%(i, train_accuracy, 
new_cost, diff))

```
```python
    Output:
Training step 0,   accuracy 0.343434, cost 34.6022, cost change 34.6022 
Training step 10,  accuracy 0.434343, cost 30.3272, cost change 30.3272 
Training step 20,  accuracy 0.646465, cost 28.3478, cost change 28.3478 
Training step 30,  accuracy 0.646465, cost 26.6752, cost change 26.6752 
Training step 40,  accuracy 0.646465, cost 25.2844, cost change 25.2844 
Training step 50,  accuracy 0.646465, cost 24.1349, cost change 24.1349 
Training step 60,  accuracy 0.646465, cost 23.1835, cost change 23.1835 
Training step 70,  accuracy 0.646465, cost 22.3911, cost change 22.3911 
Training step 80,  accuracy 0.646465, cost 21.7254, cost change 21.7254 
Training step 90,  accuracy 0.646465, cost 21.1607, cost change 21.1607 
Training step 100, accuracy 0.666667, cost 20.677,  cost change 20.677 
Training step 110, accuracy 0.666667, cost 20.2583, cost change 20.2583 
Training step 120, accuracy 0.666667, cost 19.8927, cost change 19.8927 
Training step 130, accuracy 0.666667, cost 19.5705, cost change 19.5705 
Training step 140, accuracy 0.666667, cost 19.2842, cost change 19.2842 
Training step 150, accuracy 0.666667, cost 19.0278, cost change 19.0278 
Training step 160, accuracy 0.676768, cost 18.7966, cost change 18.7966 
Training step 170, accuracy 0.69697,  cost 18.5867, cost change 18.5867 
Training step 180, accuracy 0.69697,  cost 18.3951, cost change 18.3951 
Training step 190, accuracy 0.717172, cost 18.2191, cost change 18.2191 
Training step 200, accuracy 0.717172, cost 18.0567, cost change 18.0567 
Training step 210, accuracy 0.737374, cost 17.906,  cost change 17.906 
Training step 220, accuracy 0.747475, cost 17.7657, cost change 17.7657 
Training step 230, accuracy 0.747475, cost 17.6345, cost change 17.6345 
Training step 240, accuracy 0.757576, cost 17.5113, cost change 17.5113 
Training step 250, accuracy 0.787879, cost 17.3954, cost change 17.3954 
Training step 260, accuracy 0.787879, cost 17.2858, cost change 17.2858 
Training step 270, accuracy 0.787879, cost 17.182,  cost change 17.182 
Training step 280, accuracy 0.787879, cost 17.0834, cost change 17.0834 
Training step 290, accuracy 0.787879, cost 16.9895, cost change 16.9895 
Training step 300, accuracy 0.79798,  cost 16.8999, cost change 16.8999 
Training step 310, accuracy 0.79798,  cost 16.8141, cost change 16.8141 
Training step 320, accuracy 0.79798,  cost 16.732,  cost change 16.732 
Training step 330, accuracy 0.79798,  cost 16.6531, cost change 16.6531 
Training step 340, accuracy 0.808081, cost 16.5772, cost change 16.5772 
Training step 350, accuracy 0.818182, cost 16.5041, cost change 16.5041 
Training step 360, accuracy 0.838384, cost 16.4336, cost change 16.4336 
Training step 370, accuracy 0.838384, cost 16.3655, cost change 16.3655 
Training step 380, accuracy 0.838384, cost 16.2997, cost change 16.2997 
Training step 390, accuracy 0.838384, cost 16.2359, cost change 16.2359 
Training step 400, accuracy 0.848485, cost 16.1741, cost change 16.1741 
Training step 410, accuracy 0.848485, cost 16.1141, cost change 16.1141 
Training step 420, accuracy 0.848485, cost 16.0558, cost change 16.0558 
Training step 430, accuracy 0.858586, cost 15.9991, cost change 15.9991 
Training step 440, accuracy 0.858586, cost 15.944,  cost change 15.944
 

Training step 450, accuracy 0.858586, cost 15.8903, cost change 15.8903 
Training step 460, accuracy 0.868687, cost 15.8379, cost change 15.8379 
Training step 470, accuracy 0.878788, cost 15.7869, cost change 15.7869 
Training step 480, accuracy 0.878788, cost 15.7371, cost change 15.7371 
Training step 490, accuracy 0.878788, cost 15.6884, cost change 15.6884 
Training step 500, accuracy 0.878788, cost 15.6409, cost change 15.6409 
Training step 510, accuracy 0.878788, cost 15.5944, cost change 15.5944 
Training step 520, accuracy 0.878788, cost 15.549,  cost change 15.549 
Training step 530, accuracy 0.888889, cost 15.5045, cost change 15.5045 
Training step 540, accuracy 0.888889, cost 15.4609, cost change 15.4609 
Training step 550, accuracy 0.89899,  cost 15.4182, cost change 15.4182 
Training step 560, accuracy 0.89899,  cost 15.3764, cost change 15.3764 
Training step 570, accuracy 0.89899,  cost 15.3354, cost change 15.3354 
Training step 580, accuracy 0.89899,  cost 15.2952, cost change 15.2952 
Training step 590, accuracy 0.909091, cost 15.2558, cost change 15.2558 
Training step 600, accuracy 0.909091, cost 15.217,  cost change 15.217 
Training step 610, accuracy 0.909091, cost 15.179,  cost change 15.179 
Training step 620, accuracy 0.909091, cost 15.1417, cost change 15.1417 
Training step 630, accuracy 0.909091, cost 15.105,  cost change 15.105 
Training step 640, accuracy 0.909091, cost 15.0689, cost change 15.0689 
Training step 650, accuracy 0.909091, cost 15.0335, cost change 15.0335 
Training step 660, accuracy 0.909091, cost 14.9987, cost change 14.9987 
Training step 670, accuracy 0.909091, cost 14.9644, cost change 14.9644 
Training step 680, accuracy 0.909091, cost 14.9307, cost change 14.9307 
Training step 690, accuracy 0.909091, cost 14.8975, cost change 14.8975
```
&emsp;&emsp;现在，我们来看看经过训练的模型在JSJT数据集上的表现如何，因此让我们根据测试集测试经过训练的模型:<br>
```python
# test the model against the test set
print("final accuracy on test set: %s" %str(sess.run(model_accuracy, feed_dict=(input_values:
test_input_values,output_values: test_target_values}))

Output:
final accuracy on test set: 0.9
```
&emsp;&emsp;在测试集上获得0.9的准确度非常好，您可以通过更改epoch的数量来尝试获得更好的结果。<br>
## 总结
&emsp;&emsp;在这一章中，我们对神经网络和多层神经网络的需求进行了基本的解释。我们还介绍了一些基本的例子，如线性回归和逻辑回归。<br>
&emsp;&emsp;接下来，我们将介绍更高级的示例，并演示如何使用TensorFlow构建类似于手写字符识别的东西。我们还将处理在传统机器学习中取代特征工程的架构工程的核心思想。  <br>


学号|姓名|专业
-|-|-
201802110481|辛杨|计算机软件与理论

