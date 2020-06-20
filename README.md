# sse2neon

A C/C++ header file that converts Intel SSE intrinsics to Arm/Aarch64 NEON intrinsics.

## Info
The SIMD instruction set of Intel, which is known as SSE is used in many
applications for improved performance.  ARM also have introduced an SIMD
instruction set called Neon to their processors.
Rewriting code written for SSE to work on Neon is very time consuming. and
this is a header file that can automatically convert some of the SSE
intrinsics into NEON intrinsics.

## Usage

- Put the file `sse2neon.h` in to your source code directory.

- Locate the following SSE header files included in the code: 
```C
#include <xmmintrin.h>
#include <emmintrin.h>
```

- Replace them with : 
```C
#include "sse2neon.h"
```

- On ARMv7-A targets, you need to append the following compiler option:
```shell
-mfpu=neon
```

## Run Built-in Test Suite

`sse2neon` provides a unified interface test cases for developers. These test
cases are located in `tests` directory, and the input data is specified at
runtime. Use the following commands to perform test cases:
```shell
$ make check
```

You can specify GNU toolchain for cross compilation as well.
[QEMU](https://www.qemu.org/) should be installed in advance.
```shell
$ make CROSS_COMPILE=aarch64-linux-gnu- check # ARMv8-A
```
or
```shell
$ make CROSS_COMPILE=arm-linux-gnueabihf- check # ARMv7-A
```

### Add More Test Items
Once the conversion is implemented, the test can be added with the following steps:

* File `tests/impl.h`

  Add the intrinsic in `enum InstructionTest`. The naming convention should be `IT_MM_XXX`.

* File `tests/impl.cpp`

  * For the test name generation:

    Add the corresponding switch-case in `getInstructionTestString()`.
    ```c
    case IT_MM_XX:
        ret = "MM_XXX";
        break;
    ```

  * For running the test:

    Add the corresponding switch-case in `runSingleTest()`.
    ```c
    case IT_MM_XXX:
        ret = test_mm_xxx();
        break;
    ```

  * The test implementation:

    ```c
    bool test_mm_xxx()
    {
        // The C implementation
        ...

        // The Neon implementation
        ret = _mm_xxx();

        // Compare the result of two implementations and return it
        ...
    }
    ```

## Related Projects
* [SIMDe](https://github.com/nemequ/simde): fast and portable implementations of SIMD
  intrinsics on hardware which doesn't natively support them, such as calling SSE functions on ARM.
* [SSE2NEON.h : A porting guide and header file to convert SSE intrinsics to their ARM NEON equivalent](https://codesuppository.blogspot.com/2015/02/sse2neonh-porting-guide-and-header-file.html)
* [CatBoost's sse2neon](https://github.com/catboost/catboost/blob/master/library/cpp/sse/sse2neon.h)
* [ARM\_NEON\_2\_x86\_SSE](https://github.com/intel/ARM_NEON_2_x86_SSE)
* [SSE2NEON - High Performance MPC on ARM](https://github.com/rons1404/biu-cybercenter-proj-sse2neon)
* [AvxToNeon](https://github.com/kunpengcompute/AvxToNeon)

## Licensing

`sse2neon` is freely redistributable under the MIT License.
