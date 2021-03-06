set(CMAKE_CXX_STANDARD 14)
#
# External dependencies
#
SET(Boost_USE_STATIC_LIBS OFF)
SET(Boost_USE_MULTITHREAD ON)
find_package(Boost
    1.58.0                 # Minimum or EXACT version e.g. 1.36.0
    REQUIRED               # Fail with error if Boost is not found
    COMPONENTS program_options     # Boost libraries by their canonical name
)
cmake_minimum_required(VERSION 3.10)

set(target gameoflife)

# Exit here if required dependencies are not met
message(STATUS "Lib ${target}")

# Set API export file and macro
string(MAKE_C_IDENTIFIER ${target} target_id)
string(TOUPPER ${target_id} target_id)
set(feature_file         "include/${target}/${target}_features.h")
set(export_file          "include/${target}/${target}_export.h")
set(template_export_file "include/${target}/${target}_api.h")
set(export_macro         "${target_id}_API")

# 
# Sources
# 
set(include_path "${CMAKE_CURRENT_SOURCE_DIR}")
set(include_path_thirdparty "${CMAKE_CURRENT_SOURCE_DIR}")
set(source_path  "${CMAKE_CURRENT_SOURCE_DIR}")

set(headers
    ${include_path}/gameoflife.h
    )

set(sources
    ${source_path}/gameoflife.cpp
)

# Group source files
set(header_group "Header Files (API)")
set(source_group "Source Files")

# 
# Create library
# 

# Build library
add_library(${target}# STATIC
    ${sources}
    ${headers}
)

# Create namespaced alias
add_library(${META_PROJECT_NAME}::${target} ALIAS ${target})

# Export library for downstream projects
export(TARGETS ${target} NAMESPACE ${META_PROJECT_NAME}:: FILE ${PROJECT_BINARY_DIR}/cmake/${target}/${target}-export.cmake)

# 
# Project options
# 
set_target_properties(${target}
    PROPERTIES
    ${DEFAULT_PROJECT_OPTIONS}
    FOLDER "${IDE_FOLDER}"
    VERSION ${META_VERSION}
    SOVERSION ${META_VERSION_MAJOR}
)


#
# Include directories
#

target_include_directories(${target}
    PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/include 
    ${CMAKE_CURRENT_SOURCE_DIR}/include/${target}
    ${PROJECT_BINARY_DIR}/source/include
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_BINARY_DIR}/include
    ${CMAKE_CURRENT_BINARY_DIR}
    ${Boost_INCLUDE_DIRS}

    PUBLIC
    ${DEFAULT_INCLUDE_DIRECTORIES}

    INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

# 
# Libraries
# 
#
# > When A links in B as PRIVATE, it is saying that A uses B in its
# > implementation, but B is not used in any part of A's public API.
# 
# > When A links in B as INTERFACE, it is saying that A does not use B in its
# > implementation, but B is used in A's public API.
#
# > When A links in B as PUBLIC, it is essentially a combination of PRIVATE and
# > INTERFACE. It says that A uses B in its implementation and B is also used in
# > A's public API.
target_link_libraries(${target}
    PRIVATE
    ${DEFAULT_LIBRARIES}
    PUBLIC
)

# 
# Compile definitions
# 
target_compile_definitions(${target}
    PRIVATE

    PUBLIC
    $<$<NOT:$<BOOL:${BUILD_SHARED_LIBS}>>:${target_id}_STATIC_DEFINE>
    ${DEFAULT_COMPILE_DEFINITIONS}

    INTERFACE
)

# 
# Compile options
# 
target_compile_options(${target}
    PRIVATE

    PUBLIC
    ${DEFAULT_COMPILE_OPTIONS}

    INTERFACE
)

# 
# Linker options
# 
target_link_libraries(${target}
    PRIVATE

    PUBLIC
    ${DEFAULT_LINKER_OPTIONS}

    INTERFACE
)

# Target name
set(target gameoflife-app)

# Exit here if required dependencies are not met
message(STATUS "Application ${target}")

set(headers
)

set(sources
    ${source_path}/main.cpp
)

#
# Create executable
#

# Build executable
add_executable(${target}
    ${sources}
    ${headers}
)

# Create namespaced alias
add_executable(${META_PROJECT_NAME}::${target} ALIAS ${target})


#
# Project options
#

set_target_properties(${target}
    PROPERTIES
    ${DEFAULT_PROJECT_OPTIONS}
    FOLDER "${IDE_FOLDER}"
)


#
# Include directories
#

target_include_directories(${target}
    PRIVATE
    ${DEFAULT_INCLUDE_DIRECTORIES}
    ${PROJECT_BINARY_DIR}/source/include
)


#
# Libraries
#

target_link_libraries(${target}
    PRIVATE
    ${DEFAULT_LIBRARIES}
    ${META_PROJECT_NAME}::gameoflife
    ${Boost_LIBRARIES}
    PUBLIC
)

#
# Compile definitions
#

target_compile_definitions(${target}
    PRIVATE
    ${DEFAULT_COMPILE_DEFINITIONS}
)


#
# Compile options
#

target_compile_options(${target}
    PRIVATE
    ${DEFAULT_COMPILE_OPTIONS}
)


#
# Linker options
#

target_link_libraries(${target}
    PRIVATE
    ${DEFAULT_LINKER_OPTIONS}
)
