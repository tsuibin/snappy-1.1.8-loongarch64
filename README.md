# Snappy 1.1.8 on loongarch64

    cmake -B build -DCMAKE_INSTALL_PREFIX=$PREFIX -DCMAKE_BUILD_TYPE=Release \
          -DCMAKE_POSITION_INDEPENDENT_CODE=ON -DSNAPPY_BUILD_TESTS=OFF .
    cmake --build build -j4 && cmake --install build

零补丁。

## 头文件要放到 snappy/ 子目录

Doris 的 `util/block_compression.cpp` 用 `#include <snappy/snappy-sinksource.h>`，
而 cmake 默认把 `snappy.h` 装在 `include/` 根下。需要额外拷一份：

    mkdir -p $PREFIX/include/snappy
    cp snappy.h snappy-sinksource.h snappy-c.h $PREFIX/include/snappy/

（Arrow 的 `Snappy_SOURCE=SYSTEM` 也靠这个路径找。）
