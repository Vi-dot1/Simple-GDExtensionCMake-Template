# List all .cpp files on this folder and subfolders on it
file(GLOB_RECURSE SOURCE_FILES CONFIGURE_DEPENDS *.cpp)

# add them to our target library
target_sources(${PROJECT_NAME} PUBLIC ${SOURCE_FILES})

