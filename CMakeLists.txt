cmake_minimum_required(VERSION 3.16)  # CMake in Ubuntu 20.04

project(kvstorage CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

if (UNIX AND NOT DEFINED EXTRA_CXX_FLAGS)
    # -gdwarf-4: compatibility with Valgrind on Ubuntu 22.04, see https://bugs.kde.org/show_bug.cgi?id=452758
    set(EXTRA_CXX_FLAGS "-fsanitize=undefined -fno-sanitize-recover=all -fsanitize=address -gdwarf-4")
endif (UNIX AND NOT DEFINED EXTRA_CXX_FLAGS)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${EXTRA_CXX_FLAGS}")

# Do not specify /O2 because there are Debug/Release modes.
if (MSVC)
    add_compile_options(/W4 /WX)
    add_compile_options(/D_CRT_SECURE_NO_WARNINGS)  # Disable warnings about VS-only _s functions (e.g. scanf_s).
    add_compile_options(/GR-)  # Disable RTTI
    add_link_options(/GR-)  # Disable RTTI
else (MSVC)
    if (NOT DEFINED CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE Debug CACHE STRING "Default build type from CMakeLists.txt" FORCE)  # Add debug information
    endif (NOT DEFINED CMAKE_BUILD_TYPE)

    add_compile_options(-Wall -Wextra -Werror)
    add_compile_options(-fno-rtti)  # Disable RTTI
    add_link_options(-fno-rtti)  # Disable RTTI
endif (MSVC)

enable_testing()

find_package(Threads)
find_package(Boost 1.71 REQUIRED system)

include_directories(${Boost_INCLUDE_DIRS})
link_libraries(${CMAKE_THREAD_LIBS_INIT} ${Boost_LIBRARIES})

add_executable(main src/main.cpp)

# ===== Tests =====
include_directories(thirdparty/doctest)

add_library(doctest_main src/doctest_main.cpp)
link_libraries(doctest_main)  # Link to all following tests

add_executable(main_test src/main_test.cpp)
add_test(NAME main_test_exe COMMAND ./main_test)
