cmake_minimum_required(VERSION 3.16)

if(WIN32)
  add_definitions(-DWIN32)
endif()

# The directory into which we'll copy/symlink the final exe
set(binDir "${CMAKE_CURRENT_BINARY_DIR}/../${EASYLZMA_DIST_NAME}/bin")
file(MAKE_DIRECTORY "${binDir}")

set(SRCS
        elzma.c
        util.c
)
set(HDRS
        util.h
)

include_directories(
        "${CMAKE_CURRENT_BINARY_DIR}/../${EASYLZMA_DIST_NAME}/include"
)
link_directories(
        "${CMAKE_CURRENT_BINARY_DIR}/../${EASYLZMA_DIST_NAME}/lib"
)

add_executable(elzma ${SRCS} ${HDRS})
target_link_libraries(elzma PRIVATE easylzma_s)
add_dependencies(elzma copy_public_headers)

if(WIN32)
  # Windows: copy both elzma and unelzma so they are identical executables
  set(exeExt ".exe")
  add_custom_command(
          TARGET elzma
          POST_BUILD
          COMMAND
          # Copy the built elzma.exe to binDir
          ${CMAKE_COMMAND} -E copy_if_different
          "$<TARGET_FILE:elzma>"
          "${binDir}"
          COMMAND
          # Copy it again under the name unelzma.exe
          ${CMAKE_COMMAND} -E copy_if_different
          "elzma${exeExt}"
          "unelzma${exeExt}"
          WORKING_DIRECTORY "${binDir}"
  )
else()
  # Non-Windows (Linux, macOS): symlink "unelzma" to "elzma"
  set(exeExt "")
  add_custom_command(
          TARGET elzma
          POST_BUILD
          COMMAND
          # Copy the built elzma to binDir
          ${CMAKE_COMMAND} -E copy_if_different
          "$<TARGET_FILE:elzma>"
          "${binDir}"
          COMMAND
          # Symlink unelzma -> elzma
          ${CMAKE_COMMAND} -E create_symlink
          "elzma${exeExt}"
          "unelzma${exeExt}"
          WORKING_DIRECTORY "${binDir}"
  )
endif()
