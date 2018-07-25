cmake_minimum_required(VERSION 2.8)

project(ivi-env)

include(ExternalProject)

ExternalProject_Add(wayland
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/wayland
    CONFIGURE_COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/wayland/autogen.sh --prefix=<INSTALL_DIR> --disable-documentation
    BUILD_COMMAND ${MAKE}
    INSTALL_DIR root
    )

ExternalProject_Add(wayland-protocols
    DEPENDS wayland
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/wayland-protocols
    CONFIGURE_COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/wayland-protocols/autogen.sh --prefix=<INSTALL_DIR>
    BUILD_COMMAND ${MAKE}
    INSTALL_DIR root
    )

ExternalProject_Add(wayland-ivi-extension
    DEPENDS wayland wayland-protocols
    SOURCE_DIR ${CMAKE_SOURCE_DIR}/wayland-ivi-extension
    BUILD_COMMAND make
    INSTALL_COMMAND make DESTDIR=<INSTALL_DIR> install && pwd
    INSTALL_DIR root
)


ExternalProject_Add(mesa
    DEPENDS wayland wayland-protocols
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/mesa
    CONFIGURE_COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/mesa/autogen.sh --prefix=<INSTALL_DIR> --enable-gles2  --disable-egl
    --with-egl-platforms=x11,wayland,drm --enable-gbm --enable-shared-glapi --with-gallium-drivers=r600,swrast,nouveau
    BUILD_COMMAND ${MAKE}
    INSTALL_COMMAND make DESTDIR=<INSTALL_DIR> install
    INSTALL_DIR root
    )

#ExternalProject_Add(libunwind
#    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/libunwind
#    CONFIGURE_COMMAND pwd && ${CMAKE_CURRENT_SOURCE_DIR}/libunwind/autogen.sh --prefix=<INSTALL_DIR>
#    BUILD_COMMAND ${MAKE}
#   INSTALL_DIR root
#)

ExternalProject_Add(libinput
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/libinput
    CONFIGURE_COMMAND meson ${CMAKE_CURRENT_SOURCE_DIR}/libinput --prefix=<INSTALL_DIR>
    BUILD_COMMAND ninja
    INSTALL_COMMAND ninja install
    INSTALL_DIR root
    )

ExternalProject_Add(weston
    DEPENDS wayland wayland-protocols mesa
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/weston
    CONFIGURE_COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/weston/autogen.sh --prefix=<INSTALL_DIR> --disable-setuid-install
    BUILD_COMMAND ${MAKE}
    INSTALL_DIR root
    )
