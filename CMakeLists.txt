cmake_minimum_required(VERSION 3.15)

set(ARTERY_VERSION 1.1)

project(artery-font LANGUAGES CXX VERSION ${ARTERY_VERSION})

option(ARTERY_INSTALL "Install artery as a separated project" OFF)

file(GLOB_RECURSE ARTERY_C_HEADERS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.h")
file(GLOB_RECURSE ARTERY_CPP_HEADERS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.hpp")

add_library(artery-font INTERFACE ${ARTERY_C_HEADERS} ${ARTERY_CPP_HEADERS})
target_include_directories(artery-font INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/artery-font> $<INSTALL_INTERFACE:./include>)

if(ARTERY_INSTALL)
    include(GNUInstallDirs)
    include(CMakePackageConfigHelpers)
    set(ARTERY_CONFIG_PATH "lib/cmake/artery-font")

    # install tree package config
    write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/artery-font-config-version.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY SameMajorVersion
    )

    configure_package_config_file(
        cmake/artery-font-config.cmake.in
        ${ARTERY_CONFIG_PATH}/artery-font-config.cmake
        INSTALL_DESTINATION ${ARTERY_CONFIG_PATH}
        NO_CHECK_REQUIRED_COMPONENTS_MACRO
    )

    # build tree package config
    configure_file(
        cmake/artery-font-config.cmake.in
        artery-font-config.cmake
        @ONLY
    )

    install(TARGETS artery-font EXPORT artery-font
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        FRAMEWORK DESTINATION ${CMAKE_INSTALL_LIBDIR}
    )

    export(EXPORT artery-font NAMESPACE artery-font:: FILE "${CMAKE_CURRENT_BINARY_DIR}/artery-font-targets.cmake")
    install(EXPORT artery-font FILE artery-font-targets.cmake NAMESPACE artery-font:: DESTINATION ${ARTERY_CONFIG_PATH})

    install(
        FILES
            "${CMAKE_CURRENT_BINARY_DIR}/${ARTERY_CONFIG_PATH}/artery-font-config.cmake"
            "${CMAKE_CURRENT_BINARY_DIR}/artery-font-config-version.cmake"
        DESTINATION ${ARTERY_CONFIG_PATH}
    )

    install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/artery-font
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp")
endif()
