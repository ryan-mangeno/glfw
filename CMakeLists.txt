cmake_minimum_required(VERSION 3.16)
project(GLFW C)

# ==========================================
# Core Sources (Common)
# ==========================================
set(GLFW_SOURCES
    "include/GLFW/glfw3.h"
    "include/GLFW/glfw3native.h"
    "src/internal.h"
    "src/platform.h"
    "src/mappings.h"
    "src/context.c"
    "src/init.c"
    "src/input.c"
    "src/monitor.c"
    "src/platform.c"
    "src/vulkan.c"
    "src/window.c"
    "src/egl_context.c"
    "src/osmesa_context.c"
    "src/null_platform.h"
    "src/null_joystick.h"
    "src/null_init.c"
    "src/null_monitor.c"
    "src/null_window.c"
    "src/null_joystick.c"
)

# ==========================================
# Platform Specifics (Windows)
# ==========================================
if(WIN32)
    # Add Windows-specific source files
    list(APPEND GLFW_SOURCES
        "src/win32_init.c"
        "src/win32_module.c"
        "src/win32_joystick.c"
        "src/win32_monitor.c"
        "src/win32_time.h"
        "src/win32_time.c"
        "src/win32_thread.h"
        "src/win32_thread.c"
        "src/win32_window.c"
        "src/wgl_context.c"
        # Note: egl/osmesa are already in the common list, 
        # but listing them again causes no harm in CMake.
    )

    # Add Windows Definitions
    add_compile_definitions(_GLFW_WIN32 _CRT_SECURE_NO_WARNINGS)
endif()

# ==========================================
# Create Target
# ==========================================
add_library(GLFW STATIC ${GLFW_SOURCES})

# Expose include directory to any project linking GLFW
target_include_directories(GLFW PUBLIC "include")

# ==========================================
# Static Runtime (staticruntime "on")
# ==========================================
# Forces /MT (Release) and /MTd (Debug) on MSVC
if(MSVC)
    set_property(TARGET GLFW PROPERTY 
        MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()