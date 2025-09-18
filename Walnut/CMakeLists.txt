cmake_minimum_required(VERSION 3.16)
project(Walnut)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Vulkan REQUIRED)

# Only include GUI platform files (not headless)
file(GLOB_RECURSE WALNUT_SOURCES 
    "Source/*.cpp"
    "Source/*.c"
    "Platform/GUI/*.cpp"          # Only GUI platform
    "Platform/GUI/*.c"
)

# Exclude headless files explicitly
file(GLOB_RECURSE WALNUT_HEADLESS_SOURCES 
    "Platform/Headless/*.cpp"
    "Platform/Headless/*.c"
)

# Remove headless sources from the main source list
list(REMOVE_ITEM WALNUT_SOURCES ${WALNUT_HEADLESS_SOURCES})

file(GLOB_RECURSE WALNUT_HEADERS 
    "Source/*.h"
    "Source/*.hpp"
    "Platform/GUI/*.h"            # Only GUI headers
    "Platform/GUI/*.hpp"
)

add_library(Walnut STATIC 
    ${WALNUT_SOURCES}
    ${WALNUT_HEADERS}
)

target_include_directories(Walnut PUBLIC
    Source
    Platform/GUI
    ../vendor/imgui
    ../vendor/GLFW/include
    ../vendor/stb_image
    ../vendor/glm
    ../vendor/spdlog/include
    ${Vulkan_INCLUDE_DIRS}
)

target_link_libraries(Walnut PUBLIC
    imgui
    glfw
    ${Vulkan_LIBRARIES}
)

if(WIN32)
    target_compile_definitions(Walnut PUBLIC WL_PLATFORM_WINDOWS)
    target_compile_options(Walnut PRIVATE /utf-8)
    
    set_property(TARGET Walnut PROPERTY 
        MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>DLL"
    )
    
    target_compile_options(Walnut PRIVATE /EHsc /Zc:preprocessor /Zc:__cplusplus)
endif()

target_compile_definitions(Walnut PUBLIC
    $<$<CONFIG:Debug>:WL_DEBUG>
    $<$<CONFIG:Release>:WL_RELEASE>
    $<$<CONFIG:RelWithDebInfo>:WL_RELEASE>
    $<$<CONFIG:MinSizeRel>:WL_DIST>
)

# Debug: Print what sources we're actually compiling
message(STATUS "Walnut sources: ${WALNUT_SOURCES}")
