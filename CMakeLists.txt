cmake_minimum_required(VERSION 3.16)

project(zzawidgets)

set (TARGET zzawidgets)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CPPFILES 
  src/calendar.cpp
  src/calendar_input.cpp
  src/calendar_table.cpp
  src/filename_input.cpp
  src/input_hierch.cpp
  src/password_input.cpp
  src/tabs_nonav.cpp
)

set(HPPFILES
  include/button_input.h
  include/calendar.h
  include/calendar_input.h
  include/calendar_table.h
  include/filename_input.h
  include/input_hierch.h
  include/password_input.h
  include/tabs_nonav.h
)

# Find fltk
set(FLTK_DIR "C:\\Users\\pvros\\source\\repos\\fltk\\build")
set(CMAKE_MODULE_PATH ../ ./)
find_package(FLTK CONFIG REQUIRED)
if (FLTK_FOUND)
  message(STATUS "FLTK found: ${FLTK_VERSION}")
  message(STATUS "FLTK include dir: ${FLTK_INCLUDE_DIR}")
#     set(FLTK_LIBRARIES fltk::fltk fltk::fltk_images)
else()
  message(FATAL_ERROR "FLTK not found")
endif()

include(ExternalProject)

# Add zzacommon
ExternalProject_Add(zzacommon
  GIT_REPOSITORY https://github.com/pvrose/zzacommon
  PREFIX ${CMAKE_BINARY_DIR}/zzacommon
  SOURCE_DIR ${CMAKE_SOURCE_DIR}/zzacommon
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/zzacommon/install
    -DCMAKE_ENABLE_EXPORTS=ON
)

set(CMAKE_BUILD_RPATH ${CMAKE_CURRENT_BINARY_DIR})
add_library(${TARGET} STATIC)
add_dependencies(${TARGET} zzacommon)
target_include_directories(${TARGET} PRIVATE 
  ./include
  ${CMAKE_SOURCE_DIR}/zzacommon/include
  ${FLTK_INCLUDE_DIR}
)
target_sources(${TARGET} 
  PRIVATE 
    ${CPPFILES}
  PUBLIC 
    FILE_SET widget_headers
    TYPE HEADERS
    BASE_DIRS ./include
    FILES ${HPPFILES}
)

install(TARGETS ${TARGET})

