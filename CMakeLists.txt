cmake_minimum_required(VERSION 2.8.3)
project(rosbag_record)

if(NOT WIN32)
  set_directory_properties(PROPERTIES COMPILE_OPTIONS "-Wall;-Wextra")
endif()

find_package(catkin REQUIRED COMPONENTS rosconsole roscpp xmlrpcpp)
find_package(Boost REQUIRED COMPONENTS date_time regex program_options filesystem)
#find_package(BZip2 REQUIRED)

# Support large bags (>2GB) on 32-bit systems
add_definitions(-D_FILE_OFFSET_BITS=64)

include_directories(
  ${catkin_INCLUDE_DIRS}
#  ${Boost_INCLUDE_DIRS}
#  ${BZIP2_INCLUDE_DIR}
)

link_directories(
/opt/ros/$ENV{ROS_DISTRO}/lib
)

catkin_package(
  LIBRARIES rosbag_record
# INCLUDE_DIRS include
  CATKIN_DEPENDS rosconsole roscpp std_srvs topic_tools xmlrpcpp)

add_compile_options(-std=c++11)

add_library(rosbag_record
  src/record.cpp)

target_link_libraries(rosbag_record ${catkin_LIBRARIES} ${Boost_LIBRARIES}
#  ${BZIP2_LIBRARIES}
  topic_tools
  rosbag
)

add_executable(record src/record.cpp)
target_link_libraries(record rosbag_record rosbag_storage
  rt ${Boost_LIBRARIES})

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h")
install(TARGETS rosbag_record
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})

if(CATKIN_ENABLE_TESTING)
#  catkin_add_nosetests(test)
endif()
