if (NOT ENABLE_BDE)
    message(STATUS "Not using BDE")
    return()
endif()

set(LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/bde")
set(TOOLS_DIR "${ClickHouse_SOURCE_DIR}/contrib/bde-tools")

file(GLOB_RECURSE SRCS 
    "${LIBRARY_DIR}/*.cpp"
    "${LIBRARY_DIR}*.h"
)


