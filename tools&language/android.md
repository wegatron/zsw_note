## Android app build configuration
build tools
* gradle
    An advanced build toolkit, to automate and manage the build process, while allowing you to define flexible custom build configurations, using DSL language.
* android plugin for gradle
    Works with the build toolkit to provide processes and configurable settings that are specific to building and testing Android applications.


文件目录
```
MyApp/
├── build.gradle # defines build configurations that apply to all modules in your project
├── settings.gradle # tells Gradle which modules it should include when building your app
├── gradle.properties # configure project-wide Gradle settings, such as the Gradle daemon's maximum heap size.
├── local.properties # configures local environment properties for the build system
└── app
    ├── build.gradle # defines build configurations of module
    └── ...(source code)
```

* build.gradle
    `buildscript` block to define the Gradle repositories and dependencies that are common to ll modules in the project.

## Reference
[Configure your build](https://developer.android.com/studio/build#module-level)