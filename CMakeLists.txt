cmake_minimum_required(VERSION 3.10)

project(msdf-atlas-gen CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Link msdfgen
add_subdirectory(msdfgen)

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
endif()

file(GLOB_RECURSE SOURCES "msdf-atlas-gen/*.h" "msdf-atlas-gen/*.hpp" "msdf-atlas-gen/*.cpp")

include_directories(msdf-atlas-gen PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/msdf-atlas-gen
	${CMAKE_CURRENT_SOURCE_DIR}/msdfgen
	${CMAKE_CURRENT_SOURCE_DIR}/msdfgen/include
)

add_library(msdf-atlas-gen STATIC ${SOURCES})

target_compile_definitions(msdf-atlas-gen PRIVATE _CRT_SECURE_NO_WARNINGS)

target_link_libraries(msdf-atlas-gen PUBLIC msdfgen)

# Surpress all warnings for msdfgen (Visual Studio)
target_compile_options(msdfgen PUBLIC /w)

if(WIN32)
    target_compile_definitions(msdf-atlas-gen PRIVATE _CRT_SECURE_NO_WARNINGS)
    target_compile_options(msdf-atlas-gen PRIVATE /W4)
else()
    target_compile_options(msdf-atlas-gen PRIVATE -Wall -Wextra -pedantic)
endif()

if(CMAKE_BUILD_TYPE MATCHES Debug)
    target_compile_options(msdf-atlas-gen PRIVATE -g)
    target_compile_definitions(msdf-atlas-gen PRIVATE DEBUG=1)
elseif(CMAKE_BUILD_TYPE MATCHES Release)
    target_compile_options(msdf-atlas-gen PRIVATE -O3)
endif()
