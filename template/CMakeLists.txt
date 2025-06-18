# This file just takes uses the template file from here and uses it to generate
# the proper .gdextension file for it

# We need the template to use the library
add_custom_target( template
    SOURCES
        template.gdextension.in
)
set( GD_EXTENSION_FILE_INPUT template.gdextension.in )

# so make sure is there 
add_dependencies( ${PROJECT_NAME} template )


# Workaround to add the "lib" prefix to the library in our template file if using MSYS2.
if ( MINGW )
    set( LIB_PREFIX "lib")
endif()

# Generate our project's .gdextension file from the template
set( GD_EXTENSION_FILE ${PROJECT_NAME}.gdextension )
configure_file( ${GD_EXTENSION_FILE_INPUT} ${BUILD_OUTPUT_DIRECTORY}/${GD_EXTENSION_FILE} )

unset( GD_EXTENSION_FILE )
unset( GD_EXTENSION_FILE_INPUT )
unset( LIB_PREFIX )