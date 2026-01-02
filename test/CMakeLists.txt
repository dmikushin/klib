# Test suite for klib components
# Each test is independent and tests a specific component

# Helper function to add a test executable
function(add_klib_test name)
    cmake_parse_arguments(TEST "" "" "SOURCES;LIBRARIES;ARGS" ${ARGN})

    add_executable(${name} ${TEST_SOURCES})
    target_include_directories(${name} PRIVATE ${CMAKE_SOURCE_DIR})

    if(TEST_LIBRARIES)
        target_link_libraries(${name} PRIVATE ${TEST_LIBRARIES})
    endif()

    if(TEST_ARGS)
        add_test(NAME ${name} COMMAND ${name} ${TEST_ARGS})
    else()
        add_test(NAME ${name} COMMAND ${name})
    endif()
endfunction()

# Find optional dependencies
find_package(ZLIB)
find_package(Threads)

# Header-only component tests
add_klib_test(kavl_test SOURCES kavl_test.c)
add_klib_test(kavl-lite_test SOURCES kavl-lite_test.c)
add_klib_test(kbtree_test SOURCES kbtree_test.c)
add_klib_test(khash_test SOURCES khash_test.c)
add_klib_test(khash_keith SOURCES khash_keith.c)
add_klib_test(khash_keith2 SOURCES khash_keith2.c)
add_klib_test(klist_test SOURCES klist_test.c)
add_klib_test(ksort_test SOURCES ksort_test.c)
add_klib_test(ketopt_test SOURCES ketopt_test.c ARGS -xy1 -x arg1 --foo --bar=test)
add_klib_test(kbit_test SOURCES kbit_test.c)
add_klib_test(kgraph_test SOURCES kgraph_test.c)
add_klib_test(krmq_test SOURCES krmq_test.c)

# Tests requiring ZLIB
if(ZLIB_FOUND)
    add_klib_test(kseq_test SOURCES kseq_test.c
        LIBRARIES ZLIB::ZLIB
        ARGS ${CMAKE_CURRENT_SOURCE_DIR}/kseq_test.dat)
    add_klib_test(kseq_bench SOURCES kseq_bench.c
        LIBRARIES ZLIB::ZLIB
        ARGS ${CMAKE_CURRENT_SOURCE_DIR}/kseq_bench_data.txt.gz)
    add_klib_test(kseq_bench2 SOURCES kseq_bench2.c
        ARGS ${CMAKE_CURRENT_SOURCE_DIR}/kseq_bench_data.txt)
endif()

# C++ tests (for C++ compatibility)
add_executable(ksort_test-stl ksort_test.cc)
target_include_directories(ksort_test-stl PRIVATE ${CMAKE_SOURCE_DIR})
add_test(NAME ksort_test-stl COMMAND ksort_test-stl)

add_executable(kvec_test kvec_test.cc)
target_include_directories(kvec_test PRIVATE ${CMAKE_SOURCE_DIR})
add_test(NAME kvec_test COMMAND kvec_test)

# Tests requiring compiled components
if(KLIB_BUILD_COMPONENTS)
    # kbarena test
    add_klib_test(kbarena_test
        SOURCES kbarena_test.c
        LIBRARIES klib-kbarena
    )

    # kstring tests
    add_klib_test(kstring_test
        SOURCES kstring_test.c
        LIBRARIES klib-kstring
    )

    add_klib_test(kstring_bench
        SOURCES kstring_bench.c
        LIBRARIES klib-kstring
    )

    add_klib_test(kstring_bench2
        SOURCES kstring_bench2.c
        LIBRARIES klib-kstring
    )

    # kmath test
    add_klib_test(kmin_test
        SOURCES kmin_test.c
        LIBRARIES klib-kmath m
    )

    # kthread tests
    if(Threads_FOUND)
        add_klib_test(kthread_test2
            SOURCES kthread_test2.c
            LIBRARIES klib-kthread Threads::Threads
            ARGS ${CMAKE_CURRENT_SOURCE_DIR}/kseq_bench_data.txt
        )
    endif()
endif()

# Print test summary
message(STATUS "Configured klib tests:")
message(STATUS "  Header-only component tests: enabled")
if(ZLIB_FOUND)
    message(STATUS "  ZLIB-dependent tests: enabled")
else()
    message(STATUS "  ZLIB-dependent tests: disabled (ZLIB not found)")
endif()
if(KLIB_BUILD_COMPONENTS)
    message(STATUS "  Compiled component tests: enabled")
else()
    message(STATUS "  Compiled component tests: disabled")
endif()
