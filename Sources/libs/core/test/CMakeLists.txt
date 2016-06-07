# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

include(Ryppl)
include(RypplTestSuite)

ryppl_find_and_use_package(BoostCore)

ryppl_test_suite(
    RUN
    addressof_fn_test.cpp
    addressof_np_test.cpp
    addressof_test.cpp
    addressof_test2.cpp
    checked_delete_fail.cpp
    checked_delete_fail2.cpp
    checked_delete_test.cpp
    demangle_test.cpp
    demangled_name_test.cpp
    detail_iterator_test.cpp
    eif_constructors.cpp
    eif_dummy_arg_disambiguation.cpp
    eif_lazy.cpp
    eif_lazy_test.cpp
    eif_member_templates.cpp
    eif_namespace_disambiguation.cpp
    eif_no_disambiguation.cpp
    eif_partial_specializations.cpp
    explicit_operator_bool.cpp
    explicit_operator_bool_noexcept.cpp
    get_pointer_test.cpp
    ignore_unused_test.cpp
    is_same_test.cpp
    iterator_test.cpp
    lightweight_test_fail.cpp
    lightweight_test_fail2.cpp
    lightweight_test_fail3.cpp
    lightweight_test_fail4.cpp
    lightweight_test_fail5.cpp
    lightweight_test_fail6.cpp
    lightweight_test_fail7.cpp
    lightweight_test_fail8.cpp
    lightweight_test_test.cpp
    lightweight_test_test2.cpp
    ref_ct_test.cpp
    ref_fn_test.cpp
    ref_implicit_fail.cpp
    ref_implicit_fail2.cpp
    ref_ref_test.cpp
    ref_rv_fail1.cpp
    ref_rv_fail2.cpp
    ref_rv_fail3.cpp
    ref_rv_fail4.cpp
    ref_rv_fail5.cpp
    ref_test.cpp
    scoped_enum.cpp
    sp_typeinfo_test.cpp
    swap
    typeinfo_test.cpp
    underlying_type.cpp
    visit_each_test.cpp

    COMPILE_FAIL
    explicit_operator_bool_compile_fail_conv_int.cpp
    explicit_operator_bool_compile_fail_conv_pvoid.cpp
    explicit_operator_bool_compile_fail_delete.cpp
    explicit_operator_bool_compile_fail_shift.cpp
    noncopyable_compile_fail.cpp
    scoped_enum_compile_fail_conv_from_int.cpp
    scoped_enum_compile_fail_conv_to_int.cpp

    LINK_LIBRARIES
    boost::core
    NO_SINGLE_TARGET
    )
