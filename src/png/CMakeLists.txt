# Based on https://github.com/opencv/opencv/tree/master/3rdparty/libpng
cmake_minimum_required(VERSION 3.6)

if(CMAKE_ANDROID_ARM_NEON OR IOS)
  project(png C ASM)
else()
  project(png C)
endif()


file(GLOB lib_srcs *.c)
file(GLOB lib_hdrs *.h)

if(NOT ${CMAKE_SYSTEM_PROCESSOR} MATCHES "arm")
  list(APPEND lib_srcs intel/intel_init.c intel/filter_sse2_intrinsics.c)
endif()

if(CMAKE_ANDROID_ARM_NEON)
  list(APPEND lib_srcs arm/arm_init.c arm/filter_neon.S arm/filter_neon_intrinsics.c)
endif()

add_library(png STATIC ${lib_srcs} ${lib_hdrs})

if(CMAKE_ANDROID_ARM_NEON)
  target_compile_definitions(png PRIVATE -DPNG_ARM_NEON_OPT=2)
else()
  target_compile_definitions(png PRIVATE -DPNG_ARM_NEON_OPT=0)
endif()


if(NOT ${CMAKE_SYSTEM_PROCESSOR} MATCHES "arm")
  target_compile_definitions(png PRIVATE -DPNG_INTEL_SSE)
endif()

target_link_libraries(png zlib)
target_include_directories(png PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
