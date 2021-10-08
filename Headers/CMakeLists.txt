cmake_minimum_required(VERSION 3.0)

project(Headers
  VERSION 0.1
  LANGUAGES C
)

add_library(Headers INTERFACE)
add_library(Proj::Headers ALIAS Headers)

target_include_directories(Headers
  INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include>
)

install(
  TARGETS Headers
  EXPORT ProjHeadersTargets
)

include(GNUInstallDirs)

install(
  DIRECTORY proj
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
export(
  EXPORT ProjHeadersTargets
  FILE ${PROJECT_BINARY_DIR}/Proj/ProjHeadersTargets.cmake
  NAMESPACE Proj::
)
file(
  WRITE ${PROJECT_BINARY_DIR}/ProjHeaders/ProjHeadersConfig.cmake
  "include(\"\${CMAKE_CURRENT_LIST_DIR}/ProjHeadersTargets.cmake\")"
)

set(config_package_location ${CMAKE_INSTALL_DATADIR}/cmake/Proj)
install(
  EXPORT ProjHeadersTargets
  FILE ProjHeadersTargets.cmake
  NAMESPACE Proj::
  DESTINATION ${config_package_location}
)
install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/ProjHeaders/ProjHeadersConfig.cmake
  DESTINATION ${config_package_location}
)

unset(CMAKE_SIZEOF_VOID_P)
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/ProjHeaders/ProjHeadersConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)
install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/ProjHeaders/ProjHeadersConfigVersion.cmake
  DESTINATION ${config_package_location}
)