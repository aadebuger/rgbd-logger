#  minimum required cmake version: 3.1.0
cmake_minimum_required(VERSION 3.1.0)

project(RGB-D-Logger)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Save the command line compile commands in the build output
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
endif()

# include boost
find_package(Boost 1.36.0 COMPONENTS program_options filesystem)
include_directories(${Boost_INCLUDE_DIRS})
link_directories(/usr/local/lib)
# incude opencv
find_package(OpenCV 3.4 REQUIRED)
link_directories(/usr/local/Cellar/opencv/3.4.2/lib)
message(status,${OpenCV_LIBRARY_DIR})
message(status,${OpenCV_LIBS}})

message(status,${OpenCV_LIBRARIES})
# kinect-log
add_executable(kinect-log src/kinect-log.cpp)
target_link_libraries(kinect-log ${DEPENDENCIES} ${Boost_LIBRARIES} ${OpenCV_LIBS} /usr/local/lib/libfreenect2.so)

# rs-log
add_executable(rs-log src/rs-log.cpp)
target_link_libraries(rs-log ${DEPENDENCIES} ${Boost_LIBRARIES} ${OpenCV_LIBS} realsense2 ${OpenCV_LIBRARIES})

# install
install(
    TARGETS

    kinect-log
    rs-log

    RUNTIME DESTINATION
    ${CMAKE_INSTALL_PREFIX}/bin
)
