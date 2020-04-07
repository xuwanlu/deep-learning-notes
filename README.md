# deep-learning-notes
## Python Numpy教程
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
