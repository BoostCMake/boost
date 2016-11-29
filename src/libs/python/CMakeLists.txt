# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostPython CXX)

#find_package(PythonLibs REQUIRED)

list(APPEND CURRENT_TARGET_SOURCES
    src/numeric.cpp
    src/list.cpp
    src/long.cpp
    src/dict.cpp
    src/tuple.cpp
    src/str.cpp
    src/slice.cpp
    src/converter/from_python.cpp
    src/converter/registry.cpp
    src/converter/type_id.cpp
    src/object/enum.cpp
    src/object/class.cpp
    src/object/function.cpp
    src/object/inheritance.cpp
    src/object/life_support.cpp
    src/object/pickle_support.cpp
    src/errors.cpp
    src/module.cpp
    src/converter/builtin_converters.cpp
    src/converter/arg_to_python_base.cpp
    src/object/iterator.cpp
    src/object/stl_iterator.cpp
    src/object_protocol.cpp
    src/object_operators.cpp
    src/wrapper.cpp
    src/import.cpp
    src/exec.cpp
    src/object/function_doc_signature.cpp
    )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_python SHARED
        ${CURRENT_TARGET_SOURCES}
        )
else()
    add_library(boost_python STATIC
        ${CURRENT_TARGET_SOURCES}
        )
endif()

target_include_directories(boost_python PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    ${PYTHON_INCLUDE_DIRS}
    )

set_property(TARGET boost_python APPEND PROPERTY
    COMPILE_DEFINITIONS BOOST_PYTHON_SOURCE
    )

target_link_libraries(boost_python PUBLIC
    boost::assert
    boost::foreach
    boost::math::special_functions
    boost::serialization
    boost::conversion
    boost::concept_check
    boost::graph
    boost::parameter
    ${PYTHON_LIBRARIES}
    )

if(NOT BUILD_SHARED_LIBS)
    set_property(TARGET boost_python APPEND PROPERTY
        COMPILE_DEFINITIONS "BOOST_PYTHON_STATIC_LIB=1"
        )
endif()

add_library(boost::python ALIAS boost_python)
set_property(TARGET boost_python PROPERTY EXPORT_NAME python)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_python EXPORT ${PROJECT_NAME}Targets
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
    Foreach
    Math
    Serialization
    Conversion
    ConceptCheck
    Graph
    Parameter
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

if(APPLE AND NOT ${CMAKE_TARGET_ARCHITECTURE} STREQUAL ${CMAKE_HOST_SYSTEM_PROCESSOR})
    set_target_properties(boost_python PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY ${APPLE_CODE_SIGN_IDENTITY})
    set_target_properties(boost_python PROPERTIES XCODE_ATTRIBUTE_DEVELOPMENT_TEAM ${CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM})
endif()