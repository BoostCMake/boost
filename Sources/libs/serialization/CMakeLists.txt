# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostSerialization CXX)

if(POLICY CMP0054)
    cmake_policy(SET CMP0054 NEW)
endif(POLICY CMP0054)

if(POLICY CMP0063)
    cmake_policy(SET CMP0063 NEW)
endif(POLICY CMP0063)

if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    add_definitions(-ftemplate-depth=300)
    # we use gcc to test for C++03 compatibility
    add_definitions(std=c++03)
    set(COMPILER_SUPPORTS_CXX11 FALSE)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    add_definitions(/wd4996)
    set(COMPILER_SUPPORTS_CXX11 TRUE)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "AppleClang")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ftemplate-depth=300")
    #set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++98")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
    set(CMAKE_CXX_FLAGS_DEBUG "-g -O0")
    set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-g -O3")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -stdlib=libc++ -dead_strip")
    set(COMPILER_SUPPORTS_CXX11 TRUE)
endif()

if(MSVC)
    add_definitions(
            /Gy
            /wd4244 # converting 'std::streamsize' in 'size_t'
            /D_CRT_SECURE_NO_WARNINGS
            /D_SCL_SECURE_NO_WARNINGS
    )
endif()

if(APPLE)
    # note: it seems that bjam builds both address models in any case
    # so we can defer this decision to the IDE just as we do for debug/release
    # so we'll not use this now
    # set(Boost_ADDRESS_MODEL 64 CACHE INTEGER "32/64 bits")
    set(Boost_USE_STATIC_LIBS ON CACHE BOOL "Link to Boost static libraries")
    set(Boost_USE_MULTITHREADED ON)
else()
    set(Boost_ADDRESS_MODEL 64 CACHE INTEGER "32/64 bits")
    set(Boost_USE_STATIC_LIBS ON CACHE BOOL "Link to Boost static libraries")
    set(Boost_USE_MULTITHREADED ON)
endif()

list(APPEND CURRENT_TARGET_SOURCES
    src/archive_exception.cpp
        src/basic_archive.cpp
        src/basic_iarchive.cpp
        src/basic_iserializer.cpp
        src/basic_oarchive.cpp
        src/basic_oserializer.cpp
        src/basic_pointer_iserializer.cpp
        src/basic_pointer_oserializer.cpp
        src/basic_serializer_map.cpp
        src/basic_text_iprimitive.cpp
        src/basic_text_oprimitive.cpp
        src/basic_xml_archive.cpp
        src/binary_iarchive.cpp
        src/binary_oarchive.cpp
        src/extended_type_info_no_rtti.cpp
        src/extended_type_info_typeid.cpp
        src/extended_type_info.cpp
        src/polymorphic_iarchive.cpp
        src/polymorphic_oarchive.cpp
        src/singleton.cpp
        src/stl_port.cpp
        src/text_iarchive.cpp
        src/text_oarchive.cpp
        src/void_cast.cpp
        src/xml_archive_exception.cpp
        src/xml_iarchive.cpp
        src/xml_oarchive.cpp
        src/xml_grammar.cpp
        src/utf8_codecvt_facet.cpp
        src/basic_xml_grammar.ipp
        )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_serialization SHARED
        ${CURRENT_TARGET_SOURCES}
        )
else()
    add_library(boost_serialization STATIC
        ${CURRENT_TARGET_SOURCES}
        )    
endif()

target_include_directories(boost_serialization PUBLIC
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
        )

target_link_libraries(boost_serialization PUBLIC
        boost::assert
        boost::array
        boost::align
        boost::config
        boost::detail
        boost::spirit
        boost::function
        boost::preprocessor
        boost::type_traits
        boost::type_index
        boost::io
        boost::move
        boost::iterator
        boost::utility
        boost::smart_ptr
        boost::static_assert
        boost::system
        )

add_library(boost_wserialization
        src/codecvt_null.cpp
        src/basic_text_wiprimitive.cpp
        src/basic_text_woprimitive.cpp
        src/text_wiarchive.cpp
        src/text_woarchive.cpp
        src/xml_wiarchive.cpp
        src/xml_woarchive.cpp
        src/xml_wgrammar.cpp
        src/basic_xml_grammar.ipp

        )

target_link_libraries(boost_wserialization PUBLIC
        boost_serialization
        )

add_library(boost::serialization ALIAS boost_serialization)
set_property(TARGET boost_serialization PROPERTY EXPORT_NAME serialization)
add_library(boost::wserialization ALIAS boost_wserialization)
set_property(TARGET boost_wserialization PROPERTY EXPORT_NAME wserialization)

if(CXX11_FOUND)
    set_property(TARGET boost_serialization PROPERTY CXX_STANDARD 11)
    set_property(TARGET boost_wserialization PROPERTY CXX_STANDARD 11)
endif()

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_serialization boost_wserialization
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
        Array
        Config
        Detail
        Spirit
        Function
        Preprocessor
        TypeTraits
        TypeIndex
        IO
        Iterator
        Utility
        StaticAssert
        SmartPtr
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
