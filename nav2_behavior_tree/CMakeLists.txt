cmake_minimum_required(VERSION 3.5)
project(nav2_behavior_tree CXX)

find_package(ament_cmake REQUIRED)
find_package(nav2_common REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_action REQUIRED)
find_package(rclcpp_lifecycle REQUIRED)
find_package(std_msgs REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav2_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(behaviortree_cpp_v3 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)
find_package(nav2_util REQUIRED)

nav2_package()

include_directories(
  include
)

set(library_name ${PROJECT_NAME})

set(dependencies
  rclcpp
  rclcpp_action
  rclcpp_lifecycle
  geometry_msgs
  nav2_msgs
  nav_msgs
  behaviortree_cpp_v3
  tf2
  tf2_ros
  tf2_geometry_msgs
  std_msgs
  std_srvs
  nav2_util
)

add_library(${library_name} SHARED
  src/behavior_tree_engine.cpp
)

ament_target_dependencies(${library_name}
  ${dependencies}
)

add_library(nav2_compute_path_to_pose_action_bt_node SHARED plugins/action/compute_path_to_pose_action.cpp)
list(APPEND plugin_libs nav2_compute_path_to_pose_action_bt_node)

add_library(nav2_follow_path_action_bt_node SHARED plugins/action/follow_path_action.cpp)
list(APPEND plugin_libs nav2_follow_path_action_bt_node)

add_library(nav2_back_up_action_bt_node SHARED plugins/action/back_up_action.cpp)
list(APPEND plugin_libs nav2_back_up_action_bt_node)

add_library(nav2_spin_action_bt_node SHARED plugins/action/spin_action.cpp)
list(APPEND plugin_libs nav2_spin_action_bt_node)

add_library(nav2_wait_action_bt_node SHARED plugins/action/wait_action.cpp)
list(APPEND plugin_libs nav2_wait_action_bt_node)

add_library(nav2_clear_costmap_service_bt_node SHARED plugins/action/clear_costmap_service.cpp)
list(APPEND plugin_libs nav2_clear_costmap_service_bt_node)

add_library(nav2_is_stuck_condition_bt_node SHARED plugins/condition/is_stuck_condition.cpp)
list(APPEND plugin_libs nav2_is_stuck_condition_bt_node)

add_library(nav2_goal_reached_condition_bt_node SHARED plugins/condition/goal_reached_condition.cpp)
list(APPEND plugin_libs nav2_goal_reached_condition_bt_node)

add_library(nav2_initial_pose_received_condition_bt_node SHARED plugins/condition/initial_pose_received_condition.cpp)
list(APPEND plugin_libs nav2_initial_pose_received_condition_bt_node)

add_library(nav2_reinitialize_global_localization_service_bt_node SHARED plugins/action/reinitialize_global_localization_service.cpp)
list(APPEND plugin_libs nav2_reinitialize_global_localization_service_bt_node)

add_library(nav2_rate_controller_bt_node SHARED plugins/decorator/rate_controller.cpp)
list(APPEND plugin_libs nav2_rate_controller_bt_node)

add_library(nav2_recovery_node_bt_node SHARED plugins/control/recovery_node.cpp)
list(APPEND plugin_libs nav2_recovery_node_bt_node)

add_library(nav2_navigate_to_pose_action_bt_node SHARED plugins/action/navigate_to_pose_action.cpp)
list(APPEND plugin_libs nav2_navigate_to_pose_action_bt_node)

add_library(nav2_random_crawl_action_bt_node SHARED plugins/action/random_crawl_action.cpp)
list(APPEND plugin_libs nav2_random_crawl_action_bt_node)

add_library(nav2_pipeline_sequence_bt_node SHARED plugins/control/pipeline_sequence.cpp)
list(APPEND plugin_libs nav2_pipeline_sequence_bt_node)

add_library(nav2_round_robin_node_bt_node SHARED plugins/control/round_robin_node.cpp)
list(APPEND plugin_libs nav2_round_robin_node_bt_node)

foreach(bt_plugin ${plugin_libs})
  ament_target_dependencies(${bt_plugin} ${dependencies})
  target_compile_definitions(${bt_plugin} PRIVATE BT_PLUGIN_EXPORT)
endforeach()

install(TARGETS ${library_name}
                ${plugin_libs}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib/${PROJECT_NAME}
)

install(DIRECTORY include/
  DESTINATION include/
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_export_include_directories(include)

ament_export_libraries(
  ${library_name}
  ${plugin_libs}
)

ament_export_dependencies(${dependencies})

ament_package()
