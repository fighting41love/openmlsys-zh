## 主流系统架构

正如上文提到的，嵌入表占据了推荐模型绝大部分存储而其更新具有显著的稀疏性，因此推荐系统通常采用上一章介绍的参数服务器架构来存储模型。具体来讲，所有参数被分布存储在一组参数服务器上，而训练服务器一方面从数据存储模块拉取训练数据，另一方面根据训练数据从参数服务器上拉取对应的嵌入项和所有稠密神经网络参数。训练服务器本地更新之后将本地梯度或新的参数发送回参数服务器以更新全局参数。全局参数更新可以选择全同步，半同步，或异步更新。类似的，推理服务器在接到一批用户的推荐请求后，从参数服务器拉去相应的嵌入项和稠密神经网络参数来响应用户的请求。为了提升训练（推理）的吞吐，可以在训练（推理）服务器上缓存一部分参数。

为了避免训练服务器和参数服务器之间的通信限制训练吞吐率，一些公司也在探索单机多GPU训练超大规模推荐系统。然而正如前文提到的，即使是单个推荐模型的参数量（1̃00GB）也超出了目前最新的GPU显存。有鉴于此，脸书公司的定制训练平台
-- ZionEX :cite:`zionex`利用计算设备之间的高速链接将多台设备的存储共享起来可以单机训练TB级推荐模型。然而对于更大规模的模型或中小型企业、实验室，参数服务器架构依然是性价比最高的解决方案。

为了提升在发生故障的情况下的可用性，在线服务中的深度学习推荐模型通常都采用多副本分布式部署。同一个模型的多个副本通常会被部署在至少两个不同的地理区域内的多个数据中心中，如图 :numref:`ch10-recommendation-systems`，以应对大面积停电或者网络中断而导致整个地区的所有副本都不可用。除了容错方面的考虑，部署多个副本还有其他几点优势。首先，将模型部署在靠近用户的云服务器上可以提升响应速度。其次，部署多份副本也可以拓展模型推理服务的吞吐率。

![推荐系统的分布式架构](../img/ch10/ch10-recommendation-systems.svg)
:width:`800px`
:label:`ch10-recommendation-systems`