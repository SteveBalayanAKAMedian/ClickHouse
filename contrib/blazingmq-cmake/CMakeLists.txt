option(ENABLE_BLAZING "Enable BlazingMQ" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BLAZING)
    message(STATUS "Not using BlazingMQ")
    return()
endif()

set(LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/blazingmq")

file(GLOB_RECURSE SRCS_APP
    "${LIBRARY_DIR}/src/applications/**/*.cpp"
    "${LIBRARY_DIR}/src/applications/**/*.h"
)
file(GLOB_RECURSE SRCS_GROUPS
    "${LIBRARY_DIR}/src/groups/**/*.cpp"
    "${LIBRARY_DIR}/src/groups/**/*.h"
)

file(GLOB_RECURSE BMQ_INCLUDE_DIRS 
    "${LIBRARY_DIR}/src/applications/*"
    "${LIBRARY_DIR}/src/groups/*"
)

add_library(_blazing_app STATIC ${SRCS_APP})
add_library(_blazing_groups STATIC ${SRCS_GROUPS})

add_library(_blazing INTERFACE)
add_library(ch_contrib::blazingmq ALIAS _blazing)

target_link_libraries(_blazing INTERFACE _blazing_app _blazing_groups)

target_include_directories(_blazing_app SYSTEM PUBLIC ${BMQ_INCLUDE_DIRS})
target_include_directories(_blazing_groups SYSTEM PUBLIC ${BMQ_INCLUDE_DIRS})

target_link_libraries(_blazing_app
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

target_link_libraries(_blazing_groups
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
