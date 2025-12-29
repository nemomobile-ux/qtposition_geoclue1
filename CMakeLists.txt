cmake_minimum_required(VERSION 3.16)
project(qtposition_geoclue LANGUAGES CXX)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

set(name qtposition_geoclue)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Qt6 REQUIRED COMPONENTS
    Core
    DBus
    Positioning
)

set(SOURCES
    geocluetypes.cpp
    qgeocluemaster.cpp
    qgeopositioninfosourcefactory_geoclue.cpp
    qgeopositioninfosource_geocluemaster.cpp
    qgeosatelliteinfosource_geocluemaster.cpp
)

set(HEADERS
    geocluetypes.h
    qgeocluemaster.h
    qgeopositioninfosourcefactory_geoclue.h
    qgeopositioninfosource_geocluemaster.h
    qgeosatelliteinfosource_geocluemaster.h
)

find_package(PkgConfig REQUIRED)
pkg_check_modules(GLIB REQUIRED glib-2.0 gio-2.0)

set(DBUS_DIR ${CMAKE_SOURCE_DIR}/dbus)
set(GEN_DIR ${CMAKE_BINARY_DIR}/generated)
file(MAKE_DIRECTORY ${GEN_DIR})

set_source_files_properties("${DBUS_DIR}/org.freedesktop.Geoclue.xml"
    PROPERTIES NO_NAMESPACE true
    INCLUDE "${CMAKE_SOURCE_DIR}/geocluetypes.h")
qt_add_dbus_interface(SOURCES "${DBUS_DIR}/org.freedesktop.Geoclue.xml" "geoclueinterface" )
qt_add_dbus_interface(SOURCES "${DBUS_DIR}/org.freedesktop.Geoclue.Master.xml" "masterinterface" )
qt_add_dbus_interface(SOURCES "${DBUS_DIR}/org.freedesktop.Geoclue.MasterClient.xml" "masterclientinterface" )
set_source_files_properties("${DBUS_DIR}/org.freedesktop.Geoclue.Position.xml"
    PROPERTIES NO_NAMESPACE true
    INCLUDE "${CMAKE_SOURCE_DIR}/geocluetypes.h")
qt_add_dbus_interface(SOURCES "${DBUS_DIR}/org.freedesktop.Geoclue.Position.xml" "positioninterface" )
set_source_files_properties("${DBUS_DIR}/org.freedesktop.Geoclue.Satellite.xml"
    PROPERTIES NO_NAMESPACE true
    INCLUDE "${CMAKE_SOURCE_DIR}/geocluetypes.h")
qt_add_dbus_interface(SOURCES "${DBUS_DIR}/org.freedesktop.Geoclue.Satellite.xml" "satelliteinterface" )
qt_add_dbus_interface(SOURCES "${DBUS_DIR}/org.freedesktop.Geoclue.Velocity.xml" "velocityinterface" )

add_library(${name} SHARED
    ${SOURCES}
    ${HEADERS}
    plugin.json
)

target_include_directories(${name} PRIVATE
    ${CMAKE_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
)

target_link_libraries(${name} PRIVATE
    Qt6::Core
    Qt6::DBus
    Qt6::Positioning
)

set (DISTFILES
    dbus/org.freedesktop.Geoclue.MasterClient.xml
    dbus/org.freedesktop.Geoclue.Master.xml
    dbus/org.freedesktop.Geoclue.Position.xml
    dbus/org.freedesktop.Geoclue.Satellite.xml
    dbus/org.freedesktop.Geoclue.Velocity.xml
    dbus/org.freedesktop.Geoclue.xml)

install(TARGETS ${name}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/qt6/plugins/position/
)
