cmake_minimum_required(VERSION 3.1)

project( kogmo_rtdb )

# Build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING "Build type" FORCE)
    message(STATUS "Build type not set, setting it to ${CMAKE_BUILD_TYPE}.")
endif()

# Options
option( KOGMO_RTDB_DO_POLLING "Use polling" OFF )

if(KOGMO_RTDB_DO_POLLING)
    add_definitions(-DKOGMO_RTDB_IPC_DO_POLLING)
endif(KOGMO_RTDB_DO_POLLING)

# Source files
file( GLOB RTDB_FUNCS ./objects/kogmo_rtdb_obj_*_funcs.c )
file( GLOB RTDB_RECORD ./record/kogmo_rtdb_avirawcodec.c ./record/kogmo_rtdb_timeidx.c )

set(SOURCES
    rtdb/kogmo_rtdb_obj_local.c
    rtdb/kogmo_rtdb_ipc_posix.c
    rtdb/kogmo_rtdb_rtmalloc.c
    rtdb/kogmo_time.c
    rtdb/kogmo_rtdb_helpers.c
    rtdb/kogmo_rtdb_housekeeping.c
    rtdb/kogmo_rtdb_obj_base_funcs.c
    rtdb/kogmo_rtdb_objdata.c
    rtdb/kogmo_rtdb_objmeta.c
    rtdb/kogmo_rtdb_objdata_slot.c
    rtdb/kogmo_rtdb_trace.c
    rtdb/kogmo_rtdb_utils.c
    rtdb/rtmalloc/suba.c
    ${RTDB_FUNCS}
    ${RTDB_RECORD}
)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/include/
    ${CMAKE_CURRENT_SOURCE_DIR}/
    ${CMAKE_CURRENT_SOURCE_DIR}/rtdb/
    ${CMAKE_CURRENT_SOURCE_DIR}/objects/
)

set(CMAKE_C_STANDARD 11)

add_definitions(
    -DRTMALLOC_suba
    -DKOGMO_RTDB_INCLUDE_A2_OBJECTS
    -Wall
    -Wstrict-prototypes
    -fno-strict-aliasing
    -D_GNU_SOURCE
)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pthread") # sets necessary defines and links pthread

# Build libkogmo_rtdb
add_library( kogmo_rtdb SHARED ${SOURCES} )

if(macx)
    target_link_libraries( kogmo_rtdb m)
else()
    target_link_libraries( kogmo_rtdb rt m)
endif()



if(COMMAND cmake_policy)
    cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)


# Build kogmo_rtdb_man
add_executable( kogmo_rtdb_man rtdb/kogmo_rtdb_man.c )
target_link_libraries( kogmo_rtdb_man kogmo_rtdb )

# Build kogmo_rtdb_dump
add_executable( kogmo_rtdb_dump rtdb/kogmo_rtdb_dump.c )
target_link_libraries( kogmo_rtdb_dump kogmo_rtdb )

# Build kogmo_rtdb_play
add_executable( kogmo_rtdb_play record/kogmo_rtdb_play.c )
target_link_libraries( kogmo_rtdb_play kogmo_rtdb )

# Build kogmo_rtdb_record
add_executable( kogmo_rtdb_record record/kogmo_rtdb_record.c  )
target_link_libraries( kogmo_rtdb_record kogmo_rtdb )

# Build kogmo_time_convert
add_executable( kogmo_time_convert rtdb/kogmo_time_convert.c )
target_link_libraries( kogmo_time_convert kogmo_rtdb )

# Build kogmo_rtdb_reader
add_executable( kogmo_rtdb_reader examples/kogmo_rtdb_reader.c )
target_link_libraries( kogmo_rtdb_reader kogmo_rtdb )

# Build kogmo_rtdb_writer
add_executable( kogmo_rtdb_writer examples/kogmo_rtdb_writer.c )
target_link_libraries( kogmo_rtdb_writer kogmo_rtdb )

set(rtdb_targets
    kogmo_rtdb
    kogmo_rtdb_man
    kogmo_rtdb_dump
    kogmo_rtdb_record
    kogmo_time_convert
    kogmo_rtdb_play
    kogmo_rtdb_reader
    kogmo_rtdb_writer
)

# All of RTDB
add_custom_target( rtdb )
add_dependencies( rtdb ${rtdb_targets} )

# Install headers, libs and binaries
FILE( GLOB API_HEADERS "include/*" )
install( FILES ${API_HEADERS} DESTINATION "include/kogmo_rtdb/" )
install( TARGETS ${rtdb_targets}
         ARCHIVE DESTINATION lib
         LIBRARY DESTINATION lib
         RUNTIME DESTINATION bin )

# Other files - so they are visible to IDEs such as qtcreator
add_custom_target(kogmo_resources_misc SOURCES
    ${API_HEADERS}
)
