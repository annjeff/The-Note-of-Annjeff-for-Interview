

# Python 数据分析与展示

## 一、Anaconda IDE 的基本使用方法

### 1.1 Anaconda 的特点

- 开源免费
- 支持进 800 个第三方库
- 包含多个主流工具
- 适合数据计算领域开发
- Win / Linux / OS X 跨平台

### 1.2 conda 

- conda 是一个工具，用于包管理和环境管理
- 包管理和 **pip** 类似，管理 Python 第三方库
- 环境管理能够允许用户使用不同版本 Python，并能灵活切换
- conda 将工具、第三方库、Python 版本、conda 都当作包，同等对待

### 1.3 anaconda

> 是一个集合，包括 **conda**、**某版本 Python**、一批第三方库等。

-----



## 二、NumPy 基础

### 2.1 数据维度的 Python 表示

- 一维数据：**列表**、和**集合**类型

  `[3.1398, 3.1349, 3.1376]`

  `{3.1398, 3.1349, 1.1376}`

- 二维数据：**列表类型**

  ```
  [ [3.1398, 3.1349, 3.1376],
  
  [3.1413, 3.1403, 3.1401] ]
  ```

- 多维数据：**列表类型**

- 高维数据：**字典类型**或**数据表示格式**

  ```
  dict = {
  	"firstName":"Tian",
  	"lastName":"Song"
  }
  ```

### 2.2 NumPy 功能特性

- NumPy 是一个开源的 Python 科学计算基础库
- 一个强大的 N 维数组对象 **ndarray**
- 广播功能函数
- 整合 **C/C++/Fortran**代码的工具
- 线性代数、傅里叶变换、随机数生成 

> NumPy 是 SciPy、Pandas等数据处理或科学计算库的基础

### 2.3 NumPy 的引用

```
import numpy as np
```

### 2.4 N 维数组对象：ndarray

- 数组对象可以去掉**元素间运算所需的循环**，使一维向量更像**单个数据**
- 设置专门的**数组对象**，经过优化，可以提升这类应用的运算速度
- ndarray 是一个多维数组对象，由两部分构成：
  - 实际的数据
  - 描述这些数据的元数据（数据维度、数据类型等）
- ndarray 数组一般要求所有元素类型相同（同质），数组下标从 **0**开始

```python
# np.array() 生成一个 ndarray 数组
# ndarray 在程序中的别名是：array
# np.array() 输出成 [] 形式，元素由 空格 分割
a = np.array([[0, 1, 2, 3, 4],
             9, 8, 7, 6, 5])
print(a)
# 轴（axis）:保存数据的维度
# 秩 (rank):轴的数量
```

### 2.5 ndarray 对象的属性

|   属性    |                        说明                        |
| :-------: | :------------------------------------------------: |
|   .ndim   |             秩，即轴的数量或维度的数量             |
|  .shape   |      ndarray 对象的尺度，对于矩阵，n 行 m 列       |
|   .size   | ndarray 对象元素的个数,相当于 .shape 中的 n*m 的值 |
|  .dtype   |               ndarray 对象的元素类型               |
| .itemsize |     ndarray 对象中每个元素的大小，以字节为单位     |

```python
import numpy as np

a =np.array([[0, 1, 2, 3, 4],
                [9, 8, 7, 6, 5]])

# 打印 a 的秩，即轴的数量或维度数量
print(a.ndim)   # 2

# 打印 a 的尺度
print(a.shape)  # (2,5)

# 打印 a 拥有元素的个数
print(a.size)   # 10

# 打印 ndarray 对象的元素类型
print(a.dtype)  # int32

# 打印 ndarray 对象每个元素大小，字节为单位
print(a.itemsize)   # 4
```

### 2.6 ndarray 的元素类型

|  数据类型  |                           说明                           |
| :--------: | :------------------------------------------------------: |
|    bool    |                 布尔类型，True 或 False                  |
|    intc    |    与 C 语言中的 int 类型一致，一般是 int32 或 int64     |
|    intp    | 用于索引的整数，与 C 语言中 size_t  一致，int32 或 int64 |
|    int8    |             字节长度的整数，取值：[-128,127]             |
|   int16    |          16 位长度的整数，取值：[-32768,32767]           |
|   int32    |         32 位长度的整数，取值：[-2^31^,2^31^ -1]         |
|   int64    |        64 位长度的整数，取值：[-2^63^ ,2^63^ -1]         |
|   uint8    |              8 位无符号整数，取值：[0,255]               |
|   uint16   |             16 位无符号整数，取值：[0,65535]             |
|   uint32   |           32 位无符号整数，取值：[0,2^31^ -1]            |
|   uint64   |           64 位无符号整数，取值：[0,2^64^ -1]            |
|  float16   |  16 位半精度浮点数，1 位符号位，5位指数位，10 位尾数位   |
|  float32   |  32 位半精度浮点数，1 位符号位，8位指数位，23 位尾数位   |
|  float64   |  64 位半精度浮点数，1 位符号位，11位指数位，52 位尾数位  |
| complex64  |           复数类型，实部和虚部都是 32位浮点数            |
| complex128 |           复数类型，实部和虚部都是 64 位浮点数           |

ndarray 为什么要支持这么多种元素的类型呢？

对比：Python 语法仅支持：**整数、浮点数、复数** 3种类型

ANS:

- 科学计算涉及数据较多，对存储和性能都有较高要求。
- 对元素类型精细定义，有助于 NumPy 合理使用存储空间并优化性能。
- 对元素类型精细定义，有助于程序员对程序规模有合理评估。

### 2.7 ndarray 支持非同质对象

- ndarray 数组可以由**非同质**对象构成
- 非同质 ndarray **元素**为对象类型
- 非同质 ndarray 对象**无法有效发挥 NumPy 优势，尽量避免使用

```python
# ndarray 对象 支持 非同质 对象
x = np.array([[1,2,3,4,5],
              [9,8,7,6]])
print(x.shape) # (2,)

print(x.dtype) # object

print(x.itemsize) # 8

print(x.size) # 2
```

### 2.8 ndarray 数组创建方法

- 从 Python 中的列表、元组等类型创建 ndarray 数组

    ```
    x = np.array(list/tuple)
    x = np.array(list/tuple, dtype=np.float32)
    # 当 np.array() 不指定 dtype 时，NumPy 将根据数据情况关联一个 dtype
    ```

    ```python
    ## 从列表元组中 创建 ndarray 对象
    x = np.array([0, 1, 2, 3])
    print(x) # [0 1 2 3]
    
    x2 = np.array((4, 5, 6, 7))
    print(x2) # [4 5 6 7]
    
    x3 = np.array([ [1, 2], [9, 8], (0.1, 0.2)])
    print(x3) # [[1.  2. ]
              # [9.  8. ]
              # [0.1 0.2]]
    ```

- 使用 NumPy 中函数创建 ndarray 数组，如：arange, ones, zeros等

    |         函数          |                         说明                          |
    | :-------------------: | :---------------------------------------------------: |
    |     np.arange(n)      | 类似 range() 函数，返回 ndarray 类型，元素从 0 到 n-1 |
    |    np.ones(shape)     | 根据 shape 生成一个全 1 的数组，shape 是一个元组类型  |
    |    np.zeros(shape)    | 根据 shape 生成一个全 0 的数组，shape 是一个元组类型  |
    |  np.full(shape, val)  |      根据 shape 生成一个数组，每个元素值都是 val      |
    |       np.eye(n)       |  创建一个正方形的 n*n 单位矩阵， 对角线为1，其余为 0  |
    |   np.ones_like(arr)   |       根据数组 arr 的形状，生成一个全 1 的数组        |
    |  np.zeros_like(arr)   |       根据数组 arr 的形状，生成一个全 0 的数组        |
    | np.full_like(arr,val) |     根据数组 arr 的形状，生成一个值为 val 的数组      |

    ```python
    ## 使用 NumPy中的函数，创建 ndarray()
    arr1 = np.arange(10)
    print(arr1)
    
    arr2 = np.ones((2,3))
    print(arr2)
    
    arr3 = np.zeros((2,3),dtype=np.int32)
    print(arr3)
    
    arr4 = np.eye(3)
    print(arr4)
    
    arr5 = np.full((2,3),9)
    print(arr5)
    
    arr6 = np.ones((2, 3, 4))
    print(arr6)
    
    # 根据 数组 arr1 的形状，生成一个全 1 的数组
    arr7 = np.ones_like(arr1)
    # 根据 数组 arr1 的形状，生成一个全 0 的数组
    arr8 = np.zeros_like(arr1)
    # 根据 数组 arr1 的形状，生成一个全 8 的数组
    arr9 = np.full_like(arr1,8)
    
    arr10 = np.linspace(1,10,4)
    print(arr10)
    ```

    

- 使用 NumPy 中的其他函数创建 ndarray 数组

    |       函数       |                  说明                  |
    | :--------------: | :------------------------------------: |
    |  np.linspace()   | 根据起止数据等间距地填充数据，形成数组 |
    | np.concatenate() |   将两个或多个数组合并成一个新的数组   |

```python
# linspace 有个 endpoint 指定 10 是否是生成的最后一个元素
arr11 = np.linspace(1,10,4, endpoint=False)
print(arr11)

# 将多个数组连接起来
arr12 = np.concatenate( (arr7,arr8) )
print(arr12)
```

### 2.9 ndarray 数组的维度变换

|        方法        |                         说明                          |
| :----------------: | :---------------------------------------------------: |
|  .reshape(shape)   | 不改变数组元素，返回一个 shape 形状的数组，原数组不变 |
|   .resize(shape)   |         与 .reshape() 功能一致，但修改原数组          |
| .swapaxes(ax1,ax2) |          将数组 n 个维度中的两个维度进行调换          |
|     .flatten()     |   对数组进行降维，返回折叠后的一维数组，原数组不变    |

```python
## ndarray 数组的维度变换
arr13 = np.ones((2,3,4), dtype=np.int32)
print(arr13)

arr14 = arr13.reshape((3,8))
print(arr14) # reshape 不改变原数组
arr13.resize((3,8))
print(arr13)    # resize 改变原数组
```



### 2.10 ndarray 数组的类型变换

> new_arr = arr.astype(new_type)

> astype() 方法一定会创建新的数组（原始数据的一个拷贝），即使两个类型一致。

```pyhotn
## ndarray 数组的类型变换
new_arr13 = arr13.astype(np.float32)
print(new_arr13)
```

### 2.11 ndarray 数组向列表的转换

> lst = arr.tolist()

```python
## 数组向列表转换
arr14 = np.full((2, 3, 4), 25, dtype=np.int32)
print(arr14)
```

