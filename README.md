# Use-GPU-with-Python
## PyCUDA和numba的安装**
## 1.Prerequisites
1.LLVM(GCC>=4.8.0)
2.cmake >=3.4.3
### 2.LLVM安装
我使用的版本是LLVM4.0.0，LLVM的安装需要GCC>=4.8.0(关于GCC的升级见[这里](https://github.com/KanDdy/How-to-confugure-TensorFlow-on-Redhat-6.6)).
需要如下文件([下载地址](http://llvm.org/releases/download.html#4.0.0)):
```
LLVM source code

Clang source code

Clang Tools Extra source code

Compiler RT source code

LibC++ source code
```
下载完成之后，将5个包都解压出来，得到:
```
llvm-3.5.src

cfe-3.5.src

clang-tools-extra-3.5.src

compiler-rt-3.5.src

libcxx-3.5.src
```
然后按下面的步骤组织:
```
mv cfe-4.0.0.src clang
mv clang/ llvm-4.0.0.src/tools/

mv clang-tools-extra-4.0.0.src extra
mv extra/ llvm-4.0.0.src/tools/clang/

mv compiler-rt-4.0.0.src compiler-rt
mv compiler-rt llvm-4.0.0.src/projects/
```
这样以后clang，clang-tools-extra和compiler-rt就可以和llvm一起编译了。
在llvm-4.0.0.src同一层目录上新建个目录build-4.0并进入:
```
mkdir build-4.0 && cd build-4.0
```
随后直接使用cmake编译整个LLVM文件夹:
```
cmake ../llvm-4.0.0.src
```
随后直接make和make install
```
make -j24 && make install
```
随后查看llvm安装是否完成
```
clang --version
clang version 4.0.0 (tags/RELEASE_400/final)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /usr/local/bin
```
### 3.PyCUDA安装
直接按照[官方文档](https://wiki.tiker.net/PyCuda/Installation/Linux)即可。
### 4.numba安装
#### 4.1.llvmlite安装
```
$ git clone https://github.com/numba/llvmlite
$ cd llvmlite
$ python setup.py install
```
随后
```
$ git clone https://github.com/numba/numba.git
$ cd numba
$ pip install -r requirements.txt
$ python setup.py build_ext --inplace
$ python setup.py install
```
最后，为了我们能够使numba支持CUDA(CUDA安装见[这里](https://github.com/KanDdy/How-to-confugure-TensorFlow-on-Redhat-6.6))，我们需要设置三个环境变量：NUMBAPRO_CUDA_DRIVER,NUMBAPRO_NVVM和NUMBAPRO_LIBDEVICE，他们的官方说明如下:

**NUMBAPRO_CUDA_DRIVER**
Path to the CUDA driver shared library
**NUMBAPRO_NVVM**
Path to the CUDA libNVVM shared library file
**NUMBAPRO_LIBDEVICE**
Path to the CUDA libNVVM libdevice directory which contains .bc files
如果CUDA是默认安装(`/usr/local/cuda`),则在`/etc/profile`最后加上:

```
export NUMBAPRO_NVVM=/usr/local/cuda-8.0/nvvm/lib64:$NUMBAPRO_NVVM
export NUMBAPRO_LIBDEVICE=/usr/local/cuda-8.0/nvvm/libdevice:$NUMBAPRO_LIBDEVICE
```
关于`NUMBAPRO_CUDA_DRIVER`其实就是cuBLAS,cuRAND这些库文件的路径，一般为(`/usr/local/cuda-8.0/lib64`).
最后,验证numba是否正确安装:
```
$ python
Python 3.4.1 (default, Feb 13 2017, 14:28:09)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-11)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import numba
>>> numba.__version__
'0.32.0'
>>>
```
或者
```
$ pycc --help
usage: pycc [-h] [-o OUTPUT] [-c | --llvm] [--header] [--python] [-d]
            inputs [inputs ...]

DEPRECATED - Compile Python modules to a single shared library

positional arguments:
  inputs       Input file(s)

optional arguments:
  -h, --help   show this help message and exit
  -o OUTPUT    Output file (default is name of first input -- with new ending)
  -c           Create object file from each input instead of shared-library
  --llvm       Emit llvm instead of native code
  --header     Emit C header file with function signatures
  --python     Emit additionally generated Python wrapper and extension module
               code in output
  -d, --debug  Print extra debug information
```
