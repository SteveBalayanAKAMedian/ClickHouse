option(ENABLE_BLAZING "Enable Blazing" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BLAZING)
    message(STATUS "Not using Blazing")
    return()
endif()

set(CMAKE_CXX_STANDART 17) 

set(BDE_LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/bde/groups")
set(LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/blazingmq/src/groups/bmq")


file(GLOB_RECURSE BDE_SRCS
    "${BDE_LIBRARY_DIR}/bal/*/*.cpp"
    "${BDE_LIBRARY_DIR}/bal/*/*.h"
    "${BDE_LIBRARY_DIR}/bbl/*/*.cpp"
    "${BDE_LIBRARY_DIR}/bbl/*/*.h"
    "${BDE_LIBRARY_DIR}/bdl/*/*.cpp"
    "${BDE_LIBRARY_DIR}/bdl/*/*.h"
    "${BDE_LIBRARY_DIR}/bsl/*/*.cpp"
    "${BDE_LIBRARY_DIR}/bsl/*/*.h"
)

file(GLOB_RECURSE SRCS 
    "${LIBRARY_DIR}/bmqimp/*.cpp"
    "${LIBRARY_DIR}/bmqimp/*.h"
    "${LIBRARY_DIR}/bmqp/*.cpp"
    "${LIBRARY_DIR}/bmqp/*.h"
    "${LIBRARY_DIR}/bmqa/*.cpp"
    "${LIBRARY_DIR}/bmqa/*.h"
    "${LIBRARY_DIR}/bmqscm/*.cpp"
    "${LIBRARY_DIR}/bmqscm/*.h"
    "${LIBRARY_DIR}/bmqt/*.cpp"
    "${LIBRARY_DIR}/bmqt/*.h"
)

# Collect BDE include directories
file(GLOB BDE_INCLUDE_DIRS
    "${BDE_LIBRARY_DIR}/bal/*"
    "${BDE_LIBRARY_DIR}/bbl/*"
    "${BDE_LIBRARY_DIR}/bdl/*"
    "${BDE_LIBRARY_DIR}/bsl/*"
)

# Define the BDE library
add_library(_bde ${BDE_SRCS})
add_library(ch_contrib::bde ALIAS _bde)

# Define the BlazingMQ library
add_library(_blazing ${SRCS})
add_library(ch_contrib::blazing ALIAS _blazing)

# Add include directories
target_include_directories(_bde PUBLIC ${BDE_INCLUDE_DIRS})

target_include_directories(_blazing SYSTEM PRIVATE "${LIBRARY_DIR}/bmqimp" "${LIBRARY_DIR}/bmqp")
target_include_directories(_blazing SYSTEM PUBLIC "${LIBRARY_DIR}/bmqa" "${LIBRARY_DIR}/bmqscm" "${LIBRARY_DIR}/bmqt")

target_link_libraries(_blazing
  PUBLIC
    boost::headers_only
    boost::system
    boost::filesystem
    ch_contrib::zlib
    ch_contrib::zstd
    OpenSSL::Crypto
    OpenSSL::SSL
    ch_contrib::curl
    ch_contrib::snappy
    ch_contrib::protobuf
    ch_contrib::bde
)
