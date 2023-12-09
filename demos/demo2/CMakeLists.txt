project(demo2)

cmake_minimum_required(VERSION 3.16.0)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOUIC TRUE)
set(CMAKE_AUTORCC TRUE)

find_package( Qt6 COMPONENTS Widgets Network QUIET)
if (NOT Qt6_FOUND)
	find_package( Qt5 REQUIRED COMPONENTS Widgets Network )
endif()
find_package(SmtpMime QUIET)

add_executable(demo2 demo2.cpp sendemail.cpp sendemail.h sendemail.ui)
target_link_libraries(demo2 SmtpMime Qt::Widgets Qt::Network)