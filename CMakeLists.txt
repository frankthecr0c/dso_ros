cmake_minimum_required(VERSION 2.4.6)
project(dso_ros)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
set(ROS_BUILD_TYPE Release)


set(EXECUTABLE_OUTPUT_PATH bin)
set (CMAKE_CXX_STANDARD 14)

set(DSO_PATH $ENV{DSO_PATH})
message("\n\n---- LOOKING FOR DSO at\n\"${DSO_PATH}\"")

find_package(Pangolin REQUIRED)
message("\n\n---- FOUND Pangolin Headers at\n\"${Pangolin_INCLUDE_DIRS}\"")
message("---- FOUND Pangolin Libs at\n\"${Pangolin_LIBRARIES}\"")

find_package(OpenCV REQUIRED)
message("\n\n---- FOUND OpenCV Headers at\n\"${OpenCV_INCLUDE_DIRS}\"")
message("---- FOUND OpenCV Libs at\n\"${OpenCV_LIBS}\"")

find_package(Eigen3 REQUIRED)

find_package(Boost COMPONENTS system thread REQUIRED)
message("---- FOUND Boost Libs at\n\"${Boost_LIBRARIES}\"")

find_library(DSO_LIBRARY dso ${DSO_PATH}/build/lib)

set(CMAKE_CXX_FLAGS
   "${SSE_FLAGS} -O2 -g -std=c++0x -march=native -fno-omit-frame-pointer"
)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  sensor_msgs
  cv_bridge
  roslib
)


set(SOURCE_FILES         
  src/main.cpp
)
message("---- Source File at\n\"${SOURCE_FILES}\"")


INCLUDE_DIRECTORIES(
  ${PROJECT_SOURCE_DIR}/src
  ${DSO_PATH}/src
  ${DSO_PATH}/thirdparty/Sophus
  ${OpenCV_INCLUDE_DIRS}
  ${Pangolin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIR}
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)


LIST(APPEND LINK_LIBS
  ${Boost_LIBRARIES}
  ${OpenCV_LIBS}
  ${catkin_LIBRARIES}
  ${Pangolin_LIBRARIES}
  ${DSO_LIBRARY}
  ${catkin_LIBRARIES}
)


include_directories(
  include ${catkin_INCLUDE_DIRS}
)

add_executable(dso_live ${SOURCE_FILES})
target_link_libraries(dso_live ${LINK_LIBS})

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)