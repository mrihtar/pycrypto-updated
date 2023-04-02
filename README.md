| :warning: WARNING                                                            |
|:-----------------------------------------------------------------------------|
| **PyCrypto 2.x is obsolete and contains security vulnerabilities.**<br>Please use the following alternatives: [Cryptography] or [PyCryptodome]      |

# Python Cryptography Toolkit (pycrypto)

This is an updated version of original [pycrypto] Python package (v2.6.1).
It runs and was tested under newest versions of Python (3.8, 3.9, 3.10, ...),
64-bit.

For original documentation on pycrypto see original [README] and/or visit
https://www.pycrypto.org

# Build

## Windows

Updated pycrypto can be compiled under Visual Studio 2019 or 2022.
Examples below are for VS 2019.

- clone Git repository<br>
  ```
  git clone --recurse-submodules https://github.com/mrihtar/pycrypto-updated
  cd pycrypto-updated
  ```

- define environment variables<br>
  (for vcvars_ver see version directory in `C:\Program Files\Microsoft Visual Studio\2019\BuildTools\VC\Tools\MSVC`)
  ```
  "C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\Common7\Tools\VsDevCmd.bat" -arch=amd64 -host_arch=amd64 -vcvars_ver=14.29
  set DISTUTILS_USE_SDK=1
  set CL=-FI"%VCINSTALLDIR%Tools\MSVC\14.29.30037\include\stdint.h"
  ```

- build MPIR library
  * `cd mpir\msvc\vs19`
  * fix `lib_mpir_gc\lib_mpir_gc.vcxproj`<br>
    Add VS 2019 include directory to 'Release|x64'
    ```XML
    <ClCompile>
      <AdditionalIncludeDirectories>..\..\..\;C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\VC\Tools\MSVC\14.29.30037\include</AdditionalIncludeDirectories>
      <PreprocessorDefinitions>NDEBUG;WIN32;_LIB;HAVE_CONFIG_H;_WIN64;%(PreprocessorDefinitions)</PreprocessorDefinitions>
    </ClCompile>
    ```
  * fix `lib_mpir_cxx\lib_mpir_cxx.vcxproj`<br>
    Add VS 2019 include directory to 'Release|x64'
    ```XML
    <ClCompile>
      <AdditionalIncludeDirectories>..\..\..\;C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\VC\Tools\MSVC\14.29.30037\include</AdditionalIncludeDirectories>
      <PreprocessorDefinitions>NDEBUG;WIN32;_LIB;HAVE_CONFIG_H;_WIN64;%(PreprocessorDefinitions)</PreprocessorDefinitions>
    </ClCompile>
    ```
  * build library<br>
    (last parameter for msbuild is Windows SDK version, see directory `C:\Program Files (x86)\Windows Kits\10\bin`)<br>
    `msbuild gc LIB x64 Release 10.0.19041.0`
  * msbuild produces final libraries and include files, which need to be copied
    to VS 2019 directories
    ```
    copy lib_mpir_gc\x64\Release\mpir.lib "C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\VC\Tools\MSVC\14.29.30037\lib\x64"
    copy lib_mpir_gc\x64\Release\mpir.h "C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\VC\Tools\MSVC\14.29.30037\include"
    ```
    ```
    copy lib_mpir_cxx\x64\Release\mpirxx.lib "C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\VC\Tools\MSVC\14.29.30037\lib\x64"
    copy lib_mpir_cxx\x64\Release\mpirxx.h "C:\Program Files (x86)\Microsoft Visual Studio\2019\BuildTools\VC\Tools\MSVC\14.29.30037\include"
    ```
- build pycrypto package
  * `cd pycrypto-2.6.1`
  * fix `src\inc-msvc\config.h`<br>
    ```
    /* Define to 1 if you have the declaration of `mpz_powm', and to 0 if you don't. */
    #define HAVE_DECL_MPZ_POWM 1

    /* Define to 1 if you have the declaration of `mpz_powm_sec', and to 0 if you don't. */
    #define HAVE_DECL_MPZ_POWM_SEC 0

    /* Define to 1 if you have the `gmp' library (-lgmp). */
    #undef HAVE_LIBGMP

    /* Define to 1 if you have the `mpir' library (-lmpir). */
    #define HAVE_LIBMPIR 1

    /* Define to 1 if you have the <stdint.h> header file. */
    #define HAVE_STDINT_H 1
    ```
  * `python3 setup.py build --compiler msvc`<br>
    (there will be some warnings about inconsistent dll linkage)
  * test newly compiled package<br>
    `python3 setup.py test`<br>
    1068 tests will be run and all should be successful.
  * create pycrypto wheel package<br>
    `pip3 wheel .`<br>
    This creates `pycrypto-2.6.1-cp3X-cp3X-win_amd64.whl` package, ready for
    installation with pip.

## Linux

Updated pycrypto was build and tested under python3 v3.10.6 64-bit on Ubuntu 22.04.

- clone Git repository<br>
  ```
  git clone --recurse-submodules https://github.com/mrihtar/pycrypto-updated
  cd pycrypto-updated
  ```

- prepare build environment
  ```
  sudo apt update
  sudo apt install build-essential checkinstall autoconf automake autotools-dev libtool m4
  sudo apt install python3-dev python3-pip
  ```

- build MPIR library
  * `cd mpir`
  * `./autogen.sh`<br>
    (this creates configure script)
  * `./configure`<br>
    (this creates Makefile)
  * `make`
  * `sudo make install`

- build pycrypto package
  * `cd pycrypto-2.6.1`
  * `python3 setup.py build`<br>
    (there will be some warnings about struct timespec)
  * test newly compiled package<br>
    `python3 setup.py test`<br>
    1066 tests will be run and all should be successful.
  * create pycrypto wheel package<br>
    `pip3 wheel .`<br>
    This creates `pycrypto-2.6.1-cp3X-cp3X-linux_x86_64.whl` package, ready for
    installation with pip.

[Cryptography]: https://cryptography.io
[PyCryptodome]: https://www.pycryptodome.org
[pycrypto]: https://www.pycrypto.org
[README]: pycrypto-2.6.1/README.md
