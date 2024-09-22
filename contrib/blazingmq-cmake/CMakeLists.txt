## Option to enable or disable BlazingMQ integration
option(ENABLE_BMQ "Enable BlazingMQ" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BMQ)
    message(STATUS "BlazingMQ is disabled")
    return()
endif()

# Set paths for the BlazingMQ library
set(BMQ_SOURCE_DIR "${ClickHouse_SOURCE_DIR}/contrib/blazingmq")
set(BMQ_BUILD_DIR "${CMAKE_BINARY_DIR}/blazingmq_build")
set(BMQ_INSTALL_DIR "${CMAKE_BINARY_DIR}/blazingmq_install")

# Step 1: Include BDE (bde-cmake) as a dependency
add_subdirectory(${ClickHouse_SOURCE_DIR}/contrib/bde-cmake ${CMAKE_BINARY_DIR}/bde_build)

# Step 2: Add BDE tools to CMAKE_MODULE_PATH
list(APPEND CMAKE_MODULE_PATH "${ClickHouse_SOURCE_DIR}/contrib/bde-tools/BdeBuildSystem/cmake")

# Step 3: Include BlazingMQ as a subdirectory
add_subdirectory(${BMQ_SOURCE_DIR} ${BMQ_BUILD_DIR})

# Link required libraries to BlazingMQ
target_link_libraries(_blazingmq
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
    bde_library   # Link BDE library built in bde-cmake
)

# Step 5: Create an alias for BlazingMQ in the ClickHouse build system
add_library(ch_contrib::blazingmq ALIAS _blazingmq)
