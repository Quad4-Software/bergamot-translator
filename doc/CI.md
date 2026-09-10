# Continuous Integration

Continuous integration is handled by [GitHub Actions](https://docs.github.com/en/actions). The workflows live in `.github/workflows/`.

## Workflows

| Workflow | Purpose |
| --- | --- |
| `build.yml` | Builds Python wheels and the WASM artifacts, attaches them to GitHub Releases, and attempts to publish the npm package and the Python wheel on tags. |
| `native.yml` | Native Linux and macOS builds plus the Bergamot regression test suite. |
| `windows.yml` | Native Windows x64 build. |
| `arm.yml` | Android ARM64 cross-compile using the Android NDK. |
| `coding-styles.yml` | `clang-format` and `clang-tidy` checks. |
| `codeql.yml` | Static analysis for C++, Python, and JavaScript/TypeScript. |

## Security notes

- All third-party actions are pinned to full commit SHAs.
- Jobs declare least-privilege `permissions:` blocks.
- PyPI publishing uses trusted publishing (`id-token: write`) instead of a stored token and only runs on version tags.
- npm publishing uses `--provenance` with `id-token: write` and only runs on version tags.
- GitHub Packages is not used. npm is published to `registry.npmjs.org` and Python wheels are published to PyPI.
- Tag-based publishing to npm and PyPI requires the corresponding token or trusted publisher setup. If it is not configured, the release still contains the built wheel and WASM artifacts.
- Releases are created with `softprops/action-gh-release`, pinned by SHA.
- Documentation deploys to GitHub Pages via `actions/deploy-pages`.

## Running locally

The WASM build can be reproduced locally with Docker using the same emsdk image that the workflow uses.

```bash
bash build-wasm.sh
```

Or via the package build script in `wasm/module/`:

```bash
npm run build
```

The native build follows the standard CMake flow.

```bash
mkdir build-native
cd build-native
cmake ..
make -j2
```
