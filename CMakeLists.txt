cmake_minimum_required(VERSION 3.21)
project(pcl_vendor)

find_package(ament_cmake REQUIRED)

include(ExternalProject)

set(ENV{PKG_CONFIG_PATH} "${CMAKE_INSTALL_PREFIX}/lib/pkgconfig")
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/")
find_package(FLANN REQUIRED)

ExternalProject_Add(pcl_src
  GIT_REPOSITORY https://github.91chi.fun//https://github.com/PointCloudLibrary/pcl.git
  GIT_TAG pcl-1.12.1
  # UPDATE_COMMAND "cd  ../pcl_src && git submodule update --recursive --init"
  CONFIGURE_COMMAND cd ../pcl_src-build && cmake ../pcl_src -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}/ -G Ninja -DCMAKE_BUILD_TYPE=Release  -DBUILD_visualization=OFF -DFLANN_ROOT=${FLANN_ROOT}
  BUILD_COMMAND cd ../pcl_src-build && cmake --build . --config Release
  INSTALL_COMMAND cd ../pcl_src-build && cmake --install .
)

add_library(${PROJECT_NAME} src/dependency_tracker.cc)

install(TARGETS ${PROJECT_NAME} EXPORT export_${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

# install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/install/
#   DESTINATION ${CMAKE_INSTALL_PREFIX}/
# )

ament_package(CONFIG_EXTRAS "cmake/pcl-extras.cmake.in")