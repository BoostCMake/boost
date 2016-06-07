# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

include(Ryppl)
include(RypplTestSuite)

ryppl_find_and_use_package(BoostCore)
ryppl_find_and_use_package(BoostDetail)
ryppl_find_and_use_package(BoostTest)

ryppl_test_suite(
    RUN
    throw_exception_no_both_test.cpp
    throw_exception_no_exceptions_test.cpp
    throw_exception_no_integration_test.cpp
    throw_exception_test.cpp    

    COMPILE_FAIL
    throw_exception_fail.cpp
    
    NO_SINGLE_TARGET
    )
