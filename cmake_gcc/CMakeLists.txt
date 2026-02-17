# Define minimal required version of CMake.
cmake_minimum_required(VERSION "3.25")

# Project definition
project(
	light-switch-app
	VERSION 1.0
	LANGUAGES C CXX ASM
)

# Include the definition of the slc_light-switch-app target,
# which contains the content of the SLC project
include(light-switch-app.cmake)

add_executable(light-switch-app
    # Add additional sources here
)

target_include_directories(light-switch-app PUBLIC
    # Add additional include paths here
)

target_compile_definitions(light-switch-app PUBLIC
    # Add additional macros here
)

target_compile_options(light-switch-app PUBLIC
    # Set additional compiler flags here
)

target_link_options(light-switch-app PUBLIC
    # Set additional linker flags here
)

# Link with the content defined in the SLC project
target_link_libraries(light-switch-app PRIVATE
    slc
)

# Include managed project content if available
include(light-switch-app_project.cmake OPTIONAL RESULT_VARIABLE managed_project)
if(managed_project)
    message(STATUS "Using managed project content from ${managed_project}")
endif()

# Force the gcc linker command
set_target_properties(light-switch-app PROPERTIES LINKER_LANGUAGE C)

# Create .bin, .hex and .s37 artifacts after building the project
add_custom_command(TARGET light-switch-app
    POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_SREC_CMD} "$<TARGET_FILE:light-switch-app>" "$<TARGET_FILE_DIR:light-switch-app>/$<TARGET_FILE_BASE_NAME:light-switch-app>.s37"
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_IHEX_CMD} "$<TARGET_FILE:light-switch-app>" "$<TARGET_FILE_DIR:light-switch-app>/$<TARGET_FILE_BASE_NAME:light-switch-app>.hex"
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_BIN_CMD}  "$<TARGET_FILE:light-switch-app>" "$<TARGET_FILE_DIR:light-switch-app>/$<TARGET_FILE_BASE_NAME:light-switch-app>.bin" 
)

# Run post-build pipeline to perform additional post-processing
if(post_build_command)
add_custom_command(TARGET light-switch-app
    POST_BUILD
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/..
    COMMAND ${post_build_command}
)
endif()