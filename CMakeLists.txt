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