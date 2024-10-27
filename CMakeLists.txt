cmake_minimum_required(VERSION 3.29)
project(lua C)

if (CMAKE_SYSTEM_NAME STREQUAL "Windows")
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS True)
endif ()

if (NOT CMAKE_C_COMPILER_ID STREQUAL "MSVC")
    set(CMAKE_C_FLAGS "-Wall -O2 -fno-stack-protector -fno-common -march=native")
else()
    set(CMAKE_C_FLAGS "/Od")
    set(CMAKE_STATIC_LINKER_FLAGS "${CMAKE_STATIC_LINKER_FLAGS} /ignore:4006")
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /ignore:4006")
endif()

set(CMAKE_C_STANDARD 99)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/lua)

file(GLOB LUA_SRC LIST_DIRECTORIES true ${CMAKE_CURRENT_SOURCE_DIR}/lua/*.[ch])
list(REMOVE_ITEM LUA_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lua/lua.c)
list(REMOVE_ITEM LUA_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lua/lua.h)
list(REMOVE_ITEM LUA_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lua/luac.c)
list(REMOVE_ITEM LUA_SRC ${CMAKE_CURRENT_SOURCE_DIR}/lua/onelua.c)

if(BUILD_SHARED_LIBS)
    add_library(${PROJECT_NAME} SHARED ${LUA_SRC})
else()
    add_library(${PROJECT_NAME} STATIC ${LUA_SRC})
endif()

target_compile_definitions(${PROJECT_NAME} PRIVATE MAKE_LIB=1)
target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/lua)
