cmake_minimum_required(VERSION 2.8.3)
project(waterplus_map_tools)

find_package(catkin REQUIRED COMPONENTS message_generation rviz roscpp geometry_msgs std_msgs tf visualization_msgs move_base_msgs actionlib cmake_modules )

add_service_files(FILES
  SaveWaypoints.srv
  AddNewWaypoint.srv
  GetNumOfWaypoints.srv
  GetWaypointByIndex.srv
  GetWaypointByName.srv
)

add_message_files(FILES
  Waypoint.msg
)

generate_messages(DEPENDENCIES
  std_msgs
  geometry_msgs
)

catkin_package(
  CATKIN_DEPENDS message_runtime
  INCLUDE_DIRS include
  LIBRARIES waterplus_map_tools 
  DEPENDS TinyXML
  )


include_directories(include ${catkin_INCLUDE_DIRS} ${TinyXML_INCLUDE_DIRS})
link_directories(${catkin_LIBRARY_DIRS})

set(CMAKE_AUTOMOC ON)
if(rviz_QT_VERSION VERSION_LESS "5")
  message(STATUS "Using Qt4 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt4 ${rviz_QT_VERSION} EXACT REQUIRED QtCore QtGui)
  include(${QT_USE_FILE})
else()
  message(STATUS "Using Qt5 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt5 ${rviz_QT_VERSION} EXACT REQUIRED Core Widgets)
  set(QT_LIBRARIES Qt5::Widgets)
endif()
add_definitions(-DQT_NO_KEYWORDS)

find_package(TinyXML REQUIRED)

add_library(${PROJECT_NAME} 
    include/add_waypoint_tool.h
    src/add_waypoint_tool.cpp
)
add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} ${catkin_LIBRARIES})

add_executable(wp_manager
  src/wp_manager.cpp
)
add_dependencies(wp_manager ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(wp_manager
  ${catkin_LIBRARIES} ${TinyXML_LIBRARIES}
)

## Install rules

install(TARGETS
  ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(FILES
  waterplus_map_tools_plugin.xml
DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})


add_executable(wp_nav_test
  src/wp_nav_test.cpp
)
add_dependencies(wp_nav_test ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(wp_nav_test
  ${catkin_LIBRARIES}
)

add_executable(wp_saver
  src/wp_saver.cpp
)
add_dependencies(wp_saver ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(wp_saver
  ${catkin_LIBRARIES}
)