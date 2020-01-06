cmake_minimum_required(VERSION 2.8.3)
project(rosbag_record)

set_directory_properties(PROPERTIES COMPILE_OPTIONS "-Wall;-Wextra")

find_package(catkin REQUIRED COMPONENTS rosconsole roscpp xmlrpcpp)
find_package(Boost REQUIRED COMPONENTS date_time regex program_options filesystem)
#find_package(BZip2 REQUIRED)

# Support large bags (>2GB) on 32-bit systems
add_definitions(-D_FILE_OFFSET_BITS=64)

#
#buld
#
include_directories(
  ${catkin_INCLUDE_DIRS}
)

link_directories(
/opt/ros/$ENV{ROS_DISTRO}/lib
)

catkin_package(
#  LIBRARIES rosbag_record
# INCLUDE_DIRS include
  CATKIN_DEPENDS rosconsole roscpp std_srvs topic_tools xmlrpcpp)

add_compile_options(-std=c++11)

add_executable(record src/record.cpp) 
target_link_libraries(record rosbag_storage
  ${catkin_LIBRARIES}
  rt ${Boost_LIBRARIES}
  topic_tools
  rosbag
)

#
# Install
#

## Install executables and/or libraries
install(TARGETS record
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(DIRECTORY launch/
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
        PATTERN ".svn" EXCLUDE)

if(CATKIN_ENABLE_TESTING)
#  catkin_add_nosetests(test)
endif()
