unset(_HDRS)
unset(_UIS)
unset(_SRCS)
unset(_RSCS)
unset(PLUGIN)

set( PLUGIN messagefilterplugin )
project(${PLUGIN})
cmake_minimum_required(VERSION 3.1.0)
set( CMAKE_AUTOMOC TRUE )

IF( NOT WIN32 )
	set( LIB_SUFFIX "" CACHE STRING "Define suffix of directory name (32/64)" )
	set( PLUGINS_PATH "lib${LIB_SUFFIX}/psi-plus/plugins" CACHE STRING "Install suffix for plugins" )
ELSE()
	set( PLUGINS_PATH "psi-plus/plugins" CACHE STRING "Install suffix for plugins" )
ENDIF()


add_definitions( -DQT_PLUGIN -DHAVE_QT5 )
if( WIN32 )
	add_definitions( -DQ_OS_WIN )
endif( WIN32 )
include_directories(
	${CMAKE_SOURCE_DIR}
	${CMAKE_CURRENT_BINARY_DIR}
	../../include
	.
)
set( _SRCS
	messagefilter.cpp
	options.cpp
)
set( _UIS
	options.ui
)
set( _RSCS
	resources.qrc
)

find_package( Qt5 COMPONENTS Widgets Xml REQUIRED )
set(QT_DEPLIBS
	Qt5::Widgets
	Qt5::Xml
)
qt5_wrap_ui(UIS ${_UIS})
qt5_add_resources(RSCS ${_RSCS})

add_library(
	${PLUGIN}
	MODULE
	${_SRCS}
	${UIS}
	${RSCS}
)
if( UNIX AND NOT( APPLE OR CYGWIN ) )
	target_link_libraries(
		${PLUGIN}
		${QT_DEPLIBS}
	)
	install(
		TARGETS
		${PLUGIN}
		LIBRARY
		DESTINATION
		${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
		RUNTIME DESTINATION
		${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
	)
endif()

if( WIN32 )
	target_link_libraries(
		${PLUGIN}
		${QT_DEPLIBS}
	)
	install(
		TARGETS
		${PLUGIN}
		LIBRARY
		DESTINATION
		${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
		RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
	)
endif()
