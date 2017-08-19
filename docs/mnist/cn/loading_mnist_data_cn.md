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

下面是 MNIST 官方网页上，对训练图像数据的格式说明。

```
TRAINING SET IMAGE FILE (train-images-idx3-ubyte):

[offset] [type]          [value]          [description] 
0000     32 bit integer  0x00000803(2051) magic number 
0004     32 bit integer  60000            number of images 
0008     32 bit integer  28               number of rows 
0012     32 bit integer  28               number of columns 
0016     unsigned byte   ??               pixel 
0017     unsigned byte   ??               pixel 
........ 
xxxx     unsigned byte   ??               pixel
Pixels are organized row-wise. Pixel values are 0 to 255. 0 means background (white), 255 means foreground (black).
```

如上所述，文件的的开始 128 个字节，分为 4 段，每段 4 个字节，分别表示：文件标识符（magic）；图像个数；每个图像的行数；每个图像的列数。我们需要验证一下我们下载与解压的文件是否正确：

``` shell
$ xxd -c 4 -l 16 train-images-idx3-ubyte 
00000000: 0000 0803  ....
00000004: 0000 ea60  ...`
00000008: 0000 001c  ....
0000000c: 0000 001c  ....
```

我们先检查一下文件标识符是否是 2051：

``` shell
$ ((magic=0x0803))
$ echo $magic
2051
```

再检查训练图像的数量是否是 60000：

``` shell
$ ((numImages=0xea60))
$ echo $numImages
60000
```

其他的诸如图像的行列数的，可以参照检查。