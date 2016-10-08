# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

set(test_list
        assert_test.cpp
        current_function_test.cpp
        verify_test.cpp
        # expansion tests are in exp/ so that there is a backslash in the path on Windows
        #exp/assert_exp_test.cpp
        #exp/assert_msg_exp_test.cpp
        #exp/verify_exp_test.cpp
        #exp/verify_msg_exp_test.cpp
        assert_test2.cpp
        assert_msg_test2.cpp
        )

set(test_targets)
foreach(test IN LISTS test_list)
    set(target "${PROJECT_NAME}-test-${test}")
    add_executable(${target} ${test})
    target_link_libraries(${target} PRIVATE
            boost::assert
            boost::unit_test_framework
            )
    list(APPEND test_targets ${target})
    add_test(NAME ${PROJECT_NAME}.${test}
            COMMAND $<TARGET_FILE:${target}>
            )
endforeach()

add_custom_target(${PROJECT_NAME}-test
        COMMAND ${CMAKE_CTEST_COMMAND} -R "^${PROJECT_NAME}" ${CMAKE_BINARY_DIR}
        DEPENDS ${test_targets}
        )
