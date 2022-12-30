cmake_minimum_required(VERSION 2.8)
project(vins)

set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++14")

# -DEIGEN_USE_MKL_ALL")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  geometry_msgs
  nav_msgs
  tf
  cv_bridge
  camera_models
  image_transport)


find_package(OpenCV 4 REQUIRED)
find_package(Ceres REQUIRED)
find_package(Eigen3)

# message(WARNING "OpenCV_VERSION: ${OpenCV_VERSION}")
# catkin_package(
#   INCLUDE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/src/estimator
#   ${CMAKE_CURRENT_SOURCE_DIR}/src/factor
#   ${CMAKE_CURRENT_SOURCE_DIR}/src/featureTracker
#   ${CMAKE_CURRENT_SOURCE_DIR}/src/initial
#   ${CMAKE_CURRENT_SOURCE_DIR}/src/utility
#   LIBRARIES vins_lib
# )

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src/estimator
  ${CMAKE_CURRENT_SOURCE_DIR}/src/factor
  ${CMAKE_CURRENT_SOURCE_DIR}/src/featureTracker
  ${CMAKE_CURRENT_SOURCE_DIR}/src/initial
  ${CMAKE_CURRENT_SOURCE_DIR}/src/utility ${catkin_INCLUDE_DIRS} ${CERES_INCLUDE_DIRS} ${EIGEN3_INCLUDE_DIR})

# include_directories(
# ${catkin_INCLUDE_DIRS}
# ${EIGEN3_INCLUDE_DIR}
# )
# include_directories(src/estimator)
set(vins_lib_sources
  src/estimator/parameters.cpp
  src/estimator/estimator.cpp
  src/estimator/feature_manager.cpp
  src/factor/pose_local_parameterization.cpp
  src/factor/projectionTwoFrameOneCamFactor.cpp
  src/factor/projectionTwoFrameTwoCamFactor.cpp
  src/factor/projectionOneFrameTwoCamFactor.cpp
  src/factor/marginalization_factor.cpp
  src/utility/utility.cpp
  src/utility/visualization.cpp
  src/utility/CameraPoseVisualization.cpp
  src/initial/solve_5pts.cpp
  src/initial/initial_aligment.cpp
  src/initial/initial_sfm.cpp
  src/initial/initial_ex_rotation.cpp
  src/featureTracker/feature_tracker.cpp)
add_library(vins_lib ${vins_lib_sources})

# add_library(vins_lib
# src/estimator/parameters.cpp
# src/estimator/estimator.cpp
# src/estimator/feature_manager.cpp
# src/factor/pose_local_parameterization.cpp
# src/factor/projectionTwoFrameOneCamFactor.cpp
# src/factor/projectionTwoFrameTwoCamFactor.cpp
# src/factor/projectionOneFrameTwoCamFactor.cpp
# src/factor/marginalization_factor.cpp
# src/utility/utility.cpp
# src/utility/visualization.cpp
# src/utility/CameraPoseVisualization.cpp
# src/initial/solve_5pts.cpp
# src/initial/initial_aligment.cpp
# src/initial/initial_sfm.cpp
# src/initial/initial_ex_rotation.cpp
# src/featureTracker/feature_tracker.cpp)
# set_target_properties(vins_lib PROPERTIES PUBLIC_HEADER "src/estimator/estimator.h;src/estimator/parameters.h")
target_link_libraries(vins_lib PUBLIC ${catkin_LIBRARIES} ${OpenCV_LIBS} ${CERES_LIBRARIES})

# add_executable(vins_node src/rosNodeTest.cpp)
# target_link_libraries(vins_node vins_lib)

# add_executable(kitti_odom_test src/KITTIOdomTest.cpp)
# target_link_libraries(kitti_odom_test vins_lib)

# add_executable(kitti_gps_test src/KITTIGPSTest.cpp)
# target_link_libraries(kitti_gps_test vins_lib)
# catkin_package(
# INCLUDE_DIRS ${include}
# LIBRARIES vins_lib
# CATKIN DEPENDS
# roscpp
# std_msgs
# geometry_msgs
# nav_msgs
# tf
# cv_bridge
# camera_models
# image_transport
# DEPENDS ${Eigen3} ${Ceres} ${OpenCV}
# )
# get_filename_component(COMBINED_PATH ${CMAKE_INSTALL_PREFIX}/include/vins ABSOLUTE)
file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include/vins)
file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include/camera_models/camodocal)
get_filename_component(COMBINED_PATH ${PROJECT_SOURCE_DIR} ABSOLUTE)
catkin_package(
  INCLUDE_DIRS ${CMAKE_INSTALL_PREFIX}/include/vins ${CMAKE_INSTALL_PREFIX}/include/camera_models
  LIBRARIES vins_lib
)
# set(include "/home/delivers18/talha123")
# set(pth /home/delivers18/ros_ws/src/VINS-Fusion/vins_estimator/src)
MESSAGE(WARNING "dst:  ${CMAKE_CURRENT_SOURCE_DIR}")
install(TARGETS vins_lib
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/estimator
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY
  ${CMAKE_CURRENT_SOURCE_DIR}/src/factor
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY
  ${CMAKE_CURRENT_SOURCE_DIR}/src/featureTracker
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY
  ${CMAKE_CURRENT_SOURCE_DIR}/src/initial
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/utility
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})


# file(GLOB_RECURSE headers
# "src/*.h"
# )
# install(FILES ${headers}
# DESTINATION ${CMAKE_INSTALL_PREFIX}/${CATKIN_PACKAGE_INCLUDE_DESTINATION})
# MESSAGE(WARNING "dst:  ${CATKIN_PACKAGE_INCLUDE_DESTINATION}")
# INSTALL(TARGETS vins_lib LIBRARY DESTINATION /usr/local/share/vins/lib
# PUBLIC_HEADER DESTINATION /usr/local/share/vins/include/estimator)
