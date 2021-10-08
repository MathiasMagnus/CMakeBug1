cmake_minimum_required(VERSION 3.0)

project(CMakeBug1
  LANGUAGES C
)
add_subdirectory(Headers)

# error C1083: Cannot open include file: 'proj/header1.h'
add_executable(Link_And_Genexpr source.c)
target_link_libraries(Link_And_Genexpr
  PRIVATE Proj::Headers)
target_include_directories(Link_And_Genexpr
  PRIVATE
    $<TARGET_PROPERTY:Proj::Headers,INTERFACE_INCLUDE_DIRECTORIES>/proj
)

# error C1083: Cannot open include file: 'proj/header1.h'
add_executable(Genexpr_Only_Proj_First source.c)
target_include_directories(Genexpr_Only_Proj_First
  PRIVATE
    $<TARGET_PROPERTY:Proj::Headers,INTERFACE_INCLUDE_DIRECTORIES>/proj
    $<TARGET_PROPERTY:Proj::Headers,INTERFACE_INCLUDE_DIRECTORIES>
)

# error C1083: Cannot open include file: 'header2.h'
add_executable(Genexpr_Only_Proj_Second source.c)
target_include_directories(Genexpr_Only_Proj_Second
  PRIVATE
    $<TARGET_PROPERTY:Proj::Headers,INTERFACE_INCLUDE_DIRECTORIES>
    $<TARGET_PROPERTY:Proj::Headers,INTERFACE_INCLUDE_DIRECTORIES>/proj
)

# Compiles
add_executable(Link_And_Workaround source.c)

get_target_property(PROJ_HEADERS_INTERFACE_INCLUDE_DIRECTORIES Proj::Headers INTERFACE_INCLUDE_DIRECTORIES)

if(PROJ_HEADERS_INTERFACE_INCLUDE_DIRECTORIES MATCHES "BUILD_INTERFACE")
  # When building Proj::Headers is part of this build, we have a property like for eg.
  # $<BUILD_INTERFACE:C:/SDK/Headers>;$<INSTALL_INTERFACE:include>
  # and need to touch up this property. We want the BUILD_INTERFACE part only.
  # When Proj::Headers is consumed through a Package Config file, this property
  # no longer holds unevaluated generator expressions.
  string(REGEX MATCHALL
    [[\$<BUILD_INTERFACE:(.*)>;]]
    PROJ_HEADERS_INTERFACE_INCLUDE_DIRECTORIES
    "${PROJ_HEADERS_INTERFACE_INCLUDE_DIRECTORIES}")
  set(PROJ_HEADERS_INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_MATCH_1}")
endif()

target_link_libraries(Link_And_Workaround
  PRIVATE Proj::Headers)
target_include_directories(Link_And_Workaround
  PRIVATE
    ${PROJ_HEADERS_INTERFACE_INCLUDE_DIRECTORIES}/proj
)