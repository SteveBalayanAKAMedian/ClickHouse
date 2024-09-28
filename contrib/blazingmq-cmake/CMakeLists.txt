option(ENABLE_BMQ "Enable BlazingMQ" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BMQ)
    message(STATUS "Not using BlazingMQ")
    return()
endif()

set(LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/blazingmq")

# Applications
file(GLOB_RECURSE APP_SRCS
    "${LIBRARY_DIR}/src/applications/**/*.cpp"
    "${LIBRARY_DIR}/src/applications/**/*.cc"
    "${LIBRARY_DIR}/src/applications/**/*.c"
    "${LIBRARY_DIR}/src/applications/**/*.h"
)

# Groups
file(GLOB_RECURSE GROUP_SRCS
    "${LIBRARY_DIR}/src/groups/**/*.cpp"
    "${LIBRARY_DIR}/src/groups/**/*.cc"
    "${LIBRARY_DIR}/src/groups/**/*.c"
    "${LIBRARY_DIR}/src/groups/**/*.h"
)

# Include directories for src and include
include_directories(
    "${LIBRARY_DIR}/src/applications"
    "${LIBRARY_DIR}/src/groups"
    "${LIBRARY_DIR}/include"
    "${LIBRARY_DIR}/src"
)

# Define smaller libraries for applications and groups
add_library(_blazingmq_apps ${APP_SRCS})
add_library(_blazingmq_groups ${GROUP_SRCS})

# Link dependencies to each sub-library
target_link_libraries(_blazingmq_apps
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

target_link_libraries(_blazingmq_groups
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

# Create the main BlazingMQ library by linking the smaller parts
add_library(_blazingmq INTERFACE)
target_link_libraries(_blazingmq INTERFACE _blazingmq_apps _blazingmq_groups)

# Create an alias for the BlazingMQ library
add_library(ch_contrib::blazingmq ALIAS _blazingmq)