cmake_minimum_required(VERSION 2.8.3)
project(rviz_fps_plugin)

if (EXISTS "/opt/ros/kinetic")
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} /opt/ros/kinetic)
set(CMAKE_INSTALL_PREFIX /opt/ros/kinetic)
elseif(EXISTS "/opt/ros/melodic")
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} /opt/ros/melodic)
set(CMAKE_INSTALL_PREFIX /opt/ros/melodic)
endif()

find_package(catkin REQUIRED COMPONENTS rviz)

link_directories(${catkin_LIBRARY_DIRS})

set( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -std=c++0x" )

## This plugin includes Qt widgets, so we must include Qt like so:
find_package(Qt5 ${rviz_QT_VERSION} EXACT REQUIRED Core Widgets)
#include(${QT_USE_FILE})
set(QT_LIBRARIES Qt5::Widgets)

## I prefer the Qt signals and slots to avoid defining "emit", "slots",
## etc because they can conflict with boost signals, so define QT_NO_KEYWORDS here.
add_definitions(-DQT_NO_KEYWORDS)

## Here we specify which header files need to be run through "moc",
## Qt's meta-object compiler.
qt5_wrap_cpp(MOC_FILES
  include/fps_motion_view_controller.h
  include/fps_motion_tool.h
)

catkin_package(
  DEPENDS
  CATKIN_DEPENDS rviz
  INCLUDE_DIRS
  LIBRARIES
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

# Here we specify the list of source files, including the output of
## the previous command which is stored in ``${MOC_FILES}``.
set(SOURCE_FILES
  src/fps_motion_view_controller.cpp
  src/fps_motion_tool.cpp
  ${MOC_FILES}
)

## An rviz plugin is just a shared library, so here we declare the
## library to be called ``${PROJECT_NAME}``

add_library(${PROJECT_NAME} ${SOURCE_FILES})

## Link the library with whatever Qt libraries have been defined by
## the ``find_package(Qt4 ...)`` line above.
target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} ${catkin_LIBRARIES})

install(TARGETS
  ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(FILES plugin_description.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(DIRECTORY icons
DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
