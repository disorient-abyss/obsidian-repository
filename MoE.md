## 1  MoE架构是什么意思 ? 为什么是 MoE ?

经典MoE

- **MoE (Mixture of Expert)** 混合专家模型，是一种新型的大模型架构，兴起于2023年ChatGPT横空出世后不久. 出发点是为了缓解传统Dense模型，它主要是由8个专家模型和一个gate门组成，gate的作用的决定将不同的数据丢给不同的专家的进行处理 (具体是哪个专家处理，是模型学习出来的，并不是固定的), 在Mistral中是挑选的score的Top2的专家来处理。

**MoE: Top2Expert**

![](image/Pasted%20image%2020260825161702.png)

**DeepSeek MoE架构创新**

- DeepSeek MoE大模型主要有两大创新：
    - 🍊1: 共享专家 (Shared Expert) + 路由专家 (Routed Expert)
    - 🍊2: MLA (Multi-Head Latent Attention)

![](image/Pasted%20image%2020260825163804.png)

| **模型**              | **路由专家 (Routed Experts)** | **共享专家 (Shared Experts)** | **每Token激活专家数** |
| ------------------- | ------------------------- | ------------------------- | --------------- |
| **DeepSeek V3**     | 256 个                     | 1 个                       | 9个（8个路由+1个共享）   |
| **DeepSeek V4 Pro** | 384 个                     | 1 个                       | 7个（6个路由+1个共享）   |

- MoE 最大的痛点：专家不均衡

	- 方案一：增加一个 loss
		![](image/Pasted%20image%2020260825164505.png)

	