# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

include(Ryppl)
include(RypplTestSuite)

ryppl_find_and_use_package(BoostLog)
ryppl_find_and_use_package(BoostTest)

ryppl_test_suite(
        RUN
        run/attr_attribute_set.cpp
        run/attr_attribute_set_ticket11106.cpp
        run/attr_attribute_value_impl.cpp
        run/attr_attribute_value_set.cpp
        run/attr_attribute_value_set_ticket11190.cpp
        run/attr_function.cpp
        run/attr_named_scope.cpp
        run/attr_sets_insertion_lookup.cpp
        run/attr_value_visitation.cpp
        run/core.cpp
        run/filt_attr.cpp
        run/filt_has_attr.cpp
        run/filt_matches_boost_regex.cpp
        run/filt_matches_spirit_classic.cpp
        run/filt_matches_spirit_qi.cpp
        run/filt_matches_std_regex.cpp
        run/filt_matches_xpressive.cpp
        run/form_attr.cpp
        run/form_c_decor.cpp
        run/form_char_decor.cpp
        run/form_csv_decor.cpp
        run/form_date_time.cpp
        run/form_format.cpp
        run/form_if.cpp
        run/form_message.cpp
        run/form_named_scope.cpp
        run/form_to_log_manip.cpp
        run/form_xml_decor.cpp
        run/src_record_ostream.cpp
        run/unhandled_exception_count.cpp
        run/unhandled_exception_count_np.cpp
        run/util_dynamic_type_disp.cpp
        run/util_exception_handled.cpp
        run/util_formatting_ostream.cpp
        run/util_manip_add_value.cpp
        run/util_manip_dump.cpp
        run/util_manip_to_log.cpp
        run/util_once_block.cpp
        run/util_static_type_disp.cpp
        run/util_stp_filter_parser.cpp
        run/util_stp_formatter_parser.cpp
        run/util_stp_settings_parser.cpp
        run/util_string_literal.cpp
        run/util_type_info_wrapper.cpp
        NO_SINGLE_TARGET
)
