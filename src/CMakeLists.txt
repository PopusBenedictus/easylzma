cmake_minimum_required(VERSION 3.16)

if(WIN32)
  add_definitions(-DWIN32)
endif()

file(GLOB SRCS pavlov/*.c *.c)
file(GLOB HDRS *.h pavlov/*.h easylzma/*.h)
file(GLOB PUB_HDRS easylzma/*.h)

# Set up output paths
set(libDir
        "${CMAKE_CURRENT_BINARY_DIR}/../${EASYLZMA_DIST_NAME}/lib")
set(incDir
        "${CMAKE_CURRENT_BINARY_DIR}/../${EASYLZMA_DIST_NAME}/include/easylzma")

include_directories("${CMAKE_CURRENT_SOURCE_DIR}")

# Place built libs in the correct place
set(LIBRARY_OUTPUT_PATH "${libDir}")

add_library(easylzma_s STATIC ${SRCS} ${HDRS})
add_library(easylzma SHARED ${SRCS} ${HDRS})

# Setup shared library version numbering
set_target_properties(easylzma
        PROPERTIES
        SOVERSION "${EASYLZMA_MAJOR}"
        VERSION "${EASYLZMA_MAJOR}.${EASYLZMA_MINOR}.${EASYLZMA_MICRO}"
)

# On Win32 we'll need to set up exports correctly
set(sharedLibCompileFlags "-DEASYLZMA_SHARED -DEASYLZMA_BUILD")
if(APPLE)
  set(sharedLibCompileFlags "${sharedLibCompileFlags} -fno-common")
endif()

set_target_properties(easylzma
        PROPERTIES
        COMPILE_FLAGS "${sharedLibCompileFlags}"
)

file(MAKE_DIRECTORY "${libDir}")
file(MAKE_DIRECTORY "${incDir}")

add_custom_target(copy_public_headers
        COMMENT "Copying public headers into ${incDir}"
)

foreach(header ${PUB_HDRS})
  add_custom_command(
          TARGET copy_public_headers
          POST_BUILD
          COMMAND ${CMAKE_COMMAND} -E copy_if_different
          "${header}"
          "${incDir}"
          COMMENT "Copying ${header} to ${incDir}"
  )
endforeach()
