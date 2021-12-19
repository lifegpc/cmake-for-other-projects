cmake_minimum_required(VERSION 3.15)

set(MAC_VERSION_MAJOR 7)
set(MAC_VERSION_MINOR 22)
set(MAC_VERSION_MICRO 0)
set(MAC_VERSION ${MAC_VERSION_MAJOR}.${MAC_VERSION_MINOR}.${MAC_VERSION_MICRO})

project(MAC VERSION ${MAC_VERSION})

include(GNUInstallDirs)
include(CheckCXXCompilerFlag)

if (MINGW)
    add_compile_options(-Wp,-w)
endif()

if (WIN32)
    add_compile_definitions(PLATFORM_WINDOWS)
    if (MINGW)
        add_link_options(-municode)
    endif()
endif()

option(LINK_STD_STATIC "Link std library staticly." OFF)
option(LINK_ALL_STATIC "Link everything staticly." OFF)
option(BUILD_SHARED_LIBS "Whether to build static or shared library." OFF)
option(CMAKE_BUILD_TYPE "Whether to build in release or debug mode." "Release")

if ("${CMAKE_BUILD_TYPE}" STREQUAL "")
    set(CMAKE_BUILD_TYPE "Release")
endif()

if (NOT MSVC)
    if (LINK_STD_STATIC OR LINK_ALL_STATIC)
        check_cxx_compiler_flag("-Werror -static-libstdc++" HAVE_STATIC_STDCXX)
        if (HAVE_STATIC_STDCXX AND NOT LINK_ALL_STATIC)
            add_link_options(-static-libstdc++)
        else()
            if (BUILD_SHARED_LIBS)
                message(STATUS "Changed to build static library to make sure link stdc++ staticly.")
                set(BUILD_SHARED_LIBS OFF)
            endif()
            add_link_options(-static)
            link_libraries(stdc++)
        endif()
    else()
        link_libraries(stdc++)
    endif()
else()
    if (LINK_STD_STATIC OR LINK_ALL_STATIC)
        add_compile_options(/MT)
    endif()
endif()

if (UNIX)
    add_compile_definitions(PLATFORM_LINUX)
endif() 

if (APPLE)
    add_compile_definitions(PLATFORM_APPLE)
endif()

if (MSVC)
    add_compile_definitions(UNICODE)
endif()

if (NOT MSVC)
    if (${CMAKE_BUILD_TYPE} STREQUAL "Debug")
        add_compile_options(-O0)
    else()
        add_compile_options(-O2)
        add_link_options(-s)
    endif()
endif()

set(MACLibFILES Source/MACLib/APESimple.cpp Source/MACLib/MACLib.cpp
Source/MACLib/APECompress.cpp Source/MACLib/APECompressCore.cpp
Source/MACLib/APECompressCreate.cpp Source/MACLib/BitArray.cpp Source/MACLib/APEDecompress.cpp
Source/MACLib/Old/Anti-Predictor.cpp Source/MACLib/Old/AntiPredictorExtraHigh.cpp
Source/MACLib/Old/AntiPredictorFast.cpp Source/MACLib/Old/AntiPredictorHigh.cpp
Source/MACLib/Old/AntiPredictorNormal.cpp Source/MACLib/Old/APEDecompressCore.cpp
Source/MACLib/Old/APEDecompressOld.cpp Source/MACLib/Old/UnBitArrayOld.cpp
Source/MACLib/Old/UnMAC.cpp Source/MACLib/UnBitArray.cpp Source/MACLib/UnBitArrayBase.cpp
Source/MACLib/APEHeader.cpp Source/MACLib/APEInfo.cpp Source/MACLib/APELink.cpp
Source/MACLib/APETag.cpp Source/MACLib/WAVInputSource.cpp Source/Shared/CharacterHelper.cpp
Source/Shared/CircleBuffer.cpp Source/Shared/CRC.cpp Source/Shared/GlobalFunctions.cpp
Source/MACLib/MACProgressHelper.cpp Source/MACLib/MD5.cpp Source/MACLib/Prepare.cpp
Source/Shared/StdLibFileIO.cpp Source/Shared/WinFileIO.cpp Source/MACLib/NewPredictor.cpp
Source/MACLib/NNFilter.cpp Source/MACLib/Old/Anti-Predictor.h
Source/MACLib/Old/APEDecompressCore.h Source/MACLib/Old/APEDecompressOld.h
Source/MACLib/Old/UnBitArrayOld.h Source/MACLib/Old/UnMAC.h Source/Shared/All.h
Source/MACLib/MACLib.h Source/Shared/WindowsEnvironment.h Source/MACLib/APECompress.h
Source/MACLib/APECompressCore.h Source/MACLib/APECompressCreate.h Source/MACLib/BitArray.h
Source/MACLib/APEDecompress.h Source/MACLib/UnBitArray.h Source/MACLib/UnBitArrayBase.h
Source/MACLib/APEHeader.h Source/MACLib/APEInfo.h Source/MACLib/APELink.h
Source/MACLib/APETag.h Source/MACLib/WAVInputSource.h Source/Shared/CharacterHelper.h
Source/Shared/CircleBuffer.h Source/Shared/CRC.h Source/Shared/GlobalFunctions.h
Source/Shared/IO.h Source/MACLib/MACProgressHelper.h Source/MACLib/MD5.h
Source/Shared/NoWindows.h Source/MACLib/Prepare.h Source/Shared/SmartPtr.h
Source/Shared/StdLibFileIO.h Source/Shared/WinFileIO.h
Source/MACLib/NewPredictor.h Source/MACLib/NNFilter.h Source/MACLib/Predictor.h
Source/Shared/RollBuffer.h Source/MACLib/ScaledFirstOrderFilter.h)

if (WIN32)
    set(MACLIB_TYPE STATIC)
else()
    if (BUILD_SHARED_LIBS)
        set(MACLIB_TYPE SHARED)
    else()
        set(MACLIB_TYPE STATIC)
    endif()
endif()

add_library(MACLib ${MACLIB_TYPE} ${MACLibFILES})
target_include_directories(MACLib PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/Source/Shared)

if (MSVC)
    
set(MACDLLDEF Source/MACDll/MACDll.def)
set(MACDLLRC Source/MACDll/MACDll.rc Source/MACDll/res/MACDll.rc2 Source/MACDll/res/MAC.ico)

set(MACDllFILES Source/MACDll/MACDll.cpp Source/MACDll/MACDllApp.cpp Source/MACDll/stdafx.cpp
Source/MACDll/Winamp2.cpp Source/MACDll/WinampSettingsDlg.cpp Source/MACDll/CEDecoder.cpp
Source/MACDll/CEEncoder.cpp Source/MACDll/CoolEdit.cpp Source/Shared/APEInfoDialog.cpp
Source/Shared/WAVInfoDialog.cpp Source/MACDll/MACDllApp.h Source/MACDll/Resource.h
Source/MACDll/stdafx.h Source/MACDll/In2.h Source/MACDll/Out.h Source/MACDll/Winamp2.h
Source/MACDll/WinampSettingsDlg.h Source/MACDll/Filters.h Source/Shared/APEInfoDialog.h
Source/Shared/WAVInfoDialog.h Source/Shared/MACDll.h)

set(PUBLIC_HEADERS All.h APETag.h CharacterHelper.h IO.h MACDll.h MACLib.h NoWindows.h
SmartPtr.h Version.h WindowsEnvironment.h)

set(PUBLIC_HEADERS_TEMP ${PUBLIC_HEADERS})
set(PUBLIC_HEADERS "")
foreach(HEADER ${PUBLIC_HEADERS_TEMP})
    list(APPEND PUBLIC_HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/Shared/${HEADER}")
endforeach()
unset(PUBLIC_HEADERS_TEMP)

add_library(MACDll SHARED ${MACDllFILES} ${MACDLLDEF} ${MACDLLRC})
target_include_directories(MACDll PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/Source/Shared ${CMAKE_CURRENT_SOURCE_DIR}/Source/MACLib)
target_link_libraries(MACDll MACLib)
if (MSVC AND NOT (LINK_STD_STATIC OR LINK_ALL_STATIC))
    target_compile_definitions(MACDll PRIVATE _AFXDLL)
endif()
set_target_properties(MACDll PROPERTIES PUBLIC_HEADER "${PUBLIC_HEADERS}")
set(MACDLL MACDll)

endif(MSVC)

if (MSVC)
    set(MACRC Source/Console/Console.rc)
endif()

add_executable(mac Source/Console/Console.cpp Source/Console/resource.h ${MACRC})
target_include_directories(mac PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/Source/Shared ${CMAKE_CURRENT_SOURCE_DIR}/Source/MACLib)
target_link_libraries(mac MACLib)

install(TARGETS ${MACDLL} mac
PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/mac)
