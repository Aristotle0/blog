title: netcdf-fortran以及netcdf-python安装
date: 2015-01-16 13:24:57
categories: 
tags: netcdf
---

官方指导文档：
[Building the NetCDF-4.2 and later Fortran libraries](http://www.unidata.ucar.edu/software/netcdf/docs/building_netcdf_fortran.html)

从4.2版本之后，Fortran库和C库要分开安装，而且要想安装Fortran的库必须先把C的库安好。安装NetCDF-C必须先安装zlib和hdf5，所以需要下载四个文件：
[**zlib**](http://www.zlib.net/)、
[**hdf5**](http://www.hdfgroup.org/HDF5/release/obtainsrc.html)、
[**NetCDF-C**](http://www.unidata.ucar.edu/downloads/netcdf/index.jsp)和[**NetCDF-Fortran**](http://www.unidata.ucar.edu/downloads/netcdf/index.jsp)。

<!--more-->

## 安装NetCDF库
假设安装目录为`/public/home/ed/soft/netcdf`
```bash
$ export DIR1="/public/home/ed/soft/netcdf"
```

### 安装zlib

```bash
$ ./configure --prefix=${DIR1}
$ make
$ make check
$ make install
```

### 安装HDF5

```bash
$./configure --with-zlib=${DIR1} --prefix=${DIR1}
$ make
$ make check
$ make install
```

### 安装NetCDF-C和NetCDF-Fortran

设置编译选项
```bash
export F77=ifort
export FC=ifort
export F90=ifort
export FFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
```
如果不想用`gcc`，而改用intel的编译器，则需要添加
```bash
export CC=icc
export CXX=icpc
export CFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CXXFLAGS='-O3 -xHost -ip -no-prec-div -static-intel'
export CPP='icc -E'
export CXXCPP='icpc -E'
```
对于C库
```bash
$ CPPFLAGS=-I${DIR1}/include LDFLAGS=-L${DIR1}/lib ./configure --prefix=${DIR1} --enable-netcdf-4 --disable-dap
$ make
$ make check
$ make install
```
对于Fortran库

首先依然如上设置编译选项（如果未退出终端，无须设置），此外需要添加新的编译选项
```bash
export LD_LIBRARY_PATH=${DIR1}/lib:${LD_LIBRARY_PATH}
```
然后编译过程如同C库
```bash
$ CPPFLAGS=-I${DIR1}/include LDFLAGS=-L${DIR1}/lib ./configure --prefix=${DIR1}
$ make
$ make check
$ make install
```

### 添加路径
在`~/.bashrc`添加如下几行
```bash
export PATH=/public/home/ed/soft/netcdf/bin:$PATH
export LD_LIBRARY_PATH=/public/home/ed/soft/netcdf/lib:$LD_LIBRARY_PATH
```
然后更新
```bash
$ source ~/.bashrc
```

## 安装NetCDF-Python

先下载Python整合包[Anaconda](https://store.continuum.io/cshop/anaconda/)，里面已经包括常见的工具包，但并没有[`netCDF4`](https://github.com/Unidata/netcdf4-python)，所以需要自己下载。因为`netCDF4`需要`HDF5`和`NetCDF4`，所以NetCDF库的安装不能忽略`netcdf-4`。
假设`Anaconda`的安装路径为`/public/home/ed/soft/anaconda3`。

首先，需要指定`Python`和`NetCDF`库的位置，可以在`~/.bashrc`中继续添加（接上面的添加）
```bash
export PATH=/public/home/ed/soft/anaconda3/bin:$PATH
export LD_LIBRARY_PATH=/public/home/ed/soft/anaconda3/lib:$LD_LIBRARY_PATH
```
更新
```bash
$ source ~/.bashrc
```
进入安装目录，把`setup.cfg.template`复制为`setup.cfg`，修改配置文件（文件内有提示）。

编译安装
```bash
$ python setup.py build
$ python setup.py install
```
可以查看是否安装成功
```python
> import netCDF4
```
或者，进入`test`文件夹中，执行
```bash
$ python run_all.py
```
