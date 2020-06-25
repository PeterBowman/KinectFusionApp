cmake_minimum_required(VERSION 3.1 FATAL_ERROR)

project(KinectFusionApp LANGUAGES CXX)

# Setting some paths
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)

# Path of find modules
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})

# Use modern C++
set(CMAKE_CXX_STANDARD 14)

# Set compiler flags
if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
endif()

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
if(OPENNI2_FOUND)
    include_directories("${OPENNI2_INCLUDE_DIRS}")
    set(HAVE_OPENNI2 TRUE)
endif()

find_package(realsense2)
if(realsense2_FOUND)
    set(HAVE_REALSENSE2 TRUE)
endif()
# ------------------------------------------------

configure_file(${CMAKE_SOURCE_DIR}/cmake/templates/depth_camera_config.h.in
               ${CMAKE_BINARY_DIR}/generated_headers/depth_camera_config.h)

file(GLOB KinectFusionApp_SRCS ${CMAKE_SOURCE_DIR}/src/*.cpp)
add_executable(KinectFusionApp ${KinectFusionApp_SRCS})

target_include_directories(KinectFusionApp PRIVATE ${CMAKE_SOURCE_DIR}/include
                                                   ${CMAKE_BINARY_DIR}/generated_headers)

target_link_libraries(KinectFusionApp opencv_core
                                      opencv_highgui
                                      opencv_imgcodecs
                                      opencv_imgproc
                                      Eigen3::Eigen
                                      KinectFusion)

if(OPENNI2_FOUND)
    target_link_libraries(KinectFusionApp ${OPENNI2_LIBRARIES})
endif()

if(realsense2_FOUND)
    target_link_libraries(KinectFusionApp realsense2::realsense2)
endif()
