# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.10.2)

ENABLE_LANGUAGE(ASM)

set(profilo_BASE ${CMAKE_CURRENT_SOURCE_DIR}/../../../../profilo_source)

include_directories(${profilo_BASE}/include)
include_directories(${profilo_BASE}/cpp)
include_directories(${profilo_BASE}/deps)
include_directories(${profilo_BASE}/deps/breakpad)
include_directories(${profilo_BASE}/deps/fbjni/cxx/)
include_directories(${profilo_BASE}/deps/sigmux)
include_directories(${profilo_BASE}/deps/xplat_init/)
# Declares and names the project.

#message(FATAL_ERROR  "profilo_BASE=${profilo_BASE}")

project("profilo_mmapbuf")

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds them for you.
# Gradle automatically packages shared libraries with your APK.

if (ANDROID_ABI STREQUAL "armeabi-v7a")
    set(trampoline_arm ${profilo_BASE}/deps/plthooks/trampoline_arm.c)
    else()
    set(trampoline_arm ${profilo_BASE}/deps/plthooks/trampoline_arm64.c)
endif ()

#find_package(profilo REQUIRED CONFIG)
find_package(fbjni REQUIRED CONFIG)

add_library( # Sets the name of the library.
        profilo_mmapbuf

        # Sets the library as a shared library.
        SHARED

        # Provides a relative path to your source file(s).
        ${profilo_BASE}/cpp/generated/Entry.cpp
        ${profilo_BASE}/cpp/generated/EntryType.cpp
        ${profilo_BASE}/cpp/mmapbuf/Buffer.cpp
        ${profilo_BASE}/cpp/mmapbuf/JBuffer.cpp
        ${profilo_BASE}/cpp/mmapbuf/jni.cpp
        ${profilo_BASE}/cpp/mmapbuf/MmapBufferManager.cpp
        ${profilo_BASE}/cpp/logger/BlockLogger.cpp
        ${profilo_BASE}/cpp/logger/Logger.cpp
        ${profilo_BASE}/cpp/logger/MultiBufferLogger.cpp
        ${profilo_BASE}/cpp/logger/PacketLogger.cpp
        ${profilo_BASE}/cpp/logger/lfrb/Futex.cpp
        ${profilo_BASE}/cpp/util/common.cpp
        ${profilo_BASE}/cpp/util/hooks.cpp
        ${profilo_BASE}/cpp/util/ProcFsUtils.cpp
        ${profilo_BASE}/deps/breakpad/abort_with_reason.c
        ${profilo_BASE}/deps/build/build.cpp
        ${profilo_BASE}/deps/plthooks/hooks.cpp
        ${profilo_BASE}/deps/plthooks/plthooks.cpp
        ${profilo_BASE}/deps/plthooks/trampoline.cpp
        ${trampoline_arm}
        ${profilo_BASE}/deps/fb/log.cpp
        ${profilo_BASE}/deps/linker/elfSharedLibData.cpp
        ${profilo_BASE}/deps/linker/link.cpp
        ${profilo_BASE}/deps/linker/linker.cpp
        ${profilo_BASE}/deps/linker/sharedlibs.cpp
        ${profilo_BASE}/deps/sig_safe_write/sig_safe_write.c
        ${profilo_BASE}/deps/sigmux/phaser.c
        ${profilo_BASE}/deps/sigmux/sigmux.c
        )

# Searches for a specified prebuilt library and stores the path as a
# variable. Because CMake includes system libraries in the search path by
# default, you only need to specify the name of the public NDK library
# you want to add. CMake verifies that the library exists before
# completing its build.

find_library( # Sets the name of the path variable.
        log-lib

        # Specifies the name of the NDK library that
        # you want CMake to locate.
        log)

# Specifies libraries CMake should link to your target library. You
# can link multiple libraries, such as libraries you define in this
# build script, prebuilt third-party libraries, or system libraries.

target_link_libraries( # Specifies the target library.
        profilo_mmapbuf
#        profilo::profilo
        fbjni::fbjni
        # Links the target library to the log library
        # included in the NDK.
        ${log-lib})