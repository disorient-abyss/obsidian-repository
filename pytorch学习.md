[深度学习](深度学习.md)
# 1 张量

## 1.1 基本创建方式

1. torch.tensor根据指定数据创建张量

2. torch.Tensor根据形状创建张量，也可以用来创建指定数据的张量

3. torch.IntTensor、torch.FloatTensor、torch.DoubleTensor创建指定类型的张量

```python
import torch
import numpy as np
import random
# 1. 根据已有数据创建张量
def test01():
   # 1. 创建张量标量
   data = torch.tensor(10)
   print(data)
   
# 2. numpy数组，由于data为float64，下面代码也使用该类型
   data = np.random.randn(2, 3)
   data = torch.tensor(data)
   print(data)

   #3. 列表，下面代码使用默认元素类型float32
   data = [[10.,20.,30.],[40.,50.,60.]]
   data = torch.tensor(data)
   print(data)
# 2. 创建指定形状的张量
def test02():
   
   # 1. 创建2行3列的张量，默认dtype为float32
   data = torch.Tensor(2, 3)
   print(data)
   
   # 2. 注意：如果传递列表，则创建包含指定元素的张量
   data = torch.Tensor([10])
   print(data)

   data = torch.Tensor([10, 20])
   print(data)
   
# 3. 使用具体类型的张量
def test03():
   # 1. 创建2行3列，dtype为int32的张量
   data = torch.IntTensor(2, 3)	
   print(data)

   # 2. 注意：如果传递元素类型不正确，则会进行数据类型转换
   data = torch.IntTensor([2.9, 3.3])	
   print(data)

   # 3. 其他的类型
   data = torch.ShortTensor([2, 3])    # int16
   print(data)
   data = torch.LongTensor([2, 3])     #int 64
   print(data)
   data = torch.FloatTensor([2.5, 3.3])    #float 32
   print(data)
   data = torch.DoubleTensor([-2.5, -3.3])   #float 64
   print(data)
   
   # 4. 自动判断,默认为float 32
   data = torch.Tensor([2.5, 3.3])   #float 32
   print(data)
   
if __name__ == '__main__':
print("--------------")
print("test01:")
test01()
print("--------------")
print("test02:")
test02()
print("--------------")
print("test03:")
test03()
print("--------------")
```
   
```cmd
--------------
test01:
tensor(10)
tensor([[ 0.1611,  0.3484, -1.0276],
	   [ 1.1052,  0.1520,  0.0364]], dtype=torch.float64)
tensor([[10., 20., 30.],
	   [40., 50., 60.]])
--------------
test02:
tensor([[2.7712e-12, 2.0179e-42, 0.0000e+00],
	   [0.0000e+00, 0.0000e+00, 0.0000e+00]])
tensor([10.])
tensor([10., 20.])
--------------
test03:
tensor([[742589088,      1440,         0],
	   [        0,         0,         0]], dtype=torch.int32)
tensor([2, 3], dtype=torch.int32)
tensor([2, 3], dtype=torch.int16)
tensor([2, 3]),dtype=torch.int64
tensor([2.5000, 3.3000]),dtype=torch.float32
tensor([-2.5000, -3.3000], dtype=torch.float64)
tensor([2., 3.]),dtype=torch.float32
--------------
```

现在主流大模型基本以float32位为最高精度，多数以float16与float32并存精度，更有甚者为float16与float8混合精度，以节约算力。

nvidia的B200芯片能做到原生的FP4低精度的高速计算

V3.1. 下一代国产芯片准备适配FP8

---

## 1.2 创建线性和随机张量

   1. torch.arange 和 torch.linspace 创建线性张量
   2.  torch.manual_seed 随机种子设置，torch.init_seed 用来获取当前随机数生成器的初始种子值
   3. torch.randn 创建随机张量
   
```python
import torch
import numpy as np

# 1. 创建线性空间的张量
def test01():
   # 1. 在指定区间按照步长生成元素([start, end), step)
   data = torch.arange(0, 10, 2)
   print(data)
   
# 2. 在指定区间按照元素个数生成
   data = torch.linspace(0, 11, 6)
   print(data)
   
# 3. 创建随机张量
def test02():
   # 1. 创建随机张量
   data = torch.randn(2, 3) # 创建2行3列张量
   print(data)
  
   # 2. 随机数种子设置  
   print("随机数种子：", torch.initial_seed())
   torch.manual_seed(100)
   print("随机数种子：", torch.initial_seed())

# 4. 随机种子的复现作用测试
def test03():
   # 设置随机种子 备注：如设置100后
   torch.manual_seed(100)

   #创建一个随机初始化的张量  备注：这里是101-109的随机量
   tensor1 = torch.randn(3, 3)
   print("Tensor1:\n", tensor1)
  
   # 再次设置相同的随机种子
   # torch.manual_seed(100)
  
   #再次创建一个随机初始化的张量  备注如果不重置随机种子，那么就是第110-118的随机量
   tensor2 = torch.randn(3, 3)
   print("Tensor2:\n", tensor2)
  
   #检查两个张量是否相同
   print("Are tensors equal?\n", torch.equal(tensor1, tensor2))
   
# 4. 对比封装numpy和pytorch生成的张量
def test04():
   tensor1 = torch.tensor(np.random.randn(2, 3))
   print(tensor1)

   tensor2 = torch.randn(2, 3)
   print(tensor2)
   
#numpy默认引用float64类型的数据，pytorch默认是float32类型的数据。
if __name__ == "__main__":
   test01()
   # test02()
   # test03()
   # test04()
```
   
```cmd
   test01()-------------------------------------------------------------------
   tensor([0, 2, 4, 6, 8])
   tensor([ 0.0000,  1.2222,  2.4444,  
   		 3.6667,  4.8889,  6.1111,  
   		 7.3333,  8.5556, 9.7778, 
   		 11.0000])
   
   test02()-------------------------------------------------------------------
   tensor([[-0.9829, -1.2760, -0.0962],
           [ 1.0630, -1.3398, -0.2668]])
   随机数种子： 95470888884500
   随机数种子： 100
   
   test03()-------------------------------------------------------------------
   Tensor1:
    tensor([[ 0.3607, -0.2859, -0.3938],
           [ 0.2429, -1.3833, -2.3134],
           [-0.3172, -0.8660,  1.7482]])
   Tensor2:
    tensor([[-0.2759, -0.9755,  0.4790],
           [-2.3652, -0.8047,  0.6587],
           [-0.2586, -0.2510,  0.4770]])
   Are tensors equal?
    False
   
   test04()-------------------------------------------------------------------
   tensor([[-1.7716, -1.4415, -0.6924],
           [-0.2358, -0.9273,  0.2604]], dtype=torch.float64)
   tensor([[-0.5883, -0.6131,  0.4322],
           [ 0.4612, -0.9014, -0.2675]])
```

---
## 1.3 创建 01 张量
1. torch.ones和torch.ones_like 创建全1张量
2. torch.zeros和torch.zeros_like 创建全0张量
3. torch.full创建全为指定值的张量，torch.full_like创建全为指定值的其他类型结构相似的张量 

```python
import torch

# 1. 创建全0张量
def test01():
    print("test01------------------------")
    # 1. 创建指定形状的全0张量
    data = torch.zeros(2, 3)
    print(data)

    # 2. 根据张量形状创建全0张量
    data = torch.zeros_like(data)
    print(data)

# 2. 创建全1张量
def test02():
    print("\ntest02------------------------")
    # 1. 创建指定形状的全1张量
    data = torch.ones(2, 3)
    
    print(data)

    # 2. 根据张量形状创建全1张量
    data = torch.ones_like(data)
    print(data)
# 3. 创建全为指定值的张量
def test03():
    print("\ntest03------------------------")
    # 1. 创建指定形状指定值的张量
    data = torch.full([2, 3], 10)
    print(data)
    # 2. 根据张量形状创建全指定值张量
    data = torch.full_like(data, 10)
    print(data)

if __name__ == "__main__":
    test01()
    test02()
    test03()
```

```cmd
test01------------------------
tensor([[0., 0., 0.],
        [0., 0., 0.]])
tensor([[0., 0., 0.],
        [0., 0., 0.]])

test02------------------------
tensor([[1., 1., 1.],
        [1., 1., 1.]])
tensor([[1., 1., 1.],
        [1., 1., 1.]])

test03------------------------
tensor([[10, 10, 10],
        [10, 10, 10]])
tensor([[10, 10, 10],
        [10, 10, 10]])
```

---
## 1.4 张量元素类型转换

1. tensor.type(torch.DoubleTensor)
2. torch.double()

```python
import torch

def test():
    data = torch.full([2, 3], 10.)
    print(data.dtype)

    # 将data元素类型转换为float64类型
    # 1. 第一种方法
    print("第一种方法------------")
    data = data.type("torch.DoubleTensor")
    print(data.dtype)
  
    # 转换为其他类型
    # data = data.type(torch.ShortTensor)
    # data = data.type(torch.IntTensor)
    # data = data.type(torch.LongTensor)
    # data = data.type(torch.FloatTensor)
  
    # 2.第二种方法
    print("第二种方法-------------")
    data = data.to(torch.float64)
    print(data.dtype)
    data = data.to(torch.float32)
    print(data.dtype)
    data = data.to(torch.float16)
    print(data.dtype)
    data = data.to(torch.int64)
    print(data.dtype)
    data = data.to(torch.int32)
    print(data.dtype)
    data = data.to(torch.int16)
    print(data.dtype)
    data = data.to(torch.uint8)
    print(data.dtype)
    data = data.to(torch.bool)
    print(data.dtype)

    # 3. 第三种方法(为第二种方法的简写)
    print("第三种方法------------")
    data = data.double()
    print(data.dtype)

    # 转换为其他类型
    # data = data.short()
    # data = data.int()
    # data = data.long()
    # data = data.float()

if __name__ == "__main__":
    test()
```

```cmd
torch.float32
第一种方法------------
torch.float64
第二种方法-------------
torch.float64
torch.float32
torch.float16
torch.int64
torch.int32
torch.int16
torch.uint8
torch.bool
第三种方法------------
torch.float64
```

---

# 2 张量的数值计算

## 2.1 张量的基本运算

基本运算中，包括 add、sub、mul、div、neg 等函数，以及这些函数的带下划线版本 add、sub、mul、div、neg，其中带下划线的版本为修改原数据。

```python
import numpy as np
import torch

def test():
    data = torch.randint(0, 10, [2, 3])
    print(data)
    print("-" * 50)

    # 1. 不修改原数据
    new_data = data.add(10) #等价 new_data = data + 10
    print(new_data)
    print("-" * 50)
    # 2. 直接修改原数据
    # 注意：带下划线的函数为修改原数据本身
    data.add_(10) # 等价 data.add(data.add(10)),其中"_"的意思是表示之前的表达式
    print(data)

    # 3. 其他函数
    print("-" * 50)
    print(data.sub(100))
    print(data.mul(100))
    print(data.div(100))
    print(data.neg())
    print(data.abs())

if __name__ == "__main__":
    test()
```

```cmd
tensor([[5, 3, 5],
        [2, 2, 0]])
--------------------------------------------------
tensor([[15, 13, 15],
        [12, 12, 10]])
--------------------------------------------------
tensor([[15, 13, 15],
        [12, 12, 10]])
--------------------------------------------------
tensor([[-85, -87, -85],
        [-88, -88, -90]])
tensor([[1500, 1300, 1500],
        [1200, 1200, 1000]])
tensor([[0.1500, 0.1300, 0.1500],
        [0.1200, 0.1200, 0.1000]])
tensor([[-15, -13, -15],
        [-12, -12, -10]])
tensor([[15, 13, 15],
        [12, 12, 10]])
```

---

## 2.2 阿达姆积（Hadamard product）
阿达姆积指的是同型矩阵对应位置的元素相乘
```python
import numpy as np
import torch

def test():
    data1 = torch.tensor([[1, 2], [3, 4]])
    data2 = torch.tensor([[5, 6], [7, 8]])

    # 第一种方式
    data = torch.mul(data1, data2)
    print(data)
    print("-" * 50)

    # 第二种方法
    data = data1 * data2
    print(data)
    print("-" * 50)

if __name__ == "__main__":
    test()
```

```cmd
tensor([[ 5, 12],
        [21, 32]])
--------------------------------------------------
tensor([[ 5, 12],
        [21, 32]])
--------------------------------------------------
```

---

## 2.3 点积

点积运算要求第一个矩阵 shape:(n, m)，第二个矩阵 shape(m, p)两个矩阵点积运算 shape 为:(n, p)。

1. 运算符@ 用于进行两个矩阵的点乘运算
2. torch.mm 用于进行两个矩阵点乘运算，要求输入的矩阵为2维
3. torch.bmm 用于批量进行矩阵点乘运算，要求输入的矩阵为3维
4. torch.matmul对进行点乘运算的两矩阵形状没有限定
   1. 对于输入都是二维的张量相当于mm运算
   2. 对于输入都是三维的张量相当于bmm运算
   3. 对数输入的shape不同的张量，对应的最后几个维度必须符合矩阵运算规则

```python
import torch

# 矩阵乘法
def test01():
    print( "test01()" + "-" * 50)
    data1 = torch.randint(0, 10, (3, 2))
    print("data1:\n", data1)
    data2 = torch.randint(0, 10, (2, 2))
    print("data2:\n", data2)
  
    # 第一种方法
    data  = data1 @ data2
    print(data)
    print('-' * 50)

    # 第二种方法  备注：这是推荐用法
    data = torch.matmul(data1, data2)
    print(data)
    print('-' * 50)

    # 第三种方法
    data = torch.mm(data1, data2)
    print(data)
    print('-' * 50)

# 2. torch.mm 和 torch.matmul 的区别
def test02():
    print( "\ntest02()" + "-" * 50)
    # matmul可以用于两个不同维度矩阵相乘，
    # 第一个张量：torch.randint(0, 10, (3, 4, 5))
    # 第二个张量：torch.randint(0, 10, (5, 4))
	
    # 而mm只能用于两个矩阵相乘且这两个矩阵是二维矩阵
    print(torch.matmul(torch.randint(0, 10, (3, 4, 5)), torch.randint(0, 10, (5, 4))).shape)
    # print(torch.matmul(torch.randint(0, 10, (5, 4)), torch.randint(0, 10, (2, 3, 4, 5))).shape)
    # print(torch.mm(torch.randint(0, 10, (2, 3, 4)), torch.randint(0, 10, (4, 5))).shape)
    # print(torch.mm(torch.randint(0, 10, (5, 4)), torch.randint(0, 10, (2, 3, 4, 5))).shape)

# 3. torch.mm函数的用法
def test03():
    print( "\ntest03()" + "-" * 50)
    # 批量点积运算
    # 第一个维度为batch_size
    # 矩阵的二，三维要满足矩阵乘法规则
    print(torch.backends.mps.is_available())
    data1 = torch.randint(0, 10, (3, 4, 5))
    data2 = torch.randint(0, 10, (3, 5, 8))
    data = torch.bmm(data1, data2)
    print(data.shape)

if __name__ == "__main__":
    test01()
    test02()
    test03()

```

```cmd
test01()--------------------------------------------------
data1:
 tensor([[0, 8],
        [6, 2],
        [4, 8]])
data2:
 tensor([[4, 4],
        [2, 7]])
tensor([[16, 56],
        [28, 38],
        [32, 72]])
--------------------------------------------------
tensor([[16, 56],
        [28, 38],
        [32, 72]])
--------------------------------------------------
tensor([[16, 56],
        [28, 38],
        [32, 72]])
--------------------------------------------------

test02()--------------------------------------------------
torch.Size([3, 4, 4])

test03()--------------------------------------------------
False
torch.Size([3, 4, 8])
```

## 2.4 指定运算设备
PyTorch 默认会将张量创建在 CPU 控制的内存中，即：默认的运算设备为 CPU。我们也可以将张量创建在 GPU 上，能够利用对于矩阵计算的优势加快模型训练。将张量移动到 GPU 上有两种方法:

1. 使用 cuda 方法
2. 直接在 GPU 上创建张量
3. 使用 to 方法指定设备

```python
import torch

# 1. 使用 cuda 方法
def test01():
    data = torch.tensor([10, 20, 30])
    print("存储设备:", data.device)

    # 如果安装的不是 gpu 版本的 PyTorch
    # 或电脑本身没有 NVIDIA 卡的计算环境
    # 下面代码可能会报错
	
    data = data.cuda()
    print("存储设备:", data.device)

    # 使用 cpu 函数将张量移动到 cpu 上
    data = data.cpu()
    print("存储设备:", data.device)

    # 输出结果:
    # 存储设备: cpu
    # 存储设备: cuda:0
    # 存储设备: cpu
	
# 2. 直接将张量创建在 GPU 上
def test02():
    data = torch.tensor([10, 20, 30], device="cuda")
    # data = torch.tensor([10, 20, 30], device='cpu')
    print("存储设备:", data.device)
    # 使用 cpu 函数将张量移动到 cpu 上
    data = data.cpu()
    print("存储设备:", data.device)

def test03():
    data = torch.tensor([10, 20, 30])
    data = data.to("cuda:0")
    print("存储设备:", data.device)
    data = data.to("cpu")
    print("存储设备:", data.device)

def test04():
    data1 = torch.tensor([10, 20, 30], device="cuda:0")
    # data1 = torch.tensor([10, 20, 30], device='cpu')
    data2 = torch.tensor([10, 20, 30])
    print(data1.device, data2.device)
    # RuntimeError: Expected all tensors to be on the same device,
    # but found at least two devices, cuda:0 and cpu!
    data = data1 + data2
    # data2 = torch.tensor([10, 20, 30], device='cuda:0')
    # data = data1 + data2
    print(data)

if __name__ == "__main__":
    # test01()
    # test02()
    # test03()
    test04()
```
---
# 3 张量类型的转换

**学习目标**

- 掌握张量类型转换方法
---
张量的类型转换也是经常使用的一种操作，是必须掌握的知识点。在本小节，我们主要学习如何将 numpy 数组和 PyTorch Tensor 的转化方法。

## 3.1 张量转换为 numpy 数组
使用 Tensor.numpy 函数可以将张量转换为 ndarray 数组，但是共享内存，可以使用 copy 函数避免共享。
```python
import torch
import numpy as np

# 1. 将张量转换为 numpy 数组
def test00():
    data_tensor = torch.tensor([2, 3, 4])
    # 使用张量对象中的 numpy 函数进行转换
    data_numpy = data_tensor.numpy()
	
    print(type(data_tensor))
    print(type(data_numpy))

    # 注意: data_tensor 和 data_numpy 共享内存
    # 修改其中的一个，另外一个也会发生改变
    # data_tensor[0] = 100
	
    data_numpy[0] = 100

    print(data_tensor)
    print(data_numpy)

if __name__=='__main__':
	test01()
```

## 3.2 numpy 转换为张量
1. 使用 from_numpy 可以将 ndarray 数组转换为 Tensor，默认共享内存，使用 copy 函数避免共享。
2. 使用 torch.tensor 可以将 ndarray 数组转换为 Tensor，默认不共享内存。
关于数据拷贝的区别？
    - 浅拷贝：共享内存 --- a = b
    - 深拷贝：不共享内存 --- a = copy.deepcopy(b)
```python
# 1. 使用from_numpy函数
def test01():
    data_numpy = np.array([2, 3, 4])
    # 将numpy数组转换为张量类型
    # 1. from_numpy
    # 2. torch.tensor(ndarray)
  
    # 浅拷贝
    data_tensor = torch.from_numpy(data_numpy)
  
    # numpy和tensor共享内存
    # data_numpy[0] = 100
    data_tensor[0] = 100

    print(data_tensor)
    print(data_numpy)

# 2. 使用 torch.tensor 函数
def test02():
    data_numpy = np.array([2, 3, 4])

    data_tensor = torch.tensor(data_numpy)

    # numpy 和 tensor 不共享内存
    # data_numpy[0] = 100
    data_tensor[0] = 100

    print(data_tensor)
    print(data_numpy)

# 3. 标量张量和数字的转换
def test03():
    # 当张量只包含一个元素时，可以通过 item 函数提取出该值
    data = torch.tensor([30,])
    print(type(data))
    print(data.item())
  
    data = torch.tensor(30)
    print(type(data))
    print(data.item())
  
    # data1 = torch.tensor([1.55, 2.55])
    data1 = torch.tensor([1.55])
    print(data1.item())
    print(data1.item() == 1.55)

if __name__ == "__main__":
    # test00()
    # test01()
    # test02()
    test03()
```

```powershell
<class 'torch.Tensor'>
<class 'numpy.ndarray'>
tensor([100,   3,   4])
[100   3   4]
tensor([100,   3,   4])
[100   3   4]
tensor([100,   3,   4])
[2 3 4]
<class 'torch.Tensor'>
30
<class 'torch.Tensor'>
30
1.5499999523162842
False
```

## 3.3 标量张量和数字的转换
对于只有一个元素的张量，使用 item 方法将该值从张量中提取出来。
```python
# 3. 标量张量和数字的转换
def test03():
    # 当张量只包含一个元素时，可以通过 item 函数提取出该值
    data = torch.tensor([30,])
    print(data.item())

    data = torch.tensor(30)
    print(data.item())

if __name__ == '__main__':
    test03()
```

# 4 张量拼接操作

**学习目标**
1. 掌握 torch.cat 和 torch.stack 使用
2. 张量的拼接操作在神经网络搭建过程中是非常常用的方法，在后面将要学习到的残差网络，注意力机制中都使用到了张量拼接
## 4.1 torch.cat() 拼接： 
	--结果不会改变参与运算的张量的维度
- 主要用于神经网络搭建过程中，配合张量变形的操作
```python
import torch

def test():

    data1 = torch.randint(0, 10, [3, 5, 4])
    data2 = torch.randint(0, 10, [3, 5, 4])

    print(data1)
    print(data2)
    print("-" * 50)

    # 1. 按0维度拼接
    new_data = torch.cat([data1, data2], dim=0)
    print(new_data)
    print("-" * 50)

    # 2. 按1维度拼接
    new_data = torch.cat([data1, data2], dim=1)
    print(new_data)
    print("-" * 50)

    # 3. 按2维度拼接
    new_data = torch.cat([data1, data2], dim=2)
    print(new_data)


if __name__ == "__main__":
    test()
```
备注dim=-1、-2、-3是指从后往前拼维度
## 4.2 torch.stack() 拼接：  
- 结果会改变参与运算的张量的维度，升了一个维度
- 主要用于一个批次的数据，batch_size个样本直接进行stack堆叠，直接形成三维张量
- (batch_size, seq_length, embeding_dim)
```python
def test():

    data1 = torch.randint(0, 10, [2, 3])
    data2 = torch.randint(0, 10, [2, 3])
    # data2= torch.randint(0, 10, [2, 3, 4])
    print(data1)
    print(data2)
    print('---------------------------------')
    new_data1 = torch.stack([data1, data2], dim=0)
    print(new_data1)
    print(new_data1.shape)

    new_data2 = torch.cat([data1, data2], dim=0)
    print(new_data2)
    print(new_data2.shape)

    print('---------------------------------')

# new_data2 = torch.stack([data1, data2], dim=1)

# new_data3 = torch.stack([data1, data2], dim=2)
    '''
    print(new_data1.shape)
    print(new_data2.shape)
    print(new_data3.shape)
    print(new_data1)
    print(new_data2)
    print(new_data3)
    print('-' * 50)
    '''
    '''
    x = torch.Tensor([1])
    y = torch.Tensor([2])
    z = torch.stack([x, y], dim=0)
    print(z)

    new_data = torch.stack([data1, data2], dim=1)
    print(new_data.shape)
    print(new_data)
    print('---------------------------------')

    new_data = torch.stack([data1, data2], dim=2)
    print(new_data.shape)
    print(new_data)
    '''

if __name__=="__main__":
	test()
```

# 5 张量索引操作

**学习目标**

1. 掌握张量不同索引操作
2. 我们在操作张量时，经常需要去进行获取或者修改操作，掌握张量的花式索引操作是必须的一项能力。

## 5.1 简单行，列索引

```python
import torch

data = torch.randint(0, 10, [4, 5])
print(data)
print("-" * 50)

# 1. 简单行、列索引
def test01():

    print(data[0])
    print(data[:,0])
    print('-' * 50)

# 2. 列表索引
def test02():
    # 返回 (0, 1), (1, 2) 两个位置的元素
    print(data[[0, 1], [1, 2]])
    print('-' * 50)

    # 返回 0, 1 行的 1, 2 列共4个元素
    print(data[[[0], [1]], [1, 2]])

# 3. 范围索引
def test03():
    # 前3行的前2列数据
    print(data[:3, :2])

    # 第2行到最后的前2列数据
    print(data[2:, :2])

# 布尔索引
def test04():

    # 第2列大于5的行数据
    print(data[:, 2] > 5)
    print(data[data[:, 2] > 5])

    # 第1行大于5的列数据
    print(data[1] > 5)
    print(data[:, data[1] > 5])

# 多维索引
def test05():
    data = torch.randint(0, 10, [3, 4, 5])
    print(data)
    print('-' * 50)

    print(data[0, :, :])
    print(data[:, 0, :])
    print(data[:, :, 0])
    print(data.shape)

if __name__=='__main__':
    test01()
    test02()
    test03()
    test04()
    test05()
```
---
# 6 张量的形状操作

**学习目标**

- 掌握reshape,transpose,permute,view,contigous,squeeze,unsqueeze等函数使用
- 在我们后面搭建网络模型时，数据都是基于张量形式的表示，网络层与层之间很多都是以不同的 shape 的方式进行表现和运算，我们需要掌握对张量形状的操作，以便能够更好处理网络各层之间的数据连接。

## 6.1 reshape 函数的用法
- reshape 函数可以在保证张量数据不变的前提下改变数据的维度，将其转换成指定的形状，在后面的神经网络学习时，会经常使用该函数来调节数据的形状，以适配不同网络层之间的数据传递。
```python
import torch
import numpy as np

def test():

	data = torch.tensor([[10, 20, 30], [40, 50, 60]])

    # 1. 使用 shape 属性或者 size 方法都可以获得张量的形状
    print(data.shape, data.shape[0], data.shape[1])
    print(data.size(), data.size(0), data.size(1))
  
    # 2. 使用 reshape 函数修改张量形状
    new_data = data.reshape(1, 6)
    print(new_data.shape)

if __name__ == '__main__':
    test()
```

## 6.2 transpose 和 permute 函数的使用
- transpose 函数可以实现交换张量形状的指定维度，例如：一个张量的形状为 (2, 3, 4) 可以通过 transpose 函数把 3 和 4 进行交换，将张量的形状变为 (2, 4, 3)
- permute 函数可以一次交换更多的维度。
```python
import torch
import numpy as np

def test():
    data = torch.tensor(np.random.randint(0, 10, [3, 4, 5]))
    print("data shape:", data.size())

    # 1. 交换1和2维度
    new_data = torch.transpose(data, 1, 2)
    print("data shape:", new_data.size())

    # 2. 将 data 的形状修改为 (4, 5, 3)
    new_data = torch.transpose(data, 0, 1)
    new_data = torch.transpose(new_data, 1, 2)
    print("new_data shape:", new_data.size())

    # 3. 使用 permute 函数将形状修改为 (4, 5, 3)
    new_data = torch.permute(data, [1, 2, 0])
    print("new_data shape:", new_data.size())

if __name__ == "__main__":
	test()
```

## 6.3 view 和 contigous 函数的用法
- view 函数也可以用于修改张量的形状，但是其用法比较局限，只能用于存储在整块内存中的张量。
- 在 PyTorch 中，有些张量是由不同的数据块组成的，它们并没有存储在整块内存中，view 函数无法对这样的张量进行变形处理，例如：一个张量经过了 transpose 或者 permute 函数的处理之后，就无法使用 view 函数进行形状操作。
```python
import torch
import numpy as np
  
def test():
    data = torch.tensor([[10, 20, 30], [40, 50, 60]])
    print("data shape:", data.size())

    # 1. 使用 view 函数修改形状
    new_data = data.view(3, 2)
    print("new_data shape:", new_data.shape)

    # 2. 判断张量是否使用整块内存
    print("data:", data.is_contiguous())  # True

    # 3. 使用 transpose 函数修改形状
    new_data = torch.transpose(data, 0, 1)
    print("new_data:", new_data.is_contiguous())  # False

    # new_data = new_data.view(2, 3) # RuntimeError
    # 需要先使用 contiguous 函数转换为整块内存的张量，再使用 view 函数

    print(new_data.contiguous().is_contiguous())
    new_data = new_data.contiguous().view(2, 3)
    print("new_data shape:", new_data.shape)

if __name__ == "__main__":
    test()
```

>[!Note] 注意：关于4个重要的变形方法？
>- 1: `tensor.transpose()` --- 每次只能调整2个维度的值
>- 2: `tensor.permute()` --- 每次可以调整3个维度的值
>- 3: `tensor.view()` --- 只能应用在连续内存的张量上
>- 4: `tensor.reshape()` --- 可以应用在不连续内存的张量上

>[!Note] 注意：view() 和 reshape() 两种写法的工程分工
>- 偏向 view() 的场景：
>	- 底层模型架构开发（Attention 机制拆分、FFN 维度变换）。
>	- 大模型分布式训练/推理（Megatron-LM、vLLM 等对显存极致敏感的系统）。
>	- 算子与性能优化（确保每个操作都是 $O(1)$ 的元数据修改）。
>- 偏向 reshape() 的场景：
>	- 数据预处理与 Dataset 清洗（CPU 侧流水线，代码越不容易崩越好）。
>	- 算法原型探索与快速验证（学术实验、打比赛，快速出结果优先）。
>	- 确定不关心张量是否产生一次副本的边缘逻辑（如最后的指标统计、绘图、轻量分类头展平）。
## 6.4 squeeze 和 unsqueeze函数的用法
- squeeze 函数用删除 shape 为 1 的维度，unsqueeze 在每个维度添加 1，以增加数据的形状。
```python
import torch
import numpy as np

def test01():
    data = torch.tensor([[10, 20, 30], [40, 50, 60]])
    print("data shape:", data.size())

    # 1. 使用 view 函数修改形状
    new_data = data.view(3, 2)
    print("new_data shape:", new_data.shape)

    # 2. 判断张量是否使用整块内存
    print("data:", data.is_contiguous())  # True

    # 3. 使用 transpose 函数修改形状
    new_data = torch.transpose(data, 0, 1)
    print("new_data.shape:", new_data.shape)
    print(new_data)
    print("new_data:", new_data.is_contiguous())  # False
    print("#" * 50)
  
    # new_data = new_data.view(2, 3)  # RuntimeError
    new_data = new_data.reshape(2, 3)
    print('new_data.shape:', new_data.shape)
    print(new_data.is_contiguous())
 
    new_data = new_data.reshape(1, 6)
    print("new_data:", new_data)
    print(new_data.shape)
    print(new_data.is_contiguous())
    temp_data1 = new_data.view(6, 1)
    print('temp_data1.shape:', temp_data1.shape)

    # new_data = torch.transpose(data, 0, 1)
    # print('new_data.shape:', new_data.shape)
    # print('new_data:', new_data.is_contiguous())

    # 需要先使用 contiguous 函数转换为整块内存的张量，再使用 view 函数
    # print(new_data.contiguous().is_contiguous())
    # new_data = new_data.contiguous().view(2, 3)
    # new_data = new_data.reshape(2, 3)
    # print('new_data shape:', new_data.shape)

def test02():
    data = torch.tensor(np.random.randint(0, 10, [1, 3, 1, 5]))
    print("data shape:", data.size())
    print(data)

    # 1. 去掉值为1的维度
    new_data = data.squeeze()
    print("new_data shape:", new_data.size())
    print(new_data)

    # 2. 去掉指定位置为1的维度，注意：如果指定位置不是1，则不删除
    new_data = data.squeeze(1)
    print("new_data shape:", new_data.size())

    # 3. 在2维度增加一个维度
    new_data = data.unsqueeze(-1)
    print('new_data shape:', new_data.size())

def test03():
    data = torch.tensor(np.random.randint(0, 10, [2, 4]))
    print("data:", data)

    data1 = data.reshape(4, 2)
    print(data1.shape)
    print(data1)

    data2 = torch.transpose(data, 0, 1)
    print(data2.shape)
    print(data2)

if __name__ == "__main__":
    test01()
    test02()
    test03()
```
---
# 7 张量运算函数

**学习目标**

- 掌握张量相关运算函数

## 7.1常见运算函数
- PyTorch 为每个张量封装了很多实用的计算函数，例如计算均值，平方根，求和等等。
- Tensor: `exp()`, `pow()`, `sqrt()`, `log()` -- 是针对于张量中的每一个元素进行计算的。
```python
import torch

def test():
    data = torch.randint(0, 10, [2, 3], dtype=torch.float64)
    print(data)
    print("-" * 50)

    # 1. 计算均值
    # 注意：tensor 必须为 Float 或者 Double 类型
    print(data.mean())
    print(data.mean(dim=0))  # 按列计算均值
    print(data.mean(dim=1))  # 按行计算均值
    print("-" * 50)

    # 2. 计算总和
    print(data.sum())
    print(data.sum(dim=0))
    print(data.sum(dim=1))
    print("-" * 50)

    # 3. 计算平方
    print(data.pow(2))
    print("-" * 50)

    # 4. 计算平方根
    print(data.sqrt())
    print("-" * 50)

    # 5. 指数计算, e^n 次方
    print(data.exp())
    print("-" * 50)

    # 6. 对数计算
    print(data.log())  # 以 e 为底
    print(data.log2())
    print(data.log10())

if __name__ == "__main__":
    test()
```
---
# 8 自动微分模块

**学习目标**
- 掌握梯度计算
- 自动微分 (Autograd) 模块对张量做了进一步的封装，具有自动求导功能. 自动微分模块是构成神经网络训练的必要模块，在神经网络的反向传播过程中，Autograd 模块基于正向计算的结果对当前的参数进行微分计算，从而实现网络权重参数的更新。

## 8.1 梯度基本计算
- 我们使用 `backward` 方法，`grad` 属性来实现梯度的计算和访问。
```python
 import torch
  
# 1. 单标量梯度的计算
# y = x**2 + 20
def test01():
    # 定义需要求导的张量
    # 张量的值类型必须是浮点类型
    x = torch.tensor(10, requires_grad=True, dtype=torch.float64)

	# 变量经过中间运算
    f = x**2 + 20

	# 自动微分
    f.backward()

    # 打印 x 变量的梯度
    # backward 函数计算的梯度值会存储在张量的 grad 变量中
    print(x.grad)
    print(x.data)

# 2. 单向量梯度的计算
# y = x**2 + 20
def test02():
    # 定义需要求导的张量
    x = torch.tensor([10, 20, 30, 40], requires_grad=True, dtype=torch.float64)
    # 变量经过中间计算
    f1 = x**2 + 20
  
    # 注意：
    # 由于求导的结果必须是标量
    # 而 f 的结果是：tensor([120., 420.])
    # 所以，不能直接自动微分
    # 需要将结果计算为标量才能进行计算
    f2 = f1.mean()  # f2 = 1/2 * x

    # 自动微分
    f2.backward()

    # 打印 x 变量的梯度
    print(x.grad)

if __name__ == "__main__":
    test01()
    test02()
```

## 8.2 控制梯度计算
- 我们可以通过一些方法使得在 `requires_grad=True` 的张量在某些时候计算不进行梯度计算。
- 关于反向传播的求导？
	- $f_1 = x^2 + 20$
	- $f_2 = \frac{1}{4} f_1 = \frac{1}{4}x^2 + 5$
	- $f_2' = \frac{1}{2}x$
- 为什么Pytorch中循环求导的导数值会累加在tensor.grad中？
	- 核心原因在于未来模型训练有一个重要的概念：batch_size = 32，批次样本的概念
	- 每一条样本单独经过GPU中的计算单元，进行前向forward + 反向传播backword
		 - 第 1 条样本数据，forward + backward = 得出梯度值grad1，存入x.grad中。
		 - 第 2 条样本数据，forward + backward = 得出梯度值grad2，存入x.grad中。
		 - ......
		 - 第 32 条样本数据，forward + backward = 得出梯度值grad32，存入x.grad中。
		 - 重点：这个批次样本的梯度均值 x.grad / 32，别用于来更新模型的参数！！！
		 - 在一个批次更新结束后，执行 optimizer.zero_grad() 操作，梯度清零，为下一个批次的32条样本准备！
		 
```python
import torch

# 1. 控制不计算梯度
def test01():
    x = torch.tensor(10, requires_grad=True, dtype=torch.float64, device="cuda")
    print(x.requires_grad)

	# 第一种方式：对代码进行装饰
    with torch.no_grad():
        y = x**2
        print(y.requires_grad)

    # 第二种方式：对函数进行装饰
    @torch.no_grad()
    def my_func(x):
        return x**2

	print(my_func(x).requires_grad)

    # 第三种方式
    torch.set_grad_enabled(False)
    y = x**2
    print(y.requires_grad)

# 2. 注意：累计梯度
def test02():
    # 定义需要求导张量
    x = torch.tensor([10, 20, 30, 40], requires_grad=True, dtype=torch.float64, device="cuda")

    for _ in range(3):
        f1 = x**2 + 20
        f2 = f1.mean()

		# 默认张量的 grad 属性会累计历史梯度值
        # 所以，需要我们每次手动清理上次的梯度
        # 注意：一开始梯度不存在，需要做判断
        if x.grad is not None:
            x.grad.data.zero_()

        f2.backward()
        print(x.grad)
        print("-" * 50)

# 3. 梯度下降优化最优解
def test03():
# y = x**2
    x = torch.tensor(10, requires_grad=True, dtype=torch.float64, device="cuda")
    count = 0
    for _ in range(50000):
        # 正向计算
        f = x ** 2

        # 梯度清零
        if x.grad is not None:
            x.grad.data.zero_()

		# 反向传播计算梯度
        f.backward()

        # 更新参数
        if x.grad is not None:
            x.data = x.data - 0.001 * x.grad

			count += 1
            if count % 100 == 0:
                print('%.10f' % x.data)

if __name__ == '__main__':
    # test01()
    # print('----------------------------')
    # test02()
    # print('----------------------------')
    test03()
```
## 8.3 梯度计算注意
- 当对设置 `requires_grad=True` 的张量使用 `numpy` 函数进行转换时，会出现如下报错： `Can't call numpy() on Tensor that requires grad. Use tensor.detach().numpy() instead.`
- 此时，需要先使用 `detach` 函数将张量进行分离，再使用 `numpy` 函数。
- 注意：`detach` 之后会产生一个新的张量，新的张量作为叶子结点，并且该张量和原来的张量共享数据，但是分离后的张量不需要计算梯度。
```python
import torch

# 1. detach 函数用法
def test01():
    x = torch.tensor([10, 20], requires_grad=True, dtype=torch.float64)

    # Can't call numpy() on Tensor that requires grad. Use tensor.detach().numpy() instead.
    # print(x.numpy())        # 错误
    print(x.detach().numpy()) # 正确

# 2. detach 前后张量共享内存
def test02():
    x1 = torch.tensor([10, 20], requires_grad=True, dtype=torch.float64)

	# x2 作为叶子结点
    x2 = x1.detach()

	# 两个张量的值一样
	print(id(x1.data), id(x2.data))
    x2.data = torch.tensor([100, 200])
    print(x1)
    print(x2)

    # x2 不会自动计算梯度: False
    print(x2.requires_grad)

if __name__ == '__main__':
    test01()
    test02()
```
---
# 9 模型的保存加载

**学习目标**
- 掌握PyTorch保存模型的方法
- 神经网络的训练有时需要几天，几周，甚至几个月，为了在每次使用模型时避免高代价的重复训练，我们就需要将模型序列化到磁盘中，使用的时候反序列化到内存中。
## 9.1 模型保存参数
```python
import torch
import torch.nn as nn

# ==========================================
# 核心：配置 GPU 设备
# ==========================================
# 如果有英伟达显卡，device 会变成 "cuda"，否则是 "cpu"
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
print(f"当前使用的训练设备是: {device}")

# 假设我们有一个模型
class SimpleModel(nn.Module):
    def __init__(self):
        super(SimpleModel, self).__init__()
        self.fc = nn.Linear(10, 1)
  
    def forward(self, x):
        return self.fc(x)

# 创建一个与保存时相同结构的模型
model = SimpleModel()

# ==========================================
# 核心：将模型搬移到 GPU
# ==========================================
model = model.to(device)

# 保存模型的参数
torch.save(model.state_dict(), 'model_weights_20260629.pth')
print(model)
print('------------------')
print(model.state_dict())

# 加载模型的参数
model.load_state_dict(torch.load('model_weights_20260629.pth'))
print(model)
print('------------------')
print(model.state_dict())

# 保存完整的模型 --备注：官方建议大家只保存状态字典
# torch.save(model, 'model_weights_1.pth')
# print(model)

# model = torch.load('model_weights_1.pth')
# print(model)
```
- 注意：
    - 模型结构：如果你只保存了模型的参数，那么在加载时需要确保你有与保存时相同的模型结构。
    - 设备兼容性：如果你在一个设备上保存了模型(例如GPU)，而在另一个设备上加载(例如CPU)，你可能需要使用 `map_location` 参数来指定设备。
        - `device = torch.device('cpu')`  
            `model.load_state_dict(torch.load('model_weights.pth', map_location=device))`