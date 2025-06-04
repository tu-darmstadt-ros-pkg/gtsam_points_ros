cmake_minimum_required(VERSION 3.10)
project(gtsam_points)

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)

include(ExternalProject)
#set(CMAKE_CUDA_ARCHITECTURES "70;80;86" CACHE STRING "CUDA architectures" FORCE)

ExternalProject_Add(gtsam_points_src
  GIT_REPOSITORY https://github.com/koide3/gtsam_points.git
  GIT_TAG master #${VERSION}
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cmake
    -DBUILD_WITH_CUDA=ON
    -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} -DBUILD_SHARED_LIBS=ON
    -DCMAKE_VERBOSE_MAKEFILE=ON
    -DCMAKE_BUILD_TYPE:STRING=Release
    #-DCMAKE_CUDA_ARCHITECTURES:STRING=60\;80
    #-DCMAKE_CXX_FLAGS=-fPIC
    -DCMAKE_PROJECT_INCLUDE=${CMAKE_CURRENT_SOURCE_DIR}/cuda_config.cmake
    ../gtsam_points_src
  #BUILD_COMMAND make #-j8
  #INSTALL_COMMAND make install #-j8
)

ament_export_include_directories(
  ${CMAKE_INSTALL_PREFIX}/include
)

ament_export_libraries(
  gtsam_points_cuda
  gtsam_points
)

ament_package()
