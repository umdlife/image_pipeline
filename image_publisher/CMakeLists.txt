cmake_minimum_required(VERSION 3.5)
project(image_publisher)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_lifecycle REQUIRED)
find_package(nav2_msgs REQUIRED)
find_package(nav2_util REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(image_transport REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(camera_info_manager REQUIRED)
find_package(OpenCV REQUIRED)
message(STATUS "opencv version ${OpenCV_VERSION}")

include_directories(include)
include_directories( ${OpenCV_INCLUDE_DIRS} )

set(dependencies
  rclcpp
  nav2_util
  nav2_msgs
  rclcpp_lifecycle
  cv_bridge
  image_transport
  sensor_msgs
  camera_info_manager
  OpenCV
)

# Detection Visualizer
add_library(image_publisher SHARED
  src/image_publisher.cpp)
ament_target_dependencies(
  image_publisher
  ${dependencies}
)

add_executable(image_publisher_node src/image_publisher_node.cpp)
target_include_directories(image_publisher_node PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)
ament_target_dependencies(
  image_publisher_node
  "rclcpp"
)
target_link_libraries( image_publisher_node ${OpenCV_LIBS} image_publisher )

install(TARGETS image_publisher_node
  image_publisher
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME}
)


if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
