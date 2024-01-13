A convenient build script for a [[Desktop Application]].

## Example

```CMakeLists
# CMakeLists.txt - a root build file
#
# Copyright © 2021 Oleg Iarygin <oleg@arhadthedev.net>
#
# Distributed under the MIT software license; see the accompanying
# file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.

# cxx_std_17 in CMAKE_CXX_KNOWN_FEATURES was introduced in 3.8.
#
# New versions of C++ remove unsafe practices from the standard library, so
# force the latest version available. "cxx_std_*" means "at least C++ xxx".
#
# Features removed in C++14: <https://isocpp.org/files/papers/p1319r0.html>.
# Features removed in C++17: <https://isocpp.org/files/papers/p0636r0.html>.
# Features removed in C++20: <http://open-std.org/JTC1/SC22/WG21/docs/papers/2020/p2131r0.html>.
cmake_minimum_required(VERSION 3.8)

##################################################
#
# Project and policies
#
##################################################

file(STRINGS "${CMAKE_SOURCE_DIR}/vcpkg.json" PROJECT_INFO ENCODING UTF-8)
string(REGEX MATCH "[0-9]+\.[0-9.]+" GRAMMAR2CODE_BUILD_VERSION ${PROJECT_INFO})

project(grammar2code VERSION ${GRAMMAR2CODE_BUILD_VERSION} LANGUAGES CXX)

##################################################
#
# Build details for src/version.h.in
#
##################################################

string(TIMESTAMP GRAMMAR2CODE_BUILD_TIME "%Y-%m-%d.%H-%M-%S" UTC)

find_package(Git)
if(Git_FOUND)
    execute_process(COMMAND "${GIT_EXECUTABLE}" describe --exact-match --tags
                    RESULT_VARIABLE GRAMMAR2CODE_BUILD_IS_AHEAD_OF_TAG
                    OUTPUT_QUIET
                    ERROR_QUIET)
    execute_process(COMMAND "${GIT_EXECUTABLE}" rev-parse --short HEAD
                    OUTPUT_VARIABLE GRAMMAR2CODE_BUILD_COMMIT_ID
                    OUTPUT_STRIP_TRAILING_WHITESPACE
                    ERROR_QUIET)
    execute_process(COMMAND "${GIT_EXECUTABLE}" diff --exit-code
                    RESULT_VARIABLE GRAMMAR2CODE_BUILD_IS_DIRTY
                    OUTPUT_QUIET
                    ERROR_QUIET)
endif()

##################################################
#
# User options
#
##################################################

set(GRAMMAR2CODE_USE_BOOTSTRAP NO CACHE BOOL
    "Build a primordial grammar2code with proto-grammarkdown-only support")

##################################################
#
# Package dependencies
#
##################################################

find_package(CLI11 CONFIG REQUIRED)
find_package(RapidJSON CONFIG REQUIRED)

##################################################
#
# Once-generated out-of-target files
#
##################################################

configure_file("src/version.h.in" "version.h" @ONLY)

##################################################
#
# Targets
#
##################################################

add_executable(grammar2code src/errors.cpp
                            src/grammar_processing.cpp
                            src/main.cpp
                            src/parser_config.cpp)
target_compile_features(grammar2code PRIVATE cxx_std_17)
if (GRAMMAR2CODE_USE_BOOTSTRAP)
    target_compile_definitions(grammar2code PRIVATE GRAMMAR2CODE_USE_BOOTSTRAP)
endif()
target_include_directories(grammar2code PRIVATE ${CMAKE_CURRENT_BINARY_DIR})
target_link_libraries(grammar2code PRIVATE CLI11::CLI11 rapidjson)
```

```CMakeLists
# CMakeLists.txt - a root build file
#
# Copyright (c) 2021 Oleg Iarygin <oleg@arhadthedev.net>
#
# Distributed under the MIT software license; see the accompanying
# file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.

# cxx_std_17 in CMAKE_CXX_KNOWN_FEATURES was introduced in 3.8
# find_package(GoogleTest) was introduced in 3.9
# install(FILES TYPE <type>) was introduced in 3.14
#
# New versions of C++ remove unsafe practices from the standard library, so
# force the latest version available. "cxx_std_*" means "at least C++ xxx".
#
# Features removed in C++14: <https://isocpp.org/files/papers/p1319r0.html>.
# Features removed in C++17: <https://isocpp.org/files/papers/p0636r0.html>.
# Features removed in C++20: <http://open-std.org/JTC1/SC22/WG21/docs/papers/2020/p2131r0.html>.
cmake_minimum_required(VERSION 3.14)


##################################################
#
# Project and policies
#
##################################################

file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/vcpkg.json" MANIFEST ENCODING UTF-8)
string(REGEX MATCH "[0-9]+\.[0-9.]+" BUILD_VERSION ${MANIFEST})

project(grammar2code VERSION ${BUILD_VERSION} LANGUAGES CXX)


##################################################
#
# Files generated into a build directory
#
##################################################

# A version header need to be regenerated each build to catch git checkouts.
# However, doing it in this CMakeLists.txt requires manual reconfiguration
# to trigger updates, so instead this file adds an always-rebuilt custom target
# that invokes a script to update the header conditionallys.

set(IN ${CMAKE_CURRENT_SOURCE_DIR}/src/version.h.in)
set(OUT ${CMAKE_CURRENT_BINARY_DIR}/version.h)
set(TOOLS ${CMAKE_CURRENT_SOURCE_DIR}/tools)
add_custom_target(version_info
    ALL
    COMMAND "${CMAKE_COMMAND}" -DVERSION_HEADER_TEMPLATE_PATH="${IN}"
                               -DVERSION_HEADER_OUTPUT_PATH="${OUT}"
                               -DGRAMMAR2CODE_BUILD_VERSION="${BUILD_VERSION}"
                               -P "${TOOLS}/update_version.cmake"
    BYPRODUCTS version.h)


##################################################
#
# Targets
#
##################################################

find_package(GTest CONFIG)
if(GTest_FOUND)
    set(BUILD_GMOCK NO)
    set(INSTALL_GTEST NO)
    include(CTest)
    include(GoogleTest)

    add_executable(unit_tests EXCLUDE_FROM_ALL tests/unit/always_pass.cpp)
    target_compile_features(unit_tests PRIVATE cxx_std_17)
    target_link_libraries(unit_tests PRIVATE GTest::gtest GTest::gtest_main)
    gtest_discover_tests(unit_tests)
else()
    message(WARNING GTest is not found; test target is disabled)
endif()


##################################################
#
# Installation
#
##################################################

set(CPACK_WIX_PRODUCT_GUID ec001051-3486-43e8-8195-44416166b42f)
set(CPACK_WIX_LICENSE_RTF LICENSE.txt)
set(CPACK_WIX_PROGRAM_MENU_FOLDER .)  # Create shortcuts directly in Start Menu
set(CPACK_WIX_PROPERTY_ARPHELPLINK https://github.com/arhadthedev/grammar2code)

install(FILES LICENSE.txt README.md TYPE DOC)
```