# Option to enable or disable BDE build
option(ENABLE_BDE "Enable BDE" ${ENABLE_LIBRARIES})

if(NOT ENABLE_BDE)
    message(STATUS "BDE is disabled")
    return()
endif()

# Set paths for BDE and BDE tools
set(BDE_SOURCE_DIR "${ClickHouse_SOURCE_DIR}/contrib/bde")
set(BDE_BUILD_DIR "${CMAKE_BINARY_DIR}/bde_build")
set(BDE_INSTALL_DIR "${CMAKE_BINARY_DIR}/bde_install")
set(BDE_TOOLS_DIR "${ClickHouse_SOURCE_DIR}/contrib/bde-tools")
set(BDE_COMPLETE_FILE "${BDE_BUILD_DIR}/.complete")

# Ensure bde-tools is available
if(NOT EXISTS "${BDE_TOOLS_DIR}/BdeBuildSystem/toolchains/linux/gcc-default.cmake")
    message(FATAL_ERROR "BDE tools not found at ${BDE_TOOLS_DIR}")
endif()

# Step 1: Build BDE using bbs_build_env and bbs_build
if(NOT EXISTS "${BDE_COMPLETE_FILE}")
    message(STATUS "Building BDE using bbs_build_env and bbs_build")

    # Command to set up environment
    execute_process(
        COMMAND bash -c "eval \$(bbs_build_env -u opt_64_cpp17 -b ${BDE_BUILD_DIR} -i ${BDE_INSTALL_DIR})"
        WORKING_DIRECTORY ${BDE_SOURCE_DIR}
    )

    # Command to configure BDE
    execute_process(
        COMMAND bash -c "bbs_build configure --prefix=${BDE_INSTALL_DIR}"
        WORKING_DIRECTORY ${BDE_SOURCE_DIR}
    )

    # Command to build BDE
    execute_process(
        COMMAND bash -c "bbs_build build --prefix=${BDE_INSTALL_DIR}"
        WORKING_DIRECTORY ${BDE_SOURCE_DIR}
    )

    # Command to install BDE
    execute_process(
        COMMAND bash -c "bbs_build install --install_dir=${BDE_INSTALL_DIR} --prefix=${BDE_INSTALL_DIR}"
        WORKING_DIRECTORY ${BDE_SOURCE_DIR}
    )

    # Unset environment variables
    execute_process(
        COMMAND bash -c "eval \$(bbs_build_env unset)"
        WORKING_DIRECTORY ${BDE_SOURCE_DIR}
    )

    # Create a .complete file to indicate the build has been completed
    file(WRITE ${BDE_COMPLETE_FILE} "BDE build complete")
endif()

# Step 2: Create a custom target to ensure BDE is built
add_custom_target(bde_target ALL DEPENDS ${BDE_COMPLETE_FILE})

# Step 3: Create imported library targets for BDE libraries
# Assuming libraries are output into lib64 during the build process
file(GLOB BDE_LIBS "${BDE_INSTALL_DIR}/lib64/*.a")

# Create an imported library target that references these static libraries
add_library(bde_library STATIC IMPORTED GLOBAL)
set_target_properties(bde_library PROPERTIES
    IMPORTED_LOCATION "${BDE_LIBS}"  # Points to the static libraries built by BDE
    INTERFACE_INCLUDE_DIRECTORIES "${BDE_INSTALL_DIR}/include"
)

# Step 4: Ensure other targets depend on BDE being built first
add_dependencies(bde_library bde_target)
