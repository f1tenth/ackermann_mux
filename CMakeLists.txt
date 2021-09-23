cmake_minimum_required(VERSION 3.16)

project(ackermann_mux)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(ackermann_msgs REQUIRED)
find_package(diagnostic_updater REQUIRED)

add_executable(ackermann_mux
  src/ackermann_mux.cpp
  src/ackermann_mux_node.cpp
  src/ackermann_mux_diagnostics.cpp
)
target_include_directories(ackermann_mux PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)
target_link_libraries(ackermann_mux
  ${rclcpp_TARGETS}
  ${std_msgs_TARGETS}
  ${ackermann_msgs_TARGETS}
  ${diagnostic_updater_TARGETS}
)

install(
  TARGETS ackermann_mux
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME}
)

install(
  PROGRAMS scripts/joystick_relay.py
  DESTINATION lib/${PROJECT_NAME}
)

foreach(dir launch config)
  install(DIRECTORY ${dir}/
    DESTINATION share/${PROJECT_NAME}/${dir})
endforeach()

install(
  DIRECTORY include/
  DESTINATION include
)

if(BUILD_TESTING)
  find_package(launch_testing_ament_cmake)
  add_launch_test(test/test_joystick_relay.py)
endif()

ament_export_include_directories(include)

ament_export_dependencies(
  rclcpp
  std_msgs
  ackermann_msgs
  diagnostic_updater
)

ament_package()
