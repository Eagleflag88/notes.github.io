---
share: true
---
# Tensor

## 初始化

- numpy array：
```
x_np = torch.from_numpy(np_array)
```

- other tensor
```
x_ones = torch.ones_like(x_data)
x_rand = torch.rand_like(x_data, dtype=torch.float)
```

- random：
```
shape = (2,3,)，rand_tensor = torch.rand(shape)
```
- shape是一个tuple
- constant
```
ones_tensor = torch.ones(shape)
zeros_tensor = torch.zeros(shape)
```


## 属性

- tensor.shape
- tensor.dtype
- tensor.device
- tensor.size() → 返回的是torch.size对象，还提供了元素数量的信息

## Operation

- =: tensor1 = tensor → 同一内存
- Indexing
	- 返回的也是tensor的object
	- tensor = torch.ones(4, 4), tensor[:,1] = 0
- Joining
	- t1 = torch.cat([tensor, tensor, tensor], dim=1)
	- 默认是在第0个轴做拼接
- 点乘
	- tensor.mul(tensor): 表示两个tensor点乘tensor
	- 跟tensor*tensor等价
- 矩阵乘法: tensor.matmul(tensor.T)
- in-place op: tensor.add_(5)
	- tensor本身会被改变
	- id会保持
- to Numpy: t = torch.ones(5), n = t.numpy()

## View

- view只是在外表显示上修改了shape: 底层没有变化；
	- tensor.view(2, 3)
	- 把tensor元素打乱重排，主要用于改变张量的维度大小；
	- 但是只能在contigous的内存上 → Tensor底层一维数组元素的存储顺序与Tensor按行优先一维展开的元素顺序是否一致；
- tensor.reshape: reshape = contiguous.view
- tensor.permute(1, 0): 主要用于调整张量的维度顺序，不会改变每个维度元素的多少；
- Ref: https://pytorch.org/docs/master/tensor_view.html

## Ref
[Pytorch_Tutorial](https://pytorch.org/tutorials/beginner/blitz/tensor_tutorial.html#sphx-glr-beginner-blitz-tensor-tutorial-py)

# Autograd

## Forward Pass

- prediction = model(data)
	- model
		- 训练的resnet18模型
		- 有参数
	- data = torch.rand(1, 3, 64, 64)
- loss = (prediction - labels).sum(): loss是损失值，是个标量
		
## Back Prop

- loss.backward()
	- 按照链式求导计算loss对每个resnet18的参数的导数
	- 存在参数的.grad属性中

## requires_grad

- a = torch.tensor([2., 3.], requires_grad=True)
- 当requires_grad = True: 这个变量就是trainable
- 当任意输入requires_grad = True: 输出就是trainable

## Computational Graph

- Directed acyclic graph (DAG)
	- leaf node: input tensors
	- root node: output tensors
	- 中间的每个模块都是一个function object, 需要实现下面的方法
		- forward()
		- backward()
- Iteration
	- 在训练循环里一般是一个minibatch
	- forward pass
		- 计算forward的内容
		- 维护grad_fn: Variable的获得方式
	- back prop
		- 通过grad_fn计算梯度
		- 在每个参数的.grad属性中累计梯度 → 要这个grad不清零，每调用一次backward()都会跟之前的grad加在一起
- 计算图是动态的，每一个iteration都会重建 → 保证forward pass里面的控制流能够被计算到
- frozen parameter
	- 可以通过把参数的requires_grad置false让计算图不再计算这个参数的梯度
	- 在Finetuning的时候可用
	- 也可以用torch.no_grad()来创造一个无grad的环境 → with torch.no_grad():

## detach()
- new_z = z.detach(): 返回一个new_z，这个tensor不会记得他的计算历史。但是z仍然存在计算图中

## Variable
- Tensor的封装
- 包含
	- .data: Tensor
	- .grad: 这个参数的梯度
	- .grad_fn
		- Variable通过这个知道哪个操作/函数生成了他
		- 在计算backward的时候调用的函数
- 在计算图中代表一个节点

## Ref
- [Autograd_Tutorial](https://pytorch.org/tutorials/beginner/blitz/autograd_tutorial.html)

# Model Construction

## Process
- 利用`__ini__`来定义模型的结构
- 定义模型的forward方法

## `__Init__`
- nn.Sequential
	- Container,一般用来搭建小的block
	- 比如：Conv2d+ReLU+MaxPool
- nn.Conv2d
	- 2d卷积
	- Parameter
		- 输入输出通道数
		- kernel size
		- Stride，Padding等等，具体如何发挥作用参见[[CNN Basics#Standard Convolution|CNN Basics > Standard Convolution]]
	- expect input with the shape (n_samples, channels, height, width)
- nn.ReLU: inpalce置为true会导致输入变量被保留
- nn.MaxPool2d: Pooling
- nn.AvgPool2d
- nn.Linear
	- model线性关系
	- Parameter
		- 输入feature数
		- 输出feature数
- nn.Dropout(p)
	- 输出中的某些元素会以概率p被置0
	- 放在全连接层前面
	- 参见[[Training#Regularization|Training > Regularization]]
- nn.BatchNorm2d
	- 在每个Channel里计算mean和var
	- 输入和输出的shape一致
	- Parameter
		- Channel数
		- momentum
	- 参考[[Normalization#Batch Normalization|Normalization > Batch Normalization]]
- 以上模块有默认的初始化方式
	- 可以利用下面这句来初始化的时候调用父类的初始化函数
	- ``super(Net, self).__init__()``
- torch.nn → 包含可训练的参数
- torch.nn.functional → 不包含可训练的参数

## forward()
- x = self.module(x)
- x.view: 调整tensor的组织方式
- x.flatten: 把输入展平
- 由Torch.nn搭建的模型只接受minibatch形式的输入
- 对单个sample可以用input.unsqueeze(0)来fake minibatch

## 常用方法
- model.parameters(): 返回模型的可学习参数
- model.zero_grad(): 把模型的可学习参数置零

## torch_stat
- 获得模型的相关信息 → from torchstat import stat,  stat(model, (3, 224, 224))
- Ref: https://github.com/Swall0w/torchstat

## Ref
- [Neural networks with nn modules](https://jhui.github.io/2018/02/09/PyTorch-neural-networks/)

# Data Preparation

## Transform
- 定义各种预处理变换: 针对输入
- 用来进行数据扩增
- 用Compose的方法来整合所有操作
```
transform = transforms.Compose(
    [transforms.ToTensor(),
     transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
```

## Target_Transform
- 针对标签的预处理变换

## DataSet
- ``__init__``
	- 各种dataset相关的文件夹路径
	- Parameter
		- root_dir
		- transform
- ``__getitem__``
	- 参数: idx
	- load the item (image, point cloud)
	- return the item and annotation according idx
- ``__len__``
	- returns the size of the dataset.
```
trainset = torchvision.datasets.CIFAR10(root='./data', 
										train=True, 
										download=True, 
										transform=transform)
```

## DataSet Splitting
- torch.randperm: 产生random indices of training and test set
- Splitting
	- torch.utils.data.Subset(dataset)
	- torch.utils.data.random_split(dataset)

## DataLoader
- wrap the dataset into iterable
- Parameter
	- Dataset instance
	- Shuffle
	- Batch Size
	- collate_fn → 描述如何从DataSet里面抽取组合出data sample
	- num_workers: 利用多核并行计算
	- pin_memory: 加速Host和GPU之间的传输
```
trainloader = torch.utils.data.DataLoader(trainset, 
											batch_size=batch_size, 
											shuffle=True, 
											num_workers=2)
```

# Training

## Loss Function
- criterion = nn.CrossEntropyLoss()
- loss = criterion(output, target)

## Optimizer
- torch.optim → Optimizer Constructor
- Parameter
	- 模型的待优化变量model.parameter()
	- learning rate
	- momentum
- learning rate schedular

## Training
- 对每一个epcho
	- 数据被分成多个大小一致的minibatch: 通过dataloader定义
	- 针对每个minibatch
		- 通过dataloader获得本minibatch的输入和标签 → ``for batch, (X, y) in enumerate(dataloader):``
		- 根据输入计算预测的输出 → pred = model(X)
		- 计算loss → loss = loss_fn(pred, y)
		- 清除parameter的梯度信息 → optimizer.zero_grad()。因为在 PyTorch 中，默认情况下梯度是累积的。这意味着在每次反向传播过程中，新计算的梯度会添加到已存在的梯度上；
		- 进行back prop → loss.backward()
		- 调整参数 → optimizer.step()
	- update learning rate
	- Ref: [QuickStart](https://pytorch.org/tutorials/beginner/basics/quickstart_tutorial.html)

## Testing
- 把model调整到评估模式 → model.eval()
- 开一个无grad的context → with torch.no_grad():
- 计算loss





