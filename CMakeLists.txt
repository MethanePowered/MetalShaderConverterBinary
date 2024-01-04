if (POLICY CMP0114)
    # 'ExternalProject' step targets fully adopt their steps, support new CMake build system
    cmake_policy(SET CMP0114 NEW)
endif()

if(NOT APPLE)
    return()
endif()

set(TARGET MetalShaderConverter)

include(ExternalProject)

set(BINARIES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/binaries/MacOS")
set(BINARIES_MD5 b346ebe992e499b1bfa236d219d27ef9)

list(APPEND LIBRARIES
    "${BINARIES_DIR}/lib/libmetalirconverter.dylib")

ExternalProject_Add(${TARGET}Unpack
    URL ${BINARIES_DIR}.zip
    URL_MD5 ${BINARIES_MD5}
    SOURCE_DIR ${BINARIES_DIR}
    BUILD_BYPRODUCTS ${LIBRARIES}
    BUILD_IN_SOURCE TRUE
    EXCLUDE_FROM_ALL TRUE
    DOWNLOAD_EXTRACT_TIMESTAMP TRUE
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
    STEP_TARGETS build
)

add_library(${TARGET} INTERFACE)

add_dependencies(${TARGET} ${TARGET}Unpack-build)

set_target_properties(${TARGET} PROPERTIES PREREQUISITE_BINARIES "${LIBRARIES}")

target_link_libraries(${TARGET}
    INTERFACE
        ${LIBRARIES}
)

target_include_directories(${TARGET}
    INTERFACE
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

set_target_properties(${TARGET}
    PROPERTIES
    FOLDER Externals
)
