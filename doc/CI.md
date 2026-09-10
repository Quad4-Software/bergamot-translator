# Continuous Integration

Continuous integration is handled by [GitHub Actions](https://docs.github.com/en/actions). The workflows live in `.github/workflows/`.

## Workflows

| Workflow | Purpose |
| --- | --- |
| `build.yml` | Builds Python wheels and WASM artifacts. Runs on pushes to main, pull requests, and manual triggers. |
| `release.yml` | Triggered by v*.*.* tags and manual runs. Calls build.yml, creates a GitHub Release, and publishes the npm package to GitHub Packages. |
| `publish-github-packages.yml` | Publishes the npm package to GitHub Packages. Called by release.yml and can also be triggered manually. |
| `native.yml` | Native Linux and macOS builds plus the Bergamot regression test suite. |
| `windows.yml` | Native Windows x64 build. |
| `arm.yml` | Android ARM64 cross-compile using the Android NDK. |
| `coding-styles.yml` | `clang-format` and `clang-tidy` checks. |
| `codeql.yml` | Static analysis for C++, Python, and JavaScript/TypeScript. |

Every workflow supports manual runs from the Actions tab.

## Security notes

- All third-party actions are pinned to full commit SHAs.
- Jobs declare least-privilege `permissions:` blocks.
- PyPI publishing uses trusted publishing (`id-token: write`) instead of a stored token and only runs on version tags.
- npm publishing to the public registry uses `--provenance` with `id-token: write` and only runs on version tags. It needs the NPM_TOKEN secret.
- GitHub Packages publishing uses the built-in GITHUB_TOKEN and only runs on version tags. The package is published as `@quad4-software/bergamot-translator` because GitHub Packages requires the scope to match the repository owner.
- Tag-based publishing to npm, PyPI, and GitHub Packages needs the corresponding token or trusted publisher setup. If it is not configured, the release still contains the built wheel and WASM artifacts.
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
