## Build on Windows* Systems

The software was validated on:
- Microsoft\* Windows\* 10 (64-bit) with Visual Studio 2017 and Intel® C++
  Compiler 2018 Update 3

### Software Requirements
- [CMake]\*3.11 or higher
- Microsoft\* Visual Studio 2017, 2019 or [Intel® C++ Compiler] 18.0
- (Optional) Intel® Graphics Driver for Windows* (26.20) [driver package].
- Python 3.4 or higher for Inference Engine Python API wrapper

### Build Steps

1. Clone submodules:
    ```sh
    git submodule update --init --recursive
    ```
2. By default, the build enables the Inference Engine GPU plugin to infer models
   on your Intel® Processor Graphics. This requires you to [download and install
   the Intel® Graphics Driver for Windows (26.20) [driver package] before
   running the build. If you don't want to use the GPU plugin, use the
   `-DENABLE_CLDNN=OFF` CMake build option and skip the installation of the
   Intel® Graphics Driver.    
3. Create build directory:
    ```sh
    mkdir build
    ```
4. In the `build` directory, run `cmake` to fetch project dependencies and
   generate a Visual Studio solution.

   For Microsoft\* Visual Studio 2017:
```sh
cmake -G "Visual Studio 15 2017 Win64" -DCMAKE_BUILD_TYPE=Release ..
```

   For Microsoft\* Visual Studio 2019:
```sh
cmake -G "Visual Studio 16 2019" -A x64 -DCMAKE_BUILD_TYPE=Release ..
```

   For Intel® C++ Compiler 18:
```sh
cmake -G "Visual Studio 15 2017 Win64" -T "Intel C++ Compiler 18.0" ^
    -DCMAKE_BUILD_TYPE=Release ^
    -DICCLIB="C:\Program Files (x86)\IntelSWTools\compilers_and_libraries_2018\windows\compiler\lib" ..
```

5. Build generated solution in Visual Studio or run
   `cmake --build . --config Release` to build from the command line.

6. Before running the samples, add paths to the TBB and OpenCV binaries used for
   the build to the `%PATH%` environment variable. By default, TBB binaries are
   downloaded by the CMake-based script to the `<dldt_repo>/inference-engine/temp/tbb/bin`
   folder, OpenCV binaries to the `<dldt_repo>/inference-engine/temp/opencv_4.3.0/opencv/bin`
   folder.

### Additional Build Options

- Internal JIT GEMM implementation is used by default.

- To switch to OpenBLAS GEMM implementation, use the `-DGEMM=OPENBLAS` CMake
  option and specify path to OpenBLAS using the `-DBLAS_INCLUDE_DIRS=<OPENBLAS_DIR>\include`
  and `-DBLAS_LIBRARIES=<OPENBLAS_DIR>\lib\libopenblas.dll.a` options. Download
  a prebuilt OpenBLAS\* package via the [OpenBLAS] link. mingw64* runtime
  dependencies can be downloaded via the [mingw64\* runtime dependencies] link.

- To switch to the optimized MKL-ML\* GEMM implementation, use the
  `-DGEMM=MKL` and `-DMKLROOT=<path_to_MKL>` CMake options to specify a path to
  unpacked MKL-ML with the `include` and `lib` folders. MKL-ML\* package can be
  downloaded from the Intel&reg; [MKL-DNN repository for Windows].

- Threading Building Blocks (TBB) is used by default. To build the Inference
  Engine with OpenMP* threading, set the `-DTHREADING=OMP` option.

- Required versions of TBB and OpenCV packages are downloaded automatically by
  the CMake-based script. If you want to use the automatically-downloaded
  packages but you already have installed TBB or OpenCV packages configured in
  your environment, you may need to clean the `TBBROOT` and `OpenCV_DIR`
  environment variables before running the `cmake` command; otherwise they won't
  be downloaded and the build may fail if incompatible versions were installed.

- If the CMake-based build script can not find and download the OpenCV package
  that is supported on your platform, or if you want to use a custom build of
  the OpenCV library, refer to the [Use Custom OpenCV Builds](#use-custom-opencv-builds-for-inference-engine)
  section for details.

- To switch off/on the CPU and GPU plugins, use the `cmake` options
  `-DENABLE_MKL_DNN=ON/OFF` and `-DENABLE_CLDNN=ON/OFF` respectively.

- To build the Python API wrapper, use the `-DENABLE_PYTHON=ON` option. To
  specify an exact Python version, use the following options:
   ```sh
   -DPYTHON_EXECUTABLE="C:\Program Files\Python37\python.exe" ^
   -DPYTHON_LIBRARY="C:\Program Files\Python37\libs\python37.lib" ^
   -DPYTHON_INCLUDE_DIR="C:\Program Files\Python37\include"
   ```

- nGraph-specific compilation options:
  `-DNGRAPH_ONNX_IMPORT_ENABLE=ON` enables the building of the nGraph ONNX importer.
  `-DNGRAPH_JSON_ENABLE=ON` enables nGraph JSON-based serialization.
  `-DNGRAPH_DEBUG_ENABLE=ON` enables additional debug prints.

### Building Inference Engine with Ninja* Build System

```sh
call "C:\Program Files (x86)\IntelSWTools\compilers_and_libraries_2018\windows\bin\ipsxe-comp-vars.bat" intel64 vs2017
set CXX=icl
set CC=icl
:: clean TBBROOT value set by ipsxe-comp-vars.bat, required TBB package will be downloaded by dldt cmake script
set TBBROOT=
cmake -G Ninja -Wno-dev -DCMAKE_BUILD_TYPE=Release ..
cmake --build . --config Release
```