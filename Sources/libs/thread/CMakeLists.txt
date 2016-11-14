# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostThread)

find_package(Threads)

list(APPEND sources
    src/future.cpp
    src/tss_null.cpp
    )

if(CMAKE_USE_WIN32_THREADS_INIT)
    list(APPEND sources
        src/win32/thread.cpp
        src/win32/tss_dll.cpp
        src/win32/tss_pe.cpp
        src/win32/gettickcount64.cpp
        )
else()
    list(APPEND sources
        src/pthread/thread.cpp
        src/pthread/once.cpp
        )
endif()

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_thread SHARED
        ${sources}
        )
else()
    add_library(boost_thread STATIC
        ${sources}
        )
endif()

target_include_directories(boost_thread PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    )

target_link_libraries(boost_thread PUBLIC
    boost::assert
    boost::atomic
    boost::align
    boost::algorithm
    boost::chrono
    boost::container
    boost::date_time
    boost::function
    boost::functional
    boost::io
    boost::intrusive
    boost::lexical_cast
    boost::move
    boost::optional
    boost::type_index
    ${CMAKE_THREAD_LIBS_INIT}
    )

if(BUILD_SHARED_LIBS)
    set_property(TARGET boost_thread APPEND PROPERTY
        COMPILE_DEFINITIONS "BOOST_THREAD_BUILD_DLL=1"
        )
else()
    set_property(TARGET boost_thread APPEND PROPERTY
        COMPILE_DEFINITIONS "BOOST_THREAD_BUILD_LIB=1"
        )
endif()

add_library(boost::thread ALIAS boost_thread)
set_property(TARGET boost_thread PROPERTY EXPORT_NAME thread)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_thread EXPORT ${PROJECT_NAME}Targets
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
    Atomic
    Algorithm
    Chrono
    DateTime
    Function
    IO
    Move
    Optional
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

export(TARGETS boost_thread
    NAMESPACE boost::
    FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)
