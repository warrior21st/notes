# rocksdb 环境安装

### 安装依赖库
    // macos
    brew install gflags
    brew install snappy
    brew install lz4
    brew install zlib
    brew install bzip2
    brew install zstd

    // ubuntu
    sudo apt-get install libgflags-dev
    sudo apt-get install libsnappy-dev
    sudo apt-get install zlib1g-dev
    sudo apt-get install libbz2-dev
    sudo apt-get install liblz4-dev
    sudo apt-get install libzstd-dev

### 设置环境变量
    export CPLUS_INCLUDE_PATH=${CPLUS_INCLUDE_PATH}:/usr/local/include
    export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/lib
    export LIBRARY_PATH=${LIBRARY_PATH}:/usr/local/lib

### 安装rocksdb
    // macos & ubuntu
    wget https://github.com/facebook/rocksdb/archive/refs/tags/v7.8.3.tar.gz
    tar xvzf v7.8.3.tar.gz
    cd rocksdb-7.8.3/
    make shared_lib 
    ln -fs librocksdb.so.7.8.3 librocksdb.so.7.8
    ln -fs librocksdb.so.7.8.3 librocksdb.so.7
    ln -fs librocksdb.so.7.8.3 librocksdb.so
    sudo make install
    sudo ln -fs librocksdb.so.7.8.3 /usr/local/lib/librocksdb.so.7.8
    sudo ln -fs librocksdb.so.7.8.3 /usr/local/lib/librocksdb.so.7
    sudo ln -fs librocksdb.so.7.8.3 /usr/local/lib/librocksdb.so

### grocksdb
    import "github.com/linxGnu/grocksdb"

    CGO_CFLAGS="-I/usr/local/include/rocksdb/" \
    CGO_LDFLAGS="-L/usr/local/lib -lrocksdb -lstdc++ -lm -lz -lbz2 -lsnappy -llz4 -lzstd" \
    GOOS=linux GOARCH=amd64 CGO_ENABLED=1 go build -o ./build


### 参考
- https://github.com/facebook/rocksdb
- https://github.com/facebook/rocksdb/blob/main/INSTALL.md