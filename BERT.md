# 1  BERT 简介
- BERT是2018年10⽉由 Google AI 研究院提出的⼀种预训练模型.
	- BERT的全称是 Bidirectional Encoder Representation from Transformers.
	- BERT 在机器阅读理解顶级⽔平测试 SQuAD1.1 中表现出惊⼈的成绩: 全部两个衡量指标上全⾯超越⼈
	类, 并且在 11 种不同 NLP 测试中创出 SOTA 表现. 包括将 GLUE 基准推⾼⾄ 80.4% (绝对改进 7.6%),
	MultiNLI准确度达到 86.7% (绝对改进 5.6%). 成为 NLP 发展史上的⾥程碑式的模型成就.
# 2  BERT 的架构
- 总体架构: 如下图所示, 最左边的就是 BERT 的架构图, 可以很清楚的看到 BERT 采⽤了 Transformer Encoder block 进⾏连接, 因为是⼀个典型的双向编码模型.
![](image/Pasted%20image%2020261015103741.png)
- 从上⾯的架构图中可以看到, 宏观上BERT分三个主要模块.
	- 最底层⻩⾊标记的Embedding模块.
	- 中间层蓝⾊标记的Transformer模块.
	- 最上层绿⾊标记的预微调模块.
## 2.1  Embedding 模块
- BERT中的该模块是由三种Embedding共同组成⽽成, 如下图
![](image/Pasted%20image%2020261015103844.png)
- Token Embeddings 是词嵌⼊张量, 第⼀个单词是CLS标志, 可以⽤于之后的分类任务.
- Segment Embeddings 是句⼦分段嵌⼊张量, 是为了服务后续的两个句⼦为输⼊的预训练任务.
- Position Embeddings 是位置编码张量, 此处注意和传统的Transformer不同, 不是三⻆函数计算的固定位置编码, ⽽是通过学习得出来的.
- 整个Embedding模块的输出张量就是这3个张量的直接加和结果.
## 2.2  双向 Transformer 模块
BERT 中只使用了经典 Transformer 架构中的 encoder 部分，完全舍弃了 Decoder 部分，⽽两⼤预训练任务也集
中体现在训练Transformer模块中.

## 2.3  预微调模块
- 经过中间层Transformer的处理后, BERT的最后⼀层根据任务的不同需求⽽做不同的调整即可.
- ⽐如对于sequence-level的分类任务, BERT直接取第⼀个[CLS] token 的"nal hidden state, 再加⼀层全连接层后进⾏softmax来预测最终的标签.

- 对于不同的任务, 微调都集中在预微调模块, ⼏种重要的NLP微调任务架构图展示如下：
![](image/Pasted%20image%2020261015172030.png)
- 从上图中可以发现, 在⾯对特定任务时, 只需要对预微调层进⾏微调, 就可以利⽤Transformer强⼤的注意⼒机制来模拟很多下游任务, 并得到SOTA的结果. (句⼦对关系判断, 单⽂本主题分类, 问答任务(QA), 单句贴标签(NER))

- 若⼲可选的超参数建议如下:

```python
Batch size: 16, 32
Learning rate (Adam): 5e-5, 3e-5, 2e-5
Epochs: 3, 4
```
# 3  BERT的预训练任务
BERT包含两个预训练任务:
- 任务⼀: Masked LM (带mask的语⾔模型训练)
- 任务⼆: Next Sentence Prediction (下⼀句话预测任务)

## 3.1  任务一：Masked LM
带mask的语⾔模型训练
- 关于传统的语⾔模型训练, 都是采⽤left-to-right, 或者left-to-right + right-to-left结合的⽅式, 但这种单向⽅式或者拼接的⽅式提取特征的能⼒有限. 为此BERT提出⼀个深度双向表达模型(deep bidirectional representation). 即采⽤MASK任务来训练模型.
- 1: 在原始训练⽂本中, 随机的抽取15%的token作为参与MASK任务的对象.
- 2: 在这些被选中的token中, 数据⽣成器并不是把它们全部变成[MASK], ⽽是有下列3种情况.
	- 2.1: 在80%的概率下, ⽤[MASK]标记替换该token, ⽐如my dog is hairy -> my dog is [MASK]
	- 2.2: 在10%的概率下, ⽤⼀个随机的单词替换token, ⽐如my dog is hairy -> my dog is apple
	- 2.3: 在10%的概率下, 保持该token不变, ⽐如my dog is hairy -> my dog is hairy
- 3: 模型在训练的过程中, 并不知道它将要预测哪些单词? 哪些单词是原始的样⼦? 哪些单词被遮掩成了[MASK]? 哪些单词被替换成了其他单词? 正是在这样⼀种⾼度不确定的情况下, 反倒逼着模型快速学习该token的分布式上下⽂的语义, 尽最⼤努⼒学习原始语⾔说话的样⼦. 同时因为原始⽂本中只有15%的 token 参与了MASK操作, 并不会破坏原语⾔的表达能⼒和语⾔规则.

## 3.2  任务⼆: Next Sentence Prediction
下⼀句话预测任务
- 在NLP中有⼀类重要的问题⽐如QA(Quention-Answer), NLI(Natural Language Inference), 需要模型能够很好的理解两个句⼦之间的关系, 从⽽需要在模型的训练中引⼊对应的任务. 在BERT中引⼊的就是Next Sentence Prediction任务. 采⽤的⽅式是输⼊句⼦对(A, B), 模型来预测句⼦B是不是句⼦A的真实的下⼀句话.
- 1: 所有参与任务训练的语句都被选中作为句⼦A.
	- 1.1: 其中50%的B是原始⽂本中真实跟随A的下⼀句话. (标记为IsNext, 代表正样本)
	- 1.2: 其中50%的B是原始⽂本中随机抽取的⼀句话. (标记为NotNext, 代表负样本)
- 2: 在任务⼆中, BERT模型可以在测试集上取得97%-98%的准确率.
>[!Summary] 小结
>- 学习了什么是BERT.
>	- BERT是⼀个基于Transformer Encoder的预训练语⾔模型.
>	- BERT在11种NLP测试任务中创出SOAT表现.
>- 学习了BERT的结构.
>	- 最底层的Embedding模块, 包括Token Embeddings, Segment Embeddings, Position Embeddings.
>	- 中间层的Transformer模块, 只使⽤了经典Transformer架构中的Encoder部分.
>	- 最上层的预微调模块, 具体根据不同的任务类型来做相应的处理.
>- 学习了BERT的两⼤预训练任务.
>	- MLM任务(Masked Language Model), 在原始⽂本中随机抽取15%的token参与任务.
>		- 在80%概率下, ⽤[MASK]替换该token
>		- 在10%概率下, ⽤⼀个随机的单词替换该token.
>		- 在10%概率下, 保持该token不变.
>	- NSP任务(Next Sentence Prediction), 采⽤的⽅式是输⼊句⼦对(A, B), 模型预测句⼦B是不是句⼦ A 的真实的下⼀句话.
>		- 其中50%的B是原始⽂本中真实跟随A的下⼀句话.(标记为IsNext, 代表正样本)
>		- 其中50%的B是原始⽂本中随机抽取的⼀句话. (标记为NotNext, 代表负样本)

# 4 [[Bret 的 Encoder 模块]]