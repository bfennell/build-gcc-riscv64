# build-gcc-riscv64
Build GCC/newlib for riscv32/64

    #!/bin/sh

    export INSTALL_BASE_PATH_GCC=$HOME/tmp/opt/toolchains/gcc-9.2.0-riscv64

    export PATH=$PATH:$INSTALL_BASE_PATH_GCC/bin

    mkdir -p $INSTALL_BASE_PATH_GCC

    mkdir -p $HOME/tmp/cross-gcc-build
    cd $HOME/tmp/cross-gcc-build

    wget -nc https://ftp.gnu.org/gnu/binutils/binutils-2.32.tar.xz
    wget -nc https://ftp.gnu.org/gnu/gcc/gcc-9.2.0/gcc-9.2.0.tar.gz
    #wget -nc ftp://sourceware.org/pub/newlib/newlib-2.5.0.tar.gz
    git clone https://github.com/riscv/riscv-newlib.git

    tar xJvf binutils-2.32.tar.xz
    tar xzvf gcc-9.2.0.tar.gz
    #tar xzvf newlib-2.5.0.tar.gz

    cd gcc-9.2.0
    ./contrib/download_prerequisites
    cd ..

    mkdir -p build-binutils
    cd build-binutils
    ../binutils-2.32/configure --prefix=$INSTALL_BASE_PATH_GCC --target=riscv64-unknown-elf --enable-multilib --disable-shared --disable-threads
    make -j8
    make install
    cd ..

    mkdir -p build-gcc
    cd build-gcc
    ../gcc-9.2.0/configure --prefix=$INSTALL_BASE_PATH_GCC --target=riscv64-unknown-elf --enable-languages=c,c++ --enable-multilib --disable-shared --disable-threads --with-newlib
    make -j8 all-gcc
    make install-gcc
    cd ..

    mkdir -p build-newlib
    cd build-newlib
    ../riscv-newlib/configure --prefix=$INSTALL_BASE_PATH_GCC --target=riscv64-unknown-elf --enable-multilib --disable-shared --disable-threads --disable-newlib-supplied-syscalls
    make -j8
    make install
    cd ..

    cd build-gcc
    make -j8 all
    make install
    cd ..
