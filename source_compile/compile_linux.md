# 源码编译（Linux）

## 依赖工具安装

编译 Tengine Lite 依赖 `git, g++, cmake, make` 等以下基本工具，如果没有安装，

- Ubuntu18.04 系统命令如下：

  ```bash
  sudo apt-get install cmake make g++ git
  ```

- Fedora28 系统命令如下：

  ```bash
  sudo dnf install cmake make g++ git
  ```

## 本地编译

### 下载 Tengine Lite 源码

下载 Tengine Lite 源码，位于 Tengine 的分支 tengine-lite 上：

```bash
git clone -b tengine-lite https://github.com/OAID/Tengine.git  Tengine-Lite
```

### 编译 Tengine Lite

```bash
cd Tengine-Lite
mkdir build 
cd build
cmake ..
make
make install
```

编译完成后 build/install/lib 目录会生成 `libtengine-lite.so` 文件，如下所示：

```bash
install
├── bin
│   ├── tm_benchmark
│   ├── tm_classification
│   └── tm_mobilenet_ssd
├── include
│   └── tengine_c_api.h
└── lib
    └── libtengine-lite.so
```

## 交叉编译 Arm32/64 Linux 版本

### 下载源码

```bash
git clone -b tengine-lite https://github.com/OAID/Tengine.git  Tengine-Lite
```

### 安装交叉编译工具链

Arm64 Linux 交叉编译工具链为：

```bash
sudo apt install g++-aarch64-linux-gnu
```

Arm32 Linux 交叉编译工具链为：

```bash
sudo apt install g++-arm-linux-gnueabihf
```

### 编译 Tengine Lite

Arm64 Linux 交叉编译

```bash
cd Tengine-Lite
mkdir build 
cd build
cmake -DCMAKE_TOOLCHAIN_FILE=../toolchains/aarch64-linux-gnu.toolchain.cmake ..
make
make install
```

Arm32 Linux 交叉编译

```bash
cd Tengine-Lite
mkdir build 
cd build
cmake -DCMAKE_TOOLCHAIN_FILE=../toolchains/arm-linux-gnueabihf.toolchain.cmake ..
make
make install
```

编译完成后会生成 `libtengine-lite.so` 文件，并且会把相关的头文件、`libtengine-lite.so` 文件和相关的测试程序复制到 `build/install` 目录中。
