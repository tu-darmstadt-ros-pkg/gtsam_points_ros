cmake_minimum_required(VERSION 3.8)
project(gtsam_points_ros)

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)

include(ExternalProject)

ExternalProject_Add(gtsam_points_src
  GIT_REPOSITORY https://github.com/koide3/gtsam_points.git
  GIT_TAG master #${VERSION}
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cmake
    -DBUILD_WITH_CUDA=ON
    -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} -DBUILD_SHARED_LIBS=ON #${CATKIN_DEVEL_PREFIX} -DBUILD_SHARED_LIBS=ON
    -DCMAKE_VERBOSE_MAKEFILE=ON
    -DCMAKE_BUILD_TYPE:STRING=Release
    #-DCMAKE_CXX_FLAGS=-fPIC
    #-DCMAKE_FIND_PACKAGE_NO_PACKAGE_REGISTRY=ON
    #-DCMAKE_FIND_PACKAGE_NO_SYSTEM_PACKAGE_REGISTRY=ON
    ../gtsam_points_src
  #BUILD_COMMAND make #-j8
  #INSTALL_COMMAND make install #-j8
)

#install(DIRECTORY
#  ${CMAKE_CURRENT_BINARY_DIR}/gtsam_points_src/include
#  DESTINATION include)

#install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/gtsam_points_src/lib
#  DESTINATION lib/${PROJECT_NAME})

ament_export_include_directories(
  ${CMAKE_CURRENT_INSTALL_DIR}/gtsam_points_ros/include
)

ament_export_libraries(
  gtsam_points_cuda
  gtsam_points
)

ament_package()
