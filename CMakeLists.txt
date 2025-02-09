cmake_minimum_required(VERSION 3.21)

project(dedisp VERSION 1.0.1 DESCRIPTION "CUDA Dedispersion for radio transients" LANGUAGES C CUDA)

# Build Configuration
find_package(CUDAToolkit REQUIRED)

set(CMAKE_CUDA_SEPARABLE_COMPILATION ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(CMAKE_CUDA_RUNTIME_LIBRARY Static)

# Set arch
if(NOT DEFINED CMAKE_CUDA_ARCHITECTURES)
  set(CMAKE_CUDA_ARCHITECTURES "50;75;80")
endif()

# specify the C++ standard
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_CXX_EXTENSIONS OFF)

if(NOT DEFINED CMAKE_CUDA_STANDARD)
    set(CMAKE_CUDA_STANDARD 14)
    set(CMAKE_CUDA_STANDARD_REQUIRED ON)
endif()

# Compiler Flags
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -O3 -use_fast_math -Xcompiler=-Wall ")

# Include build stuff
add_library(${PROJECT_NAME} STATIC src/dedisp.cu)
target_include_directories(${PROJECT_NAME} PUBLIC include)

# Build test program
add_executable(testdedisp example/testdedisp.c example/gasdev.c example/ran1.c)
target_link_libraries(testdedisp ${PROJECT_NAME} m CUDA::cudart_static)
set_target_properties(testdedisp PROPERTIES LINKER_LANGUAGE C)

# Tests
enable_testing()
add_test(NAME testdedisp COMMAND $<TARGET_FILE:testdedisp>)