cmake_minimum_required(VERSION 3.0)

project(libudfread VERSION 1.0.0 LANGUAGES C)

set(SRCS
  ${CMAKE_CURRENT_SOURCE_DIR}/cmake/libudfread.def
  ${CMAKE_CURRENT_SOURCE_DIR}/cmake/udfread-version.h
  ${CMAKE_CURRENT_SOURCE_DIR}/cmake/config.h
  ${CMAKE_CURRENT_SOURCE_DIR}/src/blockinput.h
  ${CMAKE_CURRENT_SOURCE_DIR}/src/default_blockinput.h
  ${CMAKE_CURRENT_SOURCE_DIR}/src/default_blockinput.c
  ${CMAKE_CURRENT_SOURCE_DIR}/src/ecma167.h
  ${CMAKE_CURRENT_SOURCE_DIR}/src/ecma167.c
  ${CMAKE_CURRENT_SOURCE_DIR}/src/udfread.c
  ${CMAKE_CURRENT_SOURCE_DIR}/src/udfread.h
  ${CMAKE_CURRENT_SOURCE_DIR}/src/udfread-version.c
)

ADD_LIBRARY(libudfread SHARED ${SRCS})
SET_TARGET_PROPERTIES(libudfread PROPERTIES LINK_FLAGS "/DEF:\"libudfread.def\"")

target_compile_definitions(libudfread
  PRIVATE
  HAVE_CONFIG_H
  PKGDATADIR=""
  _CRT_SECURE_NO_WARNINGS
  _CRT_NONSTDC_NO_WARNINGS
  inline=__inline
)
  
target_include_directories(libudfread
  PRIVATE
  $<BUILD_INTERFACE:.;cmake;src;${CMAKE_CURRENT_BINARY_DIR}>
  INTERFACE
  $<INSTALL_INTERFACE:include/libudfread>
)
