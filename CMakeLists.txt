#general info
cmake_minimum_required(VERSION 3.0.0)
set(PROJECT_NAME FightClubGame)
project(${PROJECT_NAME} VERSION 1.0.0)

#common options
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT ${PROJECT_NAME})
set(CMAKE_SUPPRESS_REGENERATION TRUE)

#cpp options
set(CMAKE_CXX_STANDARD 20)

#sources
set(SOURCES Source/main.cpp)

set(MATH_SOURCES Source/Functions/Math.cpp)
set(MATH_INCLUDES Source/Functions/Math.h)

set(DLL_GAME_SOURCES 
Source/Game/Character.cpp 
Source/Game/Weapon.cpp)

set(DLL_GAME_INCLUDES
Source/Game/Character.h
Source/Game/Weapon.h)

set(TEST_SOURCE Source/Tests/main.cpp)

source_group("Source files/Game" FILES "Source/Game/Character.cpp" "Source/Game/Weapon.cpp")
source_group("Header files/Game" FILES "Source/Game/Character.h" "Source/Game/Weapon.h")

#targets
add_executable(${PROJECT_NAME} ${SOURCES})
add_library(MathLib STATIC ${MATH_SOURCES} ${MATH_INCLUDES})
add_library(${PROJECT_NAME}Lib SHARED ${DLL_GAME_SOURCES} ${DLL_GAME_INCLUDES})
add_executable(${PROJECT_NAME}TestRunner  ${TEST_SOURCE})

target_compile_definitions(${PROJECT_NAME}Lib PRIVATE _EXPORTING)
target_compile_definitions(${PROJECT_NAME} PUBLIC _IMPORTING)

option(SKIP_GAME_OPTION "Skip game option" OFF)

if(SKIP_GAME_OPTION)
    target_compile_definitions(${PROJECT_NAME} PUBLIC SKIP_GAME)
endif()

target_link_libraries(${PROJECT_NAME} PRIVATE ${PROJECT_NAME}Lib)
target_link_libraries(${PROJECT_NAME}TestRunner PRIVATE ${PROJECT_NAME}Lib)
target_link_libraries(${PROJECT_NAME}TestRunner PRIVATE MathLib)
target_link_libraries(${PROJECT_NAME}Lib PRIVATE MathLib)

#includes
set(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}TestRunner ${PROJECT_NAME}Lib)
    
foreach(target IN LISTS TARGETS)
    target_include_directories(${target} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/Source)
endforeach()


if(MSVC)
    message("Using MSVC")
    message("MSVC Version: ${MSVC_VERSION}")
    message("MSVC Toolset version: ${MSVC_TOOLSET_VERSION}")
    message("Compiler use: ${CMAKE_CXX_COMPILER_ID}")
    target_compile_options(${PROJECT_NAME} PRIVATE /FAc)
endif()

message("---------- Project files generating for ${PROJECT_NAME} ----------")
message("compiler flags: ${CMAKE_CXX_FLAGS_DEBUG}")