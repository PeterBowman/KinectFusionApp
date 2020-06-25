cmake_minimum_required(VERSION 3.0 FATAL_ERROR)

project(KinectFusionApp LANGUAGES CXX)

# Setting some paths
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)

# Path of find modules
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})

# Use modern C++
set(CMAKE_CXX_STANDARD 14)

# ------------------------------------------------
#                 Dependencies
# ------------------------------------------------
find_package(KinectFusionLib REQUIRED)

find_package(OpenCV 3.0 REQUIRED COMPONENTS opencv_core
                                            opencv_highgui
                                            opencv_imgcodecs
                                            opencv_imgproc)

find_package(Eigen3 REQUIRED)

find_package(OpenNI2)
if (OPENNI2_FOUND)
    include_directories("${OPENNI2_INCLUDE_DIRS}")
endif (OPENNI2_FOUND)

find_package(realsense2 REQUIRED)
# ------------------------------------------------

file(GLOB KinectFusionApp_SRCS ${CMAKE_SOURCE_DIR}/src/*.cpp)
add_executable(KinectFusionApp ${KinectFusionApp_SRCS})

target_include_directories(KinectFusionApp PRIVATE ${CMAKE_SOURCE_DIR}/include)

target_link_libraries(KinectFusionApp opencv_core
                                      opencv_highgui
                                      opencv_imgcodecs
                                      opencv_imgproc
                                      Eigen3::Eigen
                                      ${OPENNI2_LIBRARY}
                                      realsense2::realsense2
                                      KinectFusion)
