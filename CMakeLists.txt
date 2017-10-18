cmake_minimum_required(VERSION 2.8.12)
project(fcl_catkin)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl/)
ExternalProject_Add(fcl_src
  GIT_REPOSITORY https://github.com/wxmerkt/fcl.git
  GIT_TAG aca750433c6e694be29b141cf5b411324b2a6b24
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_BINARY_DIR}/install -FCL_BUILD_TESTS:BOOL=OFF #-DFCL_USE_HOST_NATIVE_ARCH:BOOL=ON
)

ExternalProject_Add_Step(fcl_src CopyToDevel
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/fcl ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/fcl
    COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/lib/libfcl.so.0.6.0 ${CATKIN_DEVEL_PREFIX}/lib/libfcl.so.0.6
    DEPENDEES install
)

find_package(catkin REQUIRED)

file(MAKE_DIRECTORY include/${PROJECT_NAME})
file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME})
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
