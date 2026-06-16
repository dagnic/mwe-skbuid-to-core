cmake_minimum_required(VERSION 3.15...3.26)
project(mwe-skbuild-to-core LANGUAGES CXX)

set(PYBIND11_FINDPYTHON ON)
find_package(pybind11 CONFIG REQUIRED)

include(ExternalProject)

# ----------------------------
# Autotools library build
# ----------------------------
ExternalProject_Add(
    mycpplib_ext
    SOURCE_DIR ${CMAKE_SOURCE_DIR}/sub
    CONFIGURE_COMMAND autoreconf -fi ${CMAKE_SOURCE_DIR}/sub && ${CMAKE_SOURCE_DIR}/sub/configure --prefix=${CMAKE_SOURCE_DIR}/install
    BUILD_COMMAND make
    INSTALL_COMMAND make install
    INSTALL_DIR ${CMAKE_SOURCE_DIR}/install
)

ExternalProject_Get_Property(mycpplib_ext install_dir)

add_library(mycpplib STATIC IMPORTED GLOBAL)
add_dependencies(mycpplib mycpplib_ext)

set_target_properties(mycpplib PROPERTIES
    IMPORTED_LOCATION ${install_dir}/lib/libmycpplib.a
    INTERFACE_INCLUDE_DIRECTORIES ${install_dir}/include
)

# ----------------------------
# pybind11 module
# ----------------------------
pybind11_add_module(example src/example.cpp)
install(TARGETS example LIBRARY DESTINATION .)