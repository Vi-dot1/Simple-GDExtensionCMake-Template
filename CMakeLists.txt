cmake_minimum_required(VERSION 4.0)
project(
    Godot-CppCmake # Your project name here!
    LANGUAGES CXX
)

# Turn on link time optimization for everything on release versions
set( CMAKE_INTERPROCEDURAL_OPTIMIZATION_RELEASE ON )

# Change this to set where the library is copied when installing
# preferably, inside a Godot project
set(INSTALL_DIR ${CMAKE_SOURCE_DIR}/demo)
message( STATUS "Install directory: ${INSTALL_DIR}")

# Defines the function used in your code to initialize your module
# since I picked up the example directly from the docs, I'm using that value
set(ENTRY_SYMBOL "example_library_init")

# Create compile commands, useful for debugging but I mostly just use it for syntax highlighting, 
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
# so copy it over to the root of the repo for that
file(COPY ${CMAKE_BINARY_DIR}/compile_commands.json DESTINATION ${CMAKE_SOURCE_DIR})


#[[
 +-------------------------------------------+
 |            Defining our library           |
 +-------------------------------------------+
]]

# Where we are going to save any file related to our library
set( BUILD_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin )
message( STATUS "Output directory: ${BUILD_OUTPUT_DIRECTORY}")

# LIB_DIR is where the actual library ends up. 
# needs to be consistent with the paths in the gdextension file.
set( LIB_DIR "lib/${CMAKE_SYSTEM_NAME}" )


# Create library
add_library(${PROJECT_NAME} SHARED)
set_target_properties( ${PROJECT_NAME}
    PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${BUILD_OUTPUT_DIRECTORY}/${LIB_DIR}"
        LIBRARY_OUTPUT_DIRECTORY "${BUILD_OUTPUT_DIRECTORY}/${LIB_DIR}"
)

# If is a debug build, add a "-d" at the end of the library's name to denote this
if( NOT DEFINED CMAKE_DEBUG_POSTFIX )
    set_target_properties( ${PROJECT_NAME}
        PROPERTIES DEBUG_POSTFIX "-d"
    )
endif()

# Add source files, 
add_subdirectory(src)

# if you prefer to separate your src files from your headers, 
# just make an include folder and change the line bellow to use it instead
target_include_directories(${PROJECT_NAME} PRIVATE src)


#[[
 +-------------------------------------------+
 |  Linking godot-cpp to our shared library  |
 +-------------------------------------------+
]] 

# Generate gdextension config file
add_subdirectory(template)


# Grab custom extension if any,
# in case this variable is not defined, godot-cpp will default to the one in:
# ./godot-cpp/gdextension/extension_api.json
if(EXISTS ${CMAKE_SOURCE_DIR}/extension_api.json)
    set(GODOTCPP_CUSTOM_API_FILE ${CMAKE_SOURCE_DIR}/extension_api.json)
    message("Using bindings provided on repo")
else()
    message("Using default bindings from godot-cpp")
endif()

# Build godot-cpp and add it to our library
add_subdirectory(godot-cpp)
target_link_libraries(${PROJECT_NAME} PRIVATE godot-cpp)

# Make sure we include the bindings with our library
add_dependencies(${PROJECT_NAME} godot-cpp)


# Install, just copies the bin file from build and puts in the install directory
install(
    DIRECTORY ${CMAKE_BINARY_DIR}/bin
    DESTINATION ${INSTALL_DIR}
)