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

# Pin to a release compatible with glim's find_package(gtsam_points 1.2.2)
set(VERSION "v1.2.2")

# Stage upstream install into the build tree. ExternalProject's own install does
# not participate in ament/bloom packaging, so we re-install from here below.
set(GTSAM_POINTS_INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}/gtsam_points_install)
file(MAKE_DIRECTORY ${GTSAM_POINTS_INSTALL_DIR})

# Pass workspace prefixes so the ExternalProject can find hector's gtsam.
set(_gtsam_points_prefix_path "${CMAKE_PREFIX_PATH}")
if(DEFINED ENV{CMAKE_PREFIX_PATH} AND NOT "$ENV{CMAKE_PREFIX_PATH}" STREQUAL "")
  list(APPEND _gtsam_points_prefix_path $ENV{CMAKE_PREFIX_PATH})
endif()
string(REPLACE ";" "|" _gtsam_points_prefix_path_alt "${_gtsam_points_prefix_path}")

ExternalProject_Add(gtsam_points_src
  GIT_REPOSITORY https://github.com/koide3/gtsam_points.git
  GIT_TAG ${VERSION}
  UPDATE_COMMAND ""
  LIST_SEPARATOR |
  CMAKE_ARGS
    -DBUILD_WITH_CUDA=ON
    -DBUILD_SHARED_LIBS=ON
    -DCMAKE_BUILD_TYPE=Release
    -DCMAKE_INSTALL_PREFIX=${GTSAM_POINTS_INSTALL_DIR}
    # Keep libs under lib/ so ament's library_path hook finds them
    -DCMAKE_INSTALL_LIBDIR=lib
    -DCMAKE_PREFIX_PATH=${_gtsam_points_prefix_path_alt}
    -DCMAKE_PROJECT_INCLUDE=${CMAKE_CURRENT_SOURCE_DIR}/cuda_config.cmake
)

# Package the staged headers/libs/cmake configs into the deb / colcon install space
install(
  DIRECTORY ${GTSAM_POINTS_INSTALL_DIR}/
  DESTINATION .
  USE_SOURCE_PERMISSIONS
)

ament_export_include_directories(include)
ament_export_libraries(
  gtsam_points_cuda
  gtsam_points
)

ament_package()
