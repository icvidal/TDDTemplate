message("Running Test CMake")

set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)
add_compile_options(-O0 -g)

# (1) Look for installed version of CppUTest
if (DEFINED ENV{CPPUTEST_HOME})
    message(STATUS "Using CppUTest home: $ENV{CPPUTEST_HOME}")
    set(CPPUTEST_INCLUDE_DIRS $ENV{CPPUTEST_HOME}/include)
    set(CPPUTEST_LIBRARIES $ENV{CPPUTEST_HOME}/lib)
    set(CPPUTEST_LDFLAGS CppUTest CppUTestExt)
else ()
    message(STATUS "\$CPPUTEST_HOME not defined, using PkgConfig")
    find_package(PkgConfig REQUIRED)
    pkg_search_module(CPPUTEST REQUIRED cpputest>=3.8)
    message(STATUS "Found CppUTest version ${CPPUTEST_VERSION}")
endif ()


# (2) Our unit tests sources
set(TEST_APP_NAME ${PROJECT_NAME}_tests)

FILE(GLOB TEST_COMPONENTS "*")
list(REMOVE_ITEM TEST_COMPONENTS ${CMAKE_CURRENT_SOURCE_DIR}/CMakeLists.txt ${CMAKE_CURRENT_SOURCE_DIR}/testMain.cpp)

foreach (component IN LISTS TEST_COMPONENTS)
    get_filename_component(base_name ${component} NAME)
    list(APPEND COMPONENTS ${base_name})
endforeach ()
message(STATUS "Found the following components: ${TEST_COMPONENTS}")
message(STATUS "And this is the component list: ${COMPONENTS}")

foreach (component IN LISTS COMPONENTS)
    add_subdirectory("${component}")
    list(APPEND TEST_SOURCES ${${component}_TEST_SOURCES})
    list(APPEND TEST_INCLUDES ${${component}_TEST_INCLUDES})
endforeach ()

list(APPEND TEST_SOURCES ${TEST_FILES}
        testMain.cpp
)

# (3) Take care of CppUTest include directories
include_directories(BEFORE Mocks
        ${CPPUTEST_INCLUDE_DIRS}
        Test/include ${TEST_INCLUDES}
        ../include
)
#    include_directories(${CPPUTEST_INCLUDE_DIRS} Test/include ${TEST_INCLUDES} ../App/include ../App/AddonBoards/include ../App/Hardware/include ../App/Hal/include)
link_directories(${CPPUTEST_LIBRARIES})

## (4) Build the unit tests objects and link then with the app library
add_executable(${TEST_APP_NAME} ${TEST_SOURCES})
target_compile_options(${TEST_APP_NAME} PRIVATE -Wall -Werror -O0 -g)

target_link_libraries(${TEST_APP_NAME}  ${CPPUTEST_LDFLAGS})
##message(STATUS "Includes ${CMAKE_PROJECT_INCLUDE}")
#
## (5) Run the test once the build is done
#add_custom_command(TARGET ${TEST_APP_NAME} COMMAND ./${TEST_APP_NAME} -c POST_BUILD || (exit 0))
