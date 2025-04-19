cmake_minimum_required(VERSION 3.22)
project(polyclipping
    VERSION 22.0.0
    LANGUAGES CXX
)

# Standard‑Install‑Verzeichnisse
include(GNUInstallDirs)

# Default-Build‑Typ für Single‑Config‑Generatoren
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()

# Shared-vs-Static Option
option(BUILD_SHARED_LIBS "Build shared libraries instead of static" ON)

# Die Bibliothek
add_library(polyclipping
    clipper.cpp
)

# C++ Standard (mindestens C++11)
target_compile_features(polyclipping PUBLIC cxx_std_11)

# Include-Pfade: zur Build-Zeit aus dem Quellverzeichnis, nach Install aus ${CMAKE_INSTALL_INCLUDEDIR}
target_include_directories(polyclipping
    PUBLIC
      $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
      $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

# Versions- und SOVERSION‑Properties
set_target_properties(polyclipping PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
    WINDOWS_EXPORT_ALL_SYMBOLS ON
)

# pkg-config-File erzeugen
configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/polyclipping.pc.cmakein
  ${CMAKE_CURRENT_BINARY_DIR}/polyclipping.pc
  @ONLY
)

# ---- Install-Regeln ----

# Bibliothek installieren
install(TARGETS polyclipping
    RUNTIME   DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY   DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE   DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

# Header installieren
install(FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/clipper.hpp
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# pkg-config installieren
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/polyclipping.pc
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
)
