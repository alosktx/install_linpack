### riscv64 ubuntu 源码安装 linpack
## 1.安装mpich
下载Download mpich-3.4.1.tar.gz at https://www.mpich.org/downloads

解压
```shell
$tar -xzvf mpich-3.4.1.tar.gz
```
确保已安装以下软件
```shell
$gcc --version
$g++ --version
$cmake --version
$gfortran --version
$f77 --version
```
新建一个用于安装的文件夹
```shell
$make /home/ubuntu/mpich
```
安装
```shell
$cd  /home/ubuntu/mpich-3.4.1
$export FCFLAGS=-fallow-argument-mismatch
$export FFLAGS=-fallow-argument-mismatch
$./configure -prefix=/home/ubuntu/mpich -build=riscv64
#显示 configure complete后
$make
$make install
```
添加环境变量
```shell
$vim  ~/.bashrc
#在 ~/.bashrc 中填入以下内容
export MPI_ROOT=/home/Desktop/mpich-install
export PATH=$MPI_ROOT/bin:$PATH
export MANPATH=$MPI_ROOT/man:$MANPATH
$source ~/.bashrc

```
测试
```shell
$mpichversion
#会显示以下内容
MPICH Version:          3.4.2
MPICH Release date:     Tue Nov 12 21:23:16 CST 2019
MPICH Device:           ch3:nemesis
MPICH configure:        --enable-cxx --with-romio --enable-shared --with-comm=shared --disable-devdebug --prefix=/cm/shared/apps/mpich/ge/gcc/64/3.3.2
MPICH CC:       gcc -std=gnu99 -m64   -O2
MPICH CXX:      g++ -I/cm/shared/apps/gcc/current/include/c++/4.8.5/backward/backward_old -m64  -O2
MPICH F77:      gfortran -m64  -O2
MPICH FC:       gfortran -m64  -O2
MPICH Custom Information: 
```
表示安装成功

## 2.安装openBLAS(可使用GotoBLAS)
下载安装包 https://www.openblas.net/ <br>
解压
```shell
$tar -zxvf OpenBLAS-0.3.21.tar.gz
```
安装
```shell
$cd OpenBLAS-0.3.21
$sudo make FC=gfortran #不要使用多核编译: sudo make FC=gfortran -j32
$sudo make prefix=/usr/local install
```
## 3.安装linpack
下载安装包 https://netlib.org/benchmark/hpl/ <br>
解压
```shell
$tar -zxvf  hpl-2.3.tar.gz #不要使用超户sudo解压，不然会影响后面make中创建软连接
```
修改配置文件
```shell
$cd hpl-2.3/setup
$cp Make.Linux_PII_FBLAS ../Make.Linux_PII_FBLAS #可以起其他名字
```
修改Make.Linux
```
ARCH = Linux_Ubuntu （此处与修改makefile文件名一致）
TOPdir = $(HOME)/下载/Linpack/hpl-2.1（hpl的目录,就是hpl解压后的目录）
MPdir = /usr/lib/mpich（通过 apt-get 安装的mpich目录）
MPlib = /usr/lib/libmpi.so $(MPdir)/lib/libmpich.a
LAdir = /usr/lib/openblas-base（通过 apt-get 安装的openblas目录）
LAlib = $(LAdir)/libblas.a $(LAdir)/libblas.so
CC = /usr/bin/gcc
LINKER = mpif77 #此处可修改成f77,但是在riscv64系统上出现找不到libbacktrace的问题

```
安装
```shell
$cd hpl-2.3
$make arch=Linux #与之前修改同名
```
安装成功后会在hpl-2.3/bin/Linux下生成 HPL.dat 和 xhpl

运行
```shell
mpirun -np 64 ./xhpl
```