- 了解编码器中各个组成部分的作用.
- 掌握编码器中各个组成部分的实现过程.

# 1  编码器介绍
- 编码器部分:
	- 由N个编码器层堆叠而成
	- 每个编码器层由两个子层连接结构组成
	- 第一个子层连接结构包括一个多头自注意力子层和规范化层以及一个残差连接
	- 第二个子层连接结构包括一个前馈全连接子层和规范化层以及一个残差连接
	![321](image/Pasted%20image%2020260801215256.png)

# 2  掩码张量

## 2.1  掩码张量介绍
- 掩代表遮掩，码就是我们张量中的数值，它的尺寸不定，里面一般只有1和0的元素，代表位置被遮掩或者不被遮掩，至于是0位置被遮掩还是1位置被遮掩可以自定义，因此它的作用就是让另外一个张量中的一些数值被遮掩，也可以说被替换，它的表现形式是一个张量。

## 2.2  掩码张量的作用
- 在 Transformer 中，掩码张量的主要作用在应用 Attention (将在下一小节讲解)时，有一些生成的 Attention 张量中的值计算有可能已知了未来信息而得到的，未来信息被看到是因为训练时会把整个输出结果都一次性进行 Embedding，但是理论上解码器的的输出却不是一次就能产生最终结果的，而是一次次通过上一次结果综合得出的，因此，未来的信息可能被提前利用.所以，我们会进行遮掩.关于解码器的有关知识将在后面的章节中讲解。

## 2.3  生成掩码张量的代码分析
```python
def subsequent_mask(size):
    """生成向后遮掩的掩码张量，参数size是掩码张量最后两个维度的大小，它的最后两维形成一个方阵"""
    # 在函数中，首先定义掩码张量的形状
    attn_shape = (1, size, size)

    # 然后使用np.ones方法向这个形状中添加1元素,形成上三角阵，最后为了节约空间，
    # 再使其中的数据类型变为无符号8位整形uint8
    subsequent_mask = np.triu(np.ones(attn_shape), k=1).astype('uint8')

    # 最后将numpy类型转化为torch中的tensor，内部做一个1 - 的操作，
    # 在这个其实是做了一个三角阵的反转，subsequent_mask中的每个元素都会被1减，
    # 如果是0，subsequent_mask中的该位置由0变成1
    # 如果是1，subsequent_mask中的该位置由1变成0
    return torch.from_numpy(1 - subsequent_mask)
```

>[!Question]
>- 关于掩码张量到底有那几种？
>	1. padding mask（专门用来遮掩那些无效的Token）
>		- Transformer 架构每次输入模型的数据都是等长的!!!
>		- 另一种情况就是某一个汉字不在词表中，只能用 UNK 代替，也需要被 mask 掉
>	2. look-ahead mask（专门用来遮掩未来信息）
>		- 下三角矩阵的形式
>		- 解码器

>[!Summery]
>- **学习了什么是掩码张量：**
>	- 掩代表遮掩，码就是我们张量中的数值，它的尺寸不定，里面一般只有 1 和 0 的元素，代表位置被遮掩或者不被遮掩，至于是 0 位置被遮掩还是1位置被遮掩可以自定义，因此它的作用就是让另外一个张量中的一些数值被遮掩，也可以说被替换，它的表现形式是一个张量。   
>- **学习了掩码张量的作用：**
>	- 在 transformer 中，掩码张量的主要作用在应用 attention （将在下一小节讲解）时，有一些生成的 attention 张量中的值计算有可能已知量未来信息而得到的，未来信息被看到是因为训练时会把整个输出结果都一次性进行 Embedding，但是理论上解码器的输出却不是一次就能产生最终结果的，而是一次次通过上一次结果综合得出的，因此，未来的信息可能被提前利用。所以，我们会进行遮掩，关于解码器的有关知识将在后面的章节中讲解。
>- **学习并实现了生成向后遮掩的掩码张量函数：subsequent_mask**
>    - 它的输入是 size，代表掩码张量的大小；
>    - 它的输出是一个最后两维形成 1 方阵的下三角阵；
>    - 最后对生成的掩码张量进行了可视化分析，更深一步理解了它的用途。
# 3  注意力机制

- 我们这里使用的注意力的计算规则：
$$
\begin{aligned}
\text{Attention}(Q, K, V)  = \text{Softmax}(\frac{Q \cdot K^T}{\sqrt{d_k}}) \cdot V
\end{aligned}
$$

## 3.1  注意力计算规则的代码分析
```python
import math

import torch
import torch.nn.functional as F
from torch import nn

def attention_manual(
    query: torch.Tensor,
    key: torch.Tensor,
    value: torch.Tensor,
    mask: torch.Tensor | None = None,
    dropout: nn.Dropout | None = None,
) -> tuple[torch.Tensor, torch.Tensor]:
    """注意力机制的实现，输入分别是query，key，value，mask：掩码张量，
	dropout是nn.Dropout层的实例化对象，默认是None"""

    # 在函数中，首先取query的最后一维的大小，一般情况下就等同于我们的词嵌入维度，命名为d_k
    d_k = query.size(-1)

	# 按照注意力公式，将query与key的转置相乘，这里面key是将最后两个维度进行转置，再除以缩放系数根号下d_k，这种计算方法也称为缩放点积注意力计算。
	# 得到注意力得分张量scores
    scores = torch.matmul(query, key.transpose(-2, -1)) / math.sqrt(d_k)

	# 接着判断是否使用掩码张量
    if mask is not None:
        # 使用tensor的masked_fill方法，将掩码张量和scores张量每个位置一一比较，如果掩码张量处为0
        # 则对应的scores张量用-1e9这个值来替换，如下演示
        scores = scores.masked_fill(mask == 0, float("-inf"))

	# 对scores的最后一维进行softmax操作，使用F.softmax方法，第一个参数是softmax对象，第二个是目标维度。
	# 这样获得最终的注意力张量
    p_attn = F.softmax(scores, dim=-1)

	# 之后判断是否使用dropout进行随机置0
    if dropout is not None:
        p_attn = dropout(p_attn)

	# 最后，根据公式将p_attn与value张量相乘获得最终的query注意力表示，同时返回注意力张量
    return torch.matmul(p_attn, value), p_attn
```

>[!Summary]
>- **学习并实现了注意力计算规则的函数:  attention**
>    - 它的输入就是Q, K, V以及 mask 和 dropout,  mask 用于掩码,  dropout 用于随机置 0；  
 >   - 它的输出有两个, query 的注意力表示以及注意力张量。

>[!Question]
>- 关于掩码张量为什么不用 0 填充？
>	- $e^x$：如果 $x = 0$，结果是 $e^0 = 1$ 
>	- 只有 $x = -1e^9$，结果才是 $e ^ {-1e^9} \to 0$
>[!Question]
>- 关于张量的流转过程？(Tada)
>	- 1. input是一个数字化id的张量, x = [990, 4, 18, 225] ---> 通过 nn.Embedding(N, dim) 送入模型
>		- 需要AI工程师自主构建词表, 有一个手动过程: 自然语言 ---> 数字化 id 张量
>	- 2. 只要你用预训练模型 --- BERT + Qwen(DeepSeek)
>		- 直接引用的模型, 一次性的帮你完成了: 自然语言 ---> 数字化 id 张量 ---> nn.Embedding() 全过程！
# 4  多头注意力机制
## 4.1  多头注意力机制概念
- 从多头注意力的结构图中，貌似这个所谓的多个头就是指多组线性变换层，其实并不是，我只使用了一组线性变化层，即三个变换张量对 Q，K，V 分别进行线性变换，这些变换不会改变原有张量的尺寸，因此每个变换矩阵都是方阵，得到输出结果后，多头的作用才开始显现，每个头开始从词义层面分割输出的张量，也就是每个头都想获得一组 Q，K，V 进行注意力机制的计算，但是句子中的每个词的表示只获得一部分，也就是只分割了最后一维的词嵌入向量。这就是所谓的多头，将每个头的获得的输入送到注意力机制中，就形成多头注意力机制。

## 4.2  多头注意力机制结构图
- 多头注意力机制结构图：
![415](image/Pasted%20image%2020260804202201.png)

## 4.3  多头注意力机制的作用
- 这种结构设计能让每个注意力机制去优化每个词汇的不同特征部分，从而均衡同一种注意力机制可能产生的偏差，让词义拥有来自更多元的表达，实验表明可以从而提升模型效果。

## 4.4  多头注意力机制的代码实现

```python
import torch
from torch import nn
from utils import attention, clones


class MultiHeadedAttention(nn.Module):
    """多头注意力机制 (Multi-Head Attention)"""

    def __init__(self, head: int, d_model: int, dropout: float = 0.1):
        super().__init__()
        assert d_model % head == 0, "d_model 必须能被 head 整除"

        self.d_k = d_model // head
        self.head = head

        # 定义 4 个线性层：分别对应 Q、K、V 的投影，以及最后的 Output 拼接投影
        self.linears = clones(nn.Linear(d_model, d_model), 4)
        self.attn: torch.Tensor | None = None
        self.dropout = nn.Dropout(p=dropout)

    def forward(
        self,
        query: torch.Tensor,
        key: torch.Tensor,
        value: torch.Tensor,
        mask: torch.Tensor | None = None,
    ) -> torch.Tensor:
        
        if mask is not None:
            # 扩展为 (batch_size, 1, 1, seq_len)，以便与 (batch_size, head, seq_len, d_k) 正确广播
            mask = mask.unsqueeze(1)

        batch_size = query.size(0)

        # 1. 线性变换投影并分头 (batch_size, seq_len, d_model) -> (batch_size, head, seq_len, d_k)
        query, key, value = [
            linear(x).view(batch_size, -1, self.head, self.d_k).transpose(1, 2)
            for linear, x in zip(self.linears, (query, key, value))
        ]

        # 2. 计算点积注意力
        x, self.attn = attention(query, key, value, mask=mask, dropout=self.dropout)

        # 3. 多头拼接还原 (batch_size, head, seq_len, d_k) -> (batch_size, seq_len, d_model)
        x = x.transpose(1, 2).contiguous().view(batch_size, -1, self.head * self.d_k)

        # 4. 经过输出线性层融合信息
        return self.linears[-1](x)
```

>[!Question]
>- 关于多头注意力机制的细节？
>	- 一般来说参与神经网络计算的张量，都是 3 维度张量: (batch_size, seq_length, embedding_dim)
>	- 多头注意力中，要将 3 维张量 ---> 4 维张量

# 5  前馈全连接层的代码分析
## 5.1  前馈全连接层
- 在Transformer中前馈全连接层就是具有两层线性层的全连接网络。
- 前馈全连接层的作用：
    - 考虑注意力机制可能对复杂过程的拟合程度不够，通过增加两层网络来增强模型的能力。

## 5.2  前馈全连接层的代码分析

>[!Question]
>- 前馈全连接层到底又没有用？
>	- 一个升维矩阵 + 一个降维矩阵 --- Tensor 啥变化没有，FeedForward 有用吗？
>		- 所谓的没变化，只是 shape 没变!!
>		- 你的 tensor 经历了 2 次矩阵乘法，肯定学习到了特殊的特征和知识!!
>	- 2017年Transformer + 2018年 BERT, Google 的参数 d_model = 768, ff_model = 3072 (4倍关系)  
>	- 2018 ~ 2022年, 几乎全世界范围内的模型都默认谷歌的 4 倍规则。
>	- 2023 ~ 2026年, 没家模型的 FeedForward 的升维和降维的倍数关系, 就完全百花齐放了。
>		- 但是先升维，再降维的 FeedForward 的模式，始终没变!! 

>[!Summary]
>- 学习了什么是前馈全连接层：
>	- 在 Transformer 中前馈全连接层就是具有两层线性层的全连接网络。
>- 学习了前馈全连接层的作用：  
>    - 考虑注意力机制可能对复杂过程的拟合程度不够，通过增加两层网络来增强模型的能力。
>- 学习并实现了前馈全连接层的类：PositionwiseFeedForward
>	- 它的实例化参数为 d_model, d_ff, dropout, 分别代表词嵌入维度，线性变换维度，和置零比率.
>	- 它的输入参数 x, 表示上层的输出.
>	- 它的输出是经过 2 层线性网络变换的特征表示。
# 6  规范化层 Norm
## 6.1  Norm 规范化层的作用
- 它是所有深层网络模型都需要的标准网络层，因为随着网络层数的增加，通过多层的计算后参数可能开始出现过大或过小的情况，这样可能会导致学习过程出现异常，模型可能收敛非常的慢。因此都会在一定层数后接规范化层进行数值的规范化，使其特征数值在合理范围内。

## 6.2  规范化层的代码实现
```python
# 通过LayerNorm实现规范化层的类
class LayerNorm(nn.Module):
    def __init__(self, features, eps=1e-6):
        """初始化函数有两个参数，一个是features，表示词嵌入的维度，
        另一个是eps它是一个足够小的数，在规范化公式的分母中出现，
        防止分母为0.默认是1e-6."""
        super(LayerNorm, self).__init__()

        # 根据features的形状初始化两个参数张量a2，和b2，第一个初始化为1张量，
        # 也就是里面的元素都是1，第二个初始化为0张量，也就是里面的元素都是0，这两个张量就是规范化层的参数，
        # 因为直接对上一层得到的结果做规范化公式计算，将改变结果的正常表征，因此就需要有参数作为调节因子，
        # 使其既能满足规范化要求，又能不改变针对目标的表征.最后使用nn.parameter封装，代表他们是模型的参数.
        self.a2 = nn.Parameter(torch.ones(features))
        self.b2 = nn.Parameter(torch.zeros(features))

        # 把eps传到类中
        self.eps = eps

    def forward(self, x):
        """输入参数x代表来自上一层的输出"""
        # 在函数中，首先对输入变量x求其最后一个维度的均值，并保持输出维度与输入维度一致.
        # 接着再求最后一个维度的标准差，然后就是根据规范化公式，用x减去均值除以标准差获得规范化的结果,
        # 最后对结果乘以我们的缩放参数，即a2，*号代表同型点乘，即对应位置进行乘法操作，加上位移参数b2.返回即可.
        mean = x.mean(-1, keepdim=True)
        std = x.std(-1, keepdim=True)
        return self.a2 * (x - mean) / (std + self.eps) + self.b2
```

>[!Question]
>- Norm 层这些变幻有什么用？
>	- 减均值，除方差 --- 标准操作
>	- ✖️ 全 1 的矩阵 A，➕ 全 0 的矩阵 B，有什么用？--- 跟刚才的前馈全连接层一样???
>	- 矩阵 A， 矩阵 B，是用 nn.Parameters() 封装的模型参数，requires_grad = True，是参与反向传播的！！
>		- 随着模型的训练，矩阵 A 就不是全 1 了，矩阵 B 也不是全 0 了

>[!Summary]
> LayerNorm 层总结：
>- 学习了规范化层的作用：
>	- 它是所有深层网络模型都需要的标准网络层，因为随着网络层数的增加，通过多层的计算后参数可能开始出现过小或过大的情况，这样可能会导致学习过程出现异常，模型可能收敛非常的慢。因此都会在一定层数后接规范化层进行数值的规范化，使其特征数值在合理范围内。
>- 学习并实现了规范化层的类：LayerNorm
>	- 它的实例化参数有两个，features和eps，分别表示词嵌入特征大小，和一个足够小的数。
>	- 它的输入参数x代表来自上一层的输出。
>	- 它的输出就是经过规范化的特征表示。

# 7  子层连接结构
## 7.1  子层连接结构
- 如图所示，输入到每个子层以及规范化层的过程中，还使用了残差链接（跳跃连接），因此我们把这一部分结构整体叫做子层连接（代表子层及其链接结构），在每个编码器层中，都有两个子层，这两个子层加上周围的链接结构就形成了两个子层连接结构。
- 子层连接结构图：
![200](image/Pasted%20image%2020260808212309.png)

## 7.2  子层连接结构的代码分析
```python
# 使用SublayerConnection来实现子层连接结构的类
class SublayerConnection(nn.Module):
    def __init__(self, size, dropout=0.1):
        """它输入参数有两个，size以及dropout，  size一般都是都是词嵌入维度的大小，
        dropout本身是对模型结构中的节点数进行随机抑制的比率，
        又因为节点被抑制等效就是该节点的输出都是0，因此也可以把dropout看作是对输出矩阵的随机置0的比率。
        """
        super(SublayerConnection, self).__init__()
        # 实例化了规范化对象self.norm
        self.norm = LayerNorm(size)
        # 又使用nn中预定义的droupout实例化一个self.dropout对象.
        self.dropout = nn.Dropout(p=dropout)
		
    def forward(
        self, x: torch.Tensor,
        sublayer: Callable[[torch.Tensor], torch.Tensor]
    ) -> torch.Tensor:
        """前向逻辑函数中，接收上一个层或者子层的输入作为第一个参数，
        将该子层连接中的子层函数作为第二个参数"""
        
        # 我们首先对输出进行规范化，然后将结果传给子层处理，之后再对子层进行dropout操作，
        # 随机停止一些网络中神经元的作用，来防止过拟合。最后还有一个add操作，
        # 因为存在跳跃连接，所以是将输入x与dropout后的子层输出结果相加作为最终的子层连接输出。
        return x + self.dropout(sublayer(self.norm(x)))
```

>[!Question]
>- 关于 sublayerConnection 层的作用？
>	- 本质上就是所谓的“残差连接” --- 最大的作用是将输入端信号，直接跨多成传递到后面的神经网络层，避免原始信号过早衰减和过早的失真。
>	- 2015 年何凯明大神的大作，RestNet
>	- 2017 年 Transformer, Google 全盘继承了残差连接
>	- 2018 年 BERT, GPT3, 2022 年 ChatGPT, 2024, 全世界都沿用残差连接
>	- 中国 AI 的三板斧：
>		- 1: 2024年秋天，字节跳动Seed团队（不叫豆包，云雀） -- Hyper Connection (残差连接高速连接)
>		- 2: 2025.12.31，DeepSeek团队 mHC -- 针对于高速连接增加限制机制
>		- 3: 2026.3.16，月之暗面Kimi团队 Attention Residual （残差注意力）

# 8  编码器层
## 8.1  编码器层的作用
- 作为编码器的组成单元，每一个编码器层完成一次对输入的特征提取过程，即编码过程。

- 编码器层的构成图：
![210](image/Pasted%20image%2020260906211541.png)

## 8.2  编码器层的代码分析

- 代码实现如下：
```python
import torch
from layer_norm import LayerNorm
from sublayer_connection import SublayerConnection
from torch import nn
from utils import clones

# 使用EncoderLayer类实现编码器层
class EncoderLayer(nn.Module):
    def __init__(
        self, 
        size: int, 
        self_attn: nn.Module, 
        feed_forward: nn.Module, 
        dropout: float = 0.1
    ):
        
        super().__init__()
        self.self_attn = self_attn
        self.feed_forward = feed_forward
        # 复制两个残差连接层：一个用于自注意力，一个用于前馈网络
        self.sublayer = clones(SublayerConnection(size, dropout), 2)
        self.size = size

    def forward(
        self, 
        x: torch.Tensor, 
        mask: torch.Tensor | None = None
    ) -> torch.Tensor:
        
        """forward函数中有两个输入参数，x和mask，分别代表上一层的输出，和掩码张量mask。"""
        # 1. 经过自注意力子层 + 残差与归一化
        x = self.sublayer[0](x, lambda x: self.self_attn(x, x, x, mask))
        # 2. 经过前馈网络子层 + 残差与归一化
        return self.sublayer[1](x, self.feed_forward)


class Encoder(nn.Module):
    def __init__(self, layer: EncoderLayer, N: int):
        super().__init__()
        # 使用 clones 深拷贝 N 个独立的编码器层
        self.layers = clones(layer, N)
        # Pre-LN 结构在经过 N 层残差累加后，必须在最终输出前进行全局归一化
        self.norm = LayerNorm(layer.size)

    def forward(
        self,
        x: torch.Tensor,
        mask: torch.Tensor | None = None,
    ) -> torch.Tensor:

        # 数据顺次通过 N 个独立的编码器层
        for layer in self.layers:
            x = layer(x, mask=mask)
        # 最终归一化输出
        return self.norm(x)
```