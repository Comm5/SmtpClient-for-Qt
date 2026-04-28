# -DSmtpMime_EXAMPLES=[true|false]
#  Build the examples.
#  Default=true
cmake_minimum_required(VERSION 3.16.0)

project(SmtpMime VERSION 2.0.0 LANGUAGES CXX)


if (POLICY CMP0048)
  cmake_policy(SET CMP0048 NEW)
endif (POLICY CMP0048)


SET(CMAKE_COLOR_MAKEFILE ON)
#SET(CMAKE_VERBOSE_MAKEFILE ON)
set(CMAKE_AUTOMOC ON)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
include(FeatureSummary)

option(SMTPMIME_EXAMPLES "Build examples" ON)
option(SMTPMIME_STATIC "Build library static" OFF)
option(USE_QT6 "Build with Qt6" ON)
if (USE_QT6)
    find_package(Qt6 REQUIRED COMPONENTS Core Network OPTIONAL_COMPONENTS Widgets)
    if (Qt6_FOUND)
        message(STATUS "Using Qt6")
    else()
        message(FATAL_ERROR "Qt6 not found")
    endif()
else()
    find_package(Qt5 REQUIRED COMPONENTS Core Network OPTIONAL_COMPONENTS Widgets)
    if (Qt5_FOUND)
        message(STATUS "Using Qt5")
    else()
        message(FATAL_ERROR "Qt5 not found")
    endif()
endif()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/bin")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/lib")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/lib")

set(HEADERS
    src/emailaddress.h
    src/mimeattachment.h
    src/mimebase64encoder.h
    src/mimebase64formatter.h
    src/mimebytearrayattachment.h
    src/mimecontentencoder.h
    src/mimecontentformatter.h
    src/mimefile.h
    src/mimehtml.h
    src/mimeinlinefile.h
    src/mimemessage.h
    src/mimepart.h
    src/mimeqpencoder.h
    src/mimeqpformatter.h
    src/mimetext.h
    src/mimemultipart.h
    src/quotedprintable.h
    src/smtpclient.h
    src/SmtpMime
    src/smtpmime_global.h
)

set(SOURCES
    src/emailaddress.cpp
    src/mimeattachment.cpp
    src/mimebase64encoder.cpp
    src/mimebase64formatter.cpp
    src/mimebytearrayattachment.cpp
    src/mimecontentencoder.cpp
    src/mimecontentformatter.cpp
    src/mimefile.cpp
    src/mimehtml.cpp
    src/mimeinlinefile.cpp
    src/mimemessage.cpp
    src/mimemultipart.cpp
    src/mimepart.cpp
    src/mimeqpencoder.cpp
    src/mimeqpformatter.cpp
    src/mimetext.cpp
    src/quotedprintable.cpp
    src/smtpclient.cpp
)

if (NOT SMTPMIME_STATIC)
    # Building a shared library
    add_library(${PROJECT_NAME} SHARED ${SOURCES} ${HEADERS})

    target_compile_definitions(${PROJECT_NAME}
	    PRIVATE SMTP_MIME_LIBRARY
    )
    target_link_libraries(${PROJECT_NAME}
        PUBLIC Qt::Core Qt::Network
    )

    set(CMAKE_SKIP_BUILD_RPATH FALSE)
    set(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
    set(CMAKE_INSTALL_RPATH “${ORIGIN}”)
else()
    add_library(${PROJECT_NAME} STATIC ${SOURCES} ${HEADERS})

    target_compile_definitions(${PROJECT_NAME}
        PUBLIC SMTP_MIME_LIBRARY_STATIC
    )
endif()


set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${${PROJECT_NAME}_VERSION})
target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
        $<INSTALL_INTERFACE:include/${PROJECT_NAME}>
)

if(WIN32)
    set(CMAKE_DEBUG_POSTFIX "d")
    set_target_properties(${PROJECT_NAME} PROPERTIES DEBUG_POSTFIX ${CMAKE_DEBUG_POSTFIX})
endif()

include(CMakePackageConfigHelpers)
write_basic_package_version_file( ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake VERSION ${PROJECT_VERSION} COMPATIBILITY SameMajorVersion)

include(GNUInstallDirs)

install(
    TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(
    FILES ${HEADERS}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}
)

install(
    EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)

configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)

install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)

if(SMTPMIME_EXAMPLES)
    add_subdirectory(demos)
endif()

feature_summary(WHAT ENABLED_FEATURES DISABLED_FEATURES INCLUDE_QUIET_PACKAGES VAR enabledFeaturesText)
message(STATUS "${enabledFeaturesText}")
feature_summary(WHAT ENABLED_FEATURES DESCRIPTION "Enabled features:")
