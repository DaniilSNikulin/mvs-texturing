externalproject_add(ext_mapmap
    PREFIX          ext_mapmap
    GIT_REPOSITORY  https://github.com/dthuerck/mapmap_cpu.git
    GIT_TAG         master
    UPDATE_COMMAND  ""
    SOURCE_DIR      ${CMAKE_SOURCE_DIR}/elibs/mapmap
    CONFIGURE_COMMAND ""
    BUILD_COMMAND   ""
    INSTALL_COMMAND ""
)
add_library(mapmap INTERFACE)
add_dependencies(mapmap ext_mapmap)
target_include_directories(mapmap SYSTEM INTERFACE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/mapmap>
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/mapmap/mapmap>
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/mapmap/ext/dset>
    $<INSTALL_INTERFACE:include/tex/elibs/mapmap>
    $<INSTALL_INTERFACE:include/tex/elibs/mapmap/mapmap>
    $<INSTALL_INTERFACE:include/tex/elibs/mapmap/ext/dset>
)


externalproject_add(ext_rayint
    PREFIX          ext_rayint
    GIT_REPOSITORY  https://github.com/nmoehrle/rayint.git
    GIT_TAG         cuda
    UPDATE_COMMAND  ""
    SOURCE_DIR      ${CMAKE_SOURCE_DIR}/elibs/rayint
    CONFIGURE_COMMAND ""
    BUILD_COMMAND   ""
    INSTALL_COMMAND ""
)
add_library(rayint INTERFACE)
add_dependencies(rayint ext_rayint)
target_include_directories(rayint SYSTEM INTERFACE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/rayint/libs>
    $<INSTALL_INTERFACE:include/tex/elibs/rayint/libs>
)


externalproject_add(ext_eigen
    PREFIX          ext_eigen
    URL             https://gitlab.com/libeigen/eigen/-/archive/3.3.2/eigen-3.3.2.tar.gz
    URL_MD5         02edfeec591ae09848223d622700a10b
    SOURCE_DIR      ${CMAKE_SOURCE_DIR}/elibs/eigen
    CONFIGURE_COMMAND ""
    BUILD_COMMAND   ""
    INSTALL_COMMAND ""
)
add_library(eigen INTERFACE)
add_dependencies(eigen ext_eigen)
target_include_directories(eigen SYSTEM INTERFACE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/eigen>
    $<INSTALL_INTERFACE:include/tex/elibs/eigen>
)


externalproject_add(ext_mve
    PREFIX          mve
    GIT_REPOSITORY  https://github.com/nmoehrle/mve.git
    UPDATE_COMMAND  ""
    SOURCE_DIR      ${CMAKE_SOURCE_DIR}/elibs/mve
    CONFIGURE_COMMAND ""
    BUILD_COMMAND   make -C libs/mve && make -C libs/util #not platform independent
    BUILD_IN_SOURCE 1
    INSTALL_COMMAND ""
)
if (WIN32)
    set(lib_ext ".lib")
else()
    set(lib_ext ".a")
endif()
add_library(mve INTERFACE)
add_dependencies(mve ext_mve)
target_include_directories(mve SYSTEM INTERFACE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/mve/libs>
    $<INSTALL_INTERFACE:include/tex/elibs/mve/libs>
)
target_link_libraries(mve
  INTERFACE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/mve/libs/mve/libmve${lib_ext}>
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/elibs/mve/libs/util/libmve_util${lib_ext}>
    $<INSTALL_INTERFACE:"mve">
    $<INSTALL_INTERFACE:"mve_util">
    ${JPEG_LIBRARIES}
    ${PNG_LIBRARIES}
    ${TIFF_LIBRARIES}
)


##############################################
# Combination target

add_library(texrecon_elibs INTERFACE)
target_link_libraries(texrecon_elibs
  INTERFACE
    mapmap
    rayint
    eigen
    mve
    ${TBB_LIBRARIES}
)


##############################################
# Installation instructions

install(
  TARGETS                   texrecon_elibs mapmap rayint eigen mve
  EXPORT                    texrecon_elibs-export
  LIBRARY DESTINATION       "${CMAKE_INSTALL_LIBDIR}"
  ARCHIVE DESTINATION       "${CMAKE_INSTALL_LIBDIR}"
  RUNTIME DESTINATION       "${CMAKE_INSTALL_BINDIR}"
  INCLUDES DESTINATION      "${CMAKE_INSTALL_INCLUDEDIR}"
)
install(
  EXPORT                    texrecon_elibs-export
  FILE                      ${ELIBRARY_INTERFACE}Targets.cmake
  NAMESPACE                 ${ELIBRARY_INTERFACE}::
  DESTINATION               "${INSTALL_CONFIGDIR}"
)
install(
  DIRECTORY                 "${CMAKE_SOURCE_DIR}/elibs"
  DESTINATION               "${CMAKE_INSTALL_INCLUDEDIR}/tex"
  FILES_MATCHING PATTERN    "*.h"
)
install(
  FILES
    "${CMAKE_SOURCE_DIR}/elibs/mve/libs/mve/libmve${lib_ext}"
    "${CMAKE_SOURCE_DIR}/elibs/mve/libs/util/libmve_util${lib_ext}"
  DESTINATION
    "${CMAKE_INSTALL_LIBDIR}"
)

