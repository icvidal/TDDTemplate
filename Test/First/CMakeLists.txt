get_filename_component(CURRENT_DIR_NAME ${CMAKE_CURRENT_SOURCE_DIR} NAME)
message ("Running Test CMake for ${CURRENT_DIR_NAME}")

file(GLOB MODULE_SOURCES "./*.cpp")

set(SUPPORT_SOURCES

)

set(DRIVEN_SOURCES

)
set(${CURRENT_DIR_NAME}_TEST_SOURCES ${DRIVEN_SOURCES} ${MODULE_SOURCES} ${SUPPORT_SOURCES} PARENT_SCOPE)
