cmake_minimum_required(VERSION 3.22)
project(polyclipping
    VERSION 22.0.0
    LANGUAGES CXX
)

# ■ Standard‑Verzeichnisse aus GNUInstallDirs
include(GNUInstallDirs)

# ■ Default-Build‑Type nur für Single‑Config‑Generatoren setzen
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(CMAKE_BUILD_TYPE Release CACHE STRING
      "Choose the type of build." FORCE)
endif()

# ■ Option für Shared vs. Static
option(BUILD_SHARED_LIBS "Build shared libraries (.so/.dll) instead of static (.a/.lib)" ON)

# ■ Bibliothek anlegen
add_library(polyclipping
    clipper.cpp
)

# ■ Mindestens C++11 verlangen (oder höher, falls du Features nutzt)
target_compile_features(polyclipping PUBLIC cxx_std_11)

# ■ Include‑Verzeichnisse für Build vs. Install
target_include_directories(polyclipping
    PUBLIC
      $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>            # für den Build: hier liegt clipper.hpp
      $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>          # nach install: <prefix>/include
)

# ■ Versions‑ und SOVERSION‑Einstellungen
set_target_properties(polyclipping PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
    WINDOWS_EXPORT_ALL_SYMBOLS ON
)

# ■ pkg-config‑File generieren
configure_file(
  ${CMAKE_CURRENT_LIST_DIR}/polyclipping.pc.cmakein
  ${CMAKE_CURRENT_BINARY_DIR}/polyclipping.pc
  @ONLY
)

# ■ Install-Regeln
## Bibliothek
install(TARGETS polyclipping
    EXPORT polyclippingTargets
    RUNTIME   DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY   DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE   DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

## Header
install(FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/clipper.hpp
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

## pkg-config
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/polyclipping.pc
    DESTINATION ${CMAKE_INSTALL_PKGCONFIGDIR}
)

# ■ CMake‑Export für find_package()
install(EXPORT polyclippingTargets
    FILE polyclippingTargets.cmake
    NAMESPACE polyclipping::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/polyclipping
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/polyclippingConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
    ${CMAKE_CURRENT_LIST_DIR}/cmake/Config.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/polyclippingConfig.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/polyclipping
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/polyclippingConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/polyclippingConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/polyclipping
)
