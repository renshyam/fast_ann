cmake_minimum_required(VERSION 3.9)

project(FastANN VERSION 1.0
                DESCRIPTION "Accelerated Approximate Nearest Neighbor Search"
                LANGUAGES CXX)

file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)
if(EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "Cannot build in source directory, "
            "use a separate build directory")
endif()

option(BUILD_EXPERIMENTS "Build experiments" ON)

set(CMAKE_BUILD_TYPE Debug)

add_library(hnswlib INTERFACE)
target_compile_features(hnswlib INTERFACE cxx_std_11)
target_include_directories(hnswlib INTERFACE ${PROJECT_SOURCE_DIR}/external/hnswlib)

find_package(MPI REQUIRED)
find_package(OpenMP REQUIRED)

add_library(fast_ann INTERFACE)
target_compile_features(fast_ann INTERFACE cxx_std_11)
target_include_directories(fast_ann INTERFACE ${PROJECT_SOURCE_DIR}/include)
target_link_libraries(fast_ann INTERFACE hnswlib)
target_link_libraries(fast_ann INTERFACE MPI::MPI_CXX)
target_link_libraries(fast_ann INTERFACE OpenMP::OpenMP_CXX)

if(BUILD_EXPERIMENTS)
    add_subdirectory(experiments)
endif(BUILD_EXPERIMENTS)
