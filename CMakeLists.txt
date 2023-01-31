

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
include(hunter_cmake_args)

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
      "--without-readline --without-zlib"
    BOOTSTRAP
      ""
)


# Installing
set (EXTERNAL_PROJECT_NAME PostgreSQL)

set(include_install_dir "include")

add_library(libpq INTERFACE )
add_library(PostgreSQL::libpq ALIAS libpq)
target_link_libraries(libpq INTERFACE ${POSTGRESQL_INSTALL_DIR}/lib/libpq.a)
target_include_directories(libpq INTERFACE $<INSTALL_INTERFACE:${include_install_dir}/>)


# Layout. This works for all platforms:
#   * <prefix>/lib/cmake/<PROJECT-NAME>
#   * <prefix>/lib/
#   * <prefix>/include/
set(config_install_dir "lib/cmake/${EXTERNAL_PROJECT_NAME}")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

# Configuration
set(version_config "${generated_dir}/${EXTERNAL_PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${EXTERNAL_PROJECT_NAME}Config.cmake")
set(targets_export_name "${EXTERNAL_PROJECT_NAME}Targets")
set(namespace "${EXTERNAL_PROJECT_NAME}::")

# Include module with fuction 'write_basic_package_version_file'
include(CMakePackageConfigHelpers)

# Configure '<PROJECT-NAME>ConfigVersion.cmake'
# Note: PROJECT_VERSION is used as a VERSION
write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

# Configure '<PROJECT-NAME>Config.cmake'
# Use variables:
#   * targets_export_name
#   * EXTERNAL_PROJECT_NAME
configure_package_config_file(
    "${CMAKE_CURRENT_LIST_DIR}/cmake/modules/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)


# Targets:
install(
    TARGETS libpq
    EXPORT "${targets_export_name}"
    LIBRARY DESTINATION "lib"
    ARCHIVE DESTINATION "lib"
    RUNTIME DESTINATION "bin"
    INCLUDES DESTINATION "${include_install_dir}"
)

# Config
#   * <prefix>/lib/cmake/PostgreSQL/PostgreSQLConfig.cmake
#   * <prefix>/lib/cmake/PostgreSQL/PostgreSQLConfigVersion.cmake
#   * <prefix>/lib/cmake/PostgreSQL/PostgreSQLTargets.cmake
install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)
install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)
