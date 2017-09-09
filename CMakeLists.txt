cmake_minimum_required(VERSION 2.8.12)
project(fcl_catkin)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/fcl/)
ExternalProject_Add(fcl_src
  GIT_REPOSITORY https://github.com/wxmerkt/fcl.git
  GIT_TAG master
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX} -FCL_BUILD_TESTS:BOOL=OFF -DFCL_USE_HOST_NATIVE_ARCH:BOOL=ON
)

find_package(catkin REQUIRED)

catkin_package(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  LIBRARIES fcl
)

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include
  DESTINATION ${CMAKE_INSTALL_PREFIX}
)
install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
