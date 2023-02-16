cmake_minimum_required(VERSION 3.5 FATAL_ERROR)
project(librs232 VERSION 1.0.0 LANGUAGES C)

##############################################
# Find system dependencies
# None

##############################################
# Create target and set properties
add_library(rs232 src/rs_232.c)

# Add an alias so that library can be used inside the build tree, e.g. when testing
add_library(RS232::rs232 ALIAS rs232)

#Set target properties
target_include_directories(rs232
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

target_compile_features(rs232 PRIVATE c_std_99)
target_compile_options(rs232 PRIVATE
  $<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>:
  -Wall -Wextra -Wpedantic>)

# We don't have dependencies, this is here for documentation
#target_link_libraries(rs232
#    PUBLIC
#        Boost::headers RapidJSON::RapidJSON
#    PRIVATE
#        Boost::regex
#)

##############################################
# Installation instructions

include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/RS232)

install(TARGETS rs232
    EXPORT rs232-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

#This is required so that the exported target has the name RS232 and not rs232
set_target_properties(rs232 PROPERTIES EXPORT_NAME RS232)

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Export the targets to a script
install(EXPORT rs232-targets
    FILE
        RS232Targets.cmake
    NAMESPACE
        RS232::
    DESTINATION
        ${INSTALL_CONFIGDIR}
)

# Create a ConfigVersion.cmake file
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/RS232ConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/RS232Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/RS232Config.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

# Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/RS232Config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/RS232ConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

##############################################
## Exporting from the build tree
#configure_file(${CMAKE_CURRENT_LIST_DIR}/cmake/FindRapidJSON.cmake
#    ${CMAKE_CURRENT_BINARY_DIR}/FindRapidJSON.cmake
#    COPYONLY)

export(EXPORT rs232-targets
    FILE ${CMAKE_CURRENT_BINARY_DIR}/RS232Targets.cmake
    NAMESPACE RS232::)

#Register package in the User Package Registry
export(PACKAGE RS232)

##############################################
## Add test
# Currently none
