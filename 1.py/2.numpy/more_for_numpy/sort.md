## numpy.sort
==Return a sorted **copy** of an array==
numpy.sort(a, axis=-1, kind=None, order=None, \*, stable=None)
- axis: int or None, optional
	- default is -1, which sorts along the last axis.
- kind: {'quicksort', 'mergesort', 'heapsort', 'stable'}, optional
	- 排序算法
- order: str or list of str, optional
	- When a is an array with fields defined, this argument specifies which fields to compare first, second, etc. A single field can be specified as a string, and not all fields need be specified, but unspecified fields will still be used, in the order in which they come up in the dtype, to break ties.
- stable: bool, optional
	- Sort stability. If True, the returned array will maintain the relative order of ==a== values which compare as equal. If False or None, this is not guaranteed.  
	- Internally, this option selects ==kind='stable'==. Default: None.

The various sorting algorithms are characterized by their average speed:
- worst case performance(性能极差的情况)
- work space size(运行存储空间)
- whether they are stable.
A stable sort keeps items with the same key in the same relative order.

NumPy的四种排序算法及性能

| kind | speed | worst case | work space | stable |
| ---- | ---- | ---- | ---- | ---- |
| 'quicksort' | 1 | O(n^2) | 0 | no |
| 'heapsort' | 3 | O(n\*log(n)) | 0 | no |
| 'mergesort' | 2 | O(n\*log(n)) | ~n/2 | yes |
| 'timsort' | 2 | O(n\*log(n)) | ~n/2 | yes |
注: 数据类型决定了最终被使用的是mergesort还是timsort, 即使用户自行指定了某种算法.

For performance, ==sort== makes a temporary copy if needed to make the data contiguous in memory along the sort axis. For even better performance and reduced memory consumption, ensure that the array is already contiguous along the sort axis.
___
>NumPy中关于an array contigous的判定
- it occupies(占用/使用) an unbroken block of memory, and
- array elements with higher indexes occupy higher addresses (==no stride is negative==)
>The are two types of proper-contiguous NumPy arrays:
- Fortran-contiguous arrays refer to data that is stored column-wise, i.e. the indexing of data as stored in memory starts from the lowestdimension;
- C-contiguous, or simply contiguous arrays, refer to data that is stored row-wise, i.e. the indexing of data as stored in memory starts from the highest dimension.
- For one-dimensional arrays these notions(概念) coincide(类似/重合).
- 检测array是否为C-contiguous或Fortran-contiguous:
	- arr.flags.c_contiguous
	- arr.flags.f_contiguous

>stride的定义
- Physical memory is one-dimensional; Strides provides a mechanism to map a given index to an address in memory.
- For an N-dimensional array, its ==strides== attribute is an N-element tuple; Advancing from index ==i== to index ==i+1== on axis ==n== means adding ==a.strides\[n]== bytes to the address.
- Strides are computed automatically from an array's dtype and shape, but can be directly specified using ==as_strided==.

>ndarray.strides
- Tuple of bytes to step in each dimension when traversing an array.
- The byte offset of element ==(i[\0], i[\1], ..., i[\n])== in an array ==a== is:
	- offset = sum(np.array(i) * a.strides)
___

The sort order for ==complex numbers==(复数) is lexicographic(词典编撰的).
If both the real and imaginary parts are non-nan then the orfer is determined by the real parts except when they are equal, in which case the order is determined by the imaginary parts.
在numpy1.4.0版本之后, nan值被排到末尾.
```text
Real:[R, nan]
Complex:[R+Rj, R+nanj, nan+Rj, nan+nanj]
注: R是非空实数值
```

## ndarray.sort
Sort an array in-place.
ndarray.sort(axis=-1, kind=None, order=None)


## numpy.argsort
Returns the indices that would sort an array.
Perform an indirect sort along the given axis using the algorithm specified by the *kind* keyword. It returns an array of indices of the same-shape as *a* that index data along the given axis in sorted order.
numpy.argsort(a, axis=-1, kind=None, order=None, \*, stable=None)
注: 返回的是排序后array的元素index的array.


## numpy.lexsort
Perform an indirect stable sort using a sequence of keys.
Given multiple sorting keys, lexsort returns an array of integer indices that describe the sort order by multiple keys. The last key in the sequence is used for the primary sort order, ties atre broken by the second-to-last key, and so on.
numpy.lexsort(keys, axis=-1)
```python
surnames = ('Hertz', 'Galilei', 'Hertz')
first_names = ('Heinrich', 'Galileo', 'Gustav')
ind = np.lexsort((first_names, surnames))
print(ind)
##array([1, 2, 0])
[surnames[i] + "," first_names[i] for i in ind]
#['Galilei, Galileo', 'Hertz, Gustav', 'Hertz, Heinrich']
```



https://numpy.org/doc/stable/reference/generated/numpy.sort.html#numpy.sort