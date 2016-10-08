# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

include(Ryppl)
include(RypplTestSuite)

ryppl_find_and_use_package(BoostLogic)
ryppl_find_and_use_package(BoostTest)

ryppl_test_suite(
        RUN
        lexical_cast_abstract_test
        lexical_cast_arrays_test
        lexical_cast_containers_test
        lexical_cast_empty_input_test
        lexical_cast_filesystem_test
        lexical_cast_float_types_test
        lexical_cast_inf_nan_test
        lexical_cast_integral_types_test
        lexical_cast_iterator_range_test
        lexical_cast_loopback_test
        lexical_cast_no_comp_time_prcision
        lexical_cast_no_exceptions_test
        lexical_cast_no_locale_test
        lexical_cast_noncopyable_test
        lexical_cast_pointers_test
        lexical_cast_stream_detection_test
        lexical_cast_stream_traits_test
        lexical_cast_test
        lexical_cast_try_lexical_convert
        lexical_cast_typedefed_wchar_test
        lexical_cast_typedefed_wchar_test_runtime
        lexical_cast_vc8_bug_test
        lexical_cast_wchars_test
        COMPILE_FAIL
        lexical_cast_to_pointer_test
        NO_SINGLE_TARGET
)
