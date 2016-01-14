project(libudfread)

cmake_minimum_required(VERSION 2.8)
option(DANTEXE "Path to the ant executable" OFF)

MACRO(ADD_MSVC_PRECOMPILED_HEADER PrecompiledHeader SourcesVar)
  #IF(MSVC)
    GET_FILENAME_COMPONENT(PrecompiledBasename ${PrecompiledHeader} NAME_WE)
    SET(PrecompiledBinary "${CMAKE_CURRENT_BINARY_DIR}/${PrecompiledBasename}.pch")
    SET(Sources ${${SourcesVar}})

    SET_SOURCE_FILES_PROPERTIES(${PrecompiledHeader}
                                PROPERTIES COMPILE_FLAGS "/Yc\"${PrecompiledHeader}\" /Fp\"${PrecompiledBinary}\""
                                           OBJECT_OUTPUTS "${PrecompiledBinary}")
    SET_SOURCE_FILES_PROPERTIES(${Sources}
                                PROPERTIES COMPILE_FLAGS "/Yc\"${PrecompiledHeader}\" /FI\"${PrecompiledHeader}\" /Fp\"${PrecompiledBinary}\""
                                           OBJECT_DEPENDS "${PrecompiledBinary}")  
    # Add precompiled header to SourcesVar
    LIST(APPEND ${SourcesVar} ${PrecompiledHeader})
  #ENDIF(MSVC)
ENDMACRO(ADD_MSVC_PRECOMPILED_HEADER)

get_filename_component(PROJECT_SOURCE_DIR "../src" ABSOLUTE)
get_filename_component(EXAMPLES_SOURCE_DIR "../examples" ABSOLUTE)
get_filename_component(BASEPATH "../" ABSOLUTE)
message("${PROJECT_SOURCE_DIR}")
message("${BASEPATH}")

include_directories(
${CMAKE_CURRENT_SOURCE_DIR}
${PROJECT_SOURCE_DIR}
${BASEPATH}
)

INCLUDE (CheckIncludeFiles)
INCLUDE (CheckSymbolExists)
#CHECK_SYMBOL_EXISTS (DIR dirent.h HAVE_DIRENT_H)
#CHECK_INCLUDE_FILES (dlfcn.h HAVE_DLFCN_H)
#CHECK_INCLUDE_FILES (mntent.h HAVE_MNTENT_H)
#CHECK_INCLUDE_FILES (pthread.h HAVE_PTHREAD_H)
#CHECK_INCLUDE_FILES (strings.h HAVE_STRINGS_H)


set(SRCS    ${CMAKE_CURRENT_SOURCE_DIR}/headers_fixup.h
            ${CMAKE_CURRENT_SOURCE_DIR}/config.h
            ${CMAKE_CURRENT_SOURCE_DIR}/libudfread.def
            ${CMAKE_CURRENT_SOURCE_DIR}/udfread-version.h
            ${PROJECT_SOURCE_DIR}/blockinput.h
            ${PROJECT_SOURCE_DIR}/default_blockinput.h
            ${PROJECT_SOURCE_DIR}/default_blockinput.c
            ${PROJECT_SOURCE_DIR}/ecma167.h
            ${PROJECT_SOURCE_DIR}/ecma167.c
			${PROJECT_SOURCE_DIR}/udfread.c
			${PROJECT_SOURCE_DIR}/udfread.h
            ${PROJECT_SOURCE_DIR}/udfread-version.c
)

add_definitions(-DWIN_32 -DHAVE_CONFIG_H -D_CRT_SECURE_NO_WARNINGS)


ADD_MSVC_PRECOMPILED_HEADER("headers_fixup.h" SRCS)
ADD_LIBRARY(libudfread SHARED ${SRCS})
SET_TARGET_PROPERTIES(libudfread PROPERTIES LINK_FLAGS "/DEF:\"libudfread.def\"")


#CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cm ${CMAKE_CURRENT_SOURCE_DIR}/config.h)

# udfcat
set(cat_SRCS ${EXAMPLES_SOURCE_DIR}/udfcat.c)
ADD_MSVC_PRECOMPILED_HEADER("headers_fixup.h" cat_SRCS)
add_executable(udfcat ${cat_SRCS})
target_link_libraries(udfcat libudfread)

#udfls
set(ls_SRCS ${EXAMPLES_SOURCE_DIR}/udfls.c)
ADD_MSVC_PRECOMPILED_HEADER("headers_fixup.h" ls_SRCS)
add_executable(udfls ${ls_SRCS})
target_link_libraries(udfls libudfread)