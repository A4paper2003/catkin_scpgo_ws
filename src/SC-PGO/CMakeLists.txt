cmake_minimum_required(VERSION 2.8.3)
project(aloam_velodyne)
set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  nav_msgs
  sensor_msgs
  roscpp
  rospy
  rosbag
  std_msgs
  image_transport
  cv_bridge
  tf
  geodesy
  visualization_msgs
  message_generation
)

add_service_files(FILES
  SaveMap.srv
)

generate_messages(
   DEPENDENCIES
   std_msgs
)

find_package(PCL REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Ceres REQUIRED)
find_package(OpenMP REQUIRED)
find_package(GTSAM REQUIRED QUIET)

# Strip CUDA dependencies from GTSAM target
if(TARGET gtsam)
  set_target_properties(gtsam PROPERTIES
    INTERFACE_LINK_LIBRARIES ""
    IMPORTED_LINK_INTERFACE_LIBRARIES ""
  )
endif()

# Manually specify GTSAM libraries without CUDA
# Check both possible locations
if(EXISTS "/usr/local/lib/libgtsam.so")
  set(GTSAM_LIBS
    /usr/local/lib/libgtsam.so
  )
  if(EXISTS "/usr/local/lib/libgtsam_unstable.so")
    list(APPEND GTSAM_LIBS /usr/local/lib/libgtsam_unstable.so)
  endif()
elseif(EXISTS "/usr/lib/aarch64-linux-gnu/libgtsam.so")
  set(GTSAM_LIBS
    /usr/lib/aarch64-linux-gnu/libgtsam.so
  )
  if(EXISTS "/usr/lib/aarch64-linux-gnu/libgtsam_unstable.so")
    list(APPEND GTSAM_LIBS /usr/lib/aarch64-linux-gnu/libgtsam_unstable.so)
  endif()
else()
  message(FATAL_ERROR "Could not find GTSAM libraries")
endif()

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
  ${CERES_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${GTSAM_INCLUDE_DIR}
)

catkin_package(
  CATKIN_DEPENDS geometry_msgs nav_msgs roscpp rospy std_msgs
  DEPENDS EIGEN3 PCL
  INCLUDE_DIRS include
)

add_executable(alaserPGO_gps
  src/laserPosegraphOptimization_gps_pcd.cpp
  include/scancontext/Scancontext.cpp
)
target_compile_options(alaserPGO_gps
  PRIVATE ${OpenMP_CXX_FLAGS}
)
target_link_libraries(alaserPGO_gps
  ${catkin_LIBRARIES} 
  ${PCL_LIBRARIES} 
  ${CERES_LIBRARIES}
  ${OpenMP_CXX_FLAGS}
  ${GTSAM_LIBS}
)

add_executable(alaserPGO_ori
  src/laserPosegraphOptimization.cpp
  include/scancontext/Scancontext.cpp
)
target_compile_options(alaserPGO_ori
  PRIVATE ${OpenMP_CXX_FLAGS}
)
target_link_libraries(alaserPGO_ori
  ${catkin_LIBRARIES} 
  ${PCL_LIBRARIES} 
  ${CERES_LIBRARIES}
  ${OpenMP_CXX_FLAGS}
  ${GTSAM_LIBS}
)


#add_executable(alaserPGO
#  src/laserPosegraphOptimization_fpfh.cpp
#  include/scancontext/Scancontext.cpp
#)
#target_compile_options(alaserPGO
#  PRIVATE ${OpenMP_CXX_FLAGS}
#)
#target_link_libraries(alaserPGO
#  ${catkin_LIBRARIES} 
#  ${PCL_LIBRARIES} 
#  ${CERES_LIBRARIES}
#  ${OpenMP_CXX_FLAGS}
#  ${GTSAM_LIBS}
#)
