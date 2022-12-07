include(ExternalProject)
include(ProcessorCount)

cmake_minimum_required(VERSION 3.17.0)
project(middleware_openssl VERSION "0.0.1")

set(OPENSSL_SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/openssl-src)
set(OPENSSL_INSTALL_DIR ${CMAKE_INSTALL_PREFIX})
set(OPENSSL_INCLUDE_DIR ${OPENSSL_INSTALL_DIR}/include)
set(OPENSSL_CONFIGURE_COMMAND ${OPENSSL_SOURCE_DIR}/config)

set(configure_command
    MACHINE=${CMAKE_SYSTEM_PROCESSOR}
    CC=${CMAKE_C_COMPILER}
    ${OPENSSL_CONFIGURE_COMMAND})

set(shared_flag no-shared)
set(configure_opts ${configure_opts} ${CMAKE_C_FLAGS} threads ${shared_flag})
ProcessorCount(Ncore)


ExternalProject_Add(
    OpenSSL 
    SOURCE_DIR ${OPENSSL_SOURCE_DIR}
    GIT_REPOSITORY https://github.com/openssl/openssl.git
    GIT_TAG OpenSSL_1_1_1n
    USES_TERMINAL_DOWNLOAD TRUE
    CONFIGURE_COMMAND
    ${configure_command}
    ${configure_opts}
    --prefix=${OPENSSL_INSTALL_DIR}
    --openssldir=${OPENSSL_INSTALL_DIR}
  BUILD_COMMAND make -j${Ncore}
  TEST_COMMAND ""
  INSTALL_COMMAND make install
)

file(MAKE_DIRECTORY ${OPENSSL_INCLUDE_DIR})

add_library(OpenSSL::SSL STATIC IMPORTED GLOBAL)
set_property(TARGET OpenSSL::SSL PROPERTY IMPORTED_LOCATION ${OPENSSL_INSTALL_DIR}/lib/libssl.${OPENSSL_LIBRARY_SUFFIX})
set_property(TARGET OpenSSL::SSL PROPERTY INTERFACE_INCLUDE_DIRECTORIES ${OPENSSL_INCLUDE_DIR})
add_dependencies(OpenSSL::SSL OpenSSL)

add_library(OpenSSL::Crypto STATIC IMPORTED GLOBAL)
set_property(TARGET OpenSSL::Crypto PROPERTY IMPORTED_LOCATION ${OPENSSL_INSTALL_DIR}/lib/libcrypto.${OPENSSL_LIBRARY_SUFFIX})
set_property(TARGET OpenSSL::Crypto PROPERTY INTERFACE_INCLUDE_DIRECTORIES ${OPENSSL_INCLUDE_DIR})
add_dependencies(OpenSSL::Crypto OpenSSL)