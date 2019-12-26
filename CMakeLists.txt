cmake_minimum_required(VERSION 3.2)

project(gif VERSION 5.2.1 LANGUAGES C)

set(CMAKE_DEBUG_POSTFIX "d")

add_library(gif
  dgif_lib.c
  egif_lib.c
  gif_err.c
  gif_font.c
  gif_hash.c
  gif_hash.h
  gif_lib.h
  gif_lib_private.h
  gifalloc.c
  openbsd-reallocarray.c
  quantize.c
)

set_target_properties(gif
  PROPERTIES
    PDB_NAME gif
    PDB_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}
)
if (NOT BUILD_SHARED_LIBS)
  set_target_properties(gif
    PROPERTIES
      OUTPUT_NAME_DEBUG gifd
      COMPILE_PDB_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}
      COMPILE_PDB_NAME gif
      COMPILE_PDB_NAME_DEBUG gifd
  )
  install(FILES
    ${PROJECT_BINARY_DIR}/RelWithDebInfo/gif.pdb
    DESTINATION lib
    CONFIGURATIONS RelWithDebInfo
  )
  install(FILES
    ${PROJECT_BINARY_DIR}/Debug/gifd.pdb
    DESTINATION lib
    CONFIGURATIONS Debug
  )
else()
  install(FILES
    $<TARGET_PDB_FILE:gif>
    DESTINATION lib
  )
endif()
target_include_directories(gif
  PRIVATE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  INTERFACE
  $<INSTALL_INTERFACE:include>
)

target_compile_definitions(gif
  PRIVATE
  _CRT_SECURE_NO_WARNINGS
  _CRT_STDC_NO_DEPRECATE
)

target_compile_options(gif
  PRIVATE
    /sdl-
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)

install(TARGETS gif EXPORT gif
  RUNTIME DESTINATION bin
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
)

install(FILES
  ${CMAKE_CURRENT_SOURCE_DIR}/gif_lib.h
  DESTINATION include
)

install(EXPORT gif
  FILE
  ${PROJECT_NAME}.cmake
  NAMESPACE
    ${PROJECT_NAME}::
  DESTINATION
    lib/cmake/${PROJECT_NAME}
)

install(FILES
  cmake/${PROJECT_NAME}-config.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
  DESTINATION
    lib/cmake/${PROJECT_NAME}
)