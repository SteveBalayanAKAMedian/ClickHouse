option(ENABLE_BDE "Enable BDE" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BDE)
    message(STATUS "Not using BDE")
    return()
endif()

set(BDE_LIBRARY_DIR "${ClickHouse_SOURCE_DIR}/contrib/bde")

# Compile each group subdirectory into separate libraries
file(GLOB_RECURSE BAL_SRCS "${BDE_LIBRARY_DIR}/groups/bal/*.cpp" "${BDE_LIBRARY_DIR}/groups/bal/*.h")
file(GLOB_RECURSE BBL_SRCS "${BDE_LIBRARY_DIR}/groups/bbl/*.cpp" "${BDE_LIBRARY_DIR}/groups/bbl/*.h")
file(GLOB_RECURSE BDL_SRCS "${BDE_LIBRARY_DIR}/groups/bdl/*.cpp" "${BDE_LIBRARY_DIR}/groups/bdl/*.h")
file(GLOB_RECURSE BSL_SRCS "${BDE_LIBRARY_DIR}/groups/bsl/*.cpp" "${BDE_LIBRARY_DIR}/groups/bsl/*.h")

# Create static libraries for each subdirectory
add_library(_bal STATIC ${BAL_SRCS})
add_library(_bbl STATIC ${BBL_SRCS})
add_library(_bdl STATIC ${BDL_SRCS})
add_library(_bsl STATIC ${BSL_SRCS})

# Include directories
target_include_directories(_bal SYSTEM PUBLIC "${BDE_LIBRARY_DIR}/groups/bal")
target_include_directories(_bbl SYSTEM PUBLIC "${BDE_LIBRARY_DIR}/groups/bbl")
target_include_directories(_bdl SYSTEM PUBLIC "${BDE_LIBRARY_DIR}/groups/bdl")
target_include_directories(_bsl SYSTEM PUBLIC "${BDE_LIBRARY_DIR}/groups/bsl")

# Create a meta-library to link all sub-libraries together
add_library(_bde INTERFACE)
add_library(ch_contrib::bde ALIAS _bde)

target_link_libraries(_bde INTERFACE _bal _bbl _bdl _bsl)

# Link external dependencies to each sub-library
foreach(lib IN ITEMS _bal _bbl _bdl _bsl)
    target_link_libraries(${lib}
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
endforeach()
