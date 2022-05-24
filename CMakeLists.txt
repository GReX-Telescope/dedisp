cmake_minimum_required(VERSION 3.21)

project(dedisp VERSION 1.0.1 DESCRIPTION "CUDA Dedispersion for radio transients" LANGUAGES C CUDA)

# Build Configuration
set(CMAKE_CUDA_SEPARABLE_COMPILATION ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(NOT DEFINED CMAKE_CUDA_ARCHITECTURES)
  set(CMAKE_CUDA_ARCHITECTURES "35;50;75")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -Xcompiler=-Wall")

# Include build stuff
include_directories(src)
add_library(${PROJECT_NAME} SHARED src/dedisp.cu)
set_property(TARGET ${PROJECT_NAME} PROPERTY CUDA_RESOLVE_DEVICE_SYMBOLS ON)

install(FILES src/dedisp.h TYPE INCLUDE)

# Build test program
add_executable(testdedisp example/testdedisp.c example/gasdev.c example/ran1.c)
target_link_libraries(testdedisp PRIVATE ${PROJECT_NAME} m)
set_target_properties(testdedisp PROPERTIES LINKER_LANGUAGE C) 

# Set installation 
include(GNUInstallDirs)
install(
  TARGETS ${PROJECT_NAME}
  EXPORT DedispConfig
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Tests
enable_testing()
add_test(NAME testdedisp COMMAND $<TARGET_FILE:testdedisp>)

# CMake Config Stuff
export(TARGETS dedisp FILE "${CMAKE_CURRENT_BINARY_DIR}/DedispConfig.cmake")
install(EXPORT DedispConfig DESTINATION "${CMAKE_INSTALL_DATADIR}/dedisp/cmake")
