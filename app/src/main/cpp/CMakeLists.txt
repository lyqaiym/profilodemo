# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.18.1)

project("test")

set(profilo_BASE ${CMAKE_CURRENT_SOURCE_DIR}/../../../../profilo_source)
include_directories(${profilo_BASE}/../include)
include_directories(${profilo_BASE}/deps)
include_directories(${profilo_BASE}/cpp)
set(FBJNI_COMPILE_OPTIONS
        -Wall
        -std=c++17
        -pthread
        -fno-omit-frame-pointer
        -fexceptions
        -frtti
        -ffunction-sections
        -O3
        -DNDEBUG
        )

find_package(fbjni REQUIRED CONFIG)

add_library( # Sets the name of the library.
        test

        # Sets the library as a shared library.
        SHARED
        test.cpp
        )
target_compile_options(test PRIVATE ${FBJNI_COMPILE_OPTIONS})

target_link_libraries( # Specifies the target library.
        test
        fbjni::fbjni

        # Links the target library to the log library
        # included in the NDK.
        ${log-lib})