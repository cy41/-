## shell 头文件

#!/bin/bash

## shell 输出语句

echo "hello world"

输出变量：echo ${x}

输出变量加其他字符：echo "cy is ${x}"

## shell 变量

x="cy" 等号两侧不能有空格。

**只读变量：**

```shell
cy="hello"
readonly cy
```

删除变量：（**不可删除只读变量**）

unset cy

变量的类型：

1. 局部变量
2. 环境变量
3. shell变量

字符串可以为单引号也可以为双引号。

单引号里的变量无效，双引号中可以有变量，可以出现转义字符。

拼接字符串可以用双引号。

#### 方法

1. 获取字符串长度： echo ${#string}

2. 提取子串： echo ${string:st:len} 从st开始，截取len长度

3. 查找子串：

    ```shell
    string="runoob is a great site"
    echo `expr index "$string" io`  # 输出 4
    ```

#### 数组

```shell
array_name=(value0 value1 value2 value3)
```

1. 读取数组：${数组名[下标]}

    1. ```shell
        valuen=${array_name[n]}
        ```

    2. ```shell
        echo ${array_name[@]}  #使用 @ 符号可以获取数组中的所有元素。
        ```

2. 获取长度：

    ```shell
    # 取得数组元素的个数
    length=${#array_name[@]}
    # 或者
    length=${#array_name[*]}
    # 取得数组单个元素的长度
    lengthn=${#array_name[n]}
    ```

#### 注释

单行注释：   #

多行：

```shell
:<<EOF
注释内容...
注释内容...
注释内容...
EOF
```