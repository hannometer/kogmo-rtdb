cmake_minimum_required(VERSION 2.8)

project( kogmo_rtdb_cxx_examples )

# Build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING "Build type" FORCE)
    message(STATUS "Build type not set, setting it to ${CMAKE_BUILD_TYPE}.")
endif()

set(KOGMO_RTDB_INSTALL_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/../install")

set(KOGMO_RTDB_INCLUDE_DIRECTORIES "${KOGMO_RTDB_INSTALL_ROOT}/include/kogmo_rtdb")
set(KOGMO_RTDB_LINK_DIRS "${KOGMO_RTDB_INSTALL_ROOT}/../install/lib")
set(KOGMO_RTDB_LIBRARIES "kogmo_rtdb" )
set(KOGMO_RTDB_DEFINITIONS )

link_directories( ${KOGMO_RTDB_LINK_DIRS} )
include_directories( ${KOGMO_RTDB_INCLUDE_DIRECTORIES} )


add_executable( kogmo_rtdb_writer_cxx ../examples/kogmo_rtdb_writer_cxx.C )
target_link_libraries( kogmo_rtdb_writer_cxx ${KOGMO_RTDB_LIBRARIES} )

add_executable( kogmo_rtdb_reader_cxx ../examples/kogmo_rtdb_reader_cxx.C )
target_link_libraries( kogmo_rtdb_reader_cxx ${KOGMO_RTDB_LIBRARIES} )
