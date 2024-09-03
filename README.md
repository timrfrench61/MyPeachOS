# MyPeachOS

build: 
make clean
make

run:
qemu-system-x86_64 -hda ./boot.bin

run in GDB linux :
gdb
target remote | qemu-system-x86_64 -hda ./boot.bin -S -gdb stdio
debug:
gdb
<load symbols>
break <breakpoint>
qemu-system-x86_64 -hda ./boot.bin -S -gdb stdio

run in windows :
cd bin
qemu-system-x86_64 -hda ./boot.bin -S -gdb tcp::1234
(open another command prompt)
gdb
(gdb) target remote localhost:1234
(gdb) c ;continue
(gdb) ctrl-c
(gdb) layout asm
...
debug session
...
(gdb) quit
 
setup cross compiler:
https://wiki.osdev.org/GCC_Cross-Compiler#Installing_Dependencies
MSYS2 pacman -S mingw-w64-i686-toolchain mingw-w64-x86_64-toolchain


pacman -S mingw-w64-x86_64-gmp mingw-w64-x86_64-mpc mingw-w64-x86_64-mpfr mingw-w64-x86_64-isl


pacman -Syu base-devel
pacman -Syu gmp
pacman -Syu mpc
pacman -Syu mpfr

#export GCC = "D:/_code/gcc"
export PREFIX="$HOME/opt/cross"
export TARGET=i686-elf
export PATH="$PREFIX/bin:$PATH"

Binutils

cd $HOME/src

mkdir build-binutils
cd build-binutils
../binutils-2.35.1/configure --target=$TARGET --prefix="$PREFIX" --with-sysroot --disable-nls --disable-werror
make
make install

GCC
cd $HOME/src
# must be done in  MSYS2 mingw64 or MSYS2 mingw32 shell
# The $PREFIX/bin dir _must_ be in the PATH. We did that above.
which -- $TARGET-as || echo $TARGET-as is not in the PATH

mkdir build-gcc
cd build-gcc
../gcc-10.2.0/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --enable-languages=c,c++ --without-headers
# FIX missing gmp, mpfr, mpc, and isl
pacman -Syu
pacman -S mingw-w64-x86_64-gmp mingw-w64-x86_64-mpfr mingw-w64-x86_64-mpc mingw-w64-x86_64-isl
# FIX for 32bit
pacman -Syu
pacman -S mingw-w64-i686-gmp mingw-w64-i686-mpfr mingw-w64-i686-mpc mingw-w64-i686-isl


make all-gcc
make all-target-libgcc
make install-gcc
make install-target-libgcc



build GMP:
wget https://gmplib.org/download/gmp/gmp-10.2.0tar.xz
tar -xvf gmp-6.3.0.tar.xz
cd gmp-6.3.0
#./configure --prefix=/path/to/installation
./configure --prefix=/usr/local

make
make install


check build:
../gcc-10.2.0/configure --target=i686-elf --prefix=/usr/local/cross --disable-nls --enable-languages=c,c++ --without-headers --with-gmp=c:/msys64/path/to/installation

build mpc:

../gcc-10.2.0/configure --target=i686-elf --prefix=/usr/local/cross --disable-nls --enable-languages=c,c++ --without-headers --with-gmp c:/msys64/var/cache/pacman/pkg --with-mpfr c:/msys64/var/cache/pacman/pkg --with-mpc c:/msys64/var/cache/pacman/pkg 


../gcc-10.2.0/configure --target=i686-elf --prefix=/usr/local/cross --disable-nls --enable-languages=c,c++ --without-headers --with-gmp C:/msys64/var/lib/pacman/local/mingw-w64-x86_64-gmp-6.3.0-2 --with-mpfr C:/msys64/var/lib/pacman/local/mingw-w64-x86_64-mpfr-4.2.1-2 --with-mpc C:/msys64/var/lib/pacman/local/mingw-w64-x86_64-mpc-1.3.1-2

binutil-2.42
../binutils-2.42/configure --target=$TARGET --prefix="$PREFIX" --with-sysroot --disable-nls --disable-werror
GCC-14.1.0
../gcc-14.1.0/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --enable-languages=c,c++ --without-headers

SWL
binutils-2.43
../binutils-2.43/configure --target=$TARGET --prefix="$PREFIX" --with-sysroot --disable-nls --disable-werror


gcc-14.1.0

# Navigate to the source directory
cd $HOME/src

# Download the GCC source code
wget https://ftp.gnu.org/gnu/gcc/gcc-14.1.0/gcc-14.1.0.tar.gz

# Extract the tarball
tar -xzf gcc-14.1.0.tar.gz

# Ensure the target assembler is in the PATH
which -- $TARGET-as || echo $TARGET-as is not in the PATH

# Create a build directory
mkdir build-gcc
cd build-gcc

# Configure the build
../gcc-14.1.0/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --enable-languages=c,c++ --without-headers --disable-bootstrap

# Build GCC
make all-gcc
make all-target-libgcc

# Install GCC
make install-gcc
make install-target-libgcc


# Download the GCC source code
wget https://ftp.gnu.org/gnu/binutils/binutils-2.35.tar.gz

# Extract the tarball
tar -xzf binutils-2.35.tar.gz

# Download the GCC source code
wget https://ftp.gnu.org/gnu/gcc/gcc-10.2.0/gcc-10.2.0.tar.gz

# Extract the tarball
tar -xzf gcc-10.2.0.tar.gz


# Configure the build
../binutils-2.35/configure --target=$TARGET --prefix="$PREFIX" --with-sysroot --disable-nls --disable-werror
make
make install





# Ensure the target assembler is in the PATH
which -- $TARGET-as || echo $TARGET-as is not in the PATH

# Create a build directory
mkdir build-gcc
cd build-gcc

# Configure the build
../gcc-10.2.0/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --enable-languages=c,c++ --without-headers --disable-bootstrap


# Build GCC
make all-gcc
make all-target-libgcc

# Install GCC
make install-gcc
make install-target-libgcc

# debug in gdb
gdb
add-symbol-file ../build/kernelfull.o 0x100000
#target remote | qemu-system-x86_64 -hda ./os.bin -gdb stdio -S
target remote | qemu-system-i386 -hda ./os.bin -gdb stdio -S
#break kernel_main
break fat16_resolve
#bt ; backtrace
#layout asm


gdb
add-symbol-file ../build/kernelfull.o 0x100000
target remote | qemu-system-i386 -hda ./os.bin -gdb stdio -S
break fat16_resolve
#layout asm