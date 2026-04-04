# SDL_shadercross バイナリリリース

このリポジトリは [libsdl-org/SDL_shadercross](https://github.com/libsdl-org/SDL_shadercross) のフォークであり、バイナリリリースの自動生成パイプラインとして運用しています。

## ブランチ構成

| ブランチ | 役割 |
|---|---|
| `automation` (デフォルト) | GitHub Actions ワークフローとこの README のみ |
| `main` | upstream の完全ミラー（fast-forward 同期、マージコミットなし） |

## 仕組み

1. 毎日スケジュール実行で upstream の新規コミットをチェック
2. 新規コミットがあれば `main` を fast-forward で upstream に追従
3. 各プラットフォーム向けリリースバイナリをビルド
4. upstream のコミット SHA をタグとして GitHub Release を作成

## 対応プラットフォーム

| プラットフォーム | アセットファイル名 |
|---|---|
| Windows x64 (MSVC) | `shadercross-windows-x64.zip` |
| macOS Universal (arm64 + x86_64) | `shadercross-macos.tar.gz` |
| Linux x64 (Ubuntu 24.04) | `shadercross-linux-x64.tar.gz` |

## CMake FetchContent での使い方

```cmake
include(FetchContent)

set(SHADERCROSS_VERSION "<upstream のコミット SHA>")

if(WIN32)
    set(_SC_URL "https://github.com/wamsoft/SDL_shadercross/releases/download/${SHADERCROSS_VERSION}/shadercross-windows-x64.zip")
elseif(APPLE)
    set(_SC_URL "https://github.com/wamsoft/SDL_shadercross/releases/download/${SHADERCROSS_VERSION}/shadercross-macos.tar.gz")
else()
    set(_SC_URL "https://github.com/wamsoft/SDL_shadercross/releases/download/${SHADERCROSS_VERSION}/shadercross-linux-x64.tar.gz")
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

または、同梱の `cmake/FindShadercrossBinary.cmake` モジュールを使用:

```cmake
set(SHADERCROSS_VERSION "<upstream のコミット SHA>")
include(cmake/FindShadercrossBinary.cmake)
# SHADERCROSS_EXECUTABLE が使用可能になります
```

## リリース URL パターン

```
https://github.com/wamsoft/SDL_shadercross/releases/download/upstream-<sha>/shadercross-<platform>.<ext>
```

## 本家リポジトリ

https://github.com/libsdl-org/SDL_shadercross
