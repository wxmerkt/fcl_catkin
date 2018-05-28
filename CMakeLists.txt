cmake_minimum_required(VERSION 2.8.12)
project(fcl_catkin)

find_package(catkin REQUIRED)
include(ExternalProject)

set(HOST_OPTIMIZATION OFF)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl/)
ExternalProject_Add(fcl_src
  URL https://github.com/wxmerkt/fcl/archive/0.5.92.zip
  URL_MD5 196d581f6178b516c6d6ad9dc529b41c
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_BINARY_DIR}/install -DFCL_BUILD_TESTS:BOOL=OFF -DFCL_USE_HOST_NATIVE_ARCH:BOOL=OFF -DFCL_USE_X64_SSE:BOOL=${HOST_OPTIMIZATION} -DCMAKE_INSTALL_LIBDIR=lib
)

ExternalProject_Add_Step(fcl_src CopyToDevel
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/fcl ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/lib/libfcl.so.0.6.0 ${CATKIN_DEVEL_PREFIX}/lib/libfcl.so.0.6
  DEPENDEES install
)

file(MAKE_DIRECTORY include/${PROJECT_NAME})
# The following line is required for source builds but fatal on the ROS buildfarm
# file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME})
catkin_package(
  INCLUDE_DIRS include/${PROJECT_NAME}
  LIBRARIES libfcl.so.0.6
)

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include
  DESTINATION ${CMAKE_INSTALL_PREFIX}
)
install(
  FILES ${CATKIN_DEVEL_PREFIX}/lib/libfcl.so.0.6
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
