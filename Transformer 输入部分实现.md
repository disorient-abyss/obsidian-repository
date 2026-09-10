# 1  输入部分介绍
输入部分包含：
- 了解文本嵌入层和位置编码的作用；
- 掌握文本嵌入层和位置编码的实现过程。
![380](image/Pasted%20image%2020260801214635.png)

# 2  文本嵌入层的作用
- 无论是源文本嵌入还是目标文本嵌入，都是为了将文本中词汇的数字表示转变为向量表示，希望在这样的高维空间捕捉词汇间的关系.  
- 文本嵌入层的代码分析：
```python
# 数学计算工具包
import math
import torch
from torch import nn

# 定义Embeddings类来实现文本嵌入层，这里s说明代表两个一模一样的嵌入层，他们共享参数.
# 该类继承nn.Module，这样就有标准层的一些功能，这里我们也可以理解为一种模式，我们自己实现的所有层都会这样去写.
class Embeddings(nn.Module):
	def __init__(self, d_model: int, vocab_size: int, padding_idx: int | None = None):
        '''类的初始化函数，有两个参数，d_model: 指词嵌入的维度，vocab: 指词表的大小。'''
        # 接着就是使用super的方式指明继承nn.Module的初始化函数，我们自己实现的所有层都会这样去写.
		super().__init__()
        # 之后就是调用nn中的预定义层Embedding，获得一个词嵌入对象self.lut
		self.lut = nn.Embedding(vocab_size, d_model)
        # 最后就是将d_model传入类中
        self.d_model = d_model

    def forward(self, x):
        '''可以将其理解为该层的前向传播逻辑，所有层中都会有此函数
        当传给该类的实例化对象参数时，自动调用该类函数
        参数x: 因为Embedding层是首层，所以代表输入给模型的文本通过词汇映射后的张量'''
        # 将x传给self.lut并与根号下self.d_model相乘作为结果返回
        
        # 让 embeddings vector 在增加 之后的 postion encoing 之前相对大一些的操作，
        # 主要是为了让position encoding 相对的小，这样会让原来的 embedding vector 中的信息在和 position encoding 的信息
        # 相加时不至于丢失掉
        # 让 embeddings vector 相对大一些
        return self.lut(x) * math.sqrt(self.d_model)

if __name__ == "__main__":
# 1. 自动选择计算设备 (CUDA GPU > Apple Silicon MPS > CPU)
	if torch.cuda.is_available():
	device = torch.device("cuda")
	elif torch.backends.mps.is_available():
	device = torch.device("mps")
	else:
	device = torch.device("cpu")
	
	print(f"当前使用的设备: {device}")

# 2. 超参数设置
	d_model = 512
	vocab_size = 1000
	pad_idx = 0

# 3. 直接在目标设备上创建张量
	x = torch.tensor(
	[[100, 2, 421, pad_idx], [491, 999, 1, 221]],
	dtype=torch.long,
	device=device
	)

# 4. 实例化模型并推送到目标设备
	emb = Embeddings(d_model, vocab_size, padding_idx=pad_idx).to(device)

# 5. 前向传播
	embr = emb(x)

print("embr:", embr)
print("embr.shape:", embr.shape)
```
- nn.Embedding 演示：
```zsh
当前使用的设备: cuda
embr: tensor([[[ 13.2141,   5.5503,  -8.0091,  ...,  -8.6790, -32.5911, -19.5268],
         [  1.4544,  -4.7040, -25.2015,  ...,  17.5428, -15.5922,   7.0303],
         [ 16.1879,  11.8430,  27.2104,  ...,  32.0547,  47.5029, -25.8086],
         [-13.3101, -15.9082, -13.5364,  ...,  29.6969, -25.8742,   6.3532]],

        [[ 17.4122,  20.0218, -15.2240,  ..., -26.2812,  29.6172,  21.0942],
         [ 13.1839, -19.5190,  14.8971,  ...,   8.2525,  10.8524,  30.5449],
         [ 31.4655,   3.7070,   4.6281,  ...,   0.1783,   6.6280,   4.7119],
         [  5.8329,  21.4752, -13.3044,  ...,  -1.5392,  19.3935,  49.4214]]],
       device='cuda:0', grad_fn=<MulBackward0>)
embr.shape: torch.Size([2, 4, 512])
```

# 3  位置编码器的作用
- 因为在 Transformer 的编码器结构中，并没有针对词汇位置信息的处理，因此需要在 Embedding 层后加入位置编码器，将词汇位置不同可能会产生不同语义的信息加入到词嵌入张量中，以弥补位置信息的缺失.
- $PE_{(\text{pos}, 2i)} = \sin\left(\frac{\text{pos}}{10000^{\frac{2i}{d_{\text{model}}}}}\right)$
- $PE_{(\text{pos}, 2i+1)} = \cos\left(\frac{\text{pos}}{10000^{\frac{2i}{d_{\text{model}}}}}\right)$
	- $\text{pos}$ : 单词在序列中的位置 (从 $0$ 开始计数)，例如序列的第一个单词 $\text{pos} = 0$  
	- $d_\text{model}$ : 编码向量的维度，也是词嵌入向量的维度，在原始论文中，这个值通常为 $512$ 
	- $i$ : 表示向量中的维度索引，对于每个位置 $\text{pos}$，其位置编码是一个长度为 $d_{\text{model}}$ 的向量，$i$ 的取值范围是 $[0, d_{\text{model}} / 2)$ 
	
## 3.1  位置编码器的代码分析
  ```python
class PositionalEncoding(nn.Module):
	def __init__(self, d_model: int, dropout: float = 0.1, max_len: int = 5000):
	super().__init__()
	self.dropout = nn.Dropout(p=dropout)
	# 初始化一个位置编码矩阵，它是一个0阵，矩阵的大小是max_len x d_model。
	pe = torch.zeros(max_len, d_model)
	
	# 初始化一个绝对位置矩阵，在我们这里，词汇的绝对位置就是用它的索引去表示。
	# 所以我们首先使用arange方法获得一个连续自然数向量，然后再使用unsqueeze方法拓展向量维度使其成为矩阵，
	# 又因为参数传的是1，代表矩阵拓展的位置，会使向量变成一个max_len x 1 的矩阵。
	position = torch.arange(0, max_len, dtype=torch.float).unsqueeze(1)
	
	# 绝对位置矩阵初始化之后，接下来就是考虑如何将这些位置信息加入到位位置编码矩阵中，
	# 最简单思路就是先将max_len x 1的绝对位置矩阵，变换成max_len x d_model形状，然后覆盖原来的初始位置编码矩阵即可，
	# 要做这种矩阵变换，就需要一个1xd_model形状的变换矩阵div_term，我们对这个变换矩阵的要求除了形状外，
	# 还希望它能够将自然数的绝对位置编码缩放成足够小的数字，有助于在之后的梯度下降过程中更快的收敛，这样我们就可以开始初始化这个变换矩阵了。
	# 首先使用arange获得一个自然数矩阵，但是细心的同学们会发现，我们这里并没有按照预计的一样初始化一个1xd_model的矩阵，
	# 而是有了一个跳跃，只初始化了一半即1xd_model/2的矩阵，为什么这样做呢，其实这里并不是真正意义上的初始化了一半的矩阵，
	# 我们可以把它看作是初始化了两次，而每次初始化的变换矩阵会做不同的处理，第一次初始化的变换矩阵分布在正弦波上，第二次初始化的变换矩阵分布在余弦波上，
	# 并把这两个矩阵分别填充在位置编码矩阵的偶数和奇数位置上，组成最终的位置编码矩阵。
	div_term = torch.exp(
		torch.arange(0, d_model, 2, dtype=torch.float)
		* -(math.log(10000.0) / d_model)
	)
	pe[:, 0::2] = torch.sin(position * div_term)
	pe[:, 1::2] = torch.cos(position * div_term)
	
	# 这样我们就得到了位置编码矩阵pe，pe现在还只是一个二维矩阵，要想和embedding的输出（一个三维张量）相加，
	# 就必须拓展一个维度，所以这里使用unsqueeze拓展维度。
	pe = pe.unsqueeze(0)

	# 最后把pe位置编码矩阵注册成模型的buffer，什么是buffer呢，
	# 我们把它认为是对模型效果有帮助的，但是却不是模型结构中超参数或者参数，不需要随着优化步骤进行更新的增益对象。
	# 注册之后我们就可以在模型保存后重加载时和模型结构与参数一同被加载。
	self.register_buffer("pe", pe)
	
	def forward(self, x: torch.Tensor) -> torch.Tensor:
	"""forward函数的参数是x，表示文本序列的词嵌入表示，形状为 (batch_size, seq_len, d_model)"""
	# 在相加之前我们对pe做一些适配工作， 将这个三维张量的第二维也就是句子最大长度的那一维将切片到与输入的x的第二维相同即x.size(1)，
	# 因为我们默认max_len为5000一般来讲实在太大了，很难有一条句子包含5000个词汇，所以要进行与输入张量的适配。
	x = x + self.pe[:, : x.size(1)]
	# 最后使用self.dropout对象进行'丢弃'操作，并返回结果。
	return self.dropout(x)
  ```