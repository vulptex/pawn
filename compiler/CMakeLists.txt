#build file for CMake, see http://www.cmake.org/

CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

PROJECT(pawnc)

SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

# check for optional include files
INCLUDE(${CMAKE_ROOT}/Modules/CheckIncludeFile.cmake)
CHECK_INCLUDE_FILE("unistd.h" HAVE_UNISTD_H)
IF(HAVE_UNISTD_H)
  ADD_DEFINITIONS(-DHAVE_UNISTD_H)
ENDIF(HAVE_UNISTD_H)
CHECK_INCLUDE_FILE("inttypes.h" HAVE_INTTYPES_H)
IF(HAVE_INTTYPES_H)
  ADD_DEFINITIONS(-DHAVE_INTTYPES_H)
ENDIF(HAVE_INTTYPES_H)
CHECK_INCLUDE_FILE("stdint.h" HAVE_STDINT_H)
IF(HAVE_STDINT_H)
  ADD_DEFINITIONS(-DHAVE_STDINT_H)
ENDIF(HAVE_STDINT_H)
CHECK_INCLUDE_FILE("alloca.h" HAVE_ALLOCA_H)
IF(HAVE_ALLOCA_H)
  ADD_DEFINITIONS(-DHAVE_ALLOCA_H)
ENDIF(HAVE_ALLOCA_H)

IF(UNIX)
  ADD_DEFINITIONS(-DENABLE_BINRELOC -D_GNU_SOURCE)
  INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/../linux)
  LINK_LIBRARIES(pthread m) #CMake adds the "lib" prefix, so "pthread" becomes "libpthread"
ENDIF(UNIX)
IF(WIN32)
  ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS)
ENDIF(WIN32)
IF(APPLE)
  # OS X defaults to 64-bit build, but 64-bit issues prevent that being a good idea
  SET(CMAKE_OSX_ARCHITECTURES "i386")
ENDIF(APPLE)

# The Pawn compiler
SET(PAWNCC_SRCS sc1.c sc2.c sc3.c sc4.c sc5.c sc6.c sc7.c
	scexpand.c sci18n.c sclist.c scmemfil.c scstate.c scvars.c
	lstring.c memfile.c
	${CMAKE_CURRENT_SOURCE_DIR}/../amx/keeloq.c)
IF(WIN32)
  SET(PAWNCC_SRCS ${PAWNCC_SRCS} pawncc.rc)
ENDIF(WIN32)
IF(UNIX)
  SET(PAWNCC_SRCS ${PAWNCC_SRCS} ${CMAKE_CURRENT_SOURCE_DIR}/../linux/binreloc.c)
ENDIF(UNIX)
ADD_EXECUTABLE(pawncc ${PAWNCC_SRCS})


# Utility to create GraphViz "dot" files from the XML report (if the report contains states)
ADD_DEFINITIONS(-DEZXML_NOMMAP)
SET(STATEGRAPH_SRCS stategraph.c ezxml.c)
ADD_EXECUTABLE(stategraph ${STATEGRAPH_SRCS})


# Simple Pawn disassembler
SET(PAWNDISASM_SRCS pawndisasm.c)
ADD_EXECUTABLE(pawndisasm ${PAWNDISASM_SRCS})
