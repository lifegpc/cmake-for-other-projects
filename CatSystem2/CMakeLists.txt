cmake_minimum_required(VERSION 3.5)

project(CatSystem2)

option(CMAKE_BUILD_TYPE "Whether to build in release or debug mode." "Release")

if ("${CMAKE_BUILD_TYPE}" STREQUAL "")
    set(CMAKE_BUILD_TYPE "Release")
endif()

if (MSVC)
    add_compile_options(/utf-8)
else()
    message(FATAL_ERROR "Not supported")
endif()

include(GNUInstallDirs)

find_package(ZLIB REQUIRED)
find_package(PNG REQUIRED)

set(CAT_SYS_FILES CatSystem2/blowfish.cpp
CatSystem2/CatSystem2.cpp
CatSystem2/main.cpp)

include_directories(${ZLIB_INCLUDE_DIRS})
include_directories(${PNG_INCLUDE_DIRS})
add_executable(CatSystem2 ${CAT_SYS_FILES})
target_link_libraries(CatSystem2 PUBLIC ZLIB::ZLIB)

set(HG3PNG_FILES HG3PNG/pngfile.cpp HG3PNG/Source.cpp)
add_executable(HG3PNG ${HG3PNG_FILES})
target_link_libraries(HG3PNG PUBLIC PNG::PNG)

install(TARGETS CatSystem2 HG3PNG)
