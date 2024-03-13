cmake_minimum_required(VERSION 3.10)

project(msdf-atlas-gen CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_subdirectory(msdfgen)

set(outputdir "bin")

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
endif()

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/${PROJECT_NAME}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/${PROJECT_NAME}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/${PROJECT_NAME}")

file(GLOB_RECURSE SOURCES "msdf-atlas-gen/*.h" "msdf-atlas-gen/*.hpp" "msdf-atlas-gen/*.cpp")

include_directories(
    "msdf-atlas-gen"
    "msdfgen"
    "msdfgen/include"
)

add_library(${PROJECT_NAME} STATIC ${SOURCES})

target_compile_definitions(${PROJECT_NAME} PRIVATE _CRT_SECURE_NO_WARNINGS)

target_link_libraries(${PROJECT_NAME} PRIVATE msdfgen)

# Surpress all warnings for target library (Visual Studio)
target_compile_options(freetype PRIVATE /w)
target_compile_options(msdfgen PRIVATE /w)

if(WIN32)
    target_compile_definitions(${PROJECT_NAME} PRIVATE _CRT_SECURE_NO_WARNINGS)
    target_compile_options(${PROJECT_NAME} PRIVATE /W4)
else()
    target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -pedantic)
endif()

if(CMAKE_BUILD_TYPE MATCHES Debug)
    target_compile_options(${PROJECT_NAME} PRIVATE -g)
    target_compile_definitions(${PROJECT_NAME} PRIVATE DEBUG=1)
elseif(CMAKE_BUILD_TYPE MATCHES Release)
    target_compile_options(${PROJECT_NAME} PRIVATE -O3)
endif()
