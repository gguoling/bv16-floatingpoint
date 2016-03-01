cmake_minimum_required(VERSION 3.0)

project(BV16
  VERSION 2.1
  LANGUAGES C
)
set(FBV16_MAJOR_VERSION 2)
set(FBV16_MINOR_VERSION 1)
set(FBV16_VERSION
  ${FBV16_MAJOR_VERSION}.${FBV16_MINOR_VERSION})
 
include_directories(${CMAKE_BINARY_DIR} ./bv16)
include_directories(${CMAKE_BINARY_DIR} ./bvcommon)

option(ENABLE_STATIC "Build static library (default is shared library)." OFF)

# add_subdirectory(bv16)
# add_subdirectory(bvcommon)

set( BV16_SOURCE_FILES
    ./bv16/bitpack.c
    ./bv16/bv.c
    ./bv16/coarptch.c
    ./bv16/decoder.c
    ./bv16/encoder.c
    ./bv16/excdec.c
    ./bv16/excquan.c
    ./bv16/fineptch.c
    ./bv16/g192.c
    ./bv16/gaindec.c
    ./bv16/gainquan.c
    ./bv16/levelest.c
    ./bv16/lspdec.c
    ./bv16/lspquan.c
    ./bv16/plc.c
    ./bv16/postfilt.c
    ./bv16/ptquan.c
    ./bv16/tables.c
    ./bvcommon/a2lsp.c
    ./bvcommon/allpole.c
    ./bvcommon/allzero.c
    ./bvcommon/autocor.c
    ./bvcommon/cmtables.c
    ./bvcommon/levdur.c
    ./bvcommon/lsp2a.c
    ./bvcommon/ptdec.c
    ./bvcommon/stblchck.c
    ./bvcommon/stblzlsp.c
    ./bvcommon/utility.c
)

add_compile_options(-O -Wall)
add_compile_options(-DWMOPS=0) 
add_compile_options(-DG192BITSTREAM=0)


if(MSVC)
  list(APPEND BV16_SOURCE_FILES bv16.def)
endif(MSVC)

if(ENABLE_STATIC)
  add_library(bv16 STATIC ${BV16_SOURCE_FILES})
  add_executable(BroadVoice16  ${BV16_SOURCE_FILES})
else()
  add_library(bv16 SHARED ${BV16_SOURCE_FILES})
  add_executable(BroadVoice16  ${BV16_SOURCE_FILES})
  if(MSVC)
    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
      install(FILES ${CMAKE_CURRENT_BINARY_DIR}/Debug/bv16.pdb
        DESTINATION bin
        PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
      )
    endif()
  endif()
endif()

if(APPLE)
  target_link_libraries(bv16 "m")
endif()

# if(APPLE)
#   target_link_libraries(bv16 "-framework Cocoa -lm")
# endif()
install(TARGETS bv16
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
)

install(TARGETS BroadVoice16
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE
)


file(GLOB BV16_HEADER_FILES "bv16/*.h")
file(GLOB BV16_COMMON_HEADER_FILES "bvcommon/*.h")



install(FILES ${BV16_HEADER_FILES}
  DESTINATION include/bv16-floatingpoint/bv16
  PERMISSIONS OWNER_READ OWNER_WRITE GROUP_READ WORLD_READ
)

install(FILES ${BV16_COMMON_HEADER_FILES}
  DESTINATION include/bv16-floatingpoint/bvcommon
  PERMISSIONS OWNER_READ OWNER_WRITE GROUP_READ WORLD_READ
)
