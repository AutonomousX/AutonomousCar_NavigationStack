cmake_minimum_required(VERSION 2.8.3)
project(but_velodyne_proc)

find_package(catkin 
  REQUIRED COMPONENTS
  roscpp
  nodelet
  pcl_ros
  sensor_msgs
  nav_msgs
  tf
  velodyne_msgs
  velodyne_pointcloud
  costmap_2d
  roslint
)

find_package(PkgConfig REQUIRED)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

# Starting in ROS Jade, using cmake_modules to find Eigen is deprecated.
# Instead, find_package(Eigen3) is called which uses the cmake config included
# with the system install of Eigen. For compatibility with older ROS distros,
# we fall back to the cmake_modules version if needed.
#
# See http://wiki.ros.org/jade/Migration#Eigen_CMake_Module_in_cmake_modules
#
find_package(Eigen3)
if(NOT EIGEN3_FOUND)
  # Fallback to cmake_modules
  find_package(cmake_modules REQUIRED)
  find_package(Eigen REQUIRED)
  set(EIGEN3_INCLUDE_DIRS ${EIGEN_INCLUDE_DIRS})
else()
  set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif()

include_directories(include ${catkin_INCLUDE_DIRS} ${EIGEN3_INCLUDE_DIRS})
link_directories(${catkin_LIBRARY_DIRS} ${EIGEN3_LIBRARY_DIRS})

include_directories( ${EIGEN3_INCLUDE_DIRS})

find_package( OpenCV REQUIRED )
include_directories( ${OpenCV_INCLUDE_DIRS})

catkin_package(CATKIN_DEPENDS eigen nav_msgs sensor_msgs)
include_directories(include ${catkin_INCLUDE_DIRS})

add_library( butvelo 
             src/but_velodyne_lib/laser_scan.cpp
             src/but_velodyne_lib/ground_map.cpp
             src/but_velodyne_lib/cloud_assembler.cpp )
             
target_link_libraries(butvelo ${catkin_LIBRARIES} ${OGRE_LIBRARIES})

# Simulated laser scan nodelet
add_library( butvelo_laserscan_nodelet src/laser_scan_nodelet.cpp )
target_link_libraries( butvelo_laserscan_nodelet butvelo )

# Simulated laser scan node
add_executable( laser_scan src/laser_scan_node.cpp )
target_link_libraries( laser_scan butvelo )

# Ground map nodelet
add_library( butvelo_groundmap_nodelet src/ground_map_nodelet.cpp )
target_link_libraries( butvelo_groundmap_nodelet butvelo )
target_link_libraries( butvelo_groundmap_nodelet ${OpenCV_LIBRARIES} )

# Ground map node
add_executable( ground_map src/ground_map_node.cpp )
target_link_libraries( ground_map butvelo )
target_link_libraries( ground_map ${OpenCV_LIBRARIES} )

# Cloud assembler node
add_executable( cloud_assembler src/cloud_assembler_node.cpp )
target_link_libraries( cloud_assembler butvelo )

# TODO install nodes etc.

install(FILES 
  nodelets.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
  
roslint_cpp()

