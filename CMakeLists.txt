cmake_minimum_required(VERSION 3.12 FATAL_ERROR)

set(AUDEO_DEPENDENCIES_FOLDER_NAME "dependencies")
set(DEBUG_OUTPUT_DIRECTORY "bin/debug")
set(RELEASE_OUTPUT_DIRECTORY "bin/release")

project(audeo)
enable_language(CXX)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD 17)

set(AUDEO_COMPILE_FLAGS "")

# Release build by default
if (NOT CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE "Release")
	message(STATUS "Audeo: Build type not specified: Using Release by default.")
endif(NOT CMAKE_BUILD_TYPE)

add_definitions(
	-DMBCS
	${AUDEO_COMPILE_FLAGS}
)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/${DEBUG_OUTPUT_DIRECTORY}")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/${DEBUG_OUTPUT_DIRECTORY}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/${DEBUG_OUTPUT_DIRECTORY}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/${RELEASE_OUTPUT_DIRECTORY}")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/${RELEASE_OUTPUT_DIRECTORY}")
set(CMAKE_EXECUTABLE_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/${RELEASE_OUTPUT_DIRECTORY}")

if (MSVC)
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /W3 /MD /Od /GR /EHsc")
   	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /W3 /GL /Od /Oi /GR /Gy /EHsc")
else()
	if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
       		set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
   	endif()
endif()

add_subdirectory("dependencies")
add_subdirectory("include")
add_subdirectory("src")

add_executable(audeo
	${AUDEO_HEADER_FILES}
	${AUDEO_SOURCE_FILES}
)

set_target_properties(audeo PROPERTIES FOLDER "audeo")
source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}" FILES ${AUDEO_HEADER_FILES} ${AUDEO_SOURCE_FILES})

target_include_directories(audeo
	PUBLIC ${AUDEO_PUBLIC_INCLUDE_DIRECTORIES}
)

set(LINK_LIBRARIES SDL2)

target_link_libraries(audeo ${LINK_LIBRARIES})