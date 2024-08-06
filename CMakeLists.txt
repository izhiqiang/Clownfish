cmake_minimum_required(VERSION 3.28)

project(Clownfish VERSION 0.1.0)

# ninja
if(NOT CMAKE_GENERATOR STREQUAL "Ninja")
    message(FATAL_ERROR "This project requires the Ninja generator. \
    Refers to https://cmake.org/cmake/help/latest/manual/cmake-cxxmodules.7.html#generator-support")
endif()

# Get current system time and print the build time
execute_process(COMMAND "date" +"%Y-%m-%d %H:%M.%S" OUTPUT_VARIABLE CURRENT_SYS_TIME)
string(REGEX REPLACE "\n" "" CURRENT_SYS_TIME ${CURRENT_SYS_TIME})
message(STATUS "Build time = ${CURRENT_SYS_TIME}")

# attach additional cmake modules
list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/scripts")

if (NOT CMAKE_BUILD_TYPE)
    if (EXISTS "${CMAKE_SOURCE_DIR}/.git")
        set(default_build_type "RelWithDebInfo")
    else ()
        set(default_build_type "Debug")
    endif ()

    set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE STRING
            "Default BUILD_TYPE is ${default_build_type}" FORCE)
endif ()

message(STATUS "Build type = ${CMAKE_BUILD_TYPE}")

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")

    set(CMAKE_CXX_FLAGS "-Ofast -DNDEBUG")
    set(CMAKE_C_FLAGS  "-Ofast -DNDEBUG")

elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")

    set(CMAKE_CXX_FLAGS "-O2 -g -DNDEBUG")
    set(CMAKE_C_FLAGS  "-O2 -g -DNDEBUG")

elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "Debug")

    set(CMAKE_CXX_FLAGS "-O0 -g")
    set(CMAKE_C_FLAGS "-O0 -g")

    set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fsanitize=address -fno-stack-protector -fno-omit-frame-pointer -fno-var-tracking ")
    #    set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fsanitize=memory -fno-omit-frame-pointer")
    #    set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fsanitize=thread -fno-omit-frame-pointer")
    #    set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fsanitize=undefined -fno-omit-frame-pointer")
    #    set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -fsanitize=cfi -fno-omit-frame-pointer")
    set(CMAKE_DEBUG_POSTFIX "")

else ()
    message(FATAL_ERROR "Only support CMake build type: Debug, RelWithDebInfo, and Release")
endif ()

add_subdirectory(example)

add_subdirectory(src)

add_subdirectory(test)

add_subdirectory(package EXCLUDE_FROM_ALL)
