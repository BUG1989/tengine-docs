# 源码编译（TIM-VX）

## 简介

[TIM-VX](https://github.com/VeriSilicon/TIM-VX.git) is a software integration module provided by VeriSilicon to facilitate deployment of Neural-Networks on OpenVX enabled ML accelerators.

Tengine Lite has supported to integrate with TIM-VX Library of Verisilicon to inference CNN by [Khadas VIM3](https://www.khadas.cn/product-page/vim3)(Amlogic A311D).

## 源码准备

For some special reasons, only supported on Khadas VIM3 or x86_64 simulator to work the following steps, currently.

### 下载 TIM-VX 源码 

```bash
$ git clone https://github.com/VeriSilicon/TIM-VX.git
```

### 下载 Tengine 源码

```bash
$ git clone https://github.com/OAID/Tengine.git tengine-lite
$ cd tengine-lite
```

### 编译

#### x86_64 仿真平台准备工作

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

#### Khadas VIM3 平台准备工作

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

编译命令：

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

On the Khadas VIM3, it need to replace those libraries in the /lib/ 

### 常见问题解答
- Q: Why?
- A: Because the firmware of Khadas VIM3 maybe pre-install old version kernel module of NPU  
- Q: How to?
- A: Remove the old kernel module and replace it with the new version(in the /prebuild-sdk-a311d/lib/galcore.ko) 
```
$ rmmod galcore
$ insmod galcore.ko
```

### 配置 Uint8 运行模式

TIM-VX Library needs the uint8 network model

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

### 支持硬件列表
| Vendor  | Devices      |
| ------- | ------------ |
| Amlogic | A311D        |
| NXP     | i.MX 8M Plus |
| X86-64  | Simulator    |

### 如何获取 uint8 模型
The TIM-VX NPU backend needs the uint8 tmfile as it's input model file, you can **quantize** the tmfile from **float32** to **uint8** from here. 
- [Tengine Post Training Quantization Tools](../tools/quantize/README.md)
- [Download the uint8 quant tool](https://github.com/OAID/Tengine/releases/download/lite-v1.3/quant_tool_uint8)
