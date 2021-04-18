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

## 一些有用的类型
* std::string_view
  c++17特性, string_view顾名思义, 就是string的view(笑). std::string_view记录了字符串起始地址与长度, 实现差不多相当于std::pair<char*, size_t>. 可以指向字符串中间的一部分, 所以不一定是`\0`结尾. 注意不能用std::string_view隐式构造std::string, 但反过来是可以的.

  std::string_view还有很多其他用法, 比如以前经常会纠结, 函数参数是用const char*还是const std::string&, 两者不太兼容. 现在可以用std::string_view啦, 用值传才是正确的用法.

  ```c++
  void printValue(std::string_view name);
  ``` 

  注意"std::string_view"不拥有字符串的所有权, 因此只能作为临时变量, 否则可能造成野指针.

* std::map<std::string, int, std::less<>>
  c++14特性, 使用`std::less<>`, 则只需要可以比较就能够根据key搜索value, 并不一定是要相同类型.
  ```c++
  std::map<std::string, int> map_old;
  auto iter = map_old.find(std::string_view("key0")); // 编译失败

  std::map<std::string, int, std::less<>> map_new;
  auto iter2 = map_new.find(std::string_view("key0")); // 正常
  ```

* std::variant
  c++17特性. 实例子: 一元二次方程的求根公式, 根可能是一个, 也有可能是两个, 也有可能没有根.
  ```c++
  using Two = std::pair<double, double>;
  using Roots = std::variant<Two, double, void*>;

  Roots FindRoots(double a, double b, double c)
  {
      auto d = b*b-4*a*c;

      if (d > 0.0)
      {
          auto p = sqrt(d);
          return std::make_pair((-b + p) / 2 * a, (-b - p) / 2 * a);
      }
      else if (d == 0.0)
          return (-1*b)/(2*a);
      return nullptr;
  }

  struct RootPrinterVisitor
  {
      void operator()(const Two& roots) const
      {
          std::cout << "2 roots: " << roots.first << " " << roots.second << '\n';
      }
      void operator()(double root) const
      {
          std::cout << "1 root: " << root << '\n';
      }
      void operator()(void *) const
      {
          std::cout << "No real roots found.\n";
      }
  };

  TEST_F(TestFindRoot) {
      std::visit(RootPrinterVisitor(), FindRoots(1, -2, 1)); //(x-1)*(x-1)=0
      std::visit(RootPrinterVisitor(), FindRoots(1, -3, 2)); //(x-2)*(x-1)=0
      std::visit(RootPrinterVisitor(), FindRoots(1, 0, 2));  //x*x - 2 = 0
  }
  ```
  `std::visit`获取到`std::variant`实际存储的类型的时间复杂度为O(1), 性能不会随着`std::varant`中类型的增多而降低.
  [@使用std::variant](https://zhuanlan.zhihu.com/p/57530780)

  可以配合`std::monostate`来使用, `std::monostate`表示空(variant的index为无效的0).

## c++ 编程规范
[华为方舟编译规范](https://www.bookstack.cn/read/openarkcompiler/d2d6358058bab8c5.md)