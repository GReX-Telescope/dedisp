cmake_minimum_required(VERSION 3.18)

project(dedisp VERSION 1.0.1 DESCRIPTION "CUDA Dedispersion for radio transients" LANGUAGES C CUDA)

# Build Configuration
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# CUDA Configuration
set(CUDA_NVCC_FLAGS -O3 -use_fast_math -lineinfo -src-in-ptx)

if(NOT DEFINED CMAKE_CUDA_ARCHITECTURES)
  set(CMAKE_CUDA_ARCHITECTURES 75)
endif()

# Include build stuff
include_directories(src)
add_library(${PROJECT_NAME} SHARED src/dedisp.cu)
install(FILES src/dedisp.h TYPE INCLUDE)

# Build test program
add_executable(testdedisp example/testdedisp.c example/gasdev.c example/ran1.c)
target_link_libraries(testdedisp PRIVATE ${PROJECT_NAME} m)
set_target_properties(testdedisp PROPERTIES LINKER_LANGUAGE C) 

# Set installation 
include(GNUInstallDirs)
install(TARGETS ${PROJECT_NAME}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Tests
enable_testing()
add_test(NAME testdedisp COMMAND $<TARGET_FILE:testdedisp>)