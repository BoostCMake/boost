# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

set(test_list
    align_down_test
    align_test
    align_up_test
    aligned_alloc_test
    aligned_allocator_adaptor_test
    aligned_allocator_test
    aligned_delete_test
    alignment_of_test
    assume_aligned_test
    is_aligned_test
    )

set(test_targets)
foreach(test IN LISTS test_list)
    set(target "${PROJECT_NAME}-test-${test}")
    add_executable(${target} ${test})
    target_link_libraries(${target} PRIVATE
        boost::align
        boost::core
        boost::move
        boost::throw_exception
        )
    list(APPEND test_targets ${target})
    add_test(NAME ${PROJECT_NAME}.${test}
        COMMAND $<TARGET_FILE:${target}>
        )
endforeach()

add_custom_target(${PROJECT_NAME}-test
    COMMAND ${CMAKE_CTEST_COMMAND} -C "$<CONFIGURATION>" -R "^${PROJECT_NAME}" ${CMAKE_BINARY_DIR} DEPENDS ${test_targets}
    )

