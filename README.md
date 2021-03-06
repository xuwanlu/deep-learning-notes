# deep-learning-notes
### Python Numpy教程
quicksort example
```
def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) / 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + middle + quicksort(right)

print quicksort([3,6,8,10,1,2,1])
# Prints "[1, 1, 2, 3, 6, 8, 10]"
```

查看python版本
```
python --version
```

 Python中没有 x++ 和 x-- 的操作符
布尔逻辑用的是英语

enumerate 用于列表
```
animals = ['cat', 'dog', 'monkey']
for idx, animal in enumerate(animals):
    print '#%d: %s' % (idx + 1, animal)
```
iteritems 用于字典
```
d = {'person': 2, 'cat': 4, 'spider': 8}
for animal, legs in d.iteritems():
    print 'A %s has %d legs' % (animal, legs)
```

列表：[ ]   字典：{ }   集合：{ }   

相比于列表来说，集合是无序并且不能重复的，
元组是有序且不可改变的列表，可用作键并作为集合元素

数组Arrays是numpy中的一种数据结构，一维数组的shape是[3，]
np.array([1, 2], dtype=np.int64)  # Force a particular datatype

*是元素逐个相乘，而不是矩阵乘法。在Numpy中使用dot来进行矩阵乘法

brocast广播机制：应用于不同维度数组之间的运算

### 图像分类笔记
#### Nearest Neighbor分类器
L1距离准确率能达到38.6%
L2距离准确率能达到35.4%
L1和L2比较。比较这两个度量方式是挺有意思的。在面对两个向量之间的差异时，L2比L1更加不能容忍这些差异。也就是说，相对于1个巨大的差异，L2距离更倾向于接受多个中等程度的差异。L1和L2都是在p-norm常用的特殊形式。

如何选择k值？
交叉验证。有时候，训练集数量较小（因此验证集的数量更小），人们会使用一种被称为交叉验证的方法，这种方法更加复杂些。还是用刚才的例子，如果是交叉验证集，我们就不是取1000个图像，而是将训练集平均分成5份，其中4份用来训练，1份用来验证。然后我们循环着取其中4份来训练，其中1份来验证，最后取所有5次验证结果的平均值作为算法验证结果。针对每个k值，得到5个准确率结果，取其平均值，看哪个k值的表现更好。

对最优的超参数做记录。记录最优参数后，是否应该让使用最优参数的算法在完整的训练集上运行并再次训练呢？
因为如果把验证集重新放回到训练集中（自然训练集的数据量就又变大了），有可能最优参数又会有所变化。在实践中，不要这样做。千万不要在最终的分类器中使用验证集数据，这样做会破坏对于最优参数的估计。直接使用测试集来测试用最优参数设置好的最优模型，得到测试集数据的分类准确率，并以此作为你的kNN分类器在该数据上的性能表现。

### 线性分类笔记
上一节中KNN的不足在于，分类器需要记忆所有的训练数据并储存，并且在测试的时候需要和所有的训练数据相比较，费时费力费空间。

我们将要实现一种更强大的方法来解决图像分类问题，该方法可以自然地延伸到神经网络和卷积神经网络上。这种方法主要有两部分组成：一个是评分函数（score function），它是原始图像数据到类别分值的映射。另一个是损失函数（loss function），它是用来量化预测分类标签的得分与真实标签之间一致性的。该方法可转化为一个最优化问题，在最优化过程中，将通过更新评分函数的参数来最小化损失函数值。

线性分类器：f(xi, W, b) = Wxi + b
在上面的公式中，假设每个图像数据都被拉长为一个长度为D的列向量，大小为[D x 1]。其中大小为[K x D]的矩阵W和大小为[K x 1]列向量b为该函数的参数（parameters）。参数W被称为权重（weights）。b被称为偏差向量（bias vector），这是因为它影响输出数值，但是并不和原始数据xi产生关联。在实际情况中，人们常常混用权重和参数这两个术语。

#### 多类支持向量机损失 Multiclass Support Vector Machine Loss
简而言之，SVM的损失函数想要正确分类类别的分数比不正确类别分数高，而且至少要高△。如果不满足这点，就开始计算损失值。

##### 折叶损失（hinge loss）
max(0,-)它常被称为折叶损失（hinge loss）。有时候会听到人们使用平方折叶损失SVM（即L2-SVM），它使用的是max(0,-)的平方，将更强烈（平方地而不是线性地）地惩罚过界的边界值。不使用平方是更标准的版本，但是在某些数据集中，平方折叶损失会工作得更好。可以通过交叉验证来决定到底使用哪个。

##### 正则化：
上面损失函数有一个问题。假设有一个数据集和一个权重集W能够正确地分类每个数据（即所有的边界都满足，对于所有的i都有Li=0）。问题在于这个W并不唯一：可能有很多相似的W都能正确地分类所有的数据。
换句话说，我们希望能向某些特定的权重W添加一些偏好，对其他权重则不添加，以此来消除模糊性。这一点是能够实现的，方法是向损失函数增加一个正则化惩罚（regularization penalty）部分。
最常用的正则化惩罚是L2范式，L2范式通过对所有参数进行逐元素的平方惩罚来抑制大数值的权重。

引入正则化惩罚还带来很多良好的性质，这些性质大多会在后续章节介绍。比如引入了L2惩罚后，SVM们就有了最大边界（max margin）这一良好性质。

其中最好的性质就是对大数值权重进行惩罚，可以提升其泛化能力，因为这就意味着没有哪个维度能够独自对于整体分值有过大的影响。举个例子，假设输入向量x=[1,1,1,1]，两个权重向量w1=[1,0,0,0]，w2=[0.25,0.25,025,0.25]。那么w1x=w2x=1，两个权重向量都得到同样的内积，但是的L2惩罚是1.0，而的L2惩罚是0.25。因此，根据L2惩罚来看，更好，因为它的正则化损失更小。从直观上来看，这是因为的权重值更小且更分散。既然L2惩罚倾向于更小更分散的权重向量，这就会鼓励分类器最终将所有维度上的特征都用起来，而不是强烈依赖其中少数几个维度。在后面的课程中可以看到，这一效果将会提升分类器的泛化能力，并避免过拟合。

需要注意的是，和权重不同，偏差没有这样的效果，因为它们并不控制输入维度上的影响强度。因此通常只对权重正则化，而不正则化偏差。在实际操作中，可发现这一操作的影响可忽略不计。最后，因为正则化惩罚的存在，不可能在所有的例子中得到0的损失值，这是因为只有当W=0的特殊情况下，才能得到损失值为0。

#### Softmax分类器
SVM是最常用的两个分类器之一，而另一个就是Softmax分类器。
在Softmax分类器中，函数映射保持不变，但将这些评分值视为每个分类的未归一化的对数概率，并且将折叶损失（hinge loss）替换为交叉熵损失（cross-entropy loss）。

softmax 函数：其输入值是一个向量，向量中元素为任意实数的评分值，函数对其进行压缩，输出一个向量，其中每个元素值在0到1之间，且所有元素之和为1。所以，包含softmax函数的完整交叉熵损失看起唬人，实际上还是比较容易理解的。

让人迷惑的命名规则：精确地说，SVM分类器使用的是折叶损失（hinge loss），有时候又被称为最大边界损失（max-margin loss）。Softmax分类器使用的是交叉熵损失（corss-entropy loss）。Softmax分类器的命名是从softmax函数那里得来的，softmax函数将原始分类评分变成正的归一化数值，所有数值和为1，这样处理后交叉熵损失才能应用。注意从技术上说“softmax损失（softmax loss）”是没有意义的，因为softmax只是一个压缩数值的函数。但是在这个说法常常被用来做简称。
