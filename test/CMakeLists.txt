cmake_minimum_required(VERSION 3.16)

set(SRCS easylzma_test.c simple.c)
set(HDRS simple.h)

include_directories(
        "${CMAKE_CURRENT_BINARY_DIR}/../${EASYLZMA_DIST_NAME}/include"
)
link_directories(
        "${CMAKE_CURRENT_BINARY_DIR}/../${EASYLZMA_DIST_NAME}/lib"
)

add_executable(easylzma_test ${SRCS} ${HDRS})
target_link_libraries(easylzma_test PRIVATE easylzma_s)

# Ensure the headers have been copied before we build or run the test
add_dependencies(easylzma_test copy_public_headers)

add_custom_target(test
        COMMAND $<TARGET_FILE:easylzma_test>
        DEPENDS easylzma_test
        COMMENT "Running easylzma_test"
)
