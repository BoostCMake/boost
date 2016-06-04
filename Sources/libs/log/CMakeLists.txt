# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
project(BoostLog CXX)

if(MSVC)
    # specify the asynchronous exception handling model
    string(REGEX REPLACE " ?/EH[as]?c?" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /EHa")
endif()

add_library(boost_log
        src/attribute_name.cpp
        src/attribute_set.cpp
        src/attribute_value_set.cpp
        src/code_conversion.cpp
        src/core.cpp
        src/date_time_format_parser.cpp
        src/debug_output_backend.cpp
        src/default_attribute_names.cpp
        src/default_filter_factory.cpp
        src/default_formatter_factory.cpp
        src/default_sink.cpp
        src/dump.cpp
        src/dump_avx2.cpp
        src/dump_ssse3.cpp
        src/event.cpp
        src/event_log_backend.cpp
        src/exceptions.cpp
        src/filter_parser.cpp
        src/format_parser.cpp
        src/formatter_parser.cpp
        src/global_logger_storage.cpp
        src/init_from_settings.cpp
        src/init_from_stream.cpp
        src/light_rw_mutex.cpp
        src/matches_relation_factory.cpp
        src/named_scope.cpp
        src/named_scope_format_parser.cpp
        src/once_block.cpp
        src/parser_utils.cpp
        src/process_id.cpp
        src/process_name.cpp
        src/record_ostream.cpp
        src/settings_parser.cpp
        src/severity_level.cpp
        src/spirit_encoding.cpp
        src/syslog_backend.cpp
        src/text_file_backend.cpp
        src/text_multifile_backend.cpp
        src/text_ostream_backend.cpp
        src/thread_id.cpp
        src/thread_specific.cpp
        src/threadsafe_queue.cpp
        src/timer.cpp
        src/timestamp.cpp
        src/trivial.cpp
        src/unhandled_exception_count.cpp
        )

target_include_directories(boost_log PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        )

target_link_libraries(boost_log PUBLIC
        boost::date_time
        boost::exception
        boost::regex
        boost::spirit
        boost::xpressive
        )

add_library(boost::log ALIAS boost_log)
set_property(TARGET boost_log PROPERTY EXPORT_NAME log)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS
        boost_log
        EXPORT ${PROJECT_NAME}Targets
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        )

install(EXPORT ${PROJECT_NAME}Targets
        FILE ${PROJECT_NAME}Targets.cmake
        NAMESPACE boost::
        DESTINATION lib/cmake/${PROJECT_NAME}
        )

write_config_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
        DateTime
        Exception
        Regex
        Spirit
        Xpressive
        )
write_basic_package_version_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
        VERSION ${BOOST_VERSION}
        COMPATIBILITY AnyNewerVersion
        )

install(FILES
        "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
        "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
        DESTINATION lib/cmake/${PROJECT_NAME}
        )

export(TARGETS
        NAMESPACE boost::
        FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)
