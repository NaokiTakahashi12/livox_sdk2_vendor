cmake_minimum_required(VERSION 3.8)
project(livox_sdk2_vendor)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)

find_library(livox_sdk2_library liblivox_lidar_sdk_shared.so
  HINTS
    $ENV{HOME}/.local/lib
    ${CMAKE_INSTALL_PREFIX}/lib
    /usr/local/lib
    /usr/lib
    $ENV{LD_LIBRARY_PATH}
)
if(livox_sdk2_library)
  set(livox_sdk2_FOUND TRUE)
else()
  set(livox_sdk2_FOUND FALSE)
endif()

if(NOT livox_sdk2_FOUND)
  set(livox_sdk2_package_version 1.2.5)
  set(livox_sdk2_install_dir "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-install")
  set(extra_cmake_args)
  list(APPEND extra_cmake_args "-D CMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}")
  list(APPEND extra_cmake_args "-D CMAKE_INSTALL_PREFIX=${livox_sdk2_install_dir}")
  list(APPEND extra_cmake_args "-D CMAKE_POSITION_INDEPENDENT_CODE=ON")
  include(ExternalProject)
  externalproject_add(livox_sdk2_${livox_sdk2_package_version}
    GIT_REPOSITORY https://github.com/Livox-SDK/Livox-SDK2.git
    GIT_TAG v${livox_sdk2_package_version}
    CMAKE_ARGS ${extra_cmake_args}
  )
  install(
    DIRECTORY ${livox_sdk2_install_dir}/
    DESTINATION ${CMAKE_INSTALL_PREFIX}
    USE_SOURCE_PERMISSIONS
  )
endif()

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
