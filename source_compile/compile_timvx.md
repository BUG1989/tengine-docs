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
