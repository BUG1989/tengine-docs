# C API

## Initial

实现 Tengine 框架基础资源初始化、释放功能、版本号查询的功能。

示例：

```c++
/* inital tengine */
if (init_tengine() != 0)
{
    fprintf(stderr, "Initial tengine failed.\n");
    return -1;
}
fprintf(stderr, "tengine-lite library version: %s\n", get_tengine_version());

/* some codes */

/* release tengine */
release_tengine();
```

### `int init_tengine(void)`

Tengine 框架资源初始化（包含 Serializer 、Operator 等必要功能模块注册）在**当前进程中只能调用一次**。

参数：`None`

返回：`执行结果`

-  `0: 成功`
- `-1：失败`

### `void release_tengine(void);`

Tengine 框架资源释放，在**当前进程中只能调用一次**。

参数：`None`

返回：`None`

## Graph

## Node

## Tensor

## Graph run

## Device

## Exection

## Misc

## Plugin

## 宏定义

## 结构体

## 自定义算子

