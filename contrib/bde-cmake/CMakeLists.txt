option(ENABLE_BDE "Enable BDE" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BDE)
    message(STATUS "Not using BDE")
    return()
endif()

set(BDE_LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/bde")

file(GLOB_RECURSE BDE_SRCS
    "${BDE_LIBRARY_DIR}/groups/**/*.cpp"
    "${BDE_LIBRARY_DIR}/groups/**/*.h"
    "${BDE_LIBRARY_DIR}/standalones/**/*.cpp"
    "${BDE_LIBRARY_DIR}/standalones/**/*.h"
    "${BDE_LIBRARY_DIR}/thirdparty/**/*.cpp"
    "${BDE_LIBRARY_DIR}/thirdparty/**/*.h"
)

file(GLOB_RECURSE BDE_INCLUDE_DIRS
    "${BDE_LIBRARY_DIR}/groups/*"
    "${BDE_LIBRARY_DIR}/standalones/*"
    "${BDE_LIBRARY_DIR}/thirdparty/*"
)

add_library(_bde STATIC ${BDE_SRCS})

target_include_directories(_bde SYSTEM PUBLIC ${BDE_INCLUDE_DIRS})

target_link_libraries(_bde
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

add_library(ch_contrib::bde ALIAS _bde)
