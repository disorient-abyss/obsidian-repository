# 1  RNN 结构分析

- RNN 结构图：
	![](image/Pasted%20image%2020260731202532.png)

- 结构解释图：
	![](image/Pasted%20image%2020260731203035.png)

- 内部结构分析：
	- 我们把目光集中在中间的方块部分，它的输入有两部分，分别是 $h_{t-1}$ 以及 $x_t$​，代表上一时间步的隐层输出，以及此时间步的输入，它们进入 RNN 结构体后，会“融合”到一起，这种融合我们根据结构解释可知，是将二者进行拼接，形成新的张量 $[x_t, h_{t-1}]$，之后这个新的张量将穿过一个全连接层（线性层），该层使用 $\tanh$ 作为激活函数，最终得到该时间步的输出 $h_t$​，它将作为下一个时间步的输入和 $x_{t+1}$​ 一起进入结构体，以此类推。

- 内部结构过程演示：
	![](image/Pasted%20image%2020260731203905.png)

- 根据结构分析得出内部计算公式：

$$
\begin{aligned}
h_t = \tanh(W_t[X_t, h_{t-1} + b_t])
\end{aligned}
$$

- 激活函数 $tanh$ 的作用：
	- 用于帮助调节流经网络的值，$tanh$ 函数将值压缩在 $-1$ 和 $1$ 之间。
		![](image/Pasted%20image%2020260731211230.png)

# 2  使用 Pytorch 构建 RNN 模型

- 位置: 在torch.nn工具包之中, 通过torch.nn.RNN可调用
- nn.RNN类初始化主要参数解释:
    - input_size: 输入张量 x 中特征维度的大小
    - hidden_size: 隐层张量 h 中特征维度的大小
    - num_layers: 隐含层的数量
    - nonlinearity: 激活函数的选择，默认是 tanh
- nn.RNN类实例化对象主要参数解释:
    - input: 输入张量 x
    - h0: 初始化的隐层张量 h
- nn.RNN使用示例:
```python
# 导入工具包
import torch
from torch import nn

rnn = nn.RNN(5, 6, 1)
input = torch.randn(1, 3, 5)
h0 = torch.randn(1, 3, 6)
output, hn = rnn(input, h0)

print(output)
print(hn)
```
```bash
tensor([[[ 0.9358,  0.0077,  0.2761,  0.7255,  0.2333, -0.5212],
         [ 0.3758, -0.7311, -0.6580, -0.6845,  0.6737, -0.2484],
         [ 0.1934,  0.4168, -0.5102, -0.2299,  0.4012,  0.5873]]],
       grad_fn=<StackBackward0>)
tensor([[[ 0.9358,  0.0077,  0.2761,  0.7255,  0.2333, -0.5212],
         [ 0.3758, -0.7311, -0.6580, -0.6845,  0.6737, -0.2484],
         [ 0.1934,  0.4168, -0.5102, -0.2299,  0.4012,  0.5873]]],
       grad_fn=<StackBackward0>)
```

# 3  LSTM 介绍

- 了解 LSTM 内部结构及计算公式；
- 掌握 Pytorch 中 LSTM 工具的使用；
- 了解 LSTM 的优势与缺点。

## 3.1  LSTM 介绍
- LSTM (Long Short-Term Memory) 也称长短时记忆结构，它是传统 RNN 的变体，与经典 RNN 相比能够有效捕捉长序列之间的语义关联，缓解梯度消失或爆炸现象。同时 LSTM 的结构更复杂，它的核心结构可以分为四个部分去解析：
	- 遗忘门
	- 输入门
	- 细胞状态
	- 输出门

# 3.2 LSTM 的内部结构图
### 3.2.1 LSTM 结构分析
![](image/Pasted%20image%2020260801181658.png)
- 结构解释图：
![377](image/Pasted%20image%2020260801181841.png)


>[!Question]
>- 未来同学们要学会把公式翻译成代码，其实也就是所谓的论文复现？
>	- $[x_t, h_{t-1}]$ --- torch.cat(x_t, h_{t-1})
