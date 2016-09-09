cmake_minimum_required(VERSION 3.1)

project(RajaExamples CXX C)
set(CMAKE_CXX_STANDARD 14)

set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

find_package(OpenMP)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")

include(ExternalProject)
SET_DIRECTORY_PROPERTIES(PROPERTIES EP_PREFIX ${CMAKE_BINARY_DIR}/tpl)

if (RAJA_DIR)
  find_package(RAJA QUIET)
  include_directories(${RAJA_INCLUDE_DIR})
else ()
  if (NOT EXISTS ${PROJECT_SOURCE_DIR}/RAJA/CMakeLists.txt)
    message(FATAL_ERROR "\
RAJA_DIR was not specified, and the RAJA submodule was not found. \
Please run the following commands:\n\
git submodule init\n \
git submodule update\n")
  endif()
  add_subdirectory(RAJA)
  include_directories(${PROJECT_BINARY_DIR}/RAJA/include)
  include_directories(${PROJECT_SOURCE_DIR}/RAJA/include)
endif()

if(RAJA_ENABLE_CUDA)
  find_package(CUDA)
  if(CUDA_FOUND)
    message(STATUS "CUDA Enabled")
    list(APPEND RAJA_NVCC_FLAGS -Xcompiler ${OpenMP_CXX_FLAGS})
    set (CUDA_NVCC_FLAGS ${RAJA_NVCC_FLAGS})
    set (CUDA_PROPAGATE_HOST_FLAGS OFF)
    include_directories(${CUDA_INCLUDE_DIRS})
  endif()
endif()

include_directories(include)

add_subdirectory(LULESH-v1.0)
add_subdirectory(LULESH-v2.0)
add_subdirectory(Kripke-v1.1)
