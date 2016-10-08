# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

include(Ryppl)
include(RypplTestSuite)

ryppl_find_and_use_package(BoostTypeIndex)
ryppl_find_and_use_package(BoostCore)
ryppl_find_and_use_package(BoostMpl)
ryppl_find_and_use_package(BoostThrowException)
ryppl_find_and_use_package(BoostTest)

ryppl_test_suite(
        RUN
        ctti_print_name.cpp
        test_lib.cpp
        test_lib_anonymous.cpp
        testing_crossmodule.cpp
        testing_crossmodule_anonymous.cpp
        type_index_test.cpp
        COMPILE
        type_index_test_ctti_alignment.cpp
        COMPILE_FAIL
        type_index_test_ctti_construct_fail.cpp
        type_index_test_ctti_copy_fail.cpp
        NO_SINGLE_TARGET
)
