project (jsoncpp)

cmake_minimum_required(VERSION 2.8)

include_directories (
  include
)

set (SOURCES
  src/lib_json/json_reader.cpp
  src/lib_json/json_value.cpp
  src/lib_json/json_writer.cpp
)

add_definitions (-DPOSIX)
add_definitions (-D_REENTRANT)
add_definitions (-fvisibility=hidden)
if (APPLE)
  add_definitions (-DOSX)
endif ()

add_library (json ${SOURCES})
install (TARGETS json DESTINATION lib)
install (DIRECTORY include/json/
         DESTINATION include/json
         FILES_MATCHING PATTERN "*.h")
