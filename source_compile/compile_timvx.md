# 源码编译（TIM-VX）

## 简介

[TIM-VX](https://github.com/VeriSilicon/TIM-VX.git) 的全称是 Tensor Interface Module for OpenVX，是 VeriSilicon 提供的用于在支持 OpenVX 的其自研 ML 加速器 IP 上实现深度学习神经网络模型部署。它可以做为 Android NN、TensorFlow-Lite、MLIR、TVM、Tengine 等 Runtime Inference Framework 的 Backend 模块。

Tengine 基于 [Khadas VIM3](https://www.khadas.cn/product-page/vim3)(Amlogic A311D)单板计算机，完成了 TIM-VX 的集成，充分发挥出其内置 NPU **高性能**和 Tengine 异构计算自动切图的**易用性**。

## 准备工作

由于 TIM-VX 官方只提供了 A311D 的预编译库和 x86_64 NPU 模拟计算的预编译库，因此本文档只基于上述两种平台进行源码编译、安装、运行说明。

### 下载 TIM-VX 源码 

```bash
$ git clone https://github.com/VeriSilicon/TIM-VX.git
```

### 下载 Tengine 源码

```bash
$ git clone https://github.com/OAID/Tengine.git tengine-lite
$ cd tengine-lite
```

### x86_64 仿真平台

准备依赖文件：

```bash
$ cd <tengine-lite-root-dir>
$ mkdir -p ./3rdparty/tim-vx/lib/x86_64
$ mkdir -p ./3rdparty/tim-vx/include
$ cp -rf ../TIM-VX/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../TIM-VX/src    ./src/dev/tim-vx/
$ cp -rf ../TIM-VX/prebuilt-sdk/x86_64_linux/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../TIM-VX/prebuilt-sdk/x86_64_linux/lib/*    ./3rdparty/tim-vx/lib/x86_64/
$ rm ./src/dev/tim-vx/src/tim/vx/*_test.cc
```

### Khadas VIM3 平台

下载 TIM-VX 准备好的 A311D 预编译库：

```bash
$ wget -c https://github.com/VeriSilicon/TIM-VX/releases/download/v1.1.28/aarch64_A311D_D312513_A294074_R311680_T312233_O312045.tgz
$ tar zxvf aarch64_A311D_D312513_A294074_R311680_T312233_O312045.tgz
$ mv aarch64_A311D_D312513_A294074_R311680_T312233_O312045 prebuild-sdk-a311d
```

准备依赖文件：

```bash
$ cd <tengine-lite-root-dir>
$ mkdir -p ./3rdparty/tim-vx/lib/aarch64
$ mkdir -p ./3rdparty/tim-vx/include
$ cp -rf ../TIM-VX/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../TIM-VX/src    ./src/dev/tim-vx/
$ cp -rf ../prebuild-sdk-a311d/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../prebuild-sdk-a311d/lib/*    ./3rdparty/tim-vx/lib/aarch64/
$ rm ./src/dev/tim-vx/src/tim/vx/*_test.cc
```

## 编译

```bash
$ mkdir build && cd build
$ cmake -DTENGINE_ENABLE_TIM_VX=ON -DTENGINE_ENABLE_TIM_VX_INTEGRATION=ON ..
$ make -j4
$ make install
```

## 示例

### 依赖的库文件

```
3rdparty/tim-vx/lib/
├── libArchModelSw.so
├── libCLC.so
├── libGAL.so
├── libNNArchPerf.so
├── libOpenVX.so
├── libOpenVXU.so
└── libVSC.so

build-tim-vx-arm64/install/lib/
└── libtengine-lite.so
```

如果使用 Khadas VIM3 设备，需要使用上诉动态库替代板上 `/lib` 目录下的已有库文件。

- Q: 为什么替换库？
- A: 因为在 Khadas VIM3 预装的固件中已经包含，需要更新。我们会及时同步 Khadas VIM3 原厂，尽快打包在预装固件中。  
- Q: Khadas VIM3 需要更新驱动吗?
- A: 是的，需要使用 TIM-VX 提供的 A311D 预编译包中的 `galcore.ko` ( /prebuild-sdk-a311d/lib/galcore.ko)内核驱动文件进行更新。
```
$ rmmod galcore
$ insmod galcore.ko
```

### 配置 Uint8 运行模式

TIM-VX 后端运行需要将 Tengine 的工作模型修改为 **uint8** 推理模式

```bash
/* set runtime options */
struct options opt;
opt.num_thread = num_thread;
opt.cluster = TENGINE_CLUSTER_ALL;
opt.precision = TENGINE_MODE_UINT8;
opt.affinity = 0;
```

### 运行结果展示

```
[khadas@Khadas tengine-lite]# ./tm_classification_timvx -m squeezenet_uint8.tmfile -i cat.jpg -r 1 -s 0.017,0.017,0.017 -r 10
Tengine plugin allocator TIMVX is registered.
Image height not specified, use default 227
Image width not specified, use default  227
Mean value not specified, use default   104.0, 116.7, 122.7
tengine-lite library version: 1.2-dev
TIM-VX prerun.

model file : squeezenet_uint8.tmfile
image file : cat.jpg
img_h, img_w, scale[3], mean[3] : 227 227 , 0.017 0.017 0.017, 104.0 116.7 122.7
Repeat 10 times, thread 1, avg time 2.95 ms, max_time 3.42 ms, min_time 2.76 ms
--------------------------------------
34.786182, 278
33.942883, 287
33.732056, 280
32.045452, 277
30.780502, 282
```

## 支持硬件列表

| 芯片厂家  | 设备      |
| -------- | --------- |
| Amlogic | A311D        |
| NXP     | iMX 8M Plus |
| X86-64  | Simulator    |

## 支持算子列表

## 如何获取 uint8 模型
如果要将网络模型运行在 Tengine + TIM-VX 支持的硬件平台上，需要将 Float32 的 tmfile 转换/压缩 成 Uint8 tmfile, 具体实现可参考以下章节. 
- [模型量化-非对称量化](../user_guides/quant_tool_uint8.md)
- [Uint8 量化工具下载地址](https://github.com/OAID/Tengine/releases/download/lite-v1.3/quant_tool_uint8)
