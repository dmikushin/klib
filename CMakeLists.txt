cmake_minimum_required(VERSION 3.10)
project(klib VERSION 1.0.0 LANGUAGES C CXX)

# Set C standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Options
option(KLIB_BUILD_TESTS "Build klib tests" ON)
option(KLIB_BUILD_COMPONENTS "Build compiled components (.c files)" ON)
option(KLIB_INSTALL "Install klib files" ON)

# Header files - organized by category
set(KLIB_HEADERS_CONTAINER
    khash.h
    kbtree.h
    kavl.h
    kavl-lite.h
    kvec.h
    klist.h
)

set(KLIB_HEADERS_ALGORITHM
    ksort.h
    krmq.h
)

set(KLIB_HEADERS_IO
    kseq.h
)

set(KLIB_HEADERS_UTILITY
    kdq.h
    kbit.h
    krng.h
    ketopt.h
    kgraph.h
)

set(KLIB_HEADERS_ALL
    ${KLIB_HEADERS_CONTAINER}
    ${KLIB_HEADERS_ALGORITHM}
    ${KLIB_HEADERS_IO}
    ${KLIB_HEADERS_UTILITY}
)

# Create INTERFACE library for header-only components
# This follows klib philosophy: most components are header-only
add_library(klib-headers INTERFACE)
target_include_directories(klib-headers INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include>
)

# Components with .c implementation
# Each component is independent and can be used separately
if(KLIB_BUILD_COMPONENTS)
    # kalloc - memory allocator
    add_library(klib-kalloc OBJECT kalloc.c kalloc.h)
    target_include_directories(klib-kalloc PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # kbarena - block arena allocator
    add_library(klib-kbarena OBJECT kbarena.c kbarena.h)
    target_include_directories(klib-kbarena PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # kstring - string library
    add_library(klib-kstring OBJECT kstring.c kstring.h)
    target_include_directories(klib-kstring PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # kmath - mathematical functions
    add_library(klib-kmath OBJECT kmath.c kmath.h)
    target_include_directories(klib-kmath PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )
    target_link_libraries(klib-kmath PUBLIC m)

    # kthread - threading utilities
    add_library(klib-kthread OBJECT kthread.c kthread.h)
    target_include_directories(klib-kthread PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )
    find_package(Threads REQUIRED)
    target_link_libraries(klib-kthread PUBLIC Threads::Threads)

    # knetfile - network file access
    add_library(klib-knetfile OBJECT knetfile.c knetfile.h)
    target_include_directories(klib-knetfile PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )
    find_package(ZLIB)
    if(ZLIB_FOUND)
        target_link_libraries(klib-knetfile PUBLIC ZLIB::ZLIB)
    endif()

    # kurl - URL parsing
    add_library(klib-kurl OBJECT kurl.c kurl.h)
    target_include_directories(klib-kurl PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # kopen - smart stream opening
    add_library(klib-kopen OBJECT kopen.c)
    target_include_directories(klib-kopen PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )
    find_package(ZLIB)
    if(ZLIB_FOUND)
        target_link_libraries(klib-kopen PUBLIC ZLIB::ZLIB)
    endif()

    # khmm - Hidden Markov Model
    add_library(klib-khmm OBJECT khmm.c khmm.h)
    target_include_directories(klib-khmm PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # ksw - Smith-Waterman alignment
    add_library(klib-ksw OBJECT ksw.c ksw.h)
    target_include_directories(klib-ksw PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # knhx - Newick tree format parser
    add_library(klib-knhx OBJECT knhx.c knhx.h)
    target_include_directories(klib-knhx PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # kexpr - expression parser
    add_library(klib-kexpr OBJECT kexpr.c kexpr.h)
    target_include_directories(klib-kexpr PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # ksa - suffix array construction
    add_library(klib-ksa OBJECT ksa.c)
    target_include_directories(klib-ksa PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # keigen - eigenvalue/eigenvector computation
    add_library(klib-keigen OBJECT keigen.c keigen.h)
    target_include_directories(klib-keigen PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # kson - JSON parser
    add_library(klib-kson OBJECT kson.c kson.h)
    target_include_directories(klib-kson PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # bgzf - blocked gzip format
    add_library(klib-bgzf OBJECT bgzf.c bgzf.h)
    target_include_directories(klib-bgzf PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )
    find_package(ZLIB)
    if(ZLIB_FOUND)
        target_link_libraries(klib-bgzf PUBLIC ZLIB::ZLIB)
    endif()

    # kmempool - simple memory pool
    add_library(klib-kmempool OBJECT kmempool.c kmempool.h)
    target_include_directories(klib-kmempool PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
    )

    # Create convenience aggregate library (optional)
    # Users can link to specific components or all of them
    add_library(klib-all INTERFACE)
    target_link_libraries(klib-all INTERFACE
        klib-headers
        klib-kalloc
        klib-kbarena
        klib-kstring
        klib-kmath
        klib-kthread
        klib-knetfile
        klib-kurl
        klib-kopen
        klib-khmm
        klib-ksw
        klib-knhx
        klib-kexpr
        klib-ksa
        klib-keigen
        klib-kson
        klib-bgzf
        klib-kmempool
    )
endif()

# Tests
if(KLIB_BUILD_TESTS)
    enable_testing()
    add_subdirectory(test)
endif()

# Installation
if(KLIB_INSTALL)
    include(GNUInstallDirs)

    # Install header files
    install(FILES ${KLIB_HEADERS_ALL} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/klib)

    # Install compiled component headers
    if(KLIB_BUILD_COMPONENTS)
        install(FILES
            kalloc.h
            kbarena.h
            kstring.h
            kmath.h
            kthread.h
            knetfile.h
            kurl.h
            khmm.h
            ksw.h
            knhx.h
            kexpr.h
            keigen.h
            kson.h
            bgzf.h
            kmempool.h
            DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/klib
        )
    endif()

    # Install INTERFACE library
    install(TARGETS klib-headers
        EXPORT klibTargets
    )

    # Install component libraries
    if(KLIB_BUILD_COMPONENTS)
        install(TARGETS
            klib-kalloc
            klib-kbarena
            klib-kstring
            klib-kmath
            klib-kthread
            klib-knetfile
            klib-kurl
            klib-kopen
            klib-khmm
            klib-ksw
            klib-knhx
            klib-kexpr
            klib-ksa
            klib-keigen
            klib-kson
            klib-bgzf
            klib-kmempool
            klib-all
            EXPORT klibTargets
        )
    endif()

    # Generate and install CMake config files
    include(CMakePackageConfigHelpers)
    write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/klibConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY AnyNewerVersion
    )

    configure_package_config_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/cmake/klibConfig.cmake.in"
        "${CMAKE_CURRENT_BINARY_DIR}/klibConfig.cmake"
        INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/klib
    )

    install(EXPORT klibTargets
        FILE klibTargets.cmake
        NAMESPACE klib::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/klib
    )

    install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/klibConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/klibConfigVersion.cmake"
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/klib
    )

    # Install license
    install(FILES LICENSE.txt DESTINATION ${CMAKE_INSTALL_DOCDIR})
    install(FILES README.md DESTINATION ${CMAKE_INSTALL_DOCDIR})
endif()
