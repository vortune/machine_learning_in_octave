# 加载 MNIST 训练数据精解

本文将详细介绍手写数字数据 MNIST 的加载，以及将数据调制为适合用于机器学习训练的格式。

## 下载数据

我们首先需要从 [MNIST](http://yann.lecun.com/exdb/mnist/) 数据官网下载手写数据数据。数据库分为 4 个文件：

 * [train-images-idx3-ubyte.gz](http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz):         training set images (9912422 bytes)
 * [train-labels-idx1-ubyte.gz](http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz):           training set labels (28881 bytes)
 * [t10k-images-idx3-ubyte.gz](http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz):         test set images (1648877 bytes)
 * [t10k-labels-idx1-ubyte.gz](http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz):           test set labels (4542 bytes)

四组数据分别是：训练图像；训练标签；测试图像；测试标签。分别下载这4组数据，例如：

``` shell
$ wget http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz
```

接着，可以用 `gunzip` 工具将它们分别解压，如：

``` shell
$ gunzip -dv train-images-idx3-ubyte.gz
```

解压之后，得到一个 IDX 格式的文件，文件名是 `train-images-idx3-ubyte`。我们不必深究 IDX 文件格式的问题，我们直接根据 MNIST 官网上对文件格式的描述来分析文件的数据结构即可。

## 加载数据

这里以加载训练图像数据集文件 `train-images-idx3-ubyte` 作为例子来介绍文件的加载方法，另外三个文件可以此类推。

### Images 文件的数据格式

