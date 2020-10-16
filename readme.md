
# 数据处理总结

## 数据处理目标

数据处理的目标，简而言之就是把脏的混乱的数据，尽过一些处理转换，把数据整理成利于分析和建模的`干净的数据`。

### 干净的数据定义

1. 每个单元格只储存单一信息
2. 每列是不同的变量
3. 每行是不同的观测值
4. 每个特定观测目的组成一张表

## 数据处理过程

### 读取数据

读取各种数据源，比如（CSV、SQL、Excel、JSON、txt等），然后转换为 `dataframe` 。

注: 以下出现的`df`指的都是pandas里面一种表格类型数据类型，指的是读入到pandas以后的数据源。

```Python
# 以读取excel文件为例
df = pd.read_excel("table_01.xlsx")
```

**table_01:**

|      |  姓名  | 语文 | 数学 | 英语 | 考试类型 |
| ---: | :----: | :--: | :--: | :--: | :------: |
|    0 | 吕傲文 |  86  |  90  |  91  |   期中   |
|    1 | 张香秀 |  67  |  79  |  78  |   期中   |
|    2 |  麻寒  |  73  |  57  |  85  |   期中   |
|    3 |  廉凡  |  96  |  65  |  68  |   期中   |
|    4 | 冯乐萱 |  73  |  56  |  76  |   期中   |
|    5 | 吕傲文 |  90  |  67  |  76  |   期末   |
|    6 | 张香秀 |  89  |  96  | 100  |   期末   |
|    7 |  麻寒  |  76  |  67  |  94  |   期末   |
|    8 |  廉凡  |  84  |  62  |  83  |   期末   |
|    9 | 冯乐萱 |  65  |  75  |  56  |   期末   |

**需要注意的问题**:

* 数据编码不对(可以选择utf8、gbk、gb2312、gb18030、utf_8_sig)
* 对于空值的处理方式（把空值看作空字符串，还是NA)
* 源数据的列的位置可能不符合预期，需要设置。
* 指定要读取的列。
* 指定要读取的行。

### 查看数据信息

* 查看表的行数和列数

    ```Python
    # 表格有10行5列
    df.shape
    >>> df
    >>> (10, 5)
    ```

* 查看表的表头

    ```Python
    df.columns
    >>> df
    >>> Index(['姓名', '语文', '数学', '英语', '考试类型'], dtype='object')
    ```

* 查看表的部分内容

    ```Python
    df.head(3)
    ```

|      |  姓名  | 语文 | 数学 | 英语 | 考试类型 |
| ---: | :----: | :--: | :--: | :--: | :------: |
|    0 | 吕傲文 |  86  |  90  |  91  |   期中   |
|    1 | 张香秀 |  67  |  79  |  78  |   期中   |
|    2 |  麻寒  |  73  |  57  |  85  |   期中    |

* 查看表格详细详细

    ```Python
    df.info()
    >>>
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 10 entries, 0 to 9
    Data columns (total 5 columns):
    #   Column  Non-Null Count  Dtype
    ---  ------  --------------  -----
    0   姓名      10 non-null     object
    1   语文      10 non-null     int64
    2   数学      10 non-null     int64
    3   英语      10 non-null     int64
    4   考试类型    10 non-null     object
    dtypes: int64(3), object(2)
    memory usage: 528.0+ bytes
    ```

* 使用pandas_profiling生成数据集

    ```Python
    from pandas_profiling import ProfileReport
    profile = ProfileReport(df, title='pandas_profiling_output')
    ```

    输出结果见此链接:
    [pandas_profiling_output](data_summary.html)

### 频繁需要使用的数据操作方法

1. 重命名列名

    ```Python
    # dict是一个以原始列名为键，修改后列名为值的字典
    df.rename(columns=dict)
    ```

2. 重排序列名

    ```Python
    # reorder_columns表示新的列名顺序的数组
    df = df[reorder_columns]
    ```

3. 选择列

    ```Python
    # 使用pyjanitor扩展方法，column_names需要选择的列名组成的数组
    df.select_columns(column_names)
    # pandas原生方法
    df[column_names]
    ```

4. 移除列

    ```Python
    # 使用pyjanitor扩展方法，columns_names表示需要移除的列的名称的数组。
    df.remove_columns(column_names)
    # pandas原生方法
    df.drop(columns=column_names)
    ```

5. 增加列

    ```Python
    # 使用pyjanitor扩展方法，columns_name表示增加列的列名
    # value表示增加列的值，可以是标量也可以是数组。
    df.add_column(column_name,value)
    # pandas原生方法
    df[column_name] = value
    ```

6. 增加多列

    ```Python
    # 使用pyjanitor扩展方法
    # column_name_1 新加列名1 value_1 对应的值，依此类推..
    df.add_columns(column_name_1=value_1,column_name_2=value_2 ... column_name_n=value_n)
    ```

7. 选择行

    ```Python
    # select_index_list需要选择的行索引组成的数组
    df.loc[select_index_list,:]
    ```

8. 移除行

    ```Python
    df.drop(index=[指定需要删除行的索引])
    ```

9. 增加行

    ```Python
    df.loc[指定增加的索引名称或者索引数组,:] = data
    ```

10. 选择`df`的子集

    ```Python
    df.loc[select_index_list,select_columns_list]
    ```

11. 修改单个值

    ```Python
    df.loc[index,column] = value
    ```

12. 筛选表格行

    ```Python
    # plan 1
    df.query('筛选条件')
    # plan 2
    df.loc[(筛选条件),:]
    ```

13. 删除重复值

    ```Python
    # column_names: 指定需要比较是否重复的列，默认是比较所有列。
    # keep: first -> 保留第一次出现的重复值。last -> 最后出现的重复值。False =>删除所有重复值
    df.drop_duplicates(subset=column_names,keep='first|last|False')
    ```

14. 标记重复值

15. 分列

    ```Python
    # plan 1
    df[col_name]=df[col_name].str.split(sep='分隔符',expand=True)
    # plan 2
    df[col_name]=df[col_name].str.extract(pat='正则匹配模式',expand=True)
    ```

16. 多列合并

    ```Python
    # 使用pyjanitor方法
    df = df.concatenate_columns(column_names=[需要合并的列名的数组],new_column_name='新列名',sep="分隔符")
    ```

### 实际频繁遇到的问题

1. 观察是否存在列名命名不规范

    如果存在列名命名不规范，修改列名。
    列名的命名要求是在保证语义明确的前提下尽可能简洁，避免使用各种标点符号。（Table_01命名没有不规范不需要修改）

2. 是否存在一个变量被存储于多列

    可以使用`melt`操作将多列融化到单列，然后再标记多列对应的分类变量类型值。

    ```Python
    # table_01，科目这个变量被存储于多列(语文、数学、英语) 。
    df.melt(id_vars=["姓名","考试类型"],var_name="科目",value_name="成绩")
    ```

    |    | 姓名   | 考试类型   | 科目   |   成绩 |
    |---:|:-------|:-----------|:-------|-------:|
    |  0 | 吕傲文 | 期中       | 语文   |     86 |
    |  1 | 吕傲文 | 期中       | 数学   |     90 |
    |  2 | 吕傲文 | 期中       | 英语   |     91 |
    |  3 | 张香秀 | 期中       | 语文   |     67 |
    |  4 | 张香秀 | 期中       | 数学   |     79 |
    |  5 | 张香秀 | 期中       | 英语   |     78 |
    |  6 | 麻寒   | 期中       | 语文   |     73 |
    |  7 | 麻寒   | 期中       | 数学   |     57 |
    |  8 | 麻寒   | 期中       | 英语   |     85 |
    |  9 | 廉凡   | 期中       | 语文   |     96 |
    | 10 | 廉凡   | 期中       | 数学   |     65 |
    | 11 | 廉凡   | 期中       | 英语   |     68 |
    | 12 | 冯乐萱 | 期中       | 语文   |     73 |
    | 13 | 冯乐萱 | 期中       | 数学   |     56 |
    | 14 | 冯乐萱 | 期中       | 英语   |     76 |
    | 15 | 吕傲文 | 期末       | 语文   |     90 |
    | 16 | 吕傲文 | 期末       | 数学   |     67 |
    | 17 | 吕傲文 | 期末       | 英语   |     76 |
    | 18 | 张香秀 | 期末       | 语文   |     89 |
    | 19 | 张香秀 | 期末       | 数学   |     96 |
    | 20 | 张香秀 | 期末       | 英语   |    100 |
    | 21 | 麻寒   | 期末       | 语文   |     76 |
    | 22 | 麻寒   | 期末       | 数学   |     67 |
    | 23 | 麻寒   | 期末       | 英语   |     94 |
    | 24 | 廉凡   | 期末       | 语文   |     84 |
    | 25 | 廉凡   | 期末       | 数学   |     62 |
    | 26 | 廉凡   | 期末       | 英语   |     83 |
    | 27 | 冯乐萱 | 期末       | 语文   |     65 |
    | 28 | 冯乐萱 | 期末       | 数学   |     75 |
    | 29 | 冯乐萱 | 期末       | 英语   |     56 |

3. 是否存在一列存储了多个变量

    可以使用字符串分列或者正则表达式提取的方法拆分至多列。

    ```Python
    # plan 1
    df[col_name].str.split(sep,expand=True)
    # plan 2
    df[col_name].str.extrat(pat,expand=True)
    ```

4. 是否存在多个变量，有的存储在行，也有的储存在了列

    先进行`melt`然后再进行`unstack`

    ```Python
    # 继续引用table_01
    df.melt(id_vars=["姓名","考试类型"],var_name="科目",value_name="成绩").set_index(['姓名', '科目', '考试类型'])['成绩'].unstack(-1).reset_index()
    df.columns.name = ''
    ```

    |    | 姓名   | 科目   |   期中 |   期末 |
    |---:|:-------|:-------|-------:|-------:|
    |  0 | 冯乐萱 | 数学   |     84 |     81 |
    |  1 | 冯乐萱 | 英语   |     58 |    100 |
    |  2 | 冯乐萱 | 语文   |     83 |     84 |
    |  3 | 吕傲文 | 数学   |     67 |     61 |
    |  4 | 吕傲文 | 英语   |     88 |     67 |
    |  5 | 吕傲文 | 语文   |     67 |     77 |
    |  6 | 廉凡   | 数学   |     58 |     96 |
    |  7 | 廉凡   | 英语   |     90 |     63 |
    |  8 | 廉凡   | 语文   |     99 |     79 |
    |  9 | 张香秀 | 数学   |     58 |     91 |
    | 10 | 张香秀 | 英语   |     92 |     81 |
    | 11 | 张香秀 | 语文   |     77 |     96 |
    | 12 | 麻寒   | 数学   |     55 |     59 |
    | 13 | 麻寒   | 英语   |     81 |     81 |
    | 14 | 麻寒   | 语文   |     84 |     97 |

5. 是否存在不同观测目的的表被组合在一张表内

    根据表的定义分解到多表中，方法通常是选择需要的列，删除重复的行，保存ID列，拆分到多张表。

6. 是否存在同一观测目的的数据被储存在了多个表中

    循环读取每张表然后合并到一张表

     ```Python
    df_all = pd.DataFrame()
    for file in fies:
        df = pd.read_excel(file)
        df_all = pd.concat([df_all,df],axis=0)
     ```

7. 是否存在完全无意义的空行和空列

    删除无意义的空行或者空列

    ```Python
    # 删除空列
    df.dropna(axis='columns',how='all')
    # 删除空行
    df.dropna(axis='index',how='all')
    ```

8. 是否定义准确数据类型

    如果数据类型可能存在问题，可以转换到需要的数据类型。

    ```Python
    df[col_name]=df[col_name].astype(typename)
    ```

9. 是否需要进行多表联接,是否可以多表连接，使用哪种联接方式

    如果需要多表联接，首先需要找到匹配的ID列，匹配的ID列可以是多列组合也可以单独的ID列。
    根据具体需求，可以采用内联接，左联接，外联接。

    ```Python
    df.merge(df1,how='left|right|inner',on='联接键')
    ```

10. 是否存在重复的数据行

    如果存在完全重复的数据行，需要删除。

    ```Python
    df.drop_duplicates()
    ```

### 实际问题解决方案

根据具体数据处理需求的不同，会需要采取不同的操作，但是绝大部分不外乎以下内容。

* 根据空值合并多列

* 匹配其他表的信息

* 根据多列生成笛卡尔积的结果

* 根据多列条件赋值

* 根据某列的内容使用拆分符分割，然后堆叠数据

* 空值填充的方法

* 字符串操作与匹配

* 查找和替换

* 根据某些条件分组然后聚合，生成新表。

* 根据某些条件分组然后聚合，将结果作为新列追加到原始表格。

* 根据多条件筛选数据源

* 对多列或者多行进行计算（四则运算，计数，非重复计算，求和，平均，中位数，众数，最大值，最小值，分位数，峰度，偏度等等）

* 对表进行`map`、`apply`、`transform`、`agg`、`applymap`等操作

* 对表进行`groupby`操作然后再进行`map`、`apply`、`transform`、`agg`、`applymap`等操作

* 多表合并（纵向和横向合并）