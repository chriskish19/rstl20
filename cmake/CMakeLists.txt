cmake_minimum_required(VERSION 3.18)
project(readable_stl_20 VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)


if(NOT (CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID STREQUAL "MSVC"))
    message(FATAL_ERROR "Unsupported compiler: ${CMAKE_CXX_COMPILER_ID}")
endif()



# grab all the module files
file(GLOB modules "cpp_modules/*.cppm")
file(GLOB cpp_source_files "cpp_source/*.cpp")

option(BUILD_DLL "Build as a shared library (DLL)" OFF)

if(${BUILD_DLL})
    add_library(${PROJECT_NAME} SHARED ${modules})
    target_sources(${PROJECT_NAME} PRIVATE ${modules})
else()
    # for testing purposes
    add_executable(${PROJECT_NAME} ${cpp_source_files})
    target_sources(${PROJECT_NAME} PRIVATE ${modules})
endif()

# make os specific variable avaliable on cpp side
if(WIN32)
    set(CMAKE_USING_WINDOWS_OS 1)
    target_compile_definitions(${PROJECT_NAME} PRIVATE CMAKE_USING_WINDOWS_OS=${CMAKE_USING_WINDOWS_OS})
elseif(UNIX AND NOT APPLE)
    set(CMAKE_USING_LINUX_OS 2)
    target_compile_definitions(${PROJECT_NAME} PRIVATE CMAKE_USING_LINUX_OS=${CMAKE_USING_LINUX_OS})
elseif(APPLE)
    set(CMAKE_USING_APPLE_OS 3)
    target_compile_definitions(${PROJECT_NAME} PRIVATE CMAKE_USING_APPLE_OS=${CMAKE_USING_APPLE_OS})
endif()

