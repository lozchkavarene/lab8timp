cmake_minimum_required(VERSION 3.10)

project(print)

include_directories(lib)
add_library(print STATIC lib/print.cpp)

option(BUILD_TESTS "Build tests" OFF)

if(BUILD_TESTS)

  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB PRINT_TEST_SOURCES tests/*.cpp)
  add_executable(check ${PRINT_TEST_SOURCES})
  target_link_libraries(check PUBLIC print gtest_main)
  add_test(NAME check COMMAND check)

endif()

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
