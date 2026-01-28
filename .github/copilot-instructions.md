# Filament Copilot Instructions

## Repository Overview

**Filament** is a real-time physically based rendering (PBR) engine for Android, iOS, Linux, macOS, Windows, and WebGL. This is a large C++ project (~500+ files) that builds cross-platform graphics rendering libraries and command-line tools.

**Key Technologies:** C++17, CMake 3.22.1+, Clang 16+, Vulkan, Metal, OpenGL/ES, WebGL
**Build System:** CMake + Ninja
**Primary Language:** C++ (with Java/Kotlin for Android, Swift for iOS, JavaScript for Web)

## Critical Build Requirements

### Compiler Setup (Linux/macOS)
**ALWAYS use Clang, never GCC.** The project explicitly rejects GCC compilation.

**Linux:**
```bash
# REQUIRED: Set these environment variables before ANY build command
export CC=clang
export CXX=clang++
export CXXFLAGS=-stdlib=libc++

# Install dependencies (Ubuntu 24.04):
sudo apt install clang-16 libglu1-mesa-dev libc++-16-dev libc++abi-16-dev \
  ninja-build libxi-dev libxcomposite-dev libxxf86vm-dev
```

**Build failure without these will show:** "Detected CXX compiler GNU is unsupported" or linker errors for `-lc++`.

### Build Tool Versions
- **CMake:** 3.22.1 or newer (required)
- **Clang:** 16+ (16 for CI, 18 works locally)
- **Ninja:** 1.10+ (recommended) or Make
- **Python:** 3.7+ (for build scripts)
- **Android NDK:** 25.1+ minimum, 29.0.14206865 used in CI (for Android builds)
- **Emscripten:** 3.1.60 (for WebGL builds)
- **Java:** 17 (for Android builds)

## Building Filament

### Easy Build Script (Recommended)
The `build.sh` script at the repository root handles all platforms and build types. Always use it for consistent builds.

**Basic Usage:**
```bash
# Debug build (output: out/cmake-debug/)
./build.sh debug

# Release build (output: out/cmake-release/)
./build.sh release

# Both debug and release
./build.sh debug release

# Clean build (use when switching compilers or after build errors)
./build.sh -c debug

# Install to out/debug/ and out/release/
./build.sh -i debug release

# Build specific target (e.g., matc material compiler)
./build.sh debug matc
```

**Build Times:** Initial clean debug build takes ~5-8 minutes on 16-core machine. Incremental builds are much faster (~30s-2min).

### Platform-Specific Builds

**Android:**
```bash
# Set ANDROID_HOME first
export ANDROID_HOME=/path/to/android/sdk

# Build for ARM 64-bit (most common)
./build.sh -p android -q arm64-v8a release

# Build for all ABIs (arm64-v8a, armeabi-v7a, x86_64, x86)
./build.sh -p android release

# Build sample APK (requires host tools first)
./build.sh -p android -q arm64-v8a -k sample-hello-triangle release
```
**Important:** Android builds require host tools to be built first. The build.sh script handles this automatically when using `-p android`.

**iOS:**
```bash
# iOS device build
./build.sh -p ios release

# iOS + simulator universal build
./build.sh -p ios -s release
```

**WebGL:**
```bash
# Set EMSDK first
export EMSDK=/path/to/emsdk
source $EMSDK/emsdk_env.sh

./build.sh -p webgl release
```

### Build Artifacts
- **Libraries:** `out/cmake-{debug|release}/filament/` and `out/cmake-{debug|release}/libs/`
- **Tools:** `out/cmake-{debug|release}/tools/` (matc, cmgen, filamesh, resgen, etc.)
- **Samples:** `out/cmake-{debug|release}/samples/`

## Testing

### Unit Tests
```bash
# Build and run all tests
./build.sh -u

# Run specific test manually
./out/cmake-release/filament/test/test_material_parser
```

### CI Test Commands (from .github/workflows/presubmit.yml)
```bash
# Linux presubmit build
cd build/linux && printf "y" | ./build.sh presubmit

# Run material parser test (commonly checked)
./out/cmake-release/filament/test/test_material_parser
```

## Project Structure

### Key Directories
- **`filament/`** - Core rendering engine (minimal dependencies)
  - `filament/backend/` - Rendering backends (Vulkan, Metal, OpenGL/ES)
  - `filament/include/` - Public API headers
  - `filament/src/` - Implementation
- **`libs/`** - Supporting libraries (filamat, gltfio, utils, math, etc.)
- **`tools/`** - Host tools:
  - `matc` - Material compiler (required for materials)
  - `cmgen` - IBL environment map generator
  - `filamesh` - Mesh converter
  - `resgen` - Resource aggregator
- **`android/`** - Android libraries and samples
- **`ios/`** - iOS samples
- **`web/`** - JavaScript bindings and samples
- **`samples/`** - Desktop sample applications
- **`shaders/`** - Shader source code
- **`build/`** - Build scripts and toolchains
- **`third_party/`** - External dependencies

### Configuration Files
- **`CMakeLists.txt`** - Root build configuration
- **`.clang-format`** - Code formatting rules
- **`CODE_STYLE.md`** - Detailed style guide
- **`build/common/versions`** - CI dependency versions

## GitHub Actions Workflows

The repository has extensive CI that runs on every PR (11 jobs):

1. **build-desktop-linux** - Linux desktop build + test_material_parser
2. **build-desktop-mac** - macOS desktop build + tests
3. **build-windows** - Windows build
4. **build-android** - Android ARM64 build
5. **build-ios** - iOS build + samples
6. **build-web** - WebGL build
7. **validate-docs** - Documentation validation
8. **test-renderdiff** - Rendering comparison tests
9. **validate-wgsl-webgpu** - WebGPU WGSL shader validation
10. **test-code-correctness** - clang-tidy checks
11. **test-backend** - Backend-specific tests

**Key CI Script:** Each platform has a `build/{platform}/build.sh` script that CI calls with the `presubmit` target.

## Common Build Issues & Workarounds

### Issue: "Detected CXX compiler GNU is unsupported"
**Solution:** Set CC=clang, CXX=clang++, and CXXFLAGS=-stdlib=libc++ environment variables before building.

### Issue: Linker cannot find `-lc++`
**Solution:** Install libc++-dev and libc++abi-dev for your clang version (e.g., libc++-18-dev).

### Issue: Build artifacts in broken state after failed build
**Solution:** Use `./build.sh -c` flag to force clean build, or `rm -rf out/cmake-{debug|release}`.

### Issue: Android build fails with "ANDROID_HOME not set"
**Solution:** `export ANDROID_HOME=/path/to/Android/Sdk` or create `android/local.properties` with `sdk.dir=/path/to/Android/Sdk`.

### Issue: WebGL build fails to find Emscripten
**Solution:** Install Emscripten 3.1.60 and set `export EMSDK=/path/to/emsdk` before building.

## Code Style Guidelines

- **Formatting:** 4-space indent, 100 columns, braces at end of line
- **Naming:**
  - Classes/methods: lower camelcase
  - Constants: UPPER_CASE
  - Private members: prefix with `m` (e.g., `mAttributeName`)
  - Static members: prefix with `s`
  - Global vars: prefix with `g`
- **Headers:**
  - Use `.h` for headers, `.cpp` for implementation
  - Always include class header first in .cpp files
  - STL heavily restricted in public headers (see CODE_STYLE.md)
  - Never use `iostream` anywhere
- **Files:**
  - Public headers in `include/`, source in `src/`, tests in `test/`
  - Fully qualified includes: `#include <filament/Engine.h>`

## Material Compiler (matc)

Materials must be compiled with `matc` before use. This is a critical tool:

```bash
# Compile a material
./out/cmake-release/tools/matc/matc -o output.mat input.mat

# The matc version MUST match the runtime library version
```

## Environment Preparation for IBL

```bash
# Generate IBL from HDR/EXR environment map
./out/cmake-release/tools/cmgen/cmgen -f ktx -x ./ibls/ environment.exr

# Result: ./ibls/environment/ with pre-filtered cubemaps
```

## Important Notes for Agents

1. **Always use the build.sh script at the root** - it handles toolchain setup, dependencies, and cross-compilation properly.

2. **Clean builds are cheap insurance** - if encountering unexpected errors, use `./build.sh -c` to ensure clean state.

3. **Builds are incremental** - after the initial build, most changes result in fast rebuilds (~30s-2min vs 5-8min).

4. **Cross-platform considerations:** Desktop build produces host tools needed for mobile builds. Android/iOS builds automatically trigger partial desktop builds for tools.

5. **Test frequently during development** - run `./out/cmake-{debug|release}/filament/test/test_material_parser` to validate core functionality.

6. **Respect the compiler requirement** - GCC will never work. Always configure for Clang before any build attempts.

7. **When adding dependencies** - The project philosophy is minimal dependencies. Core runtime only uses STL (limited), robin-map (header-only), and Vulkan-specific deps (vkmemalloc, smol-v).

## Trust These Instructions

These instructions are based on actual build testing and official documentation. Only search for additional information if you encounter an error not covered here or if the instructions fail.
