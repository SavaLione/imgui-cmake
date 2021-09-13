#################################################################################
# Copyright (c) 2021, Savely Pototsky (SavaLione)
# All rights reserved.
#################################################################################
# Checking the minimum version
cmake_minimum_required (VERSION 3.13.1)

# Project name
set(IMGUI_PROJECT "imgui")
set(IMGUI_PROJECT_VERSION "1.84.2.8275456")

project(${IMGUI_PROJECT}
    VERSION ${IMGUI_PROJECT_VERSION}
    LANGUAGES CXX
)

#################################################################################
#                                     Options                                   #
#################################################################################
option(IMGUI_BUILD_STATIC "Build static library" OFF)

option(IMGUI_BUILD_BACKEND_ALLEGRO5 "Build the allegro5 backend" OFF)
option(IMGUI_BUILD_BACKEND_ANDROID "Build the android backend" OFF)
option(IMGUI_BUILD_BACKEND_DX9 "Build the dx9 backend" OFF)
option(IMGUI_BUILD_BACKEND_DX10 "Build the dx10 backend" OFF)
option(IMGUI_BUILD_BACKEND_DX11 "Build the dx11 backend" OFF)
option(IMGUI_BUILD_BACKEND_DX12 "Build the dx12 backend" OFF)
option(IMGUI_BUILD_BACKEND_GLFW "Build the glfw backend" ON)
option(IMGUI_BUILD_BACKEND_GLUT "Build the glut backend" OFF)
option(IMGUI_BUILD_BACKEND_MARMALADE "Build the marmalade backend" OFF)
option(IMGUI_BUILD_BACKEND_METAL "Build the metal backend" OFF)
option(IMGUI_BUILD_BACKEND_OPENGL2 "Build the opengl2 backend" OFF)
option(IMGUI_BUILD_BACKEND_OPENGL3 "Build the opengl2 backend" OFF)
option(IMGUI_BUILD_BACKEND_OPENGL3_LOADER "Build the opengl3 loader backend" OFF)
option(IMGUI_BUILD_BACKEND_OSX "Build the osx backend" OFF)
option(IMGUI_BUILD_BACKEND_SDL "Build the sdl backend" OFF)
option(IMGUI_BUILD_BACKEND_VULKAN "Build the vulkan backend" OFF)
option(IMGUI_BUILD_BACKEND_WGPU "Build the wgpu backend" OFF)
option(IMGUI_BUILD_BACKEND_WIN32 "Build the win32 backend" OFF)

#################################################################################
#                                      Imgui                                    #
#################################################################################

set(IMGUI_SRC
    src/imgui.cpp
    src/imgui_demo.cpp
    src/imgui_draw.cpp
    src/imgui_tables.cpp
    src/imgui_widgets.cpp
)

add_library(${IMGUI_PROJECT} STATIC ${IMGUI_SRC})
target_include_directories(${IMGUI_PROJECT} PUBLIC ${PROJECT_SOURCE_DIR}/include)

#################################################################################
#                                     Backends                                  #
#################################################################################

# OpenGL3
if (IMGUI_BUILD_BACKEND_OPENGL3)
    # imgui OpenGL3
    project(imgui_opengl3)
    set(IMGUI_BACKEND_OPENGL3_SRC src/backends/imgui_impl_opengl3.cpp)
    # OpenGL
    find_package(OpenGL REQUIRED)
    # imgui opengl3
    if(IMGUI_BUILD_STATIC)
	    add_library(imgui_opengl3 STATIC ${IMGUI_BACKEND_OPENGL3_SRC})
    else()
	    add_library(imgui_opengl3 SHARED ${IMGUI_BACKEND_OPENGL3_SRC})
    endif()
    target_link_libraries(imgui_opengl3 imgui)
    target_include_directories(imgui_opengl3 PUBLIC ${PROJECT_SOURCE_DIR}/include/backends)
    # OpenGL
    target_include_directories(imgui_opengl3 PUBLIC ${OPENGL_INCLUDE_DIR})
    target_link_libraries(imgui_opengl3 ${OPENGL_gl_LIBRARY})
endif()

# GLFW
if (IMGUI_BUILD_BACKEND_GLFW)
    # imgui glfw
    project(imgui_glfw)
    set(IMGUI_BACKEND_GLFW_SRC src/backends/imgui_impl_glfw.cpp)
    # glfw
    set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
    set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
    set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
    set(IMGUI_DEPS_GLFW_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/lib/glfw/include" CACHE PATH "imgui GLFW lib include dependence")
    if (NOT TARGET glfw)
        add_subdirectory(${PROJECT_SOURCE_DIR}/lib/glfw)
    endif()
    # imgui glfw
    if(IMGUI_BUILD_STATIC)
	    add_library(imgui_glfw STATIC ${IMGUI_BACKEND_GLFW_SRC})
    else()
	    add_library(imgui_glfw SHARED ${IMGUI_BACKEND_GLFW_SRC})
    endif()
    target_link_libraries(imgui_glfw imgui imgui_opengl3 glfw)
    target_include_directories(imgui_glfw PUBLIC ${PROJECT_SOURCE_DIR}/include/backends)
    target_include_directories(imgui_glfw PUBLIC ${GLFW_INCLUDE_DIR})
endif()