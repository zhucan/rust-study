## 														kvproto c++编译

1. 安装cargo&配置:  curl -sSf https://static.rust-lang.org/rustup.sh | sh

2. 安装protoc:

   ```shell
   $ wget https://github.com/protocolbuffers/protobuf/releases/download/v3.8.0/protobuf-cpp-3.8.0.tar.gz
   $ tar -zvxf protobuf-cpp-3.8.0.tar.gz
   $ cd protobuf-3.8.0
   $ ./autogen.sh
   $ ./configure
   $ make -j 16
   $ make install
   $ ln -s /usr/local/bin/protoc /usr/bin/protoc
   ```

3. 编译&安装cmake: 

   ```shell
   $ wget https://github.com/Kitware/CMake/releases/download/v3.22.1/cmake-22.1.tar.gz
   $ tar -zvxf cmake-3.22.1.tar.gz
   $ ./bootstrap -- -DCMAKE_USE_OPENSSL=OFF
   $ ./configure
   $ make -j 16
   $ make install
   $ ln -s /usr/local/bin/cmake /usr/bin/cmake
   ```

4. 安装golang

   ```shell
   $ wget https://github.com/golang/go/releases/tag/go1.17.6
   $ tar -C /usr/local -zxvf  go1.17.6.linux-amd64.tar.gz 
   添加/usr/local/go/bin目录到PATH变量中。添加到/etc/profile 或$HOME/.bash_profile都可以 
   ```

5. 编译&安装grpc

   ```shell
   $ yum install libunwind.x86_64 libunwind-devel.x86_64
   $ git clone https://github.com/grpc/grpc.git
   $ cd grpc  && git checkout v1.26.0  && git submodule update --init
   $ mkdir -p grpcbuild \
    && cd grpcbuild \
    && cmake .. -DgRPC_INSTALL=ON -DgRPC_BUILD_TESTS=OFF -DgRPC_PROTOBUF_PROVIDER=package -DgRPC_ZLIB_PROVIDER=package -DgRPC_CARES_PROVIDER=package -DgRPC_SSL_PROVIDER=package -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON && make install \
    && cd ..
    && make & make install
   ```

6. 升级gcc版本到7.x

   ```shell
   $ wget ftp://ftp.mirrorservice.org/sites/sourceware.org/pub/gcc/releases/gcc-7.2.0/gcc-7.2.0.tar.gz
   # tar -xvzf gcc-7.2.0.tar.gz
   $ cd gcc-7.2.0
   $ ./contrib/download_prerequisites
   $ ./configure \
       --enable-bootstrap \
       --enable-languages=c,c++,fortran,lto \
       --with-bugurl=http://bugzilla.redhat.com/bugzilla \
       --enable-shared \
       --enable-threads=posix \
       --enable-checking=release \
       --disable-multilib \
       --with-system-zlib \
       --enable-__cxa_atexit \
       --disable-libunwind-exceptions \
       --enable-gnu-unique-object \
       --enable-linker-build-id \
       --with-gcc-major-version-only \
       --enable-plugin \
       --with-linker-hash-style=gnu \
       --enable-initfini-array \
       --enable-libmpx \
       --enable-gnu-indirect-function \
       --with-tune=generic \
       --build=x86_64-redhat-linux
   $ make -j4
   $ sudo make install
   $ sudo sh -c 'echo /usr/local/lib > /etc/ld.so.conf.d/1-gcc.conf'
   $ sudo sh -c 'echo /usr/local/lib64 >> /etc/ld.so.conf.d/1-gcc.conf'
   $ sudo ldconfig -v
   ```

7. 编译kvproto

   ```shell
   $ mkdir -p kvprotobuild && cd kvprotobuild && cmake ../cpp -DCMAKE_C_COMPILER=/usr/local/bin/gcc -DCMAKE_CXX_COMPILER=/usr/local/bin/g++ && make
   
   指定更新后的gcc和g++的二进制文件路径
   ```

   

参考：https://stackoverflow.com/questions/47238577/target-requires-the-language-dialect-cxx17-with-compiler-extensions-but-cma