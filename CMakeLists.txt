cmake_minimum_required(VERSION 3.0.2)
project(fcl_catkin)

find_package(catkin REQUIRED)
include(ExternalProject)

set(HOST_OPTIMIZATION OFF)

ExternalProject_Add(fcl_src
  URL https://github.com/flexible-collision-library/fcl/archive/f4018b4e451f7850081a8c254dc4c0779bd9061f.zip
  URL_MD5 28f72b6b4f623db5de794eb4b1c4b2c8
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_BINARY_DIR}/install -DFCL_BUILD_TESTS:BOOL=OFF -DFCL_USE_HOST_NATIVE_ARCH:BOOL=OFF -DFCL_USE_X64_SSE:BOOL=${HOST_OPTIMIZATION} -DCMAKE_INSTALL_LIBDIR=lib
)
ExternalProject_Add_Step(fcl_src CopyToDevel
  COMMENT "Copying to devel"
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/fcl ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/lib/libfcl.so.0.6.1 ${CATKIN_DEVEL_PREFIX}/lib/libfcl_catkin.so.0.6
  DEPENDEES install
)

if(IS_DIRECTORY ${CMAKE_INSTALL_PREFIX})
  # message(WARNING "Compiling for install workspace (${CMAKE_INSTALL_PREFIX})")
  catkin_package(
    INCLUDE_DIRS include/${PROJECT_NAME}
    LIBRARIES libfcl_catkin.so.0.6
  )
else()
  # message(WARNING "Compiling for devel workspace")
  file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME})
  catkin_package(
    INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME} include/${PROJECT_NAME}
    LIBRARIES libfcl_catkin.so.0.6
  )
endif()

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include
  DESTINATION ${CMAKE_INSTALL_PREFIX}
)
install(
  FILES ${CATKIN_DEVEL_PREFIX}/lib/libfcl_catkin.so.0.6
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
