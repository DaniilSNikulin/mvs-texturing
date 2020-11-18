file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cpp")

add_library(tex STATIC ${SOURCES})
set_property(TARGET tex PROPERTY INTERPROCEDURAL_OPTIMIZATION True)
target_link_libraries(tex texrecon_elibs)
target_include_directories(tex
    INTERFACE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/libs>
    $<INSTALL_INTERFACE:include/tex>
)

set_target_properties(tex PROPERTIES
  EXPORT_NAME ${LIBRARY_INTERFACE}
)

install(
  TARGETS                 tex
  EXPORT                  tex-export
  LIBRARY DESTINATION     "${CMAKE_INSTALL_LIBDIR}"
  ARCHIVE DESTINATION     "${CMAKE_INSTALL_LIBDIR}"
  RUNTIME DESTINATION     "${CMAKE_INSTALL_BINDIR}"
  INCLUDES DESTINATION    "${CMAKE_INSTALL_INCLUDEDIR}"
)
install(
  EXPORT                  tex-export
  FILE                    ${LIBRARY_INTERFACE}Targets.cmake
  NAMESPACE               ${LIBRARY_INTERFACE}::
  DESTINATION             "${INSTALL_CONFIGDIR}"
)
install(
  DIRECTORY               "${CMAKE_CURRENT_SOURCE_DIR}"
  DESTINATION             "${CMAKE_INSTALL_INCLUDEDIR}"
  FILES_MATCHING PATTERN  "*.h"
)

