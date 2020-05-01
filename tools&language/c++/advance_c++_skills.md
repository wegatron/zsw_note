### static_assert
```c++
static_assert ( bool_constexpr , message )  (since C++11)
static_assert ( bool_constexpr )    (since C++17)
```
当表达式失败时, 编译报错, 信息为mesage.
example in Eigen, 在矩阵assignment时, 使用static_assert判断矩阵大小是否一致:
```c++
EIGEN_STATIC_ASSERT_SAME_MATRIX_SIZE(ActualDstTypeCleaned,Src)
```