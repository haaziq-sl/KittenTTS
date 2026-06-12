# Native Engine Release

KittenTTS is distributed as a pure Python `py3-none-any` wheel, but runtime
audio generation uses the native `kitten-inference` package. That package
contains the compiled `model_inference` extension and is built in the sibling
`cpp-nn-inference-haaziq` repository.

## Why There Are Multiple Wheels

The native extension is selected by wheel tags before package code runs. A
single normal wheel cannot cover Linux, Windows, macOS, Android, and every
CPython ABI at the same time.

The release shape is:

```text
one kitten-inference wheel per CPython ABI + OS + CPU architecture
```

For example:

```text
kitten_inference-0.1.0-cp38-cp38-manylinux_2_28_x86_64.whl
kitten_inference-0.1.0-cp314-cp314-manylinux_2_28_aarch64.whl
kitten_inference-0.1.0-cp314-cp314-win_amd64.whl
kitten_inference-0.1.0-cp314-cp314-win_arm64.whl
kitten_inference-0.1.0-cp313-cp313-android_24_arm64_v8a.whl
kitten_inference-0.1.0-cp314-cp314-macosx_11_0_arm64.whl
kitten_inference-0.1.0-cp314-cp314-macosx_11_0_x86_64.whl
```

`kittentts` can support Python 3.8+ once PyPI has matching
`kitten-inference` wheels for the user's platform.

## Automated Matrix

The native engine GitHub Actions workflow builds:

| Target | Python tags |
|---|---|
| Linux x86_64 CPU | `cp38` through `cp314` |
| Linux ARM64 / aarch64 CPU | `cp38` through `cp314` |
| Windows x86_64 CPU | `cp38` through `cp314` |
| Windows ARM64 CPU | `cp39` through `cp314` |
| macOS ARM64 CPU/Metal | `cp38` through `cp314` |
| macOS x86_64 CPU | `cp38` through `cp314` |
| Android ARM64 / Termux | `cp313` experimental |

Android starts at CPython 3.13 because those are the Android CPython tags
available in current cibuildwheel releases. It is currently non-blocking for
PyPI publishing while the Android CMake/Python development-header path is being
stabilized. Windows ARM64 starts at CPython 3.9 because current cibuildwheel
releases do not provide `cp38-win_arm64`.

ARM64 wheels are built for modern ARMv8.2 dot-product-capable cores. Older ARM
devices without dot-product support remain unvalidated.

## Release Checklist

1. Build and upload the `kitten-inference` native wheels first.
2. Confirm `pip install kitten-inference==0.1.0` works in fresh environments for
   each target Python/platform.
3. Build the `kittentts` pure Python wheel.
4. Install `kittentts` in fresh environments and run the package/native import
   smoke tests.
