cmake_minimum_required(VERSION 2.8.12)
project(fcl_catkin)

find_package(catkin REQUIRED)
include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl/)
ExternalProject_Add(fcl_src
  URL https://github.com/wxmerkt/fcl/archive/master.zip
  URL_MD5 7060f9f8bd9df21523f3ce201899e513
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_BINARY_DIR}/install -FCL_BUILD_TESTS:BOOL=OFF -DFCL_USE_HOST_NATIVE_ARCH:BOOL=OFF -DFCL_USE_X64_SSE:BOOL=OFF
)

# On some architectures the x86_64-linux-gnu prefix is added - check that
if(EXISTS ${PROJECT_BINARY_DIR}/install/lib/${CMAKE_CXX_LIBRARY_ARCHITECTURE}/libfcl.so.0.6.0)
  set(PREFIX "/${CMAKE_CXX_LIBRARY_ARCHITECTURE}")
endif()

ExternalProject_Add_Step(fcl_src CopyToDevel
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/fcl ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/lib/${PREFIX}libfcl.so.0.6.0 ${CATKIN_DEVEL_PREFIX}/lib/libfcl.so.0.6
  DEPENDEES install
)

file(MAKE_DIRECTORY include/${PROJECT_NAME})
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
