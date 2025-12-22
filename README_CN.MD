# Aseprite 自动构建工作流
中文 | [English](./README.md)

这是一个 GitHub Actions 工作流，用于自动构建和发布指定版本的 Aseprite。

## 功能概述

该工作流允许您通过指定版本标签（tag）来自动构建 Aseprite，并生成可执行文件和发布版本。主要功能包括：

- **手动触发构建**：通过 workflow_dispatch 手动输入要构建的 Aseprite 版本
- **获取发布信息**：自动从官方 Aseprite 仓库获取该版本的发布说明
- **编译源代码**：使用 MSVC 编译器和 Ninja 构建系统编译 Aseprite
- **打包文件**：将编译好的可执行文件和资源文件打包成 ZIP 格式
- **创建发布**：在仓库中创建对应版本的发布，并上传构建产物

## 使用方法

### 前置条件

- 一个 GitHub 仓库（用来存放工作流文件）
- 该仓库需要有相应的访问权限来创建发布版本

### 启动构建

1. 进入仓库的 **Actions** 标签
2. 左侧选择 **"Build a specified version"** 工作流
3. 点击 **"Run workflow"** 按钮
4. 输入要构建的 Aseprite 版本标签（例如：`v1.3.16`）
5. 点击 **"Run workflow"** 确认

### 监控进度

在工作流运行时，您可以：
- 实时查看每个步骤的执行情况
- 查看详细的日志输出
- 在步骤失败时获得错误信息

## 工作流步骤详解

### 1. 获取发布信息
```bash
步骤名称：Get Release Body and Repository Name
功能：从官方 Aseprite 仓库 API 获取指定版本的发布说明
输出：
  - body-content：发布说明文本
  - repo-name：当前仓库名称
```

### 2. 检出源代码
```bash
步骤名称：Checkout Aseprite Source Code
功能：克隆 Aseprite 官方仓库中指定版本的源代码及所有子模块
```

### 3. 设置构建工具
```bash
步骤名称：Setup Ninja Build System & Setup MSVC Compiler
功能：
  - 安装 Ninja 构建系统
  - 配置 MSVC 编译环境
```

### 4. 下载 Skia 和生成构建配置
```bash
步骤名称：Download Skia and Generate Makefile
功能：
  - 下载 Skia 图形库（Windows Release x64 版本）
  - 使用 CMake 生成 Ninja 构建文件
配置参数：
  - CMAKE_BUILD_TYPE：Release（优化构建）
  - LAF_BACKEND：skia（图形后端）
  - 禁用更新器、OpenSSL，使用 Schannel 进行 SSL/TLS
```

### 5. 编译
```bash
步骤名称：Compile
功能：使用 Ninja 编译 Aseprite 源代码
```

### 6. 打包
```bash
步骤名称：Package
功能：
  - 将 aseprite.exe 和 data 文件夹打包成 ZIP 文件
  - 文件名格式：aseprite.{版本号}.zip
```

### 7. 创建发布
```bash
步骤名称：Create Draft Release
功能：
  - 在仓库中创建对应版本的发布
  - 上传打包好的 ZIP 文件
  - 发布说明自动填充为官方版本说明
  - 状态：草稿（draft）- 需手动发布
```

## 系统要求

- **操作系统**：Windows（runner：windows-latest）
- **磁盘空间**：至少 5GB（用于源代码、Skia 库和构建文件）
- **网络**：需要能访问 GitHub 和下载 Skia 库

## 重要说明

⚠️ **EULA 许可证合规性**

根据 Aseprite 的最终用户许可协议（EULA），发布版本被设置为 **草稿状态**。这意味着：

- 草稿发布对公众不可见
- 需要手动发布才能使其公开
- 私有仓库可以直接发布
- 公开仓库请确保遵守许可证要求

如需发布，请修改 `draft: true` 为 `draft: false`，并确保您有合法权利这样做。

## 输出文件

工作流成功完成后，会生成：

- **aseprite.{版本号}.zip**：包含以下内容
  - `aseprite.exe`：可执行程序
  - `data/`：资源文件夹（包含图片、脚本等）

## 可能遇到的问题

### 构建失败
- 检查输入的版本标签是否正确
- 查看详细日志找出具体错误
- 确保网络连接正常，能下载 Skia 库

### 发布失败
- 确保 `GITHUB_TOKEN` 有足够权限
- 检查同名标签是否已存在
- 验证 ZIP 文件是否成功生成

## 自定义修改

### 改为自动发布（不推荐）
修改第一步中的：
```yaml
draft: false  # 改为 false 即可自动发布
```

### 添加其他文件到发布
在打包步骤中添加更多文件到 ZIP 压缩包。

### 支持其他操作系统
扩展 matrix 策略，添加 macOS 或 Linux 配置。

## 许可证

此工作流遵守 Aseprite 的官方许可证条款。使用本工作流时，请确保您的使用方式符合 Aseprite 的许可证要求。

## 参考资源

- [Aseprite 官方仓库](https://github.com/aseprite/aseprite)
- [GitHub Actions 文档](https://docs.github.com/en/actions)
- [CMake 构建配置](https://cmake.org/)
