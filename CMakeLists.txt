cmake_minimum_required(VERSION 3.20min)

project (lua)

include_directories ("${PROJECT_SOURCE_DIR}/src")

if (${USE_MSVC_PROJECT})
	file (GLOB_RECURSE file_list
		LIST_DIRECTORIES false
		"${PROJECT_SOURCE_DIR}/src/*.h"
		"${PROJECT_SOURCE_DIR}/src/*.hpp"
		"${PROJECT_SOURCE_DIR}/src/*.c")
else ()
	file (GLOB_RECURSE file_list
		LIST_DIRECTORIES false
		"${PROJECT_SOURCE_DIR}/src/*.c")
endif (${USE_MSVC_PROJECT})

add_definitions(
	-D_CRT_SECURE_NO_WARNINGS)

if (${ANDROID_BUILD})
	message(STATUS lua " will be built using Android configs")
	add_definitions (
		-DPLATFORM_POSIX)

	# platform abi
	if (${ANDROID_ABI} STREQUAL "arm64-v8a")
		message(STATUS lua " Android ABI: arm64")
		add_definitions (
			-DPOSIX64)
	else ()
		message(STATUS lua " Android ABI: arm")
		add_definitions (
			-DPOSIX32)
	endif (${ANDROID_ABI} STREQUAL "arm64-v8a")
else ()
	message(STATUS lua " will be built using Windows configs")
	add_definitions (
		-DPLATFORM_WINDOWS)
endif (${ANDROID_BUILD})

# if (${USE_MSVC_PROJECT})
	# set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /TP")
	# set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /TP")
# endif()

add_library (lua ${file_list})

target_include_directories (lua PUBLIC
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>)

if (${USE_MSVC_PROJECT})
	# organize filters
	foreach(_source IN ITEMS ${file_list})
		get_filename_component(_source_path "${_source}" PATH)
		file(RELATIVE_PATH _source_path_rel "${PROJECT_SOURCE_DIR}" "${_source_path}")
		string(REPLACE "/" "\\" _group_path "${_source_path_rel}")
		source_group("${_group_path}" FILES "${_source}")
	endforeach()
endif (${USE_MSVC_PROJECT})
