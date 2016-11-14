# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
project(BoostLexicalCast)

add_library(boost_lexical_cast INTERFACE)
add_library(boost::lexical_cast ALIAS boost_lexical_cast)
set_property(TARGET boost_lexical_cast PROPERTY EXPORT_NAME lexical_cast)

target_include_directories(boost_lexical_cast INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        )

target_link_libraries(boost_lexical_cast INTERFACE
        boost::container
        boost::core
        #boost::date_time
        boost::range
        boost::integer
        boost::array
        boost::math::special_functions
        boost::mpl
        boost::numeric::conversion
        boost::type_traits
        boost::unit_test_framework
        )

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_lexical_cast EXPORT ${PROJECT_NAME}Targets
        INCLUDES DESTINATION include
        )

install(EXPORT ${PROJECT_NAME}Targets
        FILE ${PROJECT_NAME}Targets.cmake
        NAMESPACE boost::
        DESTINATION lib/cmake/${PROJECT_NAME}
        )

write_config_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
        Array
        Container
        Core
        Range
        Integer
        Mpl
        NumericConversion
        TypeTraits
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

export(TARGETS boost_lexical_cast
        NAMESPACE boost::
        FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)

