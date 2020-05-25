## ubuntu g++多版本安装和切换
多版本g++安装
```bash
sudo apt install gcc-7 g++-7 gcc-8 g++-8 gcc-9 g++-9

# 使用下面的命令设置g++默认版本的优先顺序, 这里g++-9设置为90
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 90 --slave /usr/bin/g++ g++ /usr/bin/g++-9 --slave /usr/bin/gcov gcov /usr/bin/gcov-9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 80 --slave /usr/bin/g++ g++ /usr/bin/g++-8 --slave /usr/bin/gcov gcov /usr/bin/gcov-8
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 70 --slave /usr/bin/g++ g++ /usr/bin/g++-7 --slave /usr/bin/gcov gcov /usr/bin/gcov-7

# 切换g++版本
sudo update-alternatives --config gcc
```

```
Selection    Path            Priority   Status
------------------------------------------------------------
* 0            /usr/bin/gcc-9   90        auto mode
  1            /usr/bin/gcc-7   70        manual mode
  2            /usr/bin/gcc-8   80        manual mode
  3            /usr/bin/gcc-9   90        manual mode

Press  to keep the current choice[*], or type selection number: 
```

## static_assert
```c++
static_assert ( bool_constexpr , message )  (since C++11)
static_assert ( bool_constexpr )    (since C++17)
```
当表达式失败时, 编译报错, 信息为mesage.
example in Eigen, 在矩阵assignment时, 使用static_assert判断矩阵大小是否一致:
```c++
EIGEN_STATIC_ASSERT_SAME_MATRIX_SIZE(ActualDstTypeCleaned,Src)
```