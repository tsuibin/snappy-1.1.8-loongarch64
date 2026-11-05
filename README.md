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

## 更新（2026-11-04）

头文件放进 `include/snappy/` 的做法在最终 BE 编译中确认有效（`util/block_compression.cpp` 通过）。
brpc 也引用全局命名空间的 `snappy::GetUncompressedLength`，链接行须给 `-lsnappy`；最终 0 undefined symbol。
Arrow 的 Parquet snappy codec 实测压到 52%，未静默降级。
