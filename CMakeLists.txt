cmake_minimum_required (VERSION 2.8)
enable_language(CXX)
project(USBRobotAruco)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}  -std=c++0x -msse2")

set(CMAKE_VERBOSE_MAKEFILE OFF)
set(PROJECT_CONTACT sebastien.lengagne@uca.fr)

set (USBROBOTARUCO_VERSION_MAJOR 1)
set (USBROBOTARUCO_VERSION_MINOR 0)

# SET( CMAKE_BUILD_TYPE Release )

foreach(p LIB INCLUDE)
	set(var CMAKE_INSTALL_${p}DIR)
	if(NOT IS_ABSOLUTE "${${var}}")
		set(${var} "${CMAKE_INSTALL_PREFIX}/${${var}}")
	endif()
endforeach()

set(USBROBOTARUCO_GLOBAL_COMPILED "TRUE" CACHE INTERNAL "")

set (INSTALL_BIN_PATH bin )
set (INSTALL_LIB_PATH lib )
set (INSTALL_AR_PATH lib )
set (INSTALL_HEADERS_PATH include )
set (INSTALL_LIB_DIRS ${CMAKE_INSTALL_LIBDIR}lib)

set (LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib/${CMAKE_BUILD_TYPE})


file(	GLOB_RECURSE
	source_USBROBOTARUCO
	src/*.cpp
)

file(	GLOB_RECURSE
	header_USBROBOTARUCO
	include/*.h
	include/*.hxx
)

configure_file ("${PROJECT_SOURCE_DIR}/config_usbrobotaruco.h.in"
				"${PROJECT_SOURCE_DIR}/include/config_usbrobotaruco.h" )

# set the includes
include_directories(	include
						)

link_directories(	${CMAKE_BINARY_DIR}/lib/${CMAKE_BUILD_TYPE}
				)

add_library(	USBROBOTARUCO SHARED
				${source_USBROBOTARUCO})

# target_link_libraries( USBROBOTARUCO
#						)

add_executable( Sample
                "${PROJECT_SOURCE_DIR}/apps/sample.cpp")

### installation of the files
SET(INSTALL_INCLUDE_DIRS ${CMAKE_INSTALL_INCLUDEDIR}/include/USBROBOTARUCO)
SET(INSTALL_LIB_DIRS ${CMAKE_INSTALL_LIBDIR}/lib )

### Configure file
get_property(Include_Directories DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY INCLUDE_DIRECTORIES)
SET(USBROBOTARUCO_INCLUDE_DIRS_LOCAL  ${Include_Directories} CACHE INTERNAL "")
SET(USBROBOTARUCO_INCLUDE_DIRS  ${INSTALL_INCLUDE_DIRS} CACHE INTERNAL "")
SET(USBROBOTARUCO_LIBRARY_DIRS  "${CMAKE_INSTALL_LIBDIR}/lib ${CMAKE_SOURCE_DIR}/Profil-2.0.8/lib" CACHE INTERNAL "")
SET(USBROBOTARUCO_LIBRARIES USBROBOTARUCO Profil Bias ProfilPackages lr CACHE INTERNAL "")
SET(USBROBOTARUCO_FLAGS "-DUSBROBOTARUCO_FOUND" CACHE INTERNAL "")
SET(USBROBOTARUCO_FOUND "TRUE" CACHE INTERNAL "")
configure_file(UsbRobotArucoConfig.cmake.in "${PROJECT_BINARY_DIR}/UsbRobotArucoConfig.cmake" @ONLY)

add_definitions(${USBROBOTARUCO_FLAGS})

install( FILES ${header_USBROBOTARUCO} DESTINATION ${INSTALL_INCLUDE_DIRS})
install( TARGETS USBROBOTARUCO LIBRARY DESTINATION ${INSTALL_LIB_DIRS}  COMPONENT main)

install ( 	FILES "${PROJECT_BINARY_DIR}/USBROBOTARUCOConfig.cmake"
		DESTINATION "${INSTALL_LIB_PATH}/USBROBOTARUCO" COMPONENT dev )


