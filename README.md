# Bergamot Translator

> Quad4-maintained fork of the upstream [browsermt/bergamot-translator](https://github.com/browsermt/bergamot-translator) project.

Bergamot translator provides a unified API for ([Marian NMT](https://marian-nmt.github.io/) framework based) neural machine translation functionality in accordance with the [Bergamot](https://browser.mt/) project that focuses on improving client-side machine translation in a web browser.

This fork exists to keep the package, build tooling, and npm distribution current for downstream projects that depend on Bergamot, including [MeshChatX](https://github.com/Quad4-Software/meshchatx).

## Packages

This repository does not publish to GitHub Packages.

CI builds these artifacts on every main branch push and every v*.*.* tag:

- Python wheels for the package quad4-bergamot.
- the npm package @quad4/bergamot-translator and its WASM worker files.

On a version tag the workflow also attempts to:

- publish the npm package to https://registry.npmjs.org using the NPM_TOKEN secret.
- publish the Python wheel to PyPI using trusted publishing.

Both publishing steps need an npm or PyPI account and token. Until those are set up, the wheels and WASM files are only available as GitHub Release assets.

## Status

- Engine and submodules are updated to the latest public commits in the browsermt org.
- GitHub Actions are pinned to known-good SHA hashes and run with least-privilege permissions.
- WASM builds use Emscripten `3.1.8` (the version the Marian/Bergamot source is known to build with). Newer Emscripten majors are not verified.
- This repository will be repointed to a Quad4 remote when the public fork is created.

## Build Instructions

### Build Natively

Create a folder where you want to build all the artifacts (`build-native` in this case) and compile.

```bash
mkdir build-native
cd build-native
cmake ../
make -j2
```

### Build WASM

#### Prerequisite

Building on WASM requires the Emscripten toolchain. It can be downloaded and installed using following instructions.

```bash
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install 3.1.8
./emsdk activate 3.1.8
source ./emsdk_env.sh
```

Or use the convenience script which does the same.

```bash
bash build-wasm.sh
```

#### Compile

To build a version that translates with higher speeds on Firefox Nightly browser, follow these instructions.

1. Create a folder where you want to build all the artifacts and compile.

   ```bash
   mkdir build-wasm
   cd build-wasm
   emcmake cmake -DCOMPILE_WASM=on ..
   emmake make -j2
   ```

   The WASM artifacts (`.js` and `.wasm` files) will be available in the build directory.

2. Patch generated artifacts to import the GEMM library from a separate WASM module.

   ```bash
   bash ../wasm/patch-artifacts-import-gemm-module.sh
   ```

To build a version that runs on all browsers (including Firefox Nightly) but translates slowly, follow the same steps with `-DWORMHOLE=off`.

```bash
emcmake cmake -DCOMPILE_WASM=on -DWORMHOLE=off ..
emmake make -j2
bash ../wasm/patch-artifacts-import-gemm-module.sh
```

#### Recompiling

As long as you do not update any submodule, just follow the compile steps.
If you update a submodule, run this first.

```bash
git submodule update --init --recursive
```

## How to use

### Using Native version

The builds generate a library that can be integrated into any project. All the public header files are specified in the `src` folder.
A short example of how to use the APIs is provided in `app/bergamot.cpp`.

### Using WASM version

See the `README` inside the `wasm` folder for how to use the translator in JavaScript.

## License

MPL-2.0. See [LICENSE](LICENSE).
