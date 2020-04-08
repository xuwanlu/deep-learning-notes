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
