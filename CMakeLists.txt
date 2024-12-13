cmake_minimum_required(VERSION 3.5)

set(TARGET_NAME cronjob)
find_package(OpenSSL REQUIRED)

# Add ccronexpr library
add_library(ccronexpr STATIC ccronexpr/ccronexpr.c)
target_include_directories(ccronexpr PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/ccronexpr)

set(EXTENSION_NAME ${TARGET_NAME}_extension)
set(LOADABLE_EXTENSION_NAME ${TARGET_NAME}_loadable_extension)
project(${TARGET_NAME})

# Set extension sources
set(EXTENSION_SOURCES src/cronjob_extension.cpp)
include_directories(src/include ccronexpr)

# Build extension
build_static_extension(${TARGET_NAME} ${EXTENSION_SOURCES} ccronexpr/ccronexpr.c)
build_loadable_extension(${TARGET_NAME} "" ${EXTENSION_SOURCES} ccronexpr/ccronexpr.c)

# Link OpenSSL
target_link_libraries(${EXTENSION_NAME} OpenSSL::SSL OpenSSL::Crypto)
target_link_libraries(${LOADABLE_EXTENSION_NAME} OpenSSL::SSL OpenSSL::Crypto)

install(
  TARGETS ${EXTENSION_NAME}
  EXPORT "${DUCKDB_EXPORT_SET}"
  LIBRARY DESTINATION "${INSTALL_LIB_DIR}"
  ARCHIVE DESTINATION "${INSTALL_LIB_DIR}")
