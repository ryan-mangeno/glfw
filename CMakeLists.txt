cmake_minimum_required(VERSION 3.16)
project(GLFW C)

set(GLFW_SOURCES
    "include/GLFW/glfw3.h" "include/GLFW/glfw3native.h" "src/internal.h"
    "src/platform.h" "src/mappings.h" "src/context.c" "src/init.c"
    "src/input.c" "src/monitor.c" "src/platform.c" "src/vulkan.c"
    "src/window.c" "src/egl_context.c" "src/osmesa_context.c"
    "src/null_platform.h" "src/null_joystick.h" "src/null_init.c"
    "src/null_monitor.c" "src/null_window.c" "src/null_joystick.c"
)

find_package(Vulkan REQUIRED)
target_include_directories(GLFW PUBLIC ${Vulkan_INCLUDE_DIRS})
target_link_libraries(GLFW PUBLIC ${Vulkan_LIBRARIES})

if(WIN32)
    list(APPEND GLFW_SOURCES "src/win32_init.c" "src/win32_module.c" "src/win32_joystick.c"
        "src/win32_monitor.c" "src/win32_time.h" "src/win32_time.c" "src/win32_thread.h"
        "src/win32_thread.c" "src/win32_window.c" "src/wgl_context.c")
    add_compile_definitions(_GLFW_WIN32 _CRT_SECURE_NO_WARNINGS)
elseif(APPLE)
    list(APPEND GLFW_SOURCES "src/cocoa_init.m" "src/cocoa_joystick.m" "src/cocoa_monitor.m"
        "src/cocoa_window.m" "src/cocoa_time.c" "src/nsgl_context.m" "src/posix_thread.c"
        "src/posix_module.c" "src/posix_poll.c")
    add_compile_definitions(_GLFW_COCOA)
endif()

add_library(GLFW STATIC ${GLFW_SOURCES})
target_include_directories(GLFW PUBLIC "include")

if(MSVC)
    set_property(TARGET GLFW PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()

if(APPLE)
    target_compile_definitions(AcornApp PRIVATE VK_USE_PLATFORM_MACOS_MVK)
endif()
