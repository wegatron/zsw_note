# iOS Objective-C And Swift

## Objective-C Memory Management
通过NSObject实现引用计数, 以两种方式来管理.
* "Manual Retain-Release" or MRR (手动)
    * 对象创建(new, alloc, copy, mutableCopy)时引用计数为1.
    * 在两种情况下使用`retain`来增加引用计数:
        ① 有些函数会要求传入对象的所有权(函数退出时可能需要手动release一次);
        ② 保证对象是有效的.
    * 使用`release`来减少引用计数.

* 🥝"Automatic Reference Counting" or ARC (自动)
    自动的方式使用相同的机制实现引用计数, 在编译阶段自动插入代码来维护引用计数. (推荐使用)
    ```objective-c
    @autoreleasepool { ... }
    ```

## Swift

## reference
[objective-c turtorial](https://www.tutorialspoint.com/objective_c/objective_c_memory_management.htm)
[ios application](https://www.tutorialspoint.com/ios/ios_first_iphone_application.htm)
[Objective-C Property Attributes](https://academy.realm.io/posts/tmi-objective-c-property-attributes/#:~:text=Strong%20just%20means%20you%20have,and%20released%20back%20into%20memory.)

[Swift Tour](https://docs.swift.org/swift-book/GuidedTour/GuidedTour.html)