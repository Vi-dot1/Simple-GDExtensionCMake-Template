#[=======================================================================[.rst:

This file has been built using with the info I could understand by reading the godot-cpp repository and docs
made mostly for personal use, but I'll be happy if it results useful for you

Along with info written by user hristo in thatonegamedev.com:
https://thatonegamedev.com/cpp/cmake/godot-4-gdextension-for-c-using-cmake/

This kinf of a quick and dirty implementation, the bare minimum to get something running
if you want something more robust, I'll recommend asmaloney's template
https://github.com/asmaloney/GDExtensionTemplate

Ideally I will be updating this as I better understand Cmake and GDextension, 
but for the moments this should just work =)



# BEFORE DOING ANYTHING ELSE, GENERATE THE BINDINGS

Remember to get the extension api by executing from the godot binary, 
just type from the terminal whenever you saved it:

    >godot --dump-extension-api

Change godot with whatever name your executable is called.

After that you should generate the bindings generate bindings
]=======================================================================]

cmake_minimum_required(VERSION 4.0)
project(
    Godot-CppCmake
    LANGUAGES CXX
)
message(STATUS "Building shared ${PROJECT_NAME}")
message(STATUS "Using CMake ${CMAKE_VERSION}")



# cuz i'll probably want to test this
set(INSTALL_DIR ${CMAKE_SOURCE_DIR}/demo)
message( STATUS "Install directory: ${INSTALL_DIR}")

# Create compile commands, I mostly use for syntax highlighting with clang
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)


#[[
    Defining our library
]]

# LIB_DIR is where the actual library ends up. 
# This is used in both the build directory and the install directory,
# needs to be consistent with the paths in the gdextension file.
set( LIB_DIR "lib/${CMAKE_SYSTEM_NAME}" )

# Create library
add_library(${PROJECT_NAME} SHARED)
set_target_properties( ${PROJECT_NAME}
    PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${LIB_DIR}"
        LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${LIB_DIR}"
)

if( NOT DEFINED CMAKE_DEBUG_POSTFIX )
    set_target_properties( ${PROJECT_NAME}
        PROPERTIES
            DEBUG_POSTFIX "-d"
    )
endif()

install(
    DIRECTORY ${CMAKE_BINARY_DIR}/bin
    DESTINATION ${INSTALL_DIR}
)

# Add source files
add_subdirectory(src)
target_include_directories(${PROJECT_NAME} PRIVATE src)

#[[
    Linking godot-cpp to our shared library and setting it up
]]

# Generate gdextension config file
add_subdirectory(templates)

# Grab custom extension from this directory
set(GODOTCPP_CUSTOM_API_FILE ${CMAKE_SOURCE_DIR}/extension_api.json)

# build godot-cpp and add it to our library
add_subdirectory(godot-cpp)
target_link_libraries(${PROJECT_NAME} PRIVATE godot-cpp)
add_dependencies(${PROJECT_NAME} godot-cpp)
