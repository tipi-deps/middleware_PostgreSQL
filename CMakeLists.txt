

cmake_minimum_required(VERSION 3.17.0)
project(middleware_PostgreSQL VERSION "0.0.1")

set(POSTGRESQL_SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/PostgreSQL-src)
set(POSTGRESQL_INSTALL_DIR ${CMAKE_INSTALL_PREFIX})
set(POSTGRESQL_INCLUDE_DIR ${POSTGRESQL_INSTALL_DIR}/include)
set(POSTGRESQL_CONFIGURE_COMMAND ${POSTGRESQL_SOURCE_DIR}/configure)


list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}")
include(ExternalProject)
include(ProcessorCount)
include(hunter_autotools_project)

ProcessorCount(Ncore)

hunter_autotools_project("PostgreSQL"
    HUNTER_SELF
      "${CMAKE_CURRENT_LIST_DIR}"
    URL
      "https://github.com/hunter-packages/PostgreSQL/archive/PostgreSQL-10.0.0.tar.gz"
    URL_HASH
      SHA1=d35d4e958509460347f39f0ba542a17eb2228f3c
    DOWNLOAD_DIR
      "${CMAKE_CURRENT_BINARY_DIR}"
    SOURCE_DIR
      "${POSTGRESQL_SOURCE_DIR}"
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

file(MAKE_DIRECTORY ${POSTGRESQL_INCLUDE_DIR})

file(MAKE_DIRECTORY ./directory_to_simulate_install_step)
install(DIRECTORY ./directory_to_simulate_install_step 
        DESTINATION ${CMAKE_CURRENT_BINARY_DIR} 
)
