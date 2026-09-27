cmake_minimum_required(VERSION 3.20)

option(HAILO_BUILD_UT "Build Unit Tests" OFF)

project(hailo-ollama VERSION 5.4.0)
include(FetchContent)
include(GNUInstallDirs)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# External dependencies paths
set(HAILO_OLLAMA_EXTERNAL_DIR ${CMAKE_CURRENT_LIST_DIR}/external)
set(HAILO_OLLAMA_CMAKE_SCRIPTS ${CMAKE_CURRENT_LIST_DIR}/cmake/external)

find_package(OpenSSL REQUIRED)
find_package(HailoRT 5 REQUIRED)

if(MSVC)
    # Prevent Windows headers from defining min/max and other problematic macros
    add_compile_definitions(WIN32_LEAN_AND_MEAN NOMINMAX)
endif()

add_subdirectory(src)

if(HAILO_BUILD_UT)
    add_subdirectory(test)
    enable_testing()
    add_test(project-tests hailo-ollama-test)
endif()

# Install model manifests
install(DIRECTORY ${PROJECT_SOURCE_DIR}/models/manifests
    DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/hailo-ollama/models
)

set(CPACK_PACKAGE_NAME "hailo-ollama")
set(CPACK_PACKAGE_VENDOR "Hailo")
set(CPACK_PACKAGE_CONTACT "support@hailo.ai")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Hailo Ollama server for Hailo GenAI models")
set(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
set(CPACK_GENERATOR "DEB")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Hailo")
set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)
set(CPACK_DEBIAN_FILE_NAME DEB-DEFAULT)
set(CPACK_PACKAGING_INSTALL_PREFIX "/usr")

include(CPack)
