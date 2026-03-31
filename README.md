# SDL_shadercross Binary Releases

> **[日本語版はこちら (README.ja.md)](README.ja.md)**

This repository is a fork of [libsdl-org/SDL_shadercross](https://github.com/libsdl-org/SDL_shadercross), used exclusively as an automated binary release pipeline.

## Branch Structure

| Branch | Purpose |
|---|---|
| `automation` (default) | Contains only the GitHub Actions workflow and this README |
| `main` | Exact mirror of upstream (fast-forward sync, no merge commits) |

## How It Works

1. A daily scheduled workflow checks upstream for new commits
2. If new commits are found, `main` is fast-forwarded to match upstream
3. Release binaries are built for all platforms
4. Binaries are published as a GitHub Release, tagged by the upstream commit SHA

## Available Platforms

| Platform | Asset Filename |
|---|---|
| Windows x64 (MSVC) | `shadercross-windows-x64.zip` |
| macOS Universal (arm64 + x86_64) | `shadercross-macos.tar.gz` |
| Linux x64 (Ubuntu 24.04) | `shadercross-linux-x64.tar.gz` |

## Usage with CMake FetchContent

```cmake
include(FetchContent)

set(SHADERCROSS_VERSION "<upstream-commit-sha>")

if(WIN32)
    set(_SC_URL "https://github.com/yukidaore/SDL_shadercross/releases/download/${SHADERCROSS_VERSION}/shadercross-windows-x64.zip")
elseif(APPLE)
    set(_SC_URL "https://github.com/yukidaore/SDL_shadercross/releases/download/${SHADERCROSS_VERSION}/shadercross-macos.tar.gz")
else()
    set(_SC_URL "https://github.com/yukidaore/SDL_shadercross/releases/download/${SHADERCROSS_VERSION}/shadercross-linux-x64.tar.gz")
endif()

FetchContent_Declare(shadercross_bin URL "${_SC_URL}" DOWNLOAD_EXTRACT_TIMESTAMP TRUE)
FetchContent_MakeAvailable(shadercross_bin)

find_program(SHADERCROSS_EXECUTABLE
    NAMES shadercross
    PATHS "${shadercross_bin_SOURCE_DIR}/bin"
    NO_DEFAULT_PATH
    REQUIRED
)
```

Alternatively, use the provided `cmake/FindShadercrossBinary.cmake` module for a simpler integration:

```cmake
set(SHADERCROSS_VERSION "<upstream-commit-sha>")
include(cmake/FindShadercrossBinary.cmake)
# SHADERCROSS_EXECUTABLE is now available
```

## Release URL Pattern

```
https://github.com/yukidaore/SDL_shadercross/releases/download/<upstream-sha>/shadercross-<platform>.<ext>
```

## Upstream

https://github.com/libsdl-org/SDL_shadercross
