# CMake minimun version required to compile
cmake_minimum_required(VERSION 2.6)


# =================================================================
# Project variables and properties
# =================================================================
# Setup these variables and the rest of the CMakeLists.txt won't need more changes.
set(PROJECT_NAME "JsonCpp")
project("${PROJECT_NAME}")

set(PROJECT_LIBRARY_NAME "json")
set(PROJECT_SOURCE_DIR "${CMAKE_SOURCE_DIR}/src")
set(PROJECT_HEADERS_DIR "${CMAKE_SOURCE_DIR}/include")

# Specify build output directories
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib")

# =================================================================
# Macros
# =================================================================
# Check cmake was not run from the source folder
MACRO(MACRO_ENSURE_OUT_OF_SOURCE_BUILD MSG)
    STRING(COMPARE EQUAL "${CMAKE_SOURCE_DIR}"
"${CMAKE_BINARY_DIR}" insource)
    GET_FILENAME_COMPONENT(PARENTDIR ${CMAKE_SOURCE_DIR} PATH)
    STRING(COMPARE EQUAL "${CMAKE_SOURCE_DIR}"
"${PARENTDIR}" insourcesubdir)
    IF(insource OR insourcesubdir)
        MESSAGE(FATAL_ERROR "${MSG}")
    ENDIF(insource OR insourcesubdir)
ENDMACRO(MACRO_ENSURE_OUT_OF_SOURCE_BUILD)

# Find all directories inside a given directory
MACRO(SUBDIRLIST result curdir)
  FILE(GLOB children RELATIVE ${curdir} ${curdir}/*)
  SET(dirlist "")
  FOREACH(child ${children})
    IF(IS_DIRECTORY ${curdir}/${child})
        SET(dirlist ${dirlist} ${child})
    ENDIF()
  ENDFOREACH()
  SET(${result} ${dirlist})
ENDMACRO()

# =================================================================
# Source Files
# =================================================================
cmake_policy( SET CMP0009 NEW)      # Prevents cmake from following symbolic links using GLOB
file(GLOB_RECURSE PROJECT_SOURCE_FILES "${PROJECT_SOURCE_DIR}/*.cpp")

# =================================================================
# Include directories
# =================================================================
SUBDIRLIST(PROJECT_INCLUDE_DIRECTORIES ${PROJECT_HEADERS_DIR})
include_directories(${PROJECT_HEADERS_DIR} ${PROJECT_INCLUDE_DIRECTORIES})
file(GLOB_RECURSE PROJECT_HEADER_FILES "${PROJECT_HEADERS_DIR}/*.h")
set(PROJECT_SOURCE_FILES ${PROJECT_SOURCE_FILES} ${PROJECT_HEADER_FILES})


# =================================================================
# Arguments
# =================================================================
add_definitions (-DPOSIX)
add_definitions (-D_REENTRANT)
add_definitions (-fvisibility=hidden)
if (APPLE)
  add_definitions (-DOSX)
endif ()

# =================================================================
# Link sources
# =================================================================
add_library(${PROJECT_LIBRARY_NAME} ${PROJECT_SOURCE_FILES})

# =================================================================
# Install Target
# =================================================================
# Copy header files
INSTALL(FILES ${PROJECT_HEADER_FILES} DESTINATION ${CMAKE_INSTALL_PREFIX}/include/json)

# Copy library
INSTALL(FILES "${CMAKE_BINARY_DIR}/lib/lib${PROJECT_LIBRARY_NAME}.a" DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
