最近看论文源码遇到了h5py库

对于深度学习而言，往往有数以十万记的数据，跑程序的时候经常会在加载数据集的时候出现Memory error，

查了很多资料，**感觉python的h5py包处理数据集非常方便，导入数据时，并不会占据内存空间**
## 安装
```
conda install h5py
```
## 创建h5py文件

```
>>> import h5py
>>> import numpy as np
>>> f = h5py.File("mytestfile.hdf5", "w")
```
在当前文件夹创建mytestfile.hdf5


create_dataset创建一个给定形状和dtype的数据集
```
>>> dset = f.create_dataset("mydataset", (100,), dtype='i')
```
也可以
```
>>> import h5py
>>> import numpy as np
>>> with h5py.File("mytestfile.hdf5", "w") as f:
>>>     dset = f.create_dataset("mydataset", (100,), dtype='i')
```
## Groups和分层组织

“HDF”代表“分层数据格式”。 HDF5文件中的每个对象都有一个名称，它们使用/ -separator排列在POSIX样式的层次结构中：
```
>>> dset.name
u'/mydataset'
```
此系统中的“folders”称为Groups。 我们创建的File对象本身就是一个Groups，在本例中是根Groups，名为/：
```
>>> f.name
u'/'
```

创建子组是通过适当命名的create_group完成的。 但我们需要先在“append”模式下打开文件（如果存在则读/写，否则创建）
```
>>> f = h5py.File('mydataset.hdf5', 'a')
>>> grp = f.create_group("subgroup")
```
所有Group对象也都有像File这样的create_ *方法：
```
>>> dset2 = grp.create_dataset("another_dataset", (50,), dtype='f')
>>> dset2.name
u'/subgroup/another_dataset'
```

## 读取文件

假设某人发送了一个HDF5文件mytestfile.hdf5。 （要创建此文件，请阅读创建文件。）
您需要做的第一件事是打开文件进行阅读：
```
>>> import h5py
>>> f = h5py.File('mytestfile.hdf5', 'r')
```
记住h5py.File就像一个**Python字典，因此我们可以检查key**，
```
>>> list(f.keys())
['mydataset']
```
让我们将数据集检查视为对象检查
```
>>> dset = f['mydataset']
```
获得的对象不是数组，而是HDF5数据集。 与NumPy数组一样，数据集同时具有形状和数据类型：
```
>>> dset.shape
(100,)
>>> dset.dtype
dtype('int32')
```
它们还**支持切片**。 
```
>>> dset[...] = np.arange(100)
>>> dset[0]
0
>>> dset[10]
10
>>> dset[0:100:10]
array([ 0, 10, 20, 30, 40, 50, 60, 70, 80, 90])
```
