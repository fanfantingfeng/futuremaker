When operating on NumPy arrays, it is possible to access the internal data buffer directly using a view without copying data around. This ensures good performance but can also cause unwanted problems if the user is not aware of how this works.
Hence, it is important to know the difference between these two terms and to know which operations return copies and which return views.

The NumPy array is a data structure consisting of two parts:
- the contiguous(相接的) data buffer with the actual data elements
- the metadata(元数据) that contains information abut the data buffer.
The metadata includes data type, strides, and other important information that helps manipulate the ==ndarray== easily.

## View
It is possible to access the array differently by just changing certain metadata like stride and dtype without changing the data buffer.
This creates a new way of looking at the data and these new arrays are called views. The data buffer remains the same, so any changes made to a view reflects in the original copy.
A view can be forced through the ==ndarray.view()== method.

## Copy
When a new array is created by duplicating the data buffer as well as the metadata, it is called a copy.
Changes made to the copy do not reflect on the original array. Making a copy is slower and memory-consuming but sometimes necessary. A copy can be forced by using ==ndarray.copy()==.

## Indexing operations
Views are created ==when elements can be addressed with offsets and strides== in the original array.
因此, 基础索引(basic indexing)操作将总是创建views.
```python
x = np.arange(10)
y = x[1:3]  #view
y = [10, 11]
print(x)
```
==y== gets changed when ==x== is changed because it is a view.

**进阶索引(advanced indexing) 将总是创建copies.**
```python
x = np.arange(9).reshape(3,3)
y = x[[1, 2]]   #Copy
y.base is None  #True
# 我们可以改变x里的对应内容, 并不会改变y的内容
x [[1, 2]] = [[10, 11, 12], [13, 14, 15]]
y[1] == [10, 11, 12] #False
```
需要关注的是, x\[\[1, 2]] 的赋值 既不是view 也是 copy, 因为这样的赋值是进行数据的替换.

## Other operations
The ==numpy.reshape()== function creates a view where possible or a copy otherwise.
In most cases, the strides can be modified to reshape the array with a view. However, in some cases where the array becomes non-contiguous (==perhaps after a ndarray.transpose operation==), the reshaping cannot be done by modifying strides and requires a copy.
In these cases, we can raise an error by assigning the new shape to the shape attribute of the array.
```python
x = np.ones((2, 3))
y = x.T    #makes the array non-contiguous
z = y.view()
z.shape = 6  #Error
```
Taking the example of another operation, ==np.ravel()== returns a contiguous flattened view of the array wherever possible.
On the other hand, ==ndarray.flatten()== always returns a flattened copy of the array.
However, to guarantee a view in most cases, ==x.reshape(-1)== may be preferable.
```python
a = y.ravel()
a.shape = 6
print(a)   #呈现shape为6的array

b = z.flatten()
b.shape = 6
print(b)

z.reshape(6)  #it works but z will not be changed.
print(z)
```

## 判断array是view还是copy
The ==base== attribute of the ndarray makes it easy to tell if an array is a view or a copy.
The base attribute of a view returns the original array while it returns None for a copy.
```python
x = np.arage(9)
y = x.reshape(3, 3)
y.base   #.reshape() creates a view

z = y[[2, 1]]  #advanced indexing creates a copy
z.base   #None
```
base 属性不能用于判断array对象是否是new(新创建的).

