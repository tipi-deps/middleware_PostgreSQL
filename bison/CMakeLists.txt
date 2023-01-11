

cmake_minimum_required(VERSION 3.17.0)
project(middleware_bison VERSION "0.0.1")

set(BISON_SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/bison-src)
set(BISON_INSTALL_DIR ${CMAKE_INSTALL_PREFIX})
set(BISON_INCLUDE_DIR ${BISON_INSTALL_DIR}/include)
set(BISON_CONFIGURE_COMMAND ${BISON_SOURCE_DIR}/configure)


list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}")
include(ExternalProject)
include(ProcessorCount)
include(hunter_autotools_project)

ProcessorCount(Ncore)

hunter_autotools_project("bison"
    HUNTER_SELF
      "${CMAKE_CURRENT_LIST_DIR}"
    URL
      "https://ftp.gnu.org/gnu/bison/bison-3.7.6.tar.gz"
    URL_HASH
      SHA1=119bd2892897c6541bc23fb65bf6f0f8805668a1
    DOWNLOAD_DIR
      "${CMAKE_CURRENT_BINARY_DIR}"
    SOURCE_DIR
      "${BISON_SOURCE_DIR}"
    BUILD_DIR
      "${CMAKE_CURRENT_BINARY_DIR}"
    INSTALL_DIR
      "${CMAKE_INSTALL_PREFIX}"
    GLOBAL_INSTALL_DIR
      "${CMAKE_INSTALL_PREFIX}"
    PARALLEL_JOBS
      "${Ncore}"
    PACKAGE_CONFIGURATION_TYPES
      "${CMAKE_BUILD_TYPE}"
    EXTRA_FLAGS
      ""
    BOOTSTRAP
      ""
)

file(MAKE_DIRECTORY ${BISON_INCLUDE_DIR})

file(MAKE_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/directory_to_simulate_install_step)
install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/directory_to_simulate_install_step 
        DESTINATION ${CMAKE_CURRENT_BINARY_DIR} 
)
