cmake_minimum_required(VERSION 2.8.12)
project(fcl_catkin)

find_package(catkin REQUIRED)
include(ExternalProject)

set(HOST_OPTIMIZATION ON)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl/)
ExternalProject_Add(fcl_src
  URL https://github.com/wxmerkt/fcl/archive/master.zip
  URL_MD5 cad96aaad83616c5cd52041c75f206b8
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_BINARY_DIR}/install -FCL_BUILD_TESTS:BOOL=OFF -DFCL_USE_HOST_NATIVE_ARCH:BOOL=${HOST_OPTIMIZATION} -DFCL_USE_X64_SSE:BOOL=${HOST_OPTIMIZATION} -DCMAKE_INSTALL_LIBDIR=lib
)

ExternalProject_Add_Step(fcl_src CopyToDevel
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/fcl ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/lib/libfcl.so.0.6.0 ${CATKIN_DEVEL_PREFIX}/lib/libfcl.so.0.6
  DEPENDEES install
)

file(MAKE_DIRECTORY include/${PROJECT_NAME})
catkin_package(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME} include/${PROJECT_NAME} ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME}
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
