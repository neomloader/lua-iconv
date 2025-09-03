# Use a reasonably modern CMake version for better dependency handling.
cmake_minimum_required(VERSION 3.14)
project(luaiconv C)

option(LUAICONV_BUILD_SHARED "Build iconv as a shared library (e.g., iconv.so)" OFF)

if(LUAICONV_BUILD_SHARED)
    add_library(iconv SHARED luaiconv.c)
    set_target_properties(iconv PROPERTIES
        # Produce iconv.so (no lib prefix) for standard Lua module loading.
        PREFIX ""
        OUTPUT_NAME "iconv"
        SUFFIX ".so"
    )
else()
    add_library(iconv STATIC luaiconv.c)
endif()

target_include_directories(iconv PRIVATE ${LUAJIT_INCLUDE_DIR})

set_target_properties(iconv PROPERTIES
    POSITION_INDEPENDENT_CODE ON
)
target_compile_options(iconv PRIVATE -O2)

target_compile_definitions(iconv PRIVATE LUA_VERSION_NUM=501)

add_library(luaiconv::iconv ALIAS iconv)