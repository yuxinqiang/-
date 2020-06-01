# 概念

笔记对应的版本为 `webpackv4.43.0`

webpack是一个现代JavaScript应用程序的静态模块打包器（module bundler）。

当webpack处理应用程序时，会递归的构建一个*依赖关系图（dependency graph）*，其中包含程序需要的每个模块，然后将所有的模块打包成一个或多个bundle。

webpack从4.x版本开始，可以零配置使用，但是依然可以进行自定义配置。

webpack有四个**核心概念**：

- 入口（entry）
- 输出（output）
- loader
- 插件（plugins）

## 入口（entry）

**入口起点（entry point）**指示webpack应该使用哪个模块作为构建内部依赖的开始。

