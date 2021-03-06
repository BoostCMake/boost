# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
project(BoostContainer)

add_library(boost_container INTERFACE)
add_library(boost::container ALIAS boost_container)
set_property(TARGET boost_container PROPERTY EXPORT_NAME container)

target_include_directories(boost_container INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        )

target_link_libraries(boost_container INTERFACE
        boost::move
        )

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_container EXPORT ${PROJECT_NAME}Targets
        INCLUDES DESTINATION include
        )

install(EXPORT ${PROJECT_NAME}Targets
        FILE ${PROJECT_NAME}Targets.cmake
        NAMESPACE boost::
        DESTINATION lib/cmake/${PROJECT_NAME}
        )

write_config_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake")
write_basic_package_version_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
        VERSION ${BOOST_VERSION}
        COMPATIBILITY AnyNewerVersion
        )

install(FILES
        "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
        "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
        DESTINATION lib/cmake/${PROJECT_NAME}
        )

export(TARGETS boost_container
        NAMESPACE boost::
        FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)
