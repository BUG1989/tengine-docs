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

### Download Tengine Lite

```bash
$ git clone https://github.com/OAID/Tengine.git tengine-lite
$ cd tengine-lite
```

### 2.1 Prepare for x86_64 simulator platform

**non-cross-compilation**

```bash
$ cd <tengine-lite-root-dir>
$ mkdir -p ./3rdparty/tim-vx/lib/x86_64
$ mkdir -p ./3rdparty/tim-vx/include
$ cp -rf ../TIM-VX/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../TIM-VX/src    ./source/device/tim-vx/
$ cp -rf ../TIM-VX/prebuilt-sdk/x86_64_linux/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../TIM-VX/prebuilt-sdk/x86_64_linux/lib/*    ./3rdparty/tim-vx/lib/x86_64/
$ rm ./source/device/tim-vx/src/tim/vx/*_test.cc
```

Build Tengine

```bash
$ export LD_LIBRARY_PATH=<tengine-lite-root-dir>/3rdparty/tim-vx/lib/x86_64

$ cd <tengine-lite-root-dir>
$ mkdir build && cd build
$ cmake -DTENGINE_ENABLE_TIM_VX=ON ..
$ make -j4
```

### 2.2 Prepare for Khadas VIM3 platform

Prepare for VIM3 prebuild sdk:

```bash
$ wget -c https://github.com/VeriSilicon/TIM-VX/releases/download/v1.1.28/aarch64_A311D_D312513_A294074_R311680_T312233_O312045.tgz
$ tar zxvf aarch64_A311D_D312513_A294074_R311680_T312233_O312045.tgz
$ mv aarch64_A311D_D312513_A294074_R311680_T312233_O312045 prebuild-sdk-a311d

$ cd <tengine-lite-root-dir>
$ mkdir -p ./3rdparty/tim-vx/lib/aarch64
$ mkdir -p ./3rdparty/tim-vx/include
$ cp -rf ../TIM-VX/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../TIM-VX/src    ./source/device/tim-vx/
$ cp -rf ../prebuild-sdk-a311d/include/*    ./3rdparty/tim-vx/include/
$ cp -rf ../prebuild-sdk-a311d/lib/*    ./3rdparty/tim-vx/lib/aarch64/
$ rm ./source/device/tim-vx/src/tim/vx/*_test.cc
```

#### 2.2.1 cross-compilation

TOOLCHAIN_FILE in the <tengine-lite-root-dir>/toolchains
```bash
$ export LD_LIBRARY_PATH=<tengine-lite-root-dir>/3rdparty/tim-vx/lib/aarch64

$ cd <tengine-lite-root-dir>
$ mkdir build && cd build
$ cmake -DCMAKE_TOOLCHAIN_FILE=../toolchains/aarch64-linux-gnu.toolchain.cmake -DTENGINE_ENABLE_TIM_VX=ON ..
$ make -j4
```

#### 2.2.2 non-cross-compilation

Check for galcore:

```bash
$ sudo dmesg | grep Galcore
```

if  ( Galcore version < 6.4.3.p0.286725 )

```bash
$ rmmod galcore
$ insmod galcore.ko
```

Check for libOpenVX.so*:

```bash
$ sudo find / -name "libOpenVX.so*"
```

if  ( libOpenVX.so version <   libOpenVX.so.1.3.0  in  /usr/lib )

```bash
$ cd <tengine-lite-root-dir>
$ mkdir -p Backup
$ mv /usr/lib/libOpenVX.so* ./Backup
$ cp -rf ../prebuild-sdk-a311d/lib/libOpenVX.so* /usr/lib
```

Build Tengine

```bash
$ cd <tengine-lite-root-dir>
$ mkdir build && cd build
$ cmake -DTENGINE_ENABLE_TIM_VX=ON ..
$ make -j4
```