cmake_minimum_required(VERSION 3.29)
project(lua C)

if (NOT CMAKE_C_COMPILER_ID STREQUAL "MSVC")
    set(CMAKE_C_FLAGS "-Wall -O2 -fno-stack-protector -fno-common -march=native")
else()
    set(CMAKE_C_FLAGS "/Od")
    set(CMAKE_STATIC_LINKER_FLAGS "${CMAKE_STATIC_LINKER_FLAGS} /ignore:4006")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /ignore:4006")
endif()

find_program(MAKE NAMES make make.exe)
if (${MAKE} STREQUAL MAKE-NOTFOUND)
    message(FATAL_ERROR "The original Lua requires 'make' to build some missing headers. Make sure 'make' is installed and in your system's path.")
endif ()

set(CMAKE_C_STANDARD 99)

include(FetchContent)
FetchContent_Declare(lua GIT_REPOSITORY https://github.com/lua/lua.git)
FetchContent_MakeAvailable(lua)

include_directories(${luajit_SOURCE_DIR})

file(GLOB LUA_SRC LIST_DIRECTORIES true ${lua_SOURCE_DIR}/*.[ch])

if(BUILD_SHARED_LIBS)
    add_library(${PROJECT_NAME} SHARED ${LUA_SRC})
else()
    add_library(${PROJECT_NAME} STATIC ${LUA_SRC})
endif()

target_include_directories(${PROJECT_NAME} PUBLIC ${luajit_SOURCE_DIR})
