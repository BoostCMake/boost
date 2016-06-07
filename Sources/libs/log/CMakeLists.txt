# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
project(BoostLog CXX)

if(MSVC)
    # specify the asynchronous exception handling model
    string(REGEX REPLACE " ?/EH[as]?c?" "" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
    add_definitions(-D_SCL_SECURE_NO_WARNINGS)
    add_definitions(-D_SCL_SECURE_NO_DEPRECATE)
endif()

add_definitions(-DBOOST_SPIRIT_USE_PHOENIX_V3=1)
add_definitions(-DBOOST_THREAD_DONT_USE_CHRONO=1)
add_definitions(-D__STDC_CONSTANT_MACROS)

if(UNIX)
    add_definitions(-DBOOST_LOG_WITHOUT_EVENT_LOG=1)
    add_definitions(-DBOOST_LOG_USE_NATIVE_SYSLOG=1)

    if (NOT APPLE)
        add_definitions(-D_XOPEN_SOURCE=600)
        add_definitions(-D_GNU_SOURCE=1)
    endif()
endif()

list(APPEND SOURCES_LIST 
    src/attribute_name.cpp
    src/attribute_set.cpp
    src/attribute_value_set.cpp
    src/code_conversion.cpp
    src/core.cpp
    src/record_ostream.cpp
    src/severity_level.cpp
    src/global_logger_storage.cpp
    src/named_scope.cpp
    src/process_name.cpp
    src/process_id.cpp
    src/thread_id.cpp
    src/timer.cpp
    src/exceptions.cpp
    src/default_attribute_names.cpp
    src/default_sink.cpp
    src/text_ostream_backend.cpp
    src/text_file_backend.cpp
    src/text_multifile_backend.cpp
    src/syslog_backend.cpp
    src/thread_specific.cpp
    src/once_block.cpp
    src/timestamp.cpp
    src/threadsafe_queue.cpp
    src/event.cpp
    src/trivial.cpp
    src/spirit_encoding.cpp
    src/format_parser.cpp
    src/date_time_format_parser.cpp
    src/named_scope_format_parser.cpp
    src/unhandled_exception_count.cpp
    src/dump.cpp
    )

if (WIN32)
    list(APPEND SOURCES_LIST
        src/debug_output_backend.cpp
        src/light_rw_mutex.cpp
        src/simple_event_log.mc
        src/event_log_backend.cpp
        )
endif()

add_library(boost_log ${SOURCES_LIST})

target_include_directories(boost_log PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    )

if(WIN32 AND MSVC)
    set_target_properties(boost_log PROPERTIES COMPILE_FLAGS "${CMAKE_CXX_FLAGS} /EHa /bigobj /wd4503 /wd4456 /wd4559
    /wd4003")
elseif(UNIX)
    if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU" OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
        set_target_properties(boost_log PROPERTIES COMPILE_FLAGS "${CMAKE_C_FLAGS} -fvisibility=hidden")
    endif()

    if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
        set_target_properties(boost_log PROPERTIES COMPILE_FLAGS
            "${CMAKE_CXX_FLAGS} -Wno-bind-to-temporary-copy -Wno-unused-function")
    endif()
endif()

list(APPEND CURRENT_TARGET_LIBRARIES
    boost::asio
    boost::date_time
    boost::filesystem
    boost::function_types
    boost::exception
    boost::regex
    boost::thread
    boost::spirit
    boost::system
    boost::phoenix
    boost::parameter
    boost::random
    boost::xpressive
    boost::type_index
    boost::variant    
    )

if (WIN32)
    list(APPEND CURRENT_TARGET_LIBRARIES
        psapi
        advapi32
        ws2_32
        mswsock
        )
elseif(UNIX)
    list(APPEND CURRENT_TARGET_LIBRARIES
        rt
        socket
        nsl
        ipv6
        )
endif()

target_link_libraries(boost_log PUBLIC
    ${CURRENT_TARGET_LIBRARIES}
    )

add_library(boost::log ALIAS boost_log)
set_property(TARGET boost_log PROPERTY EXPORT_NAME log)

list(APPEND BOOST_LOG_SETUP_SOURCES_LIST
    parser_utils.cpp
    init_from_stream.cpp
    init_from_settings.cpp
    settings_parser.cpp
    filter_parser.cpp
    formatter_parser.cpp
    default_filter_factory.cpp
    matches_relation_factory.cpp
    default_formatter_factory.cpp
    )

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
    Asio
    DateTime
    Exception
    Regex
    Spirit
    System
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
