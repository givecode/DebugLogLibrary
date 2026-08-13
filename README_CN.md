<!-- Copyright (c) 2026 haozena. All Rights Reserved. -->

# DebugLogLibrary Plugin for Unreal Engine 5 (UE5)

简体中文 | [English](README.md)

<img src="Resources/Icon128.png" alt="DebugLogLibrary 图标" width="128">

DebugLogLibrary 是一个 Unreal Engine 运行时日志插件，提供蓝图节点和 C++ 辅助工具，可将格式化的调试信息输出到输出日志、游戏视口和 Unreal Engine Visual Logger。

## 兼容性

| 项目 | 值 |
| --- | --- |
| 插件版本 | 5.8.1 |
| Unreal Engine | 5.4-5.8 |
| 平台 | 未设置明确的平台白名单 |
| 模块类型 | Runtime |
| 加载阶段 | Earliest Possible |


## 功能特性

- 在 `DebugLog` 分类中提供可从蓝图调用的日志节点。
- 支持输出到输出日志、视口、两个目标，或完全禁用输出。
- 支持信息、警告和错误消息。
- 支持带条件判断的信息、警告和错误节点。
- 支持字符串、字符串数组、整数、浮点数、向量、向量数组和旋转值日志。
- 支持可选的时间戳、前缀、后缀、对象上下文和 Visual Logger 输出。
- 可配置屏幕显示时长、颜色和消息键值。
- 提供用于标记未完成函数和分支的辅助节点及 C++ 宏。
- 默认在 Shipping 和 Test 构建中禁用日志。

## 安装

### 第一步：从 Fab 获取插件

无论选择哪种安装方式，都必须先从 Fab 获取插件。Unreal Engine 插件的实际下载由 Epic Games Launcher 中的 Fab Library 完成。

1. 使用 Epic Games 账户登录 [Fab](https://www.fab.com/)。
2. 搜索 **DebugLogLibrary** 并打开其产品页面。
3. 如果是免费插件，请选择 **Add to My Library**；如果是付费插件，请完成购买。
4. 打开 Epic Games Launcher，进入 **Unreal Engine > Library > Fab Library**。如果插件没有立即出现，请刷新 Fab Library。

插件出现在 Fab Library 后，从以下两种安装方式中选择一种。

### 方式一：安装到引擎（推荐）

这种方式会让使用该 Unreal Engine 安装版本的所有项目都能使用此插件。

1. 在 Epic Games Launcher 的 **Fab Library** 中找到 **DebugLogLibrary**。
2. 点击 **Install to Engine**。
3. 选择受支持的 Unreal Engine 版本，然后点击 **Install**。当前版本面向 Unreal Engine 5.4-5.8。
4. 使用该引擎版本打开项目。
5. 如有需要，在 **编辑 > 插件** 中启用 **DebugLogLibrary**，并根据提示重新启动编辑器。

### 方式二：安装到单个项目

如果插件需要随源码管理提交、仅供一个项目使用，或者需要在本地修改，请采用项目级安装。

1. 按照上述步骤通过 Fab 获取插件包。如果 Fab 首先将插件安装到了引擎，请在该引擎的 `Engine/Plugins` 目录下找到已经安装的 `DebugLogLibrary` 文件夹。
2. 关闭 Unreal Editor。
3. 将完整的 `DebugLogLibrary` 文件夹复制到项目的 `Plugins` 目录：

   ```text
   YourProject/
   `-- Plugins/
       `-- DebugLogLibrary/
           |-- DebugLogLibrary.uplugin
           |-- Resources/
           `-- Source/
   ```

4. 确认 `YourProject/Plugins/DebugLogLibrary/DebugLogLibrary.uplugin` 文件存在。
5. 打开项目。如果 Unreal Engine 提示需要重新构建模块，请针对目标引擎和平台构建项目。
6. 如有需要，在 **编辑 > 插件** 中启用 **DebugLogLibrary**，并根据提示重新启动编辑器。

同一个项目只应使用一份插件。如果引擎安装目录中已经加载了该插件，请移除或禁用项目中的重复副本。

Fab 插件的官方操作流程请参阅：[在 Unreal Engine 中使用插件](https://dev.epicgames.com/documentation/en-us/unreal-engine/working-with-plugins-in-unreal-engine)。

对于 C++ 项目，请在项目的 `.Build.cs` 文件中将该模块加入适当的依赖列表：

```csharp
PrivateDependencyModuleNames.AddRange(
    new string[]
    {
        "DebugLogLibrary"
    }
);
```

## 蓝图快速入门

在蓝图图表中搜索 `DebugLog`，添加 **Print Info** 等节点，输入消息并选择输出目标。

大多数节点提供以下通用输入：

| 输入 | 说明 |
| --- | --- |
| `Prefix` | 插入到格式化消息之前的文本。 |
| `Suffix` | 追加到格式化消息之后的文本。 |
| `Logging Option` | 选择视口、输出日志、两个目标或禁用输出。 |
| `VLog` | 启用后，同时将消息写入 Visual Logger。 |
| `Settings` | 控制时间戳、紧凑格式、视口显示时长、键值和颜色。 |
| `Context Object` | 用于标识消息来源并提供 Visual Logger 上下文的可选对象。该引脚默认为 `Self`，并在节点上隐藏。 |

大多数蓝图节点默认折叠高级输入。

## 输出目标

`ELoggingOptions` 枚举提供四种模式：

| 值 | 蓝图显示名称 | 行为 |
| --- | --- | --- |
| `LO_Viewport` | Viewport | 在屏幕上显示消息。 |
| `LO_Console` | Console | 将消息写入 Unreal 输出日志。 |
| `LO_Both` | Viewport and Console | 同时写入两个目标。 |
| `LO_NoLog` | Disabled | 禁止输出消息。 |

## 日志设置

`FDebugLogSettings` 可在蓝图和 C++ 中使用，当前默认值如下：

| 设置 | 默认值 | 说明 |
| --- | --- | --- |
| `IsLogDateTime` | `true` | 在消息前添加当前本地日期和时间。 |
| `bCompact` | `false` | 对支持的向量值使用紧凑格式。 |
| `TimeToDisplay` | `5.0` 秒 | 控制视口消息的显示时长。 |
| `Key` | `-1` | 每次添加新的屏幕消息，而不是替换具有相同键值的消息。 |
| `TextColor` | `(0.0, 0.66, 1.0)` | 设置视口消息颜色。 |

格式化结果包含可选的上下文对象名称、前缀、消息和后缀。数组节点会使用数组索引标记每个元素。

## 蓝图节点参考

### 常规消息

| 节点 | 说明 |
| --- | --- |
| `PrintString` | 将字符串作为信息消息输出。 |
| `PrintStringArray` | 输出字符串数组中的每个元素及其索引。 |
| `PrintInfo` | 输出信息消息。 |
| `PrintInfo(Condition)` | 仅在条件为真时输出信息消息。 |
| `PrintWarning` | 输出警告消息。 |
| `PrintWarning(Condition)` | 仅在条件为真时输出警告消息。 |
| `PrintError` | 输出错误消息。 |
| `PrintError(Condition)` | 仅在条件为真时输出错误消息。 |

### 结构化数值

| 节点 | 说明 |
| --- | --- |
| `PrintVector` | 输出 `FVector`，支持紧凑格式。 |
| `PrintVector2D` | 输出 `FVector2D`，支持紧凑格式。 |
| `PrintArrayVector` | 输出带索引的 `FVector` 数组。 |
| `PrintArrayVector2D` | 输出带索引的 `FVector2D` 数组。 |
| `PrintRotator` | 输出选定的 Roll、Pitch 和 Yaw 分量。 |
| `PrintNumberInt` | 使用选定的数制输出整数。 |
| `PrintArrayFloat` | 输出带索引的浮点数数组。 |

### 开发辅助工具

| 节点 | 说明 |
| --- | --- |
| `PrintBranchToBeImplemented` | 输出警告，标识尚未完成的函数分支。 |
| `PrintFunctionToBeImplemented` | 输出警告，标识尚未实现的函数。 |
| `PrintFunctionISIncomplete` | 输出警告，标识尚未完成的函数。 |
| `PrintVLog` | 使用选定的严重级别直接写入 Visual Logger。 |

## Visual Logger

在标准日志节点上将 `VLog` 设置为 `true`，即可将消息同步写入 Unreal Engine Visual Logger。使用 `LO_NoLog` 禁止输出的消息不会被同步写入。

`PrintVLog` 直接写入 Visual Logger，并通过 `EDebugLogType` 支持以下严重级别：

- Info
- Success
- Warning
- Error
- Fatal

如果需要将 Visual Logger 条目与特定 Actor 或 UObject 关联，请提供有效的上下文对象。

## C++ 用法

包含蓝图函数库头文件：

```cpp
#include "DebugLogLibraryBPLibrary.h"
```

将消息同时写入输出日志和视口：

```cpp
ULog::PrintInfo(
    TEXT("Inventory initialized"),
    TEXT("[Inventory] "),
    TEXT(""),
    LO_Both
);
```

自定义屏幕消息：

```cpp
FDebugLogSettings Settings;
Settings.IsLogDateTime = true;
Settings.TimeToDisplay = 8.0f;
Settings.Key = 100;
Settings.TextColor = FLinearColor::Yellow;

ULog::PrintWarning(
    TEXT("Item data is incomplete"),
    TEXT("[Inventory] "),
    TEXT(""),
    LO_Both,
    false,
    Settings,
    this
);
```

C++ 同样可以调用条件节点：

```cpp
ULog::PrintError_WithCondition(
    TEXT("Actor reference is invalid"),
    TargetActor == nullptr,
    TEXT("[Validation] "),
    TEXT(""),
    LO_Console
);
```

## C++ 宏

公共头文件提供源码位置信息宏和便捷宏。

### 源码信息

```cpp
ULOG_GET_CUR_FUNC_NAME()
ULOG_GET_CUR_FILE()
ULOG_GET_CUR_LINE()
ULOG_GET_CODE_INFO()
ULOG_GET_CODE_INFO_WITH_PREFIX()
```

### 实现状态标记

```cpp
ULOG_FUNC_TOBEIMPLEMENTED()
ULOG_BRANCH_TOBEIMPLEMENTED(TEXT("Server path"))
ULOG_FUNC_ISINCOMPLETE()
```

插件还提供对应的 `ULOG_PRINT_*` 别名。

### 带对象名称的消息宏

以下宏会在消息前添加当前 UObject 名称，适用于可以调用 `GetName()` 的类：

```cpp
ULOG_PRINT_INFO(TEXT("Ready"));
ULOG_PRINT_INFO_BOTH(TEXT("Ready"));
ULOG_PRINT_INFO_VIEWPORT(TEXT("Ready"));

ULOG_PRINT_WARNING(TEXT("Using fallback data"));
ULOG_PRINT_WARNING_BOTH(TEXT("Using fallback data"));
ULOG_PRINT_WARNING_VIEWPORT(TEXT("Using fallback data"));

ULOG_PRINT_ERROR(TEXT("Operation failed"));
ULOG_PRINT_ERROR_BOTH(TEXT("Operation failed"));
ULOG_PRINT_ERROR_VIEWPORT(TEXT("Operation failed"));
```

## 构建配置

满足以下任一条件时，调试日志功能处于启用状态：

- 目标不是 Shipping 或 Test 构建。
- 为模块构建明确地将 `USE_LOGGING_IN_SHIPPING` 定义为 `1`。

插件默认将 `USE_LOGGING_IN_SHIPPING` 定义为 `0`，因此其日志函数在 Shipping 和 Test 构建中不会执行任何操作。蓝图函数也标记为 `DevelopmentOnly`。

## 当前实现说明

- 十进制、二进制、十六进制和八进制整数输出均已实现。十六进制使用大写字母且不添加前缀，二进制和八进制同样不添加前缀；负数保留前导负号。
- 罗马数字转换对 1 至 3999 使用标准写法。零、负数以及大于 3999 的数值会回退为十进制文本，不再返回空字符串。
- `PrintVLog` 始终以 Visual Logger 为输出目标。其 `VLog` 和 `Settings` 输入为了保持 API 一致性而保留，但当前函数没有使用它们。

## 支持与反馈

- 文档：https://github.com/givecode/DebugLogLibrary
- 反馈问题或提出新功能需求：https://github.com/givecode/DebugLogLibrary/issues

## 版权

Copyright (c) 2026 haozena. All Rights Reserved.
