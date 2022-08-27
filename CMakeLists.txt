cmake_minimum_required(VERSION 3.14)
project(linear_sum_assignment)

## Compile options
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
  set(CMAKE_CXX_EXTENSIONS OFF)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -Werror)
endif()

## Dependencies
find_package(ament_cmake REQUIRED)

include(FetchContent)
fetchcontent_declare(scipy
  GIT_REPOSITORY https://github.com/scipy/scipy.git
  GIT_TAG 2e5883ef7af4f5ed4a5b80a1759a45e43163bf3f # v1.9.1
)
fetchcontent_makeavailable(scipy)

add_library(${PROJECT_NAME} SHARED
  ${scipy_SOURCE_DIR}/scipy/optimize/rectangular_lsap/rectangular_lsap.cpp
)

target_include_directories(${PROJECT_NAME} PUBLIC
  "$<BUILD_INTERFACE:${scipy_SOURCE_DIR}/scipy/optimize/rectangular_lsap>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
)

install(
  DIRECTORY
    ${scipy_SOURCE_DIR}/scipy/optimize/rectangular_lsap/
  DESTINATION
    ${CMAKE_INSTALL_PREFIX}/include/${PROJECT_NAME}/${PROJECT_NAME}
  FILES_MATCHING PATTERN "*.h"
)

install(TARGETS ${PROJECT_NAME} EXPORT export_${PROJECT_NAME})

ament_export_targets(export_${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()


