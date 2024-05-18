option(ENABLE_BLAZING "Enable Blazing" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BLAZING)
    message(STATUS "Not using Blazing")
    return()
endif()

set(LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/blazingmq/src/groups/bmq")

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

add_library(_blazing ${SRCS})

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
)

add_library(ch_contrib::blazing ALIAS _blazing)
