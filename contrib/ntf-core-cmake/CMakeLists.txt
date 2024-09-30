option(ENABLE_NTF_CORE "Enable NTF Core" ${ENABLE_LIBRARIES})

if(NOT ENABLE_NTF_CORE)
    message(STATUS "Not using NTF Core")
    return()
endif()

set(LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/ntf-core")

file(GLOB_RECURSE SRCS
    "${LIBRARY_DIR}/groups/**/*.cpp"
    "${LIBRARY_DIR}/groups/**/*.h"
)

file(GLOB_RECURSE INCLUDE_DIRS
    "${LIBRARY_DIR}/groups/*"
)

add_library(_ntf_core STATIC ${SRCS})

target_include_directories(_ntf_core SYSTEM PUBLIC ${INCLUDE_DIRS})

target_link_libraries(_ntf_core
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

add_library(ch_contrib::ntf_core ALIAS _ntf_core)
