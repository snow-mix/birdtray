cmake_minimum_required(VERSION 3.1.0)

project(Birdtray CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

find_package(SQLite3 REQUIRED)
find_package(Qt5Core CONFIG REQUIRED)
find_package(Qt5Gui CONFIG REQUIRED)
find_package(Qt5Widgets CONFIG REQUIRED)
set(REQUIRED_MODULES ${SQLITE3_LIBRARIES} Qt5::Core Qt5::Gui Qt5::Widgets)

set(PLATFORM_SOURCES)
set(PLATFORM_HEADERS)
set(EXECUTABLE_OPTIONS)

if (WIN32)
    set(EXECUTABLE_OPTIONS WIN32)
    ENABLE_LANGUAGE(RC)
    add_definitions(-DUNICODE)
    set(PLATFORM_SOURCES src/windowtools_win.cpp src/birdtrayeventfilter.cpp src/res/birdtray.rc)
    set(PLATFORM_HEADERS src/windowtools_win.h src/birdtrayeventfilter.h)
else()
    find_package(Qt5X11Extras QUIET)
    find_library(X11 X11)
    list(APPEND REQUIRED_MODULES Qt5::X11Extras ${X11})
    set(PLATFORM_SOURCES src/windowtools_x11.cpp)
    set(PLATFORM_HEADERS src/windowtools_x11.h)
endif (WIN32)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
include_directories(${SQLITE3_INCLUDE_DIRS} src)
add_definitions(-DQT_DEPRECATED_WARNINGS)

set(SOURCES
        src/colorbutton.cpp
        src/databaseaccounts.cpp
        src/databaseunreadfixer.cpp
        src/dialogaddeditaccount.cpp
        src/dialogaddeditnewemail.cpp
        src/dialogsettings.cpp
        src/main.cpp
        src/modelaccounttree.cpp
        src/modelnewemails.cpp
        src/morkparser.cpp
        src/setting_newemail.cpp
        src/settings.cpp
        src/sqlite_statement.cpp
        src/trayicon.cpp
        src/unreadcounter.cpp
        src/utils.cpp
        src/windowtools.cpp
        src/dialogaddeditaccount.ui
        src/dialogaddeditnewemail.ui
        src/dialogsettings.ui)

set(HEADERS
        src/colorbutton.h
        src/databaseaccounts.h
        src/databaseunreadfixer.h
        src/dialogaddeditaccount.h
        src/dialogaddeditnewemail.h
        src/dialogsettings.h
        src/modelaccounttree.h
        src/modelnewemails.h
        src/morkparser.h
        src/setting_newemail.h
        src/settings.h
        src/sqlite_statement.h
        src/trayicon.h
        src/unreadcounter.h
        src/utils.h
        src/version.h
        src/windowtools.h)

set(RESOURCES src/resources.qrc)
source_group("Resources" FILES ${RESOURCES})
qt5_add_resources(RESOURCES_SOURCES ${RESOURCES})
set_source_files_properties(${RESOURCES_SOURCES} PROPERTIES GENERATED ON)

add_executable(birdtray ${EXECUTABLE_OPTIONS}
        ${SOURCES} ${PLATFORM_SOURCES} ${RESOURCES_SOURCES} ${HEADERS} ${PLATFORM_HEADERS})
target_link_libraries(birdtray ${REQUIRED_MODULES})
