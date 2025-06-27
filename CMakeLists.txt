cmake_minimum_required(VERSION 3.14)

project(
    piper_phonemize
    VERSION 1.2.0
    DESCRIPTION "Phonemization library for Piper text to speech system (Patched)"
    HOMEPAGE_URL "https://github.com/rhasspy/piper-phonemize"
    LANGUAGES CXX
)

if(NOT APPLE)
    string(APPEND CMAKE_CXX_FLAGS " -Wall -Wextra")
    string(APPEND CMAKE_C_FLAGS " -Wall -Wextra")
endif()

add_library(
    piper_phonemize SHARED
    src/phonemize.cpp
    src/phoneme_ids.cpp
    src/tashkeel.cpp
    src/shared.cpp
)

set_target_properties(piper_phonemize PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION ${PROJECT_VERSION_MAJOR}
)

# --- Dependencias (Versión Corregida) ---

# 1. onnxruntime: Usa la ruta absoluta que le proporcionamos.
set(ONNXRUNTIME_DIR "/home/adrian/dev_libs/onnxruntime-linux-x64-gpu-1.17.3")

# 2. espeak-ng: Usa pkg-config para encontrar la librería del sistema.
find_package(PkgConfig REQUIRED)
pkg_check_modules(ESPEAK_NG REQUIRED espeak-ng)

# --- Enlazado (Versión Corregida) ---

target_include_directories(
    piper_phonemize PUBLIC
    "${ONNXRUNTIME_DIR}/include"
    ${ESPEAK_NG_INCLUDE_DIRS}
)

target_link_directories(
    piper_phonemize PUBLIC
    "${ONNXRUNTIME_DIR}/lib"
)

target_link_libraries(
    piper_phonemize
    onnxruntime
    ${ESPEAK_NG_LIBRARIES}
)

target_compile_features(piper_phonemize PUBLIC cxx_std_17)

# --- Instalación (Simplificada) ---
include(GNUInstallDirs)
install(
    TARGETS piper_phonemize
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
