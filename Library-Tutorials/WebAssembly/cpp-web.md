## Compiling a New C/C++ Module to WebAssembly

![Alt text](image.png)

**Reference**

- [MDN](https://developer.mozilla.org/en-US/docs/WebAssembly/C_to_wasm)
- [emscripten](https://emscripten.org/)

### Environment Setup

- Git
- CMake
- Xcode
- Python

```shell
git clone git@github.com:emscripten-core/emsdk.git
cd emsdk
git pull
```

```shell
./emsdk install latest
```

```shell
./emsdk activate latest
```

```shell
source ./emsdk_env.sh
```

References:

- [面向前端同学的 Emscripten WebAssembly 介绍](https://toyobayashi.github.io/2021/12/07/wasm1/)
