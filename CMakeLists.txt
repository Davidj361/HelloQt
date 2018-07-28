cmake_minimum_required(VERSION 3.12.0)

project(HelloQt LANGUAGES CXX)

# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)
# Instruct CMake to run moc automatically when needed
set(CMAKE_AUTOMOC ON)
# Create code from a list of Qt designer ui files
set(CMAKE_AUTOUIC ON)

find_package(Qt5 REQUIRED COMPONENTS Core Widgets Gui)

set(project_ui
	mainwindow.ui)

set(project_headers
	mainwindow.h)

set(project_sources
	main.cpp
	mainwindow.cpp)

add_executable(${PROJECT_NAME} ${project_headers} ${project_ui} ${project_sources})
# Use the widgets module from Qt 5
target_link_libraries(${PROJECT_NAME}
	PUBLIC
	Qt5::Core
	Qt5::Gui
	Qt5::Widgets)
	
macro(qt5_copy_dll APP DLL)
    # find the release *.dll file
    get_target_property(Qt5_${DLL}Location Qt5::${DLL} LOCATION)
    # find the debug *d.dll file
    get_target_property(Qt5_${DLL}LocationDebug Qt5::${DLL} IMPORTED_LOCATION_DEBUG)

    add_custom_command(TARGET ${APP} POST_BUILD
       COMMAND ${CMAKE_COMMAND} -E copy_if_different $<$<CONFIG:Debug>:${Qt5_${DLL}LocationDebug}> $<$<NOT:$<CONFIG:Debug>>:${Qt5_${DLL}Location}> $<TARGET_FILE_DIR:${APP}>)
endmacro()

qt5_copy_dll(${PROJECT_NAME} Core)
qt5_copy_dll(${PROJECT_NAME} Gui)
qt5_copy_dll(${PROJECT_NAME} Widgets)