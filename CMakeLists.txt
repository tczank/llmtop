cmake_minimum_required(VERSION 3.10)
project(llmtop CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Dependencies (vendored)
include(FetchContent)

FetchContent_Declare(json
  URL https://github.com/nlohmann/json/releases/download/v3.11.3/json.tar.xz
)

FetchContent_Declare(ftxui
  GIT_REPOSITORY https://github.com/ArthurSonzogni/FTXUI
  GIT_TAG        v5.0.0
  GIT_SHALLOW    TRUE
)

FetchContent_Declare(cpr
  GIT_REPOSITORY https://github.com/libcpr/cpr.git
  GIT_REVISION  v1.11.2
)

set(CPR_ENABLE_SSL OFF CACHE BOOL "" FORCE)
set(CURL_ZLIB OFF CACHE STRING "" FORCE)

FetchContent_MakeAvailable(json ftxui cpr)

find_package(Threads REQUIRED)

add_executable(llmtop
  src/main.cpp
  src/sources/llamacpp_source.cpp
  src/sources/nvml_source.cpp
  src/sources/rocmm_source.cpp
  src/sources/sysfs_source.cpp
  src/sources/ollama_source.cpp
  src/sources/demo_source.cpp
  src/ui/ui.cpp
  src/ui/footer.cpp
  src/ui/header.cpp
  src/ui/gpu_panel.cpp
  src/ui/slots_panel.cpp
  src/ui/throughput_panel.cpp
  src/ui/bigtext.cpp
)

target_compile_definitions(llmtop PRIVATE LLMTOP_VERSION="${PROJECT_VERSION}" USE_ROCMM)
target_include_directories(llmtop PRIVATE src)
target_compile_options(llmtop PRIVATE -Wall -Wextra)
target_compile_options(llmtop PRIVATE -DUSE_ROCMM)

target_link_libraries(llmtop PRIVATE
  ftxui::component  # transitively provides ftxui::dom and ftxui::screen
  cpr::cpr
  nlohmann_json::nlohmann_json
  Threads::Threads
  ${CMAKE_DL_LIBS}
)
