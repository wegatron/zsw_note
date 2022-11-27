## git
gitlen, gitkaren的插件

## python 环境配置
1. 安装`Python IntelliSense(Pylance), Lighting Debuging`插件.
2. 选择python环境`CTRL`+`SHIFT`+`P`, `python:Select Interpreter`.
3. 添加运行参数. 切换到调试界面, 修改configuration
    ```json
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal",
                "args":["-p", "linux"],
                "justMyCode": true
            }
        ]
    }
    ```

## C++/CMake 环境配置

1. 安装插件
    * CMake
    * CMake Tools

2. 点击下方statusbar上的`CMake`选择`tool-kit`进行编译.
    选择一次之后, 默认会使用之前选择的`tool-kit`, 若想改换其他`took-kit`, 则可以通过命令`CTRL`+`SHIFT`+`P`, `cmake: select kit`进行配置

3. cmake 参数设置
    与cmake-gui的用法一样, 在configure完成之后, 通过命令 `cmake: Edit CMake Cache` 对cmake参数进行修改.

4. 调试环境配置

## 搜索消失

若搜索栏与`explore`显示在一起, 进入`Explore`, 在搜索区域上右键, `reset search...`.

其他, 在左侧`activity`栏中右键, `search`上打钩.

## 文件过滤(不显示)

`file` -> `settings` -> `files exclude`